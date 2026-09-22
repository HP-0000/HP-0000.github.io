`\Engine\Source\ThirdParty\AtomicQueue\AtomicQueue.h`
###### AtomicQueueCommon
하위 구현체들은 모두 이 클래스를 `base`로 상속받는다.
순서대로  `pop` , `push` 를 보장하지 않아 `FIFO` 구조는 아니다.

```cpp
alignas(PLATFORM_CACHE_LINE_SIZE) std::atomic<unsigned> head_ = {};
alignas(PLATFORM_CACHE_LINE_SIZE) std::atomic<unsigned> tail_ = {};
```
하위 `N bits` 가 `cache line element index` 를 
상위 `N bits` 가 `cache line index` 를 나타낸다.

연속된 `index`의 상·하위 N비트를 스왑해, 서로 다른 캐시라인으로의 
분산 접근 인덱스로 바꾸어 거짓 공유([스레드_동기화](Public/Study/C++/Docs/ThreadSync/스레드_동기화.md) )를 방지한다.

`was_empty` , `was_full`,`try_pop`,`try_push`를 하면 `head` 와 `tail`  모두에 접근해 
캐시 경합이 발생함으로 `pop` , `push` 만을 호출하는 것이 성능상 낫다.

`push`, `pop` 시 `head` 및  `tail` 증가 시켜 `index`를 얻고 자식클래스의 
`do_push`, `do_pop` 을 호출하고 해당 함수는 분산 접근 인덱스로 변환하여
`AtomicQueueCommon`의 원자적 연산 함수를 호출한다.

---
###### AtomicQueue
요소 [atomic](Public/Study/C++/Docs/Standard%20Template%20Library/atomic.md) 가 `std::atomic<T>::is_always_lock_free` 를 만족 할때 사용된다.
```cpp
alignas(PLATFORM_CACHE_LINE_SIZE) std::atomic<T> elements_[size_] = {};
```
`do_push_atomic`, `do_pop_atomic` 을 호출한다. 빈 요소에 `NIL` 값을 두어, 
 이를 검사하며 스핀 루프를 돌아 `push`, `pop` 한다.
- AtomicQueueB 는 동적 크기 할당으로 동작함 
###### AtomicQueue2
컨테이너에 들어갈 수 있는 요소의 제약을 없애기 위해 
각 요소의 상태를 다루는 배열을 두어 스레드 안전성을 보장한다.
```cpp
enum State : unsigned char { EMPTY, STORING, STORED, LOADING };
alignas(PLATFORM_CACHE_LINE_SIZE) std::atomic<unsigned char> states_[size_] = {};
alignas(PLATFORM_CACHE_LINE_SIZE) T elements_[size_] = {};
```
`do_pop_any`, `do_push_any` 를 호출한다. 
- AtomicQueueB2 는 동적 크기 할당으로 동작함 
[AtomicQueue2](Public/Study/Unreal/Docs/BugFix/AtomicQueue2.md)

---























