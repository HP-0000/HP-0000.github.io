 C/C++에서 구조체나 클래스, 공용체(union)의 멤버 변수 크기를 
비트(Bit) 단위로 직접 지정하는 문법

비트 배치 순서는 컴파일러/엔디언(Endian) 종속적임을 주의한다.
```cpp
struct 구조체이름
{
    타입 변수명 : 비트수;
};

// 이름 없는 비트 필드 
struct FData
{
    unsigned int Flag1  : 1;
    unsigned int        : 3; // 사용하지 않고 3비트 건너뜀 (Reserved)
    unsigned int Flag2  : 4;
};

// 정렬된 비트 필드
struct FTest_Separated
{
    // [ 0번 바이트 ]
    unsigned char i1 : 4; // 4비트
    unsigned char c1 : 1; // 1비트
    unsigned char    : 0; // 다음 바이트(1번 바이트)로 줄바꿈!

    // [ 1번 바이트 ]
    unsigned char c2 : 1; // 1번 바이트 첫 비트에서 시작
}; // 👉 sizeof(FTest_Separated) 2바이트

```





