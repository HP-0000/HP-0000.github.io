타입의 자리에 대체 작성하여 
컴파일러가 타입을 자동으로 추론하도록 하는 지시하는 키워드
변수 타입 선언, 함수 반환 타입을 대체할 수 있다.

컴파일러는 선언문을 만나면, `auto` 자리에 템플릿 매개변수 `T`를 대입한 
가상의 함수 호출 상황을 가정하여 [인자 타입 추론](Public/Study/C++/Docs/인자%20타입%20추론.md) 을 통해 타입을 결정한다. 
###### decltype(auto)
```cpp
decltype(auto) = decltype(expr) // std::type_identity_t<T> 
```

###### 구조화된 바인딩
[get](Public/Study/C++/Docs/Standard%20Template%20Library/get.md)
C++17 에 도입된 복합 데이터 언패킹 특수 선언 문법
멤버 변수가 모두  `public` 인 구조체 및 배열에 지원된다.

`auto` 와 대괄호를 사용해 데이터의 요소 개수와 정확히 일치하도록 변수를 선언한다.
```cpp
struct Point { int x; int y; int z; }; // 멤버 3개
Point pt{1, 2, 3};

auto [a, b] = pt;    // ❌ 
auto [a, b, c] = pt; // ⭕ 
```

---
[Concept](Public/Study/C++/Docs/Concept.md)