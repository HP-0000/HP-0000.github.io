range 라는 [Concept](Public/Study/C++/Docs/Concept.md)  은 다음과 같다.
```cpp
template<class T>
concept range = requires(T& t) 
{
    std::ranges::begin(t);
    std::ranges::end(t);  
};
```

`std::ranges::begin` 과 `std::ranges::end`는 
[Customization Point Object](Public/Study/C++/Docs/Design%20Pattern/Customization%20Point%20Object.md) 패턴으로 만들어진 `inline`([Linkage](Public/Study/C++/Docs/Compile/Linkage.md))전역 객체이다.

컴파일 타임에 [constexpr](Public/Study/C++/Docs/Compile/constexpr.md)  및 제약 조건으로 분기하여
다음의 3가지 우선순위로 반복자를 안전하게 탐색하여 호출한다.

1. 배열 (`T[N]`)
   - 배열의 주소 포인터를 즉시 반환한다.
2. 멤버 함수 (`t.begin()`)
   - 객체 내부에 `.begin()` 과 `.end()` 멤버 함수가 존재하면서 올바른 반복자를 반환한다.
3. [Argument-Dependent Lookup](Public/Study/C++/Docs/Argument-Dependent%20Lookup.md)
   -  탐색된 인자 `t`의 네임스페이스에 정의된 독립 함수 `begin(t)`를 ADL로 존재한다.

반복자일 최소 조건은 역참조(`*it`)를 통해 값을 꺼내고
증가 연산(`++it`)을 통해 다음 위치로 전진할 수 있어야 한다.

---
# std::ranges library

기존 C++ STL의 반복자 쌍(`begin`, `end`)을 대체하는 C++20에 도입된 차세대 표준 라이브러리.

컨테이너 객체 자체를 Range로 전달받아 처리하며, 
타입 안전성과 편의성을 제공한다.
###### Dangling Iterator 차단
임시 객체가 전달되면 반복자 대신 `std::ranges::dangling` 을 반환한다.
역참조 연산자(`*it`)가 없어, 메모리에 접근하려 하면 컴파일 에러가 발생한다.

쓰레기 포인터를 참조하는 참사를 컴파일 타임에 막아준다.

```cpp
// 임시 벡터를 넘기면 it은 반복자가 아니라 'std::ranges::dangling' 타입이 됨!
auto it = std::ranges::max_element(std::vector<int>{1, 5, 2});

// std::cout << *it; // ❌ 컴파일 에러! (역참조 불가)
```
###### Sentinel 유연성
`end`가 반환하는 순회를 멈추는 조건이 `operator==` 로 오버로딩 된 객체.
`begin`과 반환 타입이 달라도 된다.
###### Projection
데이터의 추출과 연산을 분리하여 유연하게 작동한다.
  ```cpp
  struct User { std::string name; int age; };
  std::vector<User> users;

  std::ranges::sort(users, std::ranges::greater{}, &User::age);
  ```

---
######  View
원본 range 객체에 대해 순회 과정에서 실행할 연산 규칙을 정해둔 range

`Range Adaptor Closure Object`는 순회 과정에서 실행할 연산 규칙을 받아 
파이프 연산자 `|` 를  오버로딩하여 좌측의 range 객체를 받아 View를 반환한다.

```cpp
std::vector<int> numbers = { 1, 2, 3, 4, 5, ...};

auto my_view = numbers 
    | std::views::filter([](int n) { return n % 2 == 0; })
    | std::views::transform([](int n) { return n * n; })
    | std::views::take(3);

for (int x : my_view) 
{
    std::cout << x << "\n";
}
```