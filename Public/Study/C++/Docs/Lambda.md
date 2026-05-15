---
---
실행할 함수와 사용할 변수를 선언해 두면
컴파일러가 `operator(Args ...)` 를 가진 익명 구조체를 대신 생성해 준다.
###### 기본 문법
[후행 반환](Public/Study/C++/Docs/후행%20반환.md) 형식으로 반환 타입을 명시할 수 있다

```cpp
[캡처](인자) -> 반환타입
{ 
	... 
};
```

###### C++20 템플릿 람다
```cpp
auto x = [캡처]<typename T>(인자) -> 반환타입
{ 
	... 
};
```
###### Overload 패턴

```cpp
template<typename... Ts> 
struct overloaded : Ts... 
{ 
    using Ts::operator()...; 
};


auto x = overloaded 
{
    []() {},    
    []() {}   
};
```

`overloaded` 는 [생성자 호출] 의 `Aggregate initialization` 을 거친다.
상속 받은 부모 생성자로 람다 표현식이 전달되는데 `prvalue` 임으로
부모 서브 오브젝트 메모리 위치에 직접 생성된다.


