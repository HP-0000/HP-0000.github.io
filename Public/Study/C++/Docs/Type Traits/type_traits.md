컴파일 타임에 타입과 관련된 검사 및 변형 동작을 위한 표준 라이브러리
###### 접미사 규칙
[변수 템플릿](Public/Study/C++/Docs/변수%20템플릿.md)
- `_t` : 결과가 **타입(Type)** (`typename std::trait<T>::type`)
- `_v` : 결과가 **값(Value)** (`std::trait<T>::value`)
---
# 타입 변형
###### `std::remove_reference_t<T>`
- [참조](Public/Study/C++/Docs/참조.md) 제거 (`T&`, `T&&` $\rightarrow$ `T`)
###### `std::remove_const_t<T>`
[const, mutable](Public/Study/C++/Docs/Compile/const,%20mutable.md)
`const T*`, `T const*`, `const T&`, `T const&` 를 제외하고
`const`를 제거 한다.
###### `std::remove_pointer_t<T>`
포인터를 하나 제거한다. (`T*` $\rightarrow$ `T`)
###### `std::decay_t<T>`
함수에 값을 인자를 넘길 때 일어나는 모든 암시적 변환들을 적용한다.
- `const` 제거
- 참조 제거
- 배열(`T[]`) 을 포인터(`T*`) 변환
- 함수( `Ret(Args...)`)를  함수 포인터(`Ret(*)(Args...)`) 변환
###### `std::add_lvalue_reference_t<T>` 
참조(`&`) 를 붙인다.
###### `std::add_rvalue_reference_t<T>`
참조(`&&`) 를 붙인다.

# 조건부 타입 선택 
###### `std::conditional_t<bool, TypeA, TypeB>`
조건에 따라 `TypeA` 또는`TypeB` 가 된다.
###### `std::enable_if_t<bool, T>`
`true`일 때만 유효한 타입 `T`가 된다.
[Substitution_Failure_Is_Not_An_Error](Public/Study/C++/Docs/Compile/Substitution_Failure_Is_Not_An_Error.md) 를 유도하는데 주로 사용된다.
# 타입 검사

###### `std::is_same_v<T, U>`
두 타입이 일치하는지 확인한다.
###### `std::is_base_of_v<Base, Derived>`
`Base`가 `Derived`의 부모 클래스인지 확인한다.
###### `std::is_pointer_v<T>` 
타입이 포인터인지 확인한다.
###### `std::is_reference_v<T>`
타입이  참조인지 확인한다.
###### `std::is_invocable_v<Callable, Args...>`
주어진 인자(`Args...`)로 `Callable`이 호출 가능한 대상인지 확인한다.
###### `std::is_trivially_copyable_v<T>`
컴파일러 내장 함수를 사용하며 다음을 체크해준다.
- 복사 생성, 이동 생성, 복사 대입, 이동 대입 중 하나 이상 존재할 것
- `User-declared` 된 복사 및 이동 생성자 가 없을 것
- `User-declared` 된 복사 및 이동 대입 연산자가 없을 것
- `User-declared` 된 소멸자가 없을 것
- 가상 함수 가 없을 것
# 예외 안전성 검사
###### `std::is_nothrow_move_constructible_v<T>` 
`T`의 이동 생성자(`T(T&&)`)에 `noexcept`가 붙어 있는지 확인한다.  
###### `std::is_nothrow_copy_constructible_v<T>`
`T`의 복사 생성자(`T(const T&)`)에 `noexcept`가 붙어 있는지 확인한다.
###### `std::is_nothrow_move_assignable_v<T>`
`T`의 이동 대입 연산자(`operator=(T&&)`)에 `noexcept`가 붙어 있는지 확인한다.
###### `std::is_nothrow_copy_assignable_v<T>`
`T`의 복사 대입 연산자(`operator=(const T&)`)에 `noexcept`가 붙어 있는지 확인한다.
###### `std::is_nothrow_destructible_v<T>`
`T`의 소멸자에 `noexcept`가 붙어 있는지 확인한다.  
###### `std::is_nothrow_invocable_v<Callable, Args...>`
`Callable`을 `Args...` 인자로 호출했을 때, 해당 함수가 `noexcept`로 선언되어 예외를 던지지 않는지 확인한다.