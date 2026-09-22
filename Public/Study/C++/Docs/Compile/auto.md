타입의 자리에 대체 작성하여 
컴파일러가 타입을 자동으로 추론하도록 하는 지시하는 키워드
변수 타입 선언, 함수 반환 타입을 대체할 수 있다.

컴파일러는 선언문을 만나면, `auto` 자리에 템플릿 매개변수 `T`를 대입한 
가상의 함수 호출 상황을 가정하여 [인자 타입 추론](Public/Study/C++/Docs/인자%20타입%20추론.md) 을 통해 타입을 결정한다. 
###### decltype(auto)
```cpp
decltype(auto) = decltype(expr) // std::type_identity_t<T> 
```

---
[Concept](Public/Study/C++/Docs/Concept.md)