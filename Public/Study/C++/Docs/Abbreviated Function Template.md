C++20 부터 일반 함수 매개변수 자리에 [auto](Public/Study/C++/Docs/auto.md)를 작성하면
컴파일러가 이를 템플릿 함수로 변환해준다.
###### 제약 없는 템플릿
```cpp
void foo(auto x);
```
###### Concept 제약이 있는 템플릿
[Concept](Public/Study/C++/Docs/Concept.md)
```cpp
// "정수형(std::integral)만 받는 auto 매개변수"
void foo(std::integral auto x);

// 위 코드는 아래와 완전히 동일함
template <std::integral T>
void foo(T x);
```
###### 축약 표현의 한계
축약 표현은 매개변수마다 독립된 템플릿 타입을 생성한다.
```cpp
auto add_different(std::floating_point auto a, std::floating_point auto b) 
{
    return a + b;
}
```

컴파일러는 아래와 같이 템플릿을 만들어 준다.
```cpp
template <typename T1, typename T2>
    requires std::floating_point<T1> && std::floating_point<T2>
auto add_different(T1 a, T2 b) {
    return a + b;
}
```
`a`,`b` 각각에 `float` , `double` 을 넘길 수 있다.

두 타입을 완전히 일치시키고 싶다면 아래와 같이 직접 만들어 사용해야한다.
```cpp
template <std::floating_point T>
T add_same(T a, T b) {
    return a + b;
}
```