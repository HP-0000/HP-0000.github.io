`\Engine\Source\Runtime\Core\Private\HAL\ThreadingBase.cpp`
현재 스레드에 태그를 달아 실행 상태를 조회할 수 있게 한다.

각 스레드들은 `thread_local`  변수인 `ActiveTaskTag` 를 가지며 
`ETaskTag::EStaticInit` 로 생성 초기화 된다.

`\Engine\Source\Runtime\Launch\Private\Launch.cpp`
`GuardedMain` 에서 가장 먼저 `FTaskTagScope Scope(ETaskTag::EGameThread)` 가 호출된다.

생성자 내부에서 `FTaskTagScope::GetStaticThreadId` 가 호출되면서 
함수 지역 변수인 `ThreadID`를 메인 스레드의 ID로 초기화한다.

[FRunnableThread](Public/Study/Unreal/Docs/FRunnableThread.md) 에 의해 호출된 `SetTls` 는 내부적으로 
`FTaskTagScope::SetTagNone` 를 호출하여 `ActiveTaskTag` 를 `ENone` 초기화 한다.

엔진에서 명시된 태그 목록은 다음과 같다.
```cpp
enum class ETaskTag : int32
{
	ENone						= 0 << 0,
	EStaticInit					= 1 << 0,
	EGameThread					= 1 << 1,
	ESlateThread				= 1 << 2,
	// Removed EAudioThread		= 1 << 3,
	ERenderingThread			= 1 << 4,
	ERhiThread					= 1 << 5,
	EAsyncLoadingThread			= 1 << 6,
	EEventThread				= 1 << 7,

	ENamedThreadBits			= (EEventThread << 1) - 1,
	EParallelThread				= 1 << 30, //This can be used when multiple threads or jobs are involved (usually a parallel for) It will avoid the check for uniqueness of the named thread tag.
	EWorkerThread				= 1 << 29 | EParallelThread,
	EParallelRenderingThread	= ERenderingThread | EParallelThread,
	EParallelGameThread			= EGameThread | EParallelThread,
	EParallelRhiThread			= ERhiThread | EParallelThread,
	EParallelLoadingThread		= EAsyncLoadingThread | EParallelThread,
};
```

