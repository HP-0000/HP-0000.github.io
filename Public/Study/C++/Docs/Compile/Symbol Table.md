###### 바이너리 심볼 테이블
컴파일러는 [Name Mangling](Public/Study/C++/Docs/Compile/Name%20Mangling.md) 을 거친 뒤 
[Linkage](Public/Study/C++/Docs/Compile/Linkage.md) 를 위해 심볼 테이블을 만들어 낸다.

---

컴파일러는 소스코드를 읽으며 
컴파일러 심볼 테이블과 `Abstract Syntax Tree` 를 생성한다.
###### 컴파일러 심볼 테이블 
스코프 마다 해시맵을 생성하며 트리 구조로 저장한다.
해시맵의 키는 선언된 식별자이며
값은 식별자의 타입, 상수 여부, 접근 제어 등의 상세 정보이다.
######  Abstract Syntax Tree
컴파일 에러를 감시하며 코드를 구문 트리 형태로 생성한다.
컴파일러는 `cpp` 의 `AST`를 모두 생성한 후 최종적으로 기계어로 변환한다.

###### 탐색
탐색 과정에서는 오로지 이름만이 고려된다.
###### Qualified Name Lookup
스코프가 명시되 있는 함수를 호출하는 경우 
심볼 테이블의 해당 스코프의 해시맵을 한번 탐색 한다.
실패시 컴파일 오류가 발생한다.

네임스페이스가 명시되지 않은 함수 식별자를 인자와 함께 호출 한다면
동시에 두 가지 방식으로 후보군을 모은 뒤 최적의 함수를 찾는다.
###### Unqualified Name Lookup
네임스페이스가 명시되지 않은 함수식별자를 인자와 함께 호출 할 때
컴파일러는 심볼 테이블의 해당 스코프의 해시맵을 탐색 후 실패시 
상위 스코프를 향해 반복 탐색한다.탐색 도중 이름을 하나라도 찾으면 상위 탐색을 중단한다.

[Argument-Dependent Lookup](Public/Study/C++/Docs/Argument-Dependent%20Lookup.md)
네임스페이스가 명시되지 않은 함수식별자를 인자와 함께 호출 할 때
컴파일러는 인자의 스코프에 대한 Abstract Syntax Tree를 한번 탐색한다.

---
Customization Point를 제공하는 템플릿을 만들 수 있다.
```cpp
namespace defaultLib
{
	template <typename T>
	void swap(T& a, T& b); 
}

namespace myCode
{
	struct MyType;
}

template <typename T>
void Process(T& a, T& b) {
    using defaultLib::swap; 
    swap(a, b);
}

myCode::MyType x,y;
Process(x,y);
```

`Unqualified Name Lookup`에 의해 `defaultLib::swap` 이 후보군에 추가되며
커스텀 함수를 구현하지 않은 경우 기본 값으로 호출 된다.

커스텀 함수를 구현한 경우 
```cpp
namespace myCode
{
	void swap(MyType& a, MyType& b); 
}
```
Argument-Dependent Lookup에 의해 `myCode::swap` 이 후보군에 추가되며
최종적으로 호출 된다.

---
###### PDB(Program Database)
빌드된 `.exe` 에는 실제 주소로 치환된 기계어만이 들어있기 때문에
디버깅할 수 없어 

컴파일러는 `.exe` 에 `.pdb`의 `GUID`를 저장하고 `.exe` 내부의 모든 기계어 명령어 주소를 
소스 코드(함수명, 파일 경로, 줄 번호, 지역 변수)와 1:1로 매핑한 `.pdb` 파일을 별도로 생성한다.

