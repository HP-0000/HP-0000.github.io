[스레드_동기화](Public/Study/C++/Docs/ThreadSync/스레드_동기화.md)
원자적 연산과 메모리 배리어 기반 동기화를 제공한다.
```cpp
std::atomic<T> x;
```

타입 `T`가 정렬되어있다면,
8 바이트 이하는 락 없이 수행되며
8 바이트 초과 ~ 16 이하는 RMW  명령어로  변환되어 수행된다.
16 바이트를 초과하는 경우 소프트웨어 락을 걸어 연산한다.
소프트웨어 락이 걸리는지는 다음으로 확인할 수 있다.
```cpp
std::atomic<T>::is_always_lock_free
```
###### store
원자적 연산을 수행한다.
```cpp
x.store(T desired, std::memory_order);
x = desired; //이때 memory_order는 seq_cst 사용됨
```
###### load
원자적 연산을 수행한다.
```cpp
T y = x.load(std::memory_order);
T y  = x; //이때 memory_order는 seq_cst 사용됨
```
###### exchange
새 값을 쓰고 이전 값을 반환
```cpp
T y = x.exchange(T desired, std::memory_order);
```
### Compare-And-Swap
`expected`와 현재 값이 같으면 `desired`로 교체하고 `true`를 반환한다. 
`expected`와 현재 값이 다르면 `expected`를 현재 값으로 덮어쓰고 `false`를 반환한다.
###### compare_exchange_weak
하드웨어 설계로 인해 성공하더라도 실패가 반환될 수 있음 
```cpp
bool y = x.compare_exchange_weak(T& expected, T desired,
                           std::memory_order success,
                           std::memory_order failure);
```

###### compare_exchange_strong
실패가 반환되더라도 내부적으로 루프를 돌며 검증해 확실한 성공 실패 만을 반환함
```cpp
bool y = x.compare_exchange_strong(T& expected, T desired,
                           std::memory_order success,
                           std::memory_order failure);
```

CAS를 통해 포인터 변수의 주소를 비교할 경우, 해제와 재할당을 인지하지 못함으로 
유효하지 않은 메모리를 조작하는 버그를 유발할 수 있다.
###### 산술 및 비트 연산
원자적으로 수행되며, 반환 값은 연산 전 값이다.

| `fetch_add(val)`                   |
| :--------------------------------- |
| `fetch_sub(val,std::memory_order)` |
| `fetch_and(val,std::memory_order)` |
| `fetch_or(val,std::memory_order)`  |
| `fetch_xor(val,std::memory_order)` |
###  동기화 대기 및 알림
###### wait
현재 값이 `old_val`과 같으면 스레드를 재운다.
```cpp
x.wait(T old_val, std::memory_order);
```
###### notify
`wait()`로 자고 있는 스레드를 깨운다
```cpp
x.notify_one();
x.notify_all();
```

###### Memory order
메모리 배리어를 지정한다.
[CPU 메모리 모델](Public/Study/컴퓨터%20구조/Docs/CPU%20메모리%20모델.md)
하드웨어적으로 안전한 아키텍처라도 컴파일러의 동작 때문에 명시적 통제가 필요하다.