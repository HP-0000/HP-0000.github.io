`\Engine\Source\Runtime\Core\Public\Containers\LockFreeList.h`
###### `TLockFreeAllocOnceIndexedAllocator`
`Interlocked`로 할당 될 요소 인덱스를 증가 시키며 [atomic](Public/Study/C++/Docs/Standard%20Template%20Library/atomic.md) 의 `CAS` 를 활용해 블록 단위로 메모리를 할당한 뒤 블록 내에서 각 요소의 자리에 생성자를 호출한다. 정해진 최대 요소 개수만큼 선형적으로 메모리를 할당하며, 할당된 메모리들은 해제 하지 않는다.







