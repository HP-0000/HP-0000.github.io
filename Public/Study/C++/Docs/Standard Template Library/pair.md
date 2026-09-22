두 데이터를 한 쌍으로 묶어주는 가장 단순한 구조체
```cpp
template <class T1, class T2>
struct pair {
    T1 first;   // 첫 번째 데이터
    T2 second;  // 두 번째 데이터
};
```

선언 방법은 다음과 같다.
[auto](Public/Study/C++/Docs/Compile/auto.md)
[인자 타입 추론](Public/Study/C++/Docs/인자%20타입%20추론.md)
```cpp
#include <utility>
#include <string>

// 1. 기본 선언
std::pair<int, std::string> p1(1, "Apple");

// 2.C++11: 템플릿 인자 타입 자동 추론
auto p2 = std::make_pair(2, "Banana");

// 3. C++17 CTAD 
std::pair p3(3, "Cherry"); // 알아서 std::pair<int, const char*>로 추론

// 4. C++17 구조화된 바인딩 
auto [id, name] = p1; // id = 1, name = "Apple"
```

[get](Public/Study/C++/Docs/Standard%20Template%20Library/get.md) 을 통해 접근 할 수 있으며 
[삼중 비교 연산자](Public/Study/C++/Docs/삼중%20비교%20연산자.md) 가 기본으로 구현된다.
멤버 함수로 `swap` 이 지원된다. 

[apply](Public/Study/C++/Docs/Standard%20Template%20Library/apply.md) 
###### `piecewise_construct`
생성자의 파라미터를 제공하여 직접 생성 하도록 한다.
```cpp
std::pair<Person, Person> p(
    std::piecewise_construct,
    std::forward_as_tuple("철수", 20), // first를 위한 생성자 재료
    std::forward_as_tuple("영희", 22)  // second를 위한 생성자 재료
);
```
[tuple](Public/Study/C++/Docs/Standard%20Template%20Library/tuple.md)

