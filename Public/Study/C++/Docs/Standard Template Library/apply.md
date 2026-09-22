묶음 객체의 원소들을 함수의 파라미터로 언패킹하여 호출해준다.
  - `std::tuple`, `std::pair`, `std::array` 에 사용된다.

[tuple](Public/Study/C++/Docs/Standard%20Template%20Library/tuple.md)
[pair](Public/Study/C++/Docs/Standard%20Template%20Library/pair.md)
[array](Public/Study/C++/Docs/Standard%20Template%20Library/array.md)

```cpp
#include <tuple>
#include <utility>
#include <array>
#include <iostream>

void PrintUserInfo(int id, std::string name, double score) 
{
    std::cout << id << ": " << name << " (" << score << ")\n";
}

int Add(int a, int b) {
    return a + b;
}

int main() {
    // 1. tuple 
    std::tuple<int, std::string, double> user(101, "Alice", 92.5);
    std::apply(PrintUserInfo, user); // PrintUserInfo(101, "Alice", 92.5) 로 호출됨

    // 2. pair 
    std::pair<int, int> p(10, 20);
    int sum = std::apply(Add, p); // Add(10, 20) 으로 호출됨

    // 3. array 
    std::array<int, 2> arr{30, 40};
    int arr_sum = std::apply(Add, arr); // Add(30, 40) 으로 호출됨
}
```
