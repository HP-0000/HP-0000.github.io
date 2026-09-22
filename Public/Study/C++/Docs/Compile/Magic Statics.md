
함수 내부의 `static` 지역 변수가 처음 초기화될 때 컴파일러는 하드웨어 수준에서 동기화를 보장한다.
전역 객체 생성 및 인스턴스 참조 획득에 유용하게 사용된다.

```cpp

FPageAllocator::FPageAllocator()
{
	Instance = this;
}

FORCENOINLINE FPageAllocator& FPageAllocator::Construct()
{
	static FPageAllocator PageAllocator;
	return *Instance;
}
```

컴파일러가 초기화 여부를 확인하고 최초 초기화를 동기화하기 위한 guard 코드를 생성한다. 
호출 시 guard 확인을 위한 코드로 인해 비용이 발생할 수 있다.

그래서 인스턴스 참조 획득 코드를 따로 구현해 둔다.
```cpp
FPageAllocator& FPageAllocator::Get()
{
	if (LIKELY(Instance != nullptr))
	{
		return *Instance; 
	}
	return Construct(); 
}
```

---

프로그램 종료시 모든 전역 객체 들의 소멸자가 호출되며 의존성 문제가 발생할 수 있다.
따라서 다음과 같이 소멸자 호출을 방지한다.

```cpp

FPageAllocator
{
	alignas(FPageAllocator) 
	static unsigned char Data[sizeof(FPageAllocator)];
	static FPageAllocator* Instance;
}

FPageAllocator::FPageAllocator()
{
	Instance = this;
}

FORCENOINLINE FPageAllocator& FPageAllocator::Construct()
{
	static FPageAllocator* PageAllocator = ::new((void*)Data) FPageAllocator();
	return *Instance;
}

FPageAllocator& FPageAllocator::Get()
{
	if (LIKELY(Instance != nullptr))
	{
		return *Instance; 
	}
	return Construct(); 
}
```

`Data` 는 바이트 배열 임으로 소멸자가 호출되지 않으며
`PageAllocator` 또한 포인터 임으로 소멸자가 호출되지 않는다.

위의 코드는 `Allocator`는 `new` 의 오버 로딩 구현에 사용됨으로 [메모리 할당](Public/Study/C++/Docs/메모리%20할당.md) `Placement New` 를 
사용하기 위해 일부러 `Data` 를 한번 더 사용했다.

