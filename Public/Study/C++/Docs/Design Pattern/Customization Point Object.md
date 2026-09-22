인스턴스화(구체화)되지 않은 템플릿 함수는 인자로 넘길 수 없다.

```cpp
template <typename T>
void Myfunc(T x) { }

void caller(auto f) {};

caller(Myfunc<int>); // ⭕
caller(Myfunc); // 컴파일 에러 ❌
```

템플릿 [operator](Public/Study/C++/Docs/operator.md) 를 오버로딩 한 
전역 객체 안에 템플릿 함수를 넣어 컴파일 에러 없이 인자로 전달 후 호출하도록 하는 패턴이다.

```cpp
struct __myfunc
{
	template <typename T>
	void operator()(T x) const { } const
};

void caller(auto f) 
{
	f(10);
	f("hello");

}

inline constexpr __myfunc func;
caller(func);
```
---

