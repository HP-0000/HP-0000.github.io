---
---
자기 자신을 템플릿 인자로 부모 클래스에 넘기는 패턴
```
// 부모 클래스 (Base)
template <typename Derived>
class Base {
    void interface() {
        // 나를 상속받은 '진짜 자식'의 함수를 호출함!
        static_cast<Derived*>(this)->implementation();
    }
};

// 자식 클래스 (Derived)
class Child : public Base<Child> {
    void implementation() {
        // 실제 로직
    }
};
```

부모가 자식의 타입을 이미 알고 있기 때문에, 
가상 함수 테이블 없이도 자식의 함수를 직접 호출할 수 있다.


