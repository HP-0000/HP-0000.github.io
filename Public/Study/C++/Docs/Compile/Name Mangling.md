C++ 컴파일러는 클래스의 비정적 멤버 함수를 컴파일할 때
객체지향 문법을 제거하고 C 스타일의 일반 함수를 만들어낸다.

함수명은 스코프와 매개변수 정보를 통해 새로 만들어진다.
##### extern "C"
전역 스코프의 일반 함수 앞에 선언한다.

컴파일러가 네임 맹글링을 사용하지 않고 함수명 그대로 기계어를 만들도록 한다.

C 컴파일러가 만든 라이브러리를 사용하기 위해서는
네임 맹글링 되지 않은 이름으로 호출해야하기에 다음과 같이 사용한다.
```cpp
extern "C" {
	#include "sqlite3.h"
}
```

C++로 작성한 동적 라이브러리를 타 언어에서 호출할 수 있도록 
함수명 그대로 심볼을 남기기 위해 사용한다.

```CPP
extern "C" void InitEngine() {}

extern "C" {
    void StartGame();
    void StopGame();
    int  GetScore();
}
```

C 컴파일러는 `extern "C"`라는 문법 자체를 모르기 때문에
C++로 빌드될 때만 조건부로 켜지도록 매크로를 감싸서 작성한다.
```cpp
#ifdef __cplusplus
extern "C" {
#endif

void MyLibrary_Function1();
void MyLibrary_Function2();

#ifdef __cplusplus
}
#endif
```

[Implicit Object Parameter](Public/Study/C++/Docs/Compile/Implicit%20Object%20Parameter.md)