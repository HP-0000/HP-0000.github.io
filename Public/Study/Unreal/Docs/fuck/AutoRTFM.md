---
---

> **Auto Rollback Transactions for Failure Memory**  
> 에픽게임즈가 Unreal Engine에 도입한 C++ 트랜잭셔널 메모리 시스템

---
## 왜 만들었나?

- 트랜잭션 의미론(실패 시 롤백)을 C++ 코드에도 적용하기 위해
- 개발자가 코드를 수정하지 않아도 자동으로 롤백 가능하게 만드는 것이 목표

---
### 컴파일 과정
C++ 코드
  ↓
Clang  ← "C++를 읽는 부분" (프론트엔드)
  ↓
LLVM IR  ← "중간 언어" (어떤 언어든 여기로 변환됨)
  ↓
LLVM   ← "최적화 + 기계어 변환하는 부분" (백엔드)
  ↓
기계어 (.exe)

에픽은 **Clang+LLVM 전체를 포크**해서 자체 수정 버전을 만들고, 거기에 AutoRTFMPass을 추가함

```
LLVM IR
  ↓ AutoRTFMPass  ← 에픽이 추가
```
### 함수 클로닝
AutoRTFMPass는 원본 함수를 바탕으로 Write 추적 코드를 삽입하여 클론을 자동 생성한다.

| 버전                      | 호출 시점      | 동작                   |
| ----------------------- | ---------- | -------------------- |
| 원본 `TakeDamage()`       | 트랜잭션 **밖** | 평소와 동일, 오버헤드 없음      |
| 클론 `TakeDamagertfm()` | 트랜잭션 **안** | write마다 이전 값을 로그에 기록 |

## Abort를 이용한 롤백 원리

AutoRTFM 런타임은 **메모리 주소와 변경 전 값의 로그**를 유지합니다. 
컴파일러는 메모리에 쓰기가 발생할 때마다 런타임 함수를 호출하도록 코드를 삽입합니다.

```
트랜잭션 시작
  ↓
HP -= 10;  → 로그에 [HP 주소, 이전값=100] 기록
bIsDead = true; → 로그에 [bIsDead 주소, 이전값=false] 기록
  ↓
Abort 발생
  ↓
로그를 역순으로 순회 → 각 주소에 이전값 복원
  ↓
HP=100, bIsDead=false 원복 완료
```

Abort 시 되돌릴 수 없는 작업(ex: malloc)은 `AutoRTFM::OnAbort` 핸들러를 등록해서 수동으로 처리합니다. 
malloc의 경우 Abort 시 free를 호출하는 식

---
## 스레드 안전성

AutoRTFM은 **싱글스레드 트랜잭션만** 다룹니다. Verse가 호출하는 게임플레이 코드는 이미 메인스레드에서만 실행된다고 가정하기 때문에, 동시성 문제를 고려하지 않아 읽기(read)는 추적하지 않고 쓰기(write)만 추적 된다.

> 멀티스레드까지 지원하는 건 **AutoSTM** (Software Transactional Memory) 이라고 별도로 부르며, AutoRTFM은 그 전 단계입니다.