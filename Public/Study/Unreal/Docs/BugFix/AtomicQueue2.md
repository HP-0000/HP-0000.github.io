[AtomicQueue](Public/Study/Unreal/Docs/Container/AtomicQueue.md)

`do_pop_any` 에서 `spsc_` 가 아닌 경우 
`CAS` 연산을 통해 `LOADING` 또는 `LOADING` 상태에 진입하는데 메모리 베리어를
 `std::memory_order_relaxed` 를 쓰고 있다.
```cpp

for (;;) 
{
	unsigned char expected = STORED;
	if (LIKELY(state.compare_exchange_strong(expected, LOADING,
	std::memory_order_relaxed, std::memory_order_relaxed))) 
	{
		T element{ std::move(q_element) };
		state.store(EMPTY, std::memory_order_release);
		return element;
	
	}
```

`q_element`가 쓰기 완료되었음을 보장해야함으로  아마도  `std::memory_order_acquire` 를 사용해야 
할 것이다.
