---
---

[캐시](/Public/Study/컴퓨터%20구조/Docs/캐시)
[SIMD(Single Instruction Multiple Data)](/Public/Study/컴퓨터%20구조/Docs/SIMD(Single%20Instruction%20Multiple%20Data))
# Alignment
CPU는 2의 거듭 제곱으로 메모리를 읽는다. 기본 수체계가 이진수다.
[비트 연산](/Public/Study/C++/Docs/비트%20연산)
그래서 메모리를 쓸때 정렬해주는 것이 성능에 좋다
### 1. pragma pack(n) : 패딩 제거 
구조체 멤버 사이의 빈 공간(Padding)을 강제로 없애 메모리를 최소화합니다. 
주로 네트워크 패킷이나 파일 입출력 시 규격을 맞추기 위해 사용합니다.

```cpp
[[pragma]] pack(push, 1) // 1바이트 단위로 꽉꽉 채움 (패딩 제거)
struct PackedStruct {
    char a;   // 1 byte
    int b;    // 4 bytes (원래라면 3바이트 패딩이 붙지만 바로 뒤에 붙음)
};
[[pragma]] pack(pop)     // 기존 정렬 설정으로 복구
// sizeof(PackedStruct) == 5
```
### 2. `alignas(n)` : 정렬 기준 상향 (줄 세우기)
구조체나 변수의 시작 주소를 특정 배수($n$)로 강제합니다. SIMD 연산처럼 데이터가 반드시 특정 경계에 맞아야 할 때 사용합니다.

```cpp
struct alignas(16) FastVector {
    float v[4]; 
};
// 이 구조체는 메모리 어디에 생성되든 항상 16의 배수 주소에서 시작함
```
### 3. `alignof(T)` : 정렬 정보 확인 (뒷조사)

특정 타입이나 구조체가 메모리에서 몇 바이트 단위로 정렬되는지(어떤 배수 주소에 놓여야 하는지) 정보를 가져옵니다.

```cpp
size_t n1 = alignof(int);      // 4 반환
size_t n2 = alignof(char);     // 1 반환

struct MyStruct { char a; int b; };
size_t n3 = alignof(MyStruct); // int를 따라 4 반환
```

---


