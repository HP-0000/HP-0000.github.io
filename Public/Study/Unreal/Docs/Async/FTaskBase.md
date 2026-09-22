`LowLevelTasks::Tasks_Impl::FTaskBase` 클래스 내부에는 다음의 선언이 있다.

```cpp
mutable void* UserData = nullptr;
FPackedDataAtomic PackedData; //Task 관리에 대한 데이터

using FTaskDelegate = TTaskDelegate<FTask*(bool), LOWLEVEL_TASK_SIZE - sizeof(FPackedData) - sizeof(void*)>;		
FTaskDelegate Runnable;
```

캐시 효율을 위해 `FTaskBase` 가 64바이트로 구성되며
FTaskDelegate를 통해 실행할 Lambda 를 담는다.

`TTaskDelegate` 의 내부에는 다음의 선언이 있다.
```cpp
static constexpr uint32 InlineStorageSize = TotalSize - sizeof(TTaskDelegateBase);
mutable char InlineStorage[InlineStorageSize];
TTaskDelegateBase CallableWrapper;

```

람다의 크기가`InlineStorageSize` 을 초과할 경우, [[ConcurrentLinearAllocator](Public/Study/Unreal/Docs/Allocator/ConcurrentLinearAllocator.md)
를 이용해 람다 크기만큼 할당받은 포인터를 `InlineStorage` 에 복사한다.

작거나 같은 경우에는, `InlineStorage` 안에 복사하여
캐시 효율적이게 된다.

---
`class FTask final : private Tasks_Impl::FTaskBase`
내부의 `FTask* FTask::ExecuteTask()` 함수 구현을 살펴보면

Atomic 변수를 이용해 `ETaskState` 의 상태를 

최초로 ETaskState::RunningFlag 로 전환한 경우,
`FTaskDelegate` 임시 변수를 선언 후 
람다 객체를 실행 후 임시 변수로 이동 시킨다.
`ETaskState::CompletedFlag` 로 전환된다.
`ExecuteTask` 함수가 종료됨에 따라 임시 변수가 파괴되면서 람다 객체가 소멸 된다.



최초로 전환에 실패한 경우

작동 스케줄을 이해하고
rhi 들어가고
렌더링 파트 조금 다 이해하고




---


그다음 수학 정리하고
광원이해하고 
디퓨즈 이후 
레이트레이싱 이해하고 
다시 렌더링 코드 분석 고