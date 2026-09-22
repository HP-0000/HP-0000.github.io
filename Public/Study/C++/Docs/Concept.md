C++20부터 컴파일러는 기존의 치환 실패(SFINAE) 처리뿐만 아니라, 
명시적인 제약 조건 만족 여부를 검사하여 후보 함수를 결정한다.

템플릿 인자의 제약 조건을 명명할때 사용한다.
(조건식의 작성에는 [requires 표현식](Public/Study/C++/Docs/requires%20표현식.md) 가 활용됨)
```cpp
template <typename T>
concept x = 조건식;
```

템플릿의 오버로딩이 명확해진다.
```cpp
template <x T>
void foo(T val);
```

`Concept` 를 직접 사용하는 대신 `requires` 를 템플릿 파라미터 목록 바로 뒤나 클래스 이름 뒤 또는 함수 시그니처 뒤에 작성해 제약 조건을 명시할 수 있다. 

```cpp
template <auto N> requires (N >= 0 && N <= 100) 
struct Percentage;

template <auto N>
struct Percentage requires (N >= 0 && N <= 100);
```

인스턴스 제약을 주기 위해 [Substitution_Failure_Is_Not_An_Error](Public/Study/C++/Docs/Compile/Substitution_Failure_Is_Not_An_Error.md)을 이용하려면 다음과 같이 템플릿을 중첩해서 작성해야 하며 가독성과 유지보수성이 크게 떨어진다. 또한 컴파일러가 불필요한 템플릿 인스턴스화를 반복하면서 컴파일 비용도 증가한다.

```cpp
template <typename T, 
          typename std::enable_if<std::is_integral<T>::value, int>::type = 0>
void foo(T); // T는 정수형 타입
```

반면 C++20의 Concepts를 사용하면 컴파일러가
제약 검사를 통해 후보를  빠르게 판별하므로 컴파일 비용을 줄일 수 있다.
템플릿 선언부의 제약 조건을 통해 인자 의도를 직관적으로 파악할 수 있으며, 
조건 불만족 시 컴파일 에러의 원인도 명확하게 파악할 수 있다.
[Abbreviated Function Template](Public/Study/C++/Docs/Abbreviated%20Function%20Template.md)
```cpp
template <std::integral T>
void foo(T);
```
###### 클래스 템플릿의 조건부 멤버 함수
클래스의 템플릿 인자에 대해 제약을 만족할 때만 멤버 함수가 구현된다.

```cpp
template <typename T>
class DataBuffer {
public:
    void print() const requires std::is_integral_v<T> 
    {}
};
```
###### 변수 제약
[auto](Public/Study/C++/Docs/Compile/auto.md) 의 추론된 타입에 대해 제약을 검증한다.
```cpp
std::integral auto x = 10;   // int
std::integral auto y = 3.14; // 컴파일 에러
```
