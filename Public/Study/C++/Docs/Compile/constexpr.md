---
---
컴파일 타임에 계산되는 상수를 정의하는 데 사용하는 C++ 키워드. 
런타임 계산을 줄여 성능을 최적화하고 코드 안정성을 높임
######  생성자
```cpp
struct Point 
{
    int x, y;
    constexpr Point(int x, int y) : x(x), y(y) {}
};

constexpr Point p{10, 20};
```

컴파일 타임에 객체`p`의 바이너리 데이터가 생성된다.
######  변수
`constexpr` 변수는 반드시 컴파일 시점에 값이 결정되어야 함, 이후 변경 불가.

```cpp
// 컴파일 타임에 50이 계산됨
constexpr int value = 10 * 5; 

// 런타임에 값이 결정되기 때문에 컴파일 에러
constexpr int error_value = get_runtime_value();
```

`constinit` 는 정적 수명을 가진 변수에 한정되어 사용되며
반드시 컴파일 시점에 값이 결정되어야 하며 런타임에 값이 수정 가능하다.
###### 함수
`constexpr` 함수는 컴파일 계산 값을 인자로 호출되면 컴파일 타임에 계산된다.
컴파일러는  가상 메모리 관리자를 통해 `constexpr` 환경에서 할당한 메모리를 해제하였는지, 
관련된 메모리 주소를 리턴하고 있는지 검사하여 컴파일 오류를 발생 시킨다.

인자가 런타임에 결정되면 아무 제약 없이 일반 함수처럼 런타임에 실행 동작한다.
```cpp
constexpr int power(int base, int exp) {
    int res = 1;
    for (int i = 0; i < exp; ++i) {
        res *= base;
    }
    return res;
}

// 컴파일 시점에 계산
constexpr int result = power(2, 10);

// 런타임에 계산
int dynamic_result = power(2, input); 
```

런타임에 호출 되면 컴파일 에러를 발생시키도록 하려면
`consteval`를 대신 사용한다.
###### if constexpr
컴파일 시점에 참인 코드 블록만 남기고 거짓(false)인 블록은 코드에서 삭제하여 컴파일한다.
거짓인 블록에 대해서는 타입 검사를 면제한다.
