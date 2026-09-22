값 자체와 존재 여부 플래그를 스택에 함께 보관한다.
```cpp

std::optional<std::string> FindUserName(int id) {
    if (id == 1) return "Alice";
    if (id == 2) return "Bob";
    return std::nullopt; // '값 없음'을 명시적으로 반환
}

auto name = FindUserName(1);
```

```cpp
if (name.has_value()) 
{ 
	std::cout << *name << "\n"; 
	std::cout << name.value() << "\n";
}
```

```cpp
auto unknown = FindUserName(99); // nullopt 반환
std::cout << unknown.value_or("Guest") << "\n"; // nullopt 인 경우 지정된 값 반환
```
