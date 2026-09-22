어떤 타입이든 담을 수 있는 컨테이너.
데이터의 타입 정보를 내부에 보관한다.

타입 대입시 작은 데이터는 스택을 사용하나, 큰 데이터에 대해서는 동적 할당과 해제가 일어난다.
```cpp

std::any a = 10;                // int 저장
a = 3.14;                       // double로 변경
a = std::string("Hello World"); // string으로 변경
```
###### has_value
```cpp
if (a.has_value()) {
	std::cout << "타입: " << a.type().name() << "\n";
}
```
###### std::any_cast 
값을 얻을 때 사용한다.
```cpp
std::string s = std::any_cast<std::string>(a);
std::cout << s << "\n"; // "Hello World"
int wrong = std::any_cast<int>(a); // ❌ 타입 불일치 std::bad_any_cast 예외 발생
```

포인터로 꺼내는 경우 예외 대신 `nullptr`이 반환된다.
```cpp
if (std::string* ptr = std::any_cast<std::string>(&a)) {
	std::cout << "포인터 접근: " << *ptr << "\n";
}
```

```cpp
a.reset(); // a.has_value() == false 
```

---

후보군이 정해져 있다면 [variant](Public/Study/C++/Docs/Standard%20Template%20Library/variant.md) 를 사용한다.

