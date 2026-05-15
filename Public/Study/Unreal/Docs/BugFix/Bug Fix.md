5.8.1 Source Build
#### 문제 상황
프로젝트를 생성 하면 `Engine\Saved` 위치에 `AutoLoadProject.txt` 가 생성된다.

이제 엔진을 실행하면 최근 파일 자동 실행 기능이 시작되며
다음의 경고 메세지가 작동한다.
*The following modules are missing or built with a different engine*

#### 문제 분석
`LaunchEngineLoop.cpp` 에서 
`ProjectBinariesRootDirectory` 변수가 빈 상태로 선언된다.

`LaunchUpdateMostRecentProjectFile` 가 실행되고 
`Engine\Saved` 위치에 `.InProgress` 파일이 생성된다.
정상적으로 프로젝트가 Auto Load 되지 않은 경우, 해당 파일이 남게 되어 
다음 실행에서는 Auto Load를 무시하고 에디터를 띄운다.

`.InProgress` 파일이 처음 생성 되는 경우, `AutoLoadProject.txt`를 읽어
프로젝트의 `.uproject` 경로를 가져오게 된다.

문제점은 이제 읽어온 경로를 가지고 `ProjectBinariesRootDirectory`를 
업데이트 해야 할 것 같은데 하지 않고 있다.

`FModuleManager::PendingGameBinariesDirectories` 에 
`ProjectBinariesRootDirectory`경로를 사용해 프로젝트 모듈 경로를 추가한다.

따라서 빈 `Root` 경로를 따라가면서 프로젝트 모듈을 찾지 못해 
해당 경고 문구를 내보낸다.

#### 문제 해결
`LaunchUpdateMostRecentProjectFile`  함수 실행 이후 
다음의 코드를 실행하도록 하였다.

`ProjectBinariesRootDirectory = FPaths::GetPath(FPaths::GetProjectFilePath());`