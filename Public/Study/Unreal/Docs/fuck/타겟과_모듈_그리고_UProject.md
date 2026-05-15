---
---
#### Target.cs 
- Modular Build : 각 모듈들을 DLL 로 만든다.
- Monolithic Build : 각 모듈을 모아 하나의 EXE 로 만든다.
빌드 할 모듈 목록을 이곳에 추가한다.
#### Build.cs 
모듈을 구성하는 코드는 아래의 두 폴더에 위치한다.
- Public Folder : 외부에서 인클루드 가능한 헤더파일들이 들어가는 폴더
- Private Folder : 외부로 부터 숨길 헤더 파일과 CPP 파일들이 들어가는 폴더

의존성 모듈 목록을 이곳에 추가한다.

위 파일들을 만들고 모듈 등록 CPP 코드를 작성한 뒤 GenerateProjectFiles.bat 을 실행 하면 빌드 및 솔루션 생성해준다.
#### .UProject
모듈 혹은 플러그인 목록을 작성한다.









