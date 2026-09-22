특정 기호들은 `operator` 키워드를 사용하여 함수로 오버로딩 할 수 있다.
이때, 인자 중 최소 한개가 사용자 정의 객체여야하며 
함수 호출 연산자  `()` 를 제외하곤  매개 변수의 개수가 정해져있다.
###### 연산자 오버로딩
연산자를 호출하기 위해서는 객체가 연산자의 좌변에 와야한다.
```cpp
class MyInt 
{
int val;
public:
	MyInt(int v) : val(v) {}
	//`operator+` 는 단 한 개의 매개 변수로 오버로딩이 가능하다.
    MyInt operator+(const MyInt& rhs) const
    {
	    return MyInt(val + rhs.val);
    }
};

MyInt a(10);
auto b = a + 5; // a.operator+(MyInt(5)) 
auto c = 5 + a; // ❌ 컴파일 에러 5.operator+(a) 

auto d = a.operator+(5) // ⭕
```

[Argument-Dependent Lookup](Public/Study/C++/Docs/Argument-Dependent%20Lookup.md) 를 이용한다.
```cpp
class MyInt
{
	int val;
public:
	MyInt(int v) : val(v) {}
	// 멤버 함수가 아닌 독립 함수이다.
	friend MyInt operator+(const MyInt& lhs, const MyInt& rhs)
	{
		return MyInt(lhs.val + rhs.val);
	}
};

MyInt a(10);
auto b = a + 5; // operator+(a, MyInt(5))
auto c = 5 + a; // operator+(MyInt(5), a)

auto d = a.operator+(5) // ❌ 멤버 함수가 아님
auto d = MyInt::operator+(a,5) // ❌ 멤버 함수가 아님
auto d = ::operator+(a,5)// ❌ 네임 스페이스 명시로 ADL 작동 X
auto d = operator+(a,5)// ⭕ ADL
```