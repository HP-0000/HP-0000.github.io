 C/C++에서 구조체나 클래스, 공용체(union)의 멤버 변수 크기를 
비트(Bit) 단위로 직접 지정하는 문법

비트 배치 순서는 컴파일러/엔디언(Endian) 종속적임을 주의한다.
###### UNION과 익명 구조체
```cpp
union FTaggedPtr
{
    struct 
    {
        void*  Ptr;        // 8바이트
        uint64 VersionTag; // 8바이트
    };
    __int128 Raw128;       // 16바이트 
};
```
익명 구조체를 통해 다음과 같이 접근 할 수 있다.
```cpp
FTaggedPtr x;

x.Ptr;
x.VersionTag.
```
###### 비트필드
타입의 크기에 맞는 상자에 
같은 타입인 경우, 나란히 비트를 이어 붙인다.
타입이 다르거나 상자 용량이 초과되면 다음 상자를 연다.
각 상자는 메모리 정렬된다.
###### Unnamed Bitfield
변수명을 적지 않고 타입과 비트 수만 적는 특수 문법아다.
현재 상자를 닫는다.
```cpp
struct FTest_Separated
{
	// [ 0 바이트 ]
	unsigned char c1 : 4; // 4비트
	unsigned char c2 : 1; // 1비트
	unsigned char : 0; // Unnamed Bitfield
	// [ 1 바이트 ]
	unsigned char c3 : 1; 
}; 
```
---