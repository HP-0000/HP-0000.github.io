[Symbol Table](Public/Study/C++/Docs/Compile/Symbol%20Table.md)

네임스페이스가 명시되지 않은 함수식별자를 인자와 함께 호출 할 때
컴파일러는 인자의 스코프에 대한 Abstract Syntax Tree를 탐색한다.

---
###### friend 멤버 함수
함수에 `friend` 키워드를 붙이면  해당 함수는
클래스 내부 값에 접근 가능하면서도 클래스 스코프에 속하지 않는다.
또한 컴파일러 내부 심볼 테이블에 저장되지 않는다.

```cpp
namespace Game {
    class MyInt {
        friend void print(MyInt x) { ... }
    };
}

Game::MyInt a;
a.print(a); // ❌ print 함수는 Game 네임 스페이스에 속함 
Game::print(a); // ❌ 컴파일러 내부 심볼 테이블에 존재 하지 않음. 문법 오류
```

Abstract Syntax Tree 에는 `friend` 멤버 함수 선언이 있음으로 
```cpp
print(a); // ⭕ by ADL
```

[operator](Public/Study/C++/Docs/operator.md) 오버로딩에 유용하게 사용된다.

---
