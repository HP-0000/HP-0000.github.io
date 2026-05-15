언리얼 엔진(UE5)은 C# 기반의 자체 빌드 툴(UnrealBuildTool, UBT)을 사용한다.
### UBT(Unreal Build Tool)
빌드를 시작하면 수정된 파일들에 대해 UHT를 호출하고 컴파일러에게 전달하여 빌드한다.

`*.Build.cs` 파일에서 
`ModuleRules` 클래스를 상속받아 모듈 클래스를 정의한다.
다른 모듈 클래스와 의존성을 정의한다.
모놀리딕 빌드가 아닌 경우, 모듈에 대한 dll 파일이 생성된다.

`*.Target.cs`  파일에서
대표 모듈 클래스를 정의하고 빌드 환경과 관련된 정보를 작성한다.
바이너리 실행 파일이 생성된다.

모듈 클래스가 대표하는 대상은  `*.Build.cs` 경로 하위에 존재하는 C++ 파일들이다.
UHT 가 자동하며 코드 자동 생성을 거친 뒤
UBT가 MSVC의 컴파일과 링커를 호출하여 빌드를 수행한다.
### UHT(Unreal Header Tool)
C++ 헤더에서 U로 시작하는 매크로를 발견하면 파싱하여 
상속관계와 멤버 함수, 멤버 변수 목록을 파악한다.

`*.gen.cpp`
메모리 오프셋 등의 클래스 보조 정보가 담긴 `Uclass` 등의
`UObject` 를 생성하는 코드를 만드는 파일이다.

`*.generated.h`
`GENERATED_BODY()` 를 치환하는 매크로가 담긴다.
프라이빗 멤버 접근을 위한 friend 선언,  `Uclass` 에 접근하는 함수 `StaticClass` 등을 주입한다.
원본 헤더 파일에 이를 직접 인클루드 해야한다.

`*.init.gen.cpp`
모듈 소속의 `UObject` 를 생성하고  [가상 경로](Public/Study/Unreal/Docs/Path/가상%20경로.md)를 등록한다.

---
