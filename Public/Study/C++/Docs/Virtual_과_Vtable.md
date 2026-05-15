---
---
Virtual 키워드가 존재하는 클래스에 대하여 하나의 VTABLE이 만들어진다,
이 안에는 가상 함수의 주소를 담아 둔다. 자식은 VTABLE 을 만들때 부모 것을 그대로 복사한 뒤 
자신의 재정의를 덮어 쓴다.

부모는 VTABLE 의 주소를 가리키는 VPTR 포인터를 갖는다.
자식은 VPTR 자리에 자신의 VTABLE 주소를 넣는다.

컴파일은 CPP 단위 임으로 상속 받을 녀석이 누군지 모르기에 
직접 주소를 명령어 안에 박을 수 없다. 즉 런타임에는 메모리로 부터 직접 주소를 
읽는 작업이 필요해지고, 성능 이슈가 생긴다.

[Curiously Recurring Template Pattern](/Public/Study/C++/Docs/Curiously%20Recurring%20Template%20Pattern)