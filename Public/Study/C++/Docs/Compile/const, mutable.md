###### const
해당 타입은 값이 고정된다.
- 주소값 고정 타입 : 포인터 `*`의 **오른쪽**에 작성 (`T* const`)
- 데이터값 고정 타입
  타입 `T`의 **왼쪽 또는 오른쪽**에 작성 
  (`const T*`, `T const*`, `const T`, `T const`, `const T&`, `T const& `)
###### 함수
```cpp
const int&  GetTarget(const FString& InName)  const
```
###### 반환형에 붙을 때
반환 타입은 const임 
###### 매개변수에 붙을 때
함수 안에서 매개변수의 값은 읽기 전용임
매개 변수가 참조나 포인터인 경우 함수는 오버로딩된다.
###### 함수 끝에 붙을 때
[Implicit Object Parameter](Public/Study/C++/Docs/Compile/Implicit%20Object%20Parameter.md) 
함수 안에서 클래스 멤버 변수 값은 읽기 전용임
함수는 오버로딩된다.
###### mutable
const의 제약을 무력화하여, 예외적으로 값의 수정을 허용하는 속성 키워드이다.
클래스 멤버 변수 선언 앞, 람다 함수의 매개변수 괄호 바로 뒤에 위치할 수 있다.


