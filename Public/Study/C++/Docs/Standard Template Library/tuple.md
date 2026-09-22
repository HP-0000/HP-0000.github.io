요소가 2개로 고정되는 [pair](Public/Study/C++/Docs/Standard%20Template%20Library/pair.md) 에서 더욱 확장되어 
데이터들을 $N$개 묶어주는 구조체 

선언 및 사용 방법은 다음과 같다.
```cpp
#include <tuple>
#include <string>

// 1. 기본 선언
std::tuple<int, std::string, double> t1(1, "Apple", 4.5);

// 2. C++11: 템플릿 인자 타입 자동 추론
auto t2 = std::make_tuple(2, "Banana", 3.0);

// 3. C++17 CTAD 
std::tuple t3(3, "Cherry", 1.2); // 알아서 std::tuple<int, const char*, double>로 추론

// 4. C++17 구조화된 바인딩
auto [id, name, score] = t1; // id = 1, name = "Apple", score = 4.5
```

[get](Public/Study/C++/Docs/Standard%20Template%20Library/get.md) 을 통해 접근 할 수 있으며 
[삼중 비교 연산자](Public/Study/C++/Docs/삼중%20비교%20연산자.md) 가 기본으로 구현된다. (0번 인덱스부터 사전순 비교)
멤버 함수로 `swap` 이 지원된다. 

[apply](Public/Study/C++/Docs/Standard%20Template%20Library/apply.md) 