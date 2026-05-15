값을 인자로 받아 요소를 제거한다.
```cpp
template <typename ForwardIterator, typename T>
ForwardIterator remove(ForwardIterator first, ForwardIterator last, const T& value);
```

읽기 포인터를 전진시키며 탐색 범위 내의 메모리를 순회하여
처음으로 제거 요소를 발견하면 그 자리에 쓰기 포인터 위치를 고정한다.
읽기 포인터를 전진 시키며 제거할 값이 아닌 정상 요소를 만날 때마다
쓰기 포인터 위치에 값을 덮어 쓰고 쓰기 포인터 위치를 전진 시킨다.
순회가 끝나면  쓰기 포인터를 반환한다. ( 잉여 객체들이 소멸자 호출 없이 남는다)

시간 복잡도 :  $O(N)$ 
공간 복잡도 :  $O(1)$

