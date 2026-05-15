---
---
[값](Public/Study/C++/Docs/값.md) , [참조](Public/Study/C++/Docs/참조.md), [초기화](Public/Study/C++/Docs/초기화.md)
# unique_ptr
객체의 소유권을 하나의 포인터가 독점적으로 가지도록 하는 스마트 포인터

```
std::unique_ptr<T, Deleter>
```
스마트 포인터의 수명이 다햇을때 `Deleter`를 호출하여 소유한 객체의 수명을 관리한다.

`unique_ptr` 은 이동 생성자, 이동 대입 연산자를 통해 포인터의 단독 소유권을 보장한다.
복사 생성자와 복사 대입 연산자는 존재하지 않는다.
# shared_ptr
`shared_ptr`은 객체 포인터(`_Ptr`)와 참조 관리 객체 포인터(`_Rep`)를 갖는다.

복사 생성자와 복사 대입 연산자를 통해 동일한 `_Ptr` 과 `_Rep` 을 공유하며 
`_Rep` 의 `strong_count` 를 변경한다.

`strong_count` 값이 0이 되는 경우, `_Ptr` 수명이 만료된다.
# weak_ptr
`weak_ptr`은 객체 포인터(`_Ptr`)와 참조 관리 객체 포인터(`_Rep`)를 갖는다.

복사 생성자와 복사 대입 연산자를 통해 동일한 `_Ptr` 과 `_Rep` 을 공유하며 
`_Rep` 의 `weak_count` 를 변경한다.

`shared_ptr`를 인자로 받는 생성자와 대입 연산자 오버로딩을 통해 
동일한 `_Ptr` 과 `_Rep` 을 공유하며  `_Rep` 의 `weak_count` 를 변경한다.

`strong_count`, `weak_count` 의 값이 0이 되는 경우, `_Rep` 수명이 만료된다.

---
###### 공유  포인터 생성
`_Ptr` 생성 후, `shared_ptr`의 생성자 인자로 넘겨받아 `_Rep`를  할당하는 대신
`make_shared`를 사용하면 객체와 참조 관리 객체의 메모리를 한번에 할당하기 때문에 
명령어를 줄일 수 있다.

다만 후자의 경우 `_Ptr` 수명이 만료되더라도 `_Rep` 수명이 만료될 때 까지
메모리 해제가 일어나지 않는다.
###### 공유 포인터 반환
클래스가 `std::enable_shared_from_this`를 상속받으면
`weak_ptr`을 멤버 변수로 갖게 되며, 

외부에서 `shared_ptr`로 생성될 경우 `weak_ptr`로 대입연산이 발생한다.
이후, 클래스 내부에서 `shared_from_this()` 를 호출하면
기존 `_Rep`를 공유하는 안전한 `shared_ptr`을 반환할 수 있다.

