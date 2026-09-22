C++ 컴파일러는 클래스의 비정적 멤버 함수를 컴파일할 때
객체지향 문법을 제거하고 C 스타일의 일반 함수를 만들어낸다.

[Name Mangling](Public/Study/C++/Docs/Compile/Name%20Mangling.md) 이후 함수의 0번째 매개변수로 
호출 객체 자신의 주소 `Class* const this`를 삽입해 만든다. 
함수 내부의 멤버 변수 접근(`val`)은 `this->val`로 치환된다.

[const, mutable](Public/Study/C++/Docs/Compile/const,%20mutable.md)
함수 끝에 `const` 가 붙는 경우
`const Class* const this` 가 삽입된다.


