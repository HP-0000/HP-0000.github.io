스택을 사용하며 값을 저장할 타입들을 여럿 선언 할 수 있다.
내부적으로 대입 된 값의 타입을 추적해 둔다.
```cpp
std::variant<int, double, std::string> v = 10;
v = 3.14;
v = "Hello"
```
###### `std::holds_alternative`
현재 값의 타입을 확인한다.
```cpp
if (std::holds_alternative<std::string>(v)) 
{
    std::cout << "string 타입입니다.\n";
}
```

[get](Public/Study/C++/Docs/Standard%20Template%20Library/get.md) 을 통해 값에 접근한다.
###### `get_if`
예외를 발생시키지 않고 값을 얻는다. 주소값을 인자로 받으며 반환 값은 포인터이다.
```cpp
if (auto pVal = std::get_if<std::string>(&v)) {
    std::cout << "성공: " << *pVal << '\n';
} else {
    std::cout << "해당 타입이 아닙니다.\n";
}
```
###### `std::visit<ReturnType>(vis, v)
내부적으로 함수 포인터 테이블을 만들어 호출한다. 
다형성을 만들면서도 가상 함수와 달리 인라인화가 가능함으로 성능이 더 낫다.

```cpp
template <class Visitor, class... Variants>
std::visit(Visitor&& vis, Variants&&... vars);
```
`Variants` :  하나 이상의 `std::variant` 객체들
`Visitor`  : `operator()`가 정의된 호출 가능 객체
- 넘겨진 모든 variant들의 가능한 모든 타입 조합에 대해 빠짐없이 구현되어 있어야만 컴파일된다.
- 모든 분기가 동일한 타입을 반환해야 한다.

```cpp
struct DataProcessor {
    void operator()(int i) const { std::cout << "정수 처리: " << i * 2 << '\n'; }
    void operator()(double d) const { std::cout << "실수 처리: " << d + 0.5 << '\n'; }
    void operator()(const std::string& s) const { std::cout << "문자열 길이: " << s.size() << '\n'; }
};

std::variant<int, double, std::string> v = "test";
std::visit(DataProcessor{}, v);
```

[Lambda](Public/Study/C++/Docs/Compile/Lambda.md) 의 오버로드 패턴으로 간략하게 만들 수 있다.
```cpp
template<class... Ts> struct overloaded : Ts... { using Ts::operator()...; };

std::variant<int, double, std::string> v = 3.14;

std::visit(overloaded {
    [](int i) { std::cout << "int: " << i << '\n'; },
    [](double d) { std::cout << "double: " << d << '\n'; },
    [](const std::string& s) { std::cout << "string: " << s << '\n'; }
}, v);
```
###### `std::monostate`
variant는 단순 선언시, 첫 번째 타입의 기본 생성자를 호출해 생성된다.
비어있는 상태를 패턴 매칭하고 싶거나, 첫 번째 타입에 기본 생성자가 없을 때
사용하는 빈 구조체이다.
```cpp
std::variant<std::monostate, int, std::string> v;

if (std::holds_alternative<std::monostate>(v)) {
    std::cout << "현재 비어있는 상태입니다.\n";
}

v = 100; 
v.emplace<std::monostate>();
```

