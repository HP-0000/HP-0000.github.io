---
---

GPU의 구체적인 작업 명령은 명령 큐에 넣어 한번에 복사해서 보낸다
GPU의 내부 동작을 최적화(병렬 동작) 하기 위해 명령 유형 타입을 지정한
명령 리스트 큐를 만들어 사용한다.

# 명령 리스트 큐 생성
```
typedef enum D3D12_COMMAND_QUEUE_FLAGS  
{  
    D3D12_COMMAND_QUEUE_FLAG_NONE = 0,  
    D3D12_COMMAND_QUEUE_FLAG_DISABLE_GPU_TIMEOUT = 0x1  // 시간 초과시 DXGI_ERROR_DEVICE_REMOVED 발생
}D3D12_COMMAND_QUEUE_FLAGS;

typedef enum D3D12_COMMAND_LIST_TYPE  
{  
    D3D12_COMMAND_LIST_TYPE_DIRECT  = 0,  // 
    D3D12_COMMAND_LIST_TYPE_BUNDLE  = 1,  
    D3D12_COMMAND_LIST_TYPE_COMPUTE = 2,  
    D3D12_COMMAND_LIST_TYPE_COPY    = 3,  
    D3D12_COMMAND_LIST_TYPE_VIDEO_DECODE    = 4,  
    D3D12_COMMAND_LIST_TYPE_VIDEO_PROCESS   = 5,  
    D3D12_COMMAND_LIST_TYPE_VIDEO_ENCODE    = 6,  
    D3D12_COMMAND_LIST_TYPE_NONE    = -1  
}D3D12_COMMAND_LIST_TYPE;

typedef struct D3D12_COMMAND_QUEUE_DESC  
{  
    D3D12_COMMAND_LIST_TYPE Type;  // 명령 목록 유형
    INT Priority;   // 큐의 우선순위
    D3D12_COMMAND_QUEUE_FLAGS Flags;   
    UINT NodeMask;  // GPU 어댑터 마스크
}D3D12_COMMAND_QUEUE_DESC;

HRESULT CreateCommandQueue(   
	const D3D12_COMMAND_QUEUE_DESC *pDesc,  
    REFIID riid,  
    void **ppCommandQueue);  
```

# 명령어 메모리 풀 생성

```
HRESULT CreateCommandAllocator(   
	D3D12_COMMAND_LIST_TYPE type,  
    REFIID riid,  
    void **ppCommandAllocator); // ID3D12CommandAllocator 생성
```

# 명령 리스트 생성
```
HRESULT CreateCommandList(   
	UINT nodeMask,  // GPU 어댑터 마스크
	D3D12_COMMAND_LIST_TYPE type,  
    ID3D12CommandAllocator *pCommandAllocator,  
    ID3D12PipelineState *pInitialState,  // 초기 PSO 지정
    REFIID riid,  
    void **ppCommandList); //
```

# 명령 추가

**DIRECT / BUNDLE / COMPUTE / COPY** 타입 명령어는 
ID3D12GraphicsCommandList 를 통해 추가한다.

**VIDEO_DECODE / VIDEO_PROCESS / VIDEO_ENCODE** 타입 명령어는 
ID3D12VideoEncodeCommandList를 통해 추가한다.

기록이 끝나면 Close를 호출한다.
GPU 작업 완료시 할당자와 명령 리스트의 Reset을 호출하여 재사용 한다.

# 명령 실행
```
void ExecuteCommandLists(   
	UINT NumCommandLists,  // CommandList 개수
    ID3D12CommandList *const *ppCommandLists); // CommandList 배열
```

큐 내의 명령은 순서가 보장되며 여러 명령 큐의및 CPU 동기화를 위해 펜스를 사용한다.
[Fence](/Public/Study/Graphics/Docs/DirectX12/Docs/Fence)



