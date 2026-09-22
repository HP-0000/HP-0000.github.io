인자 [값](Public/Study/C++/Docs/값.md)의 타입을 반환한다.
인자 [값](Public/Study/C++/Docs/값.md)이 계산식인 경우 다음과 같이 반환된다. 
- `prvalue` :  순수 타입
- `xvalue` : 우측값 참조
- `lvalue` : 좌측값 참조  

생성할 수 없는 객체의 멤버에 접근할 때 다음의 템플릿을 활용한다.
```cpp
template <typename T>
T&& DeclVal();
```

```cpp
decltype(DeclVal<T>().member);
decltype(DeclVal<T>().function());
```