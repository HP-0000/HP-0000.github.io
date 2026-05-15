템플릿 선언시 템플릿 인자 타입을 명시한다.

```cpp
template <int N>
struct MyTool;
```

템플릿 구현 호출 시 템플릿 인자에 값을 전달 한다.
```cpp
MyTool<5> tool1;
MyTool<5> tool2;
MyTool<0> tool3;
tool1 = tool2 // ⭕ same Type
tool1 = tool3 // ❌ 컴파일 에러 different Type
```

C++20 이후, 객체를 NTTP에 사용할 수 있다.
이때 객체의 멤버 변수는 모두`public` 이어야 한다.
```cpp
struct Point 
{
    int x, y;
};


template <Point P>
struct MapTile;

// 호출 시 실제 객체 값을 전달
constexpr Point p1{10, 20};
constexpr Point p2{10, 20};
constexpr Point p3{10, 0};

MapTile<p1> tile1;
MapTile<p2> tile2; 
MapTile<p3> tile3; 

tile1 = tile2 // ⭕ same Type
tile1 = tile3 // ❌ 컴파일 에러 different Type

```

---

NTTP 사용시, [auto](Public/Study/C++/Docs/auto.md) 를 사용해 추론된 인자로 선언된 템플릿을 사용할 때
```cpp
template <auto N>
struct MyTool;
```

[Concept](Public/Study/C++/Docs/Concept.md) 를 이용해 추론 범위를 제약할 수 있다.
```cpp
template <std::integral auto N>
struct MyTool;
```







