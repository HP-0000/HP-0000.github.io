엔진이 제공하는 기본 전역 힙 메모리 할당자에 대한 전역 변수이다.
런타임에 결정된 `FMalloc` 을 가상 함수를 거쳐 호출한다.

[Magic Statics](Public/Study/C++/Docs/Compile/Magic%20Statics.md) 를 통해 
`UE::Private::GMalloc` 은 단 한번 초기화 되며
```cpp
/* 
	\Engine\Source\Runtime\Core\Private\Misc\CoreGlobals.cpp` 
*/
CORE_API FMalloc* UE::Private::GMalloc = nullptr;	
CORE_API FMalloc* const& GMalloc = UE::Private::GMalloc;
```
다른 모듈들은 같은 주소의 할당자를 사용하게 된다.
```cpp
/* 
	\Engine\Source\Runtime\Core\Public\HAL\MemoryBase.h` 
*/
CORE_API extern class FMalloc* const& GMalloc;
```
메모리 할당 연산자에 대한 오버로딩이 정의되있다.
```cpp
/^
	\Engine\Source\Runtime\Core\Public\HAL\PerModuleInline.inl
^/

REPLACEMENT_OPERATOR_NEW_AND_DELETE
UE_DEFINE_FMEMORY_WRAPPERS
```

메모리는 `FMemory` 를 거쳐 `GMalloc`에 접근한다.
`FMemory`는 메모리 할당에 대한 로깅 및 [AutoRTFM](Public/Study/Unreal/Docs/AutoRTFM.md) 작업을 위해  `GMalloc`을 래핑한 객체이다.

---





```
[OS 메모리 (VirtualAlloc / mmap)]
       │
       ├── 1. 전역 힙 계층 (FMalloc 계열 - GMalloc)
       ├── 2. 휘발성/임시 메모리 계층 (Linear / Stack 계열)
       ├── 3. 컨테이너 내부 정책 계층 (TArray, TSet 등)
       ├── 4. 엔진 특화 서브시스템 계층 (UObject, LLM 등)
       ├── 5. GPU / VRAM 계층 (RHI)
       └── 6. 디버그 / 프로파일링 계층
```


## Tier 2. 휘발성/임시 메모리 계층 (Frame / Scratchpad)
> **특징:** $O(1)$ 포인터 덧셈 할당. 개별 해제(`Free`) 없음. 프레임/스코프 종료 시 **일괄 리셋(Bulk Free)**.

* **`FMemStackBase` / `FMemMark`**
  * 렌더링/애니메이션 등 단일 스레드 루프에서 가장 많이 쓰는 스택 할당자. 책갈피(`Mark`) 꽂아두고 마구 쓰다 `Pop()`하면 소멸.
* **`ConcurrentLinearAllocator`** (질문하셨던 그 녀석)
  * 멀티스레드 워커들이 원자적(Atomic)으로 락 없이 포인터 밀면서 임시 메모리 땡겨 쓰는 R&D용 할당자.
* **`TlsLinearAllocator`**
  * 스레드 로컬 저장소(TLS)에 바인딩된 단일 스레드 전용 선형 할당자.

---

## Tier 3. 컨테이너 템플릿 정책 계층 (Container Policies)
> **특징:** `TArray<T, Allocator>` 뒤에 붙는 템플릿 인자. 컴파일 타임 100% 인라인 최적화.

* **`FDefaultAllocator`**
  * 아무것도 안 쓰면 들어가는 기본값. 그냥 `FMemory::Malloc` (GMalloc) 호출.
* **`TInlineAllocator<N>`** (실무 최적화 1순위)
  * 데이터 개수가 $N$개 이하일 때는 **스택(Stack)**에 배열을 잡고, $N$개를 초과할 때만 힙(Heap)으로 탈출.
* **`TFixedAllocator<N>`**
  * 무조건 스택에 $N$개 고정. $N$개 넘어가면 크래시 발생 (동적 힙 할당을 절대 허용하지 않음).
* **`TSparseArrayAllocator` / `TSetAllocator`**
  * `TSet`, `TMap`처럼 데이터가 듬성듬성 비어있는 희소 배열을 위한 슬롯 관리용 할당자.

---

## Tier 4. 엔진 특화 서브시스템 계층 (Subsystem Heaps)
> **특징:** 엔진의 특정 기능만을 위해 완전히 격리된 메모리 풀.

* **`FUObjectArray` / `FUObjectCluster`**
  * UObject 전용 메모리 풀. GC(가비지 컬렉터)가 빠른 속도로 순회하고 표시(Mark & Sweep)할 수 있도록 물리적으로 묶어둠.
* **`FLLMAllocator`**
  * LLM(Low Level Memory Tracker) 전용. 메모리 누수 감시자가 엔진 힙을 건드려 무한 루프 도는 걸 막기 위해 OS API(`VirtualAlloc`)를 직접 씀.
* **`FTaskTagAllocator`**
  * Task Graph(작업 스레드) 시스템에서 태스크 노드들을 쪼갤 때 쓰는 전용 할당자.

---

## Tier 5. GPU / VRAM 계층 (RHI - Render Hardware Interface)
> **특징:** RAM이 아니라 비디오 메모리(VRAM)를 관리. 반환값이 `void*`가 아닌 GPU 리소스 핸들이나 오프셋.

* **`FRHITransientResourceAllocator`** (UE5 RDG 핵심)
  * 렌더 패스 간 VRAM 돌려막기(Aliasing) 전용. 그림자 패스가 끝나면 그 VRAM 주소 그대로 포스트 프로세싱이 덮어씀.
* **`FD3D12BuddyAllocator`**
  * DX12 VRAM 파편화 방지용. 2의 거듭제곱($2^N$) 크기로 비디오 메모리를 잘라주는 버디 할당자.
* **`FD3D12FastAllocator` / `RingBuffer`**
  * CPU가 매 프레임 GPU로 쏴주는 유니폼/상수 버퍼(Upload Heap)를 원형 링 버퍼로 순환 관리.
* **`FVirtualTexturePhysicalSpace`**
  * 나나이트/버추얼 텍스처용. 화면에 보이는 타일만 VRAM 텍스처 아틀라스에 퍼즐처럼 끼워 넣는 타일 할당자.

---
