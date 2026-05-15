---
---
비상용 메모리를 미리 할당해 둔 뒤 할당자를 제공한다.

#### 할당자 구현
BookkeepingPool 에 FPtrInfo 배열을 생성한다. 
SmallMemoryPool 을 사이즈 별로 구역으로 쪼갠다.
각 구역을 담당하는 FMallocCrashPool 이 FPtrInfo의 사용을 관리한다.

FPtrInfo 은 주소와 사이즈를 제공한다.
BookkeepingPool을 선형 탐색하여 주소를 통해 FPtrInfo를 찾고.
마찬가지로 FMallocCrashPool 에 접근 하여 할당과 해제를 관리한다.

만약 구역을 넘어서는 메모리 할당이 요구되는 경우 주소 OFFSET을 밀면서
LargeMemoryPool에 FPtrInfo를 포함하여 제공한다. 재사용은 고려하지 않는다.