
컨테이너에서 원소를 꺼내는 템플릿 함수를 지칭한다.
사용하는 컨테이너에 따라 `<utility>`, `<tuple>`, `<array>`, `<variant>`에 
각각의 구현이 정의되어 있다.

---
원소의 참조를 반환한다.
###### `std::get<Index>(obj)`
컴파일 타임 상수를 인자로 제공하여 값을 얻는다.
###### `std::get<Type>(obj)`
컨테이너 안에 해당 자료형 타입이 유일하게 한 개 존재할 때 값을 꺼낸다.

---

[auto](Public/Study/C++/Docs/Compile/auto.md) 구조화된 바인딩은 컴파일러가 `std::get` 호출하는 코드로 변환한다.

```cpp
auto [x, y] = p;

// ================= [ 컴파일러의 변환 코드 ] =================
auto __hidden = p; 
decltype(auto) x = std::get<0>(__hidden); 
decltype(auto) y = std::get<1>(__hidden);
```