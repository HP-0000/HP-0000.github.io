---
---

```cpp
noexcept(bool)
```
[Exception Handling](Public/Study/C++/Docs/Exception%20Handling.md)
###### 키워드
함수의 선언이나 정의에서 매개변수 괄호 () 바로 뒤에 명시하여 
예외를 발생시키지 않음을 컴파일러에게 알린다.

아래와 같이 사용하면 기본적으로 `true` 로 해석한다.
```cpp
noexcept
```

컴파일러 수준에서 복잡한 `Landing Pad` 및 `Exception Table` 추가 동작을 하지 않음으로 바이너리 용량이 감소하고, 예외가 발생하지 않는 함수임으로 상태 보존을 위해 스택 메모리에 값을 기록할 필요가 없어 레지스터 위주로 최적화된 코드를 생성할 수 있다.

컴파일러가  생성한 각각의 [특수 멤버 함수](Public/Study/C++/Docs/특수%20멤버%20함수.md) 들은 
멤버 변수들 객체들의 동일한 특수 멤버 함수가 `noexcept` 붙는 경우
컴파일러에 의해 `noexcept` 가 붙는다.

이때 부모 객체가 존재하는 경우, 부모의 특수 함수가 `noexcept` 일 경우에 붙는다.
소멸자에 한해서는 예외를 처리할 방법이 없음으로  `noexcept` 를 반드시 붙인다.
###### 연산자
함수가 `noexcept` 선언하였는지 검사
```cpp
void funcA() noexcept {};
void funcB() {};

bool b1 = noexcept(funcA) // true
bool b2 = noexcept(funcB) // false
```

컴파일러 규칙상 자체적으로 예외를 발생시키도록 정해진 키워드들은 false를 반환한다.
 `throw`, `new`, `dynamic_cast<참조>`, `typeid

---
[type_traits](Public/Study/C++/Docs/Type%20Traits/type_traits.md) 를 통해 `noexcept` 키워드의 유무를 판단할 수 있다.
C++표준 자료구조(STL) 는 if [constexpr](Public/Study/C++/Docs/constexpr.md) 을 활용해 
타입 `T` 의 이동할 때 `noexcept` 인지에 따라 복사와 이동을 결정한다.