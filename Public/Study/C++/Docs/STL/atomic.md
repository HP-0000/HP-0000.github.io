[스레드_동기화](Public/Study/C++/Docs/ThreadSync/스레드_동기화.md)

원자적 연산과 메모리 배리어 기반 동기화를 제공한다.
```cpp
std::atomic<T> x;
```

타입 `T`가 정렬되있고 정해진 크기를 만족한다면 하드웨어 명령어를 통해
원자적 연산을 수행한다. 원자적 연산이 수행되는지는 다음으로 확인할 수 있다.
```cpp
std::atomic<T>::is_always_lock_free
```
그렇지 않다면 내부적으로 소프트웨어 락을 걸고 연산한다.

###### store
값을 원자적으로 저장
```cpp
x.store(T desired, std::memory_order);
x = desired; //이때 memory_order는 seq_cst 사용됨
```

###### load
현재 값을 원자적으로 읽음

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
###### 산술 및 비트 연산
원자적으로 수행되며, 반환 값은 연산 전 값이다.

| `fetch_add(val)` |
| :--------------- |
| `fetch_sub(val)` |
| `fetch_and(val)` |
| `fetch_or(val)`  |
| `fetch_xor(val)` |
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

| ordering  | LL  | LS  | SS  | SL  |
| :-------- | :-: | :-: | :-: | :-: |
| `relaxed` |  ✗  |  ✗  |  ✗  |  ✗  |
| `release` |  ✗  |  ✓  |  ✓  |  ✗  |
| `acquire` |  ✓  |  ✓  |  ✗  |  ✗  |
| `acq_rel` |  ✓  |  ✓  |  ✓  |  ✗  |
| `seq_cst` |  ✓  |  ✓  |  ✓  |  ✓  |
