내부적으로 전용 문법을 사용해 작성된 요구 사항 목록을
모두 만족하는지 검사하고 하나라도 만족하지 못하면 false로 평가된다.

```cpp
requires (Args ...) 
{
    요구사항 목록
}
```
###### Simple Requirement
컴파일 가능한 식인지 검사
```cpp
template <typename T>
concept MyConcept = requires(T a, T b)
{
	// T끼리 '+' 연산이 가능한가?
	a + b; 
	// .draw() 멤버 함수가 존재하는가?
	a.draw(); 
	// 역참조(*) 연산자가 가능한가?
	*a; 
}
```
###### Type Requirement
타입 이름이 존재하는지 검사
```cpp
template <typename T>
concept MyConcept = requires
{
	// T 내부에 'value_type'이라는 타입 정의가 있는가?
	typename T::value_type; 
	// std::vector<T>로 인스턴스화가 가능한 타입인가?
	typename std::vector<T>; 
}
```

###### Compound Requirement
표현식이 유효한지,  `noexcept` 가 있는지 , 표현식의 결과 타입이 조건을 만족하는지 검사한다.
```cpp
{ 표현식 } noexcept -> concept;
```

```cpp
template <typename T>
concept MyConcept = requires(T a)
{
    // a.size()가 존재해야 하고, 그 반환 타입이 std::integral(정수형)이어야 함
    { a.size() } -> std::integral;

    // a.clear()가 존재해야 하고, 예외가 발생하지 않음
    { a.clear() } noexcept;

    // a.data()가 존재해야하고, 반환 타입이 반드시 'const char*'와 완벽히 같아야 함
    { a.data() } -> std::same_as<const char*>;
}
```
###### Nested Requirement
조건식의 계산 결과를 검사한다.
```cpp
template <typename T>
concept MyConcept = requires 
{ 
	// T의 크기가 4바이트 이상이어야 함
    requires sizeof(T) >= 4;
                      
    // 기본 생성자가 있어야 함
    requires std::is_default_constructible_v<T>; 
};
```

---
[Concept](Public/Study/C++/Docs/Concept.md) 를 정의하는 조건식에 사용한다.







