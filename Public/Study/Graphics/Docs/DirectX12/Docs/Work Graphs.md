---
---

GPU의 커맨드 프로세서(Command Processor, CP)는 단순히 
CPU가 넘긴 커맨드 리스트라는 작업 지시서를 선형적으로 작업을 수행한다. 

CPU와 GPU 작업 동기화는 매우 느리며
작업은 점차 CPU 개입없는, GPU Driven 으로 변화하고 있다.
[Execute Indirect](/Public/Study/Graphics/Docs/DirectX12/Docs/Execute%20Indirect)
GPU 에서 연산을 하더라도 그 결과를 쓰기 위해서는 VRAM 버퍼에 저장하고 그것을 사용하는 명령을 
커맨드 리스트로 작성해야 함으로 무거운 VRAM 왕복 과정이 필요하다.

GPU가 진화하며 하드웨어 스케줄러가 생겼다.
GPU는 작업을 단순히 수행하지 않고 분배할 수 있게 되었다.

워크 그래프는 노드라는 스케줄링 되는 작업단위가 있다.
각 노드는 연산 결과물을 온칩 캐시로 빠르게 다음 노드로 전달 할 수 있으며
이때 같은 노드로 전달되는 데이터들은 잠시 모아서 한번에 노드를 병령 실행하는 최적화가 일어난다.

[Graphics PipeLine](/Public/Study/Graphics/Docs/DirectX12/Docs/GraphicsPipeLine/Graphics%20PipeLine)

메쉬 노드는 MS(AS는 노드 대체 가능) - 래스터 라이저 - PS 가 묶이면서 
같은 메쉬 노드로 향하는 명령은 최적화가 일어난다. 

### 상태 객체 생성

PSO를 생성하는 기존 API는
```
CreateGraphicsPipelineState
CreateComputePipelineState
CreatePipelineState
```

다음으로 통합되었다.
```
typedef enum D3D12_STATE_OBJECT_TYPE  
{  
    D3D12_STATE_OBJECT_TYPE_COLLECTION  = 0,  // 모듈 상태 객체
    D3D12_STATE_OBJECT_TYPE_RAYTRACING_PIPELINE = 3,  //  실행 가능한 레이트레이싱 파이프라인
    D3D12_STATE_OBJECT_TYPE_EXECUTABLE  = 4  // 범용 실행 가능한 파이프라인 상태 객체
}D3D12_STATE_OBJECT_TYPE;

typedef struct D3D12_STATE_OBJECT_DESC  
{  
    D3D12_STATE_OBJECT_TYPE Type;  
    UINT NumSubobjects;  
    const D3D12_STATE_SUBOBJECT *pSubobjects;  
}D3D12_STATE_OBJECT_DESC;

HRESULT CreateStateObject(   
	const D3D12_STATE_OBJECT_DESC *pDesc,  // 상태 객체 정의
    REFIID riid,  
    void **ppStateObject); // ID3D12StateObject
```

COLLECTION 타입으로 CreateStateObject 하면 셰이더 코드 번역 문법 오류 검사 가 이루어진다.
이를 통해 여러 셰이더를 병렬적으로 처리한다.

컬렉션을 각기 다른 상태 객체 구성에 추가하여 CreateStateObject 하면 링킹이 수행된다.
링킹에는 루트 시그니처 충돌 검사, 이름 매칭,함수 포인터 테이블 생성 등의 작업이 포함된다.

메모리의 한계로 모든 상태 객체를 미리 만들어 놓을 수 는 없다.
그러나 CreateStateObject는 서브 오브젝트 전체에 대해 다시 최적화 작업을 수행하기 때문에 
런타임에 호출 시 렉이 유발된다.

아래는 최적화를 포기하는 대신 단순 링킹으로 빠르게 상태 객체를 생성하는 함수다.
```
HRESULT AddToStateObject(   
	const D3D12_STATE_OBJECT_DESC *pAddition,  
    ID3D12StateObject *pStateObjectToGrowFrom,  
    REFIID riid,  
    void **ppNewStateObject); // ID3D12StateObject
```

상태 객체는 여러 서브 오브젝트로 구성된다. 
[Shader](/Public/Study/Graphics/Docs/DirectX12/Docs/Shader)
셰이더는 lib_6_x 형식으로 컴파일하면 링크 가능한 DXIL 모듈이 생긴다.
서브오브젝트 혹은 DXIL 모듈에 정의한 서브 오브젝트들을
DXIL 모듈 안의 Export 된 함수들에 대해 연결 또는 오버라이드하여 
상태의 다양한 조합을 유연하게 관리할 수 있다.


워크 그래프에서 프로그램은 각 노드가 각 노드에 출력을 지정하여 값을 보내면
다중의 노드가 병렬 적으로 처리하는 구조이다.

루트 시그니처의 경우 커다란 버퍼 리소스를 할당하여 노드별로 루트 시그니처에 맞는 
루트 파라미터 값을 배열 하여 NodeLocalRootArgumentsTable를 만들어 공급하면 
각 노드는 인덱스로 로컬 루트 인자 테이블에 접근한다.

```
typedef enum D3D12_STATE_SUBOBJECT_TYPE  
{  
    D3D12_STATE_SUBOBJECT_TYPE_STATE_OBJECT_CONFIG  = 0,  
    D3D12_STATE_SUBOBJECT_TYPE_GLOBAL_ROOT_SIGNATURE  = 1,  
    D3D12_STATE_SUBOBJECT_TYPE_LOCAL_ROOT_SIGNATURE = 2, 
    D3D12_STATE_SUBOBJECT_TYPE_NODE_MASK  = 3,  // 멀티 GPU 마스크
    D3D12_STATE_SUBOBJECT_TYPE_DXIL_LIBRARY = 5,  // 셰이더 DXIL
    D3D12_STATE_SUBOBJECT_TYPE_EXISTING_COLLECTION  = 6,  // 모듈 상태 객체 
    
    // 서브 오브젝트를 export에 붙임
    D3D12_STATE_SUBOBJECT_TYPE_SUBOBJECT_TO_EXPORTS_ASSOCIATION = 7,
    // DXIL 서브 오브젝트를 export에 붙임
    D3D12_STATE_SUBOBJECT_TYPE_DXIL_SUBOBJECT_TO_EXPORTS_ASSOCIATION = 8,
    
    D3D12_STATE_SUBOBJECT_TYPE_RAYTRACING_SHADER_CONFIG = 9,  
    D3D12_STATE_SUBOBJECT_TYPE_RAYTRACING_PIPELINE_CONFIG = 10,  
    D3D12_STATE_SUBOBJECT_TYPE_HIT_GROUP = 11,  //충돌시 실행될 셰이더 묶음 
    D3D12_STATE_SUBOBJECT_TYPE_RAYTRACING_PIPELINE_CONFIG1 = 12,  
    D3D12_STATE_SUBOBJECT_TYPE_WORK_GRAPH   = 13,  // Work Graph 정의
    
    //그래픽스 상태 정의
    D3D12_STATE_SUBOBJECT_TYPE_STREAM_OUTPUT    = 14,  
    D3D12_STATE_SUBOBJECT_TYPE_BLEND   = 15,  
    D3D12_STATE_SUBOBJECT_TYPE_SAMPLE_MASK  = 16,  
    D3D12_STATE_SUBOBJECT_TYPE_RASTERIZER  = 17,  
    D3D12_STATE_SUBOBJECT_TYPE_DEPTH_STENCIL  = 18,  
    D3D12_STATE_SUBOBJECT_TYPE_INPUT_LAYOUT = 19,  
    D3D12_STATE_SUBOBJECT_TYPE_IB_STRIP_CUT_VALUE  = 20,  
    D3D12_STATE_SUBOBJECT_TYPE_PRIMITIVE_TOPOLOGY  = 21,  
    D3D12_STATE_SUBOBJECT_TYPE_RENDER_TARGET_FORMATS   = 22,  
    D3D12_STATE_SUBOBJECT_TYPE_DEPTH_STENCIL_FORMAT = 23,  
    D3D12_STATE_SUBOBJECT_TYPE_SAMPLE_DESC  = 24,  
    D3D12_STATE_SUBOBJECT_TYPE_FLAGS   = 26,  
    D3D12_STATE_SUBOBJECT_TYPE_DEPTH_STENCIL1   = 27,  
    D3D12_STATE_SUBOBJECT_TYPE_VIEW_INSTANCING  = 28,  
    D3D12_STATE_SUBOBJECT_TYPE_GENERIC_PROGRAM  = 29,  
    D3D12_STATE_SUBOBJECT_TYPE_DEPTH_STENCIL2   = 30,  
    
    D3D12_STATE_SUBOBJECT_TYPE_MAX_VALID =( D3D12_STATE_SUBOBJECT_TYPE_DEPTH_STENCIL2 + 1 )   
}D3D12_STATE_SUBOBJECT_TYPE;

typedef struct D3D12_STATE_SUBOBJECT  
{  
    D3D12_STATE_SUBOBJECT_TYPE Type;  
    const void *pDesc;  // Type에 해당하는 상세 정보 구조체 포인터
}D3D12_STATE_SUBOBJECT
```
---
###### D3D12_STATE_SUBOBJECT_TYPE_STATE_OBJECT_CONFIG
```
typedef enum D3D12_STATE_OBJECT_FLAGS  
{  
    D3D12_STATE_OBJECT_FLAG_NONE    = 0,  
    //현재 State Object가 다른 State Object의 Export를 참조할 수 있음
    D3D12_STATE_OBJECT_FLAG_ALLOW_LOCAL_DEPENDENCIES_ON_EXTERNAL_DEFINITIONS  = 0x1,
      
    //다른 State Object가 현재 State Object의 Export를 참조할 수 있음
    D3D12_STATE_OBJECT_FLAG_ALLOW_EXTERNAL_DEPENDENCIES_ON_LOCAL_DEFINITIONS = 0x2,
      
    //현재 State Object를 `AddToStateObject`의 기반으로 확장 가능하게 함
    D3D12_STATE_OBJECT_FLAG_ALLOW_STATE_OBJECT_ADDITIONS    = 0x4  
}D3D12_STATE_OBJECT_FLAGS;

typedef struct D3D12_STATE_OBJECT_CONFIG  
{  
    D3D12_STATE_OBJECT_FLAGS Flags;  
}D3D12_STATE_OBJECT_CONFIG;
```

###### D3D12_STATE_SUBOBJECT_TYPE_GLOBAL_ROOT_SIGNATURE
```
ID3D12RootSignature*
```
###### D3D12_STATE_SUBOBJECT_TYPE_LOCAL_ROOT_SIGNATURE
```
ID3D12RootSignature*
```
###### D3D12_STATE_SUBOBJECT_TYPE_DXIL_LIBRARY
```
typedef enum D3D12_EXPORT_FLAGS  
{  
	D3D12_EXPORT_FLAG_NONE  = 0  
}D3D12_EXPORT_FLAGS;

typedef struct D3D12_EXPORT_DESC  
{  
    LPCWSTR Name;   // 외부에서 사용할 함수의 이름
    LPCWSTR ExportToRename; // 새 이름으로 재정의
    D3D12_EXPORT_FLAGS Flags;  
}D3D12_EXPORT_DESC;

typedef struct D3D12_DXIL_LIBRARY_DESC  
{  
    D3D12_SHADER_BYTECODE DXILLibrary; // DXIL 바이트코드
    UINT NumExports;  
    const D3D12_EXPORT_DESC *pExports;  // 어떤 함수를 가져올 것인가
} D3D12_DXIL_LIBRARY_DESC;
```
###### D3D12_STATE_SUBOBJECT_TYPE_EXISTING_COLLECTION
```
typedef struct D3D12_EXISTING_COLLECTION_DESC  
{  
    ID3D12StateObject *pExistingCollection;  
    UINT NumExports;  
    const D3D12_EXPORT_DESC *pExports; // 어떤 함수를 가져올 것인가
} D3D12_EXISTING_COLLECTION_DESC;
```
###### D3D12_STATE_SUBOBJECT_TYPE_SUBOBJECT_TO_EXPORTS_ASSOCIATION
```
typedef struct D3D12_SUBOBJECT_TO_EXPORTS_ASSOCIATION  
{  
    const D3D12_STATE_SUBOBJECT *pSubobjectToAssociate; // 연결할 서브 오브젝트
    UINT NumExports;  
    LPCWSTR *pExports;  //어떤 EXPORT에 적용할지 
}D3D12_SUBOBJECT_TO_EXPORTS_ASSOCIATION;
```
###### D3D12_STATE_SUBOBJECT_TYPE_DXIL_SUBOBJECT_TO_EXPORTS_ASSOCIATION
```
typedef struct D3D12_DXIL_SUBOBJECT_TO_EXPORTS_ASSOCIATION  
{  
    LPCWSTR SubobjectToAssociate;  // HLSL에 정의한 서브오브젝트
    UINT NumExports;  
    LPCWSTR *pExports; // 어떤 EXPORT에 적용할지 
}D3D12_DXIL_SUBOBJECT_TO_EXPORTS_ASSOCIATION; 
```
###### D3D12_STATE_SUBOBJECT_TYPE_RAYTRACING_SHADER_CONFIG
```
typedef struct D3D12_RAYTRACING_SHADER_CONFIG  
{  
    UINT MaxPayloadSizeInBytes;  
    UINT MaxAttributeSizeInBytes;  
}D3D12_RAYTRACING_SHADER_CONFIG;
```
###### D3D12_STATE_SUBOBJECT_TYPE_RAYTRACING_PIPELINE_CONFIG
```
typedef struct D3D12_RAYTRACING_PIPELINE_CONFIG  
{  
    UINT MaxTraceRecursionDepth;  
}D3D12_RAYTRACING_PIPELINE_CONFIG;
```
###### D3D12_STATE_SUBOBJECT_TYPE_HIT_GROUP 
```
typedef enum D3D12_HIT_GROUP_TYPE  
{  
    D3D12_HIT_GROUP_TYPE_TRIANGLES  = 0,  
    D3D12_HIT_GROUP_TYPE_PROCEDURAL_PRIMITIVE   = 0x1  
}D3D12_HIT_GROUP_TYPE;

typedef struct D3D12_HIT_GROUP_DESC  
{  
    LPCWSTR HitGroupExport;  
    D3D12_HIT_GROUP_TYPE Type;  
    LPCWSTR AnyHitShaderImport;  
    LPCWSTR ClosestHitShaderImport;  
    LPCWSTR IntersectionShaderImport;  
}D3D12_HIT_GROUP_DESC;
```
###### D3D12_STATE_SUBOBJECT_TYPE_RAYTRACING_PIPELINE_CONFIG1
```
typedef enum D3D12_RAYTRACING_PIPELINE_FLAGS  
{  
    D3D12_RAYTRACING_PIPELINE_FLAG_NONE = 0,  
    D3D12_RAYTRACING_PIPELINE_FLAG_SKIP_TRIANGLES   = 0x100,  
    D3D12_RAYTRACING_PIPELINE_FLAG_SKIP_PROCEDURAL_PRIMITIVES   = 0x200,  
    D3D12_RAYTRACING_PIPELINE_FLAG_ALLOW_OPACITY_MICROMAPS  = 0x400  
}D3D12_RAYTRACING_PIPELINE_FLAGS;

typedef struct D3D12_RAYTRACING_PIPELINE_CONFIG1  
{  
    UINT MaxTraceRecursionDepth;  
    D3D12_RAYTRACING_PIPELINE_FLAGS Flags;  
}D3D12_RAYTRACING_PIPELINE_CONFIG1;
```
###### D3D12_STATE_SUBOBJECT_TYPE_WORK_GRAPH
```
typedef struct D3D12_NODE_OUTPUT_OVERRIDES  
{  
    UINT OutputIndex;  // 출력 인자의 인덱스
    const D3D12_NODE_ID *pNewName; // 출력 할 노드
    const BOOL *pAllowSparseNodes; // 출력 노드가 비어있을대 에러를 내지 않을 것인가.
    const UINT *pMaxRecords;  // 최대 레코드 수 
    const UINT *pMaxRecordsSharedWithOutputIndex; // 최대 레코드 공유받을 인자의 인덱스   
}D3D12_NODE_OUTPUT_OVERRIDES;
    
typedef struct D3D12_BROADCASTING_LAUNCH_OVERRIDES  
{  
    const UINT *pLocalRootArgumentsTableIndex;  // NodeLocalRootArgumentsTable 인덱스 
    const BOOL *pProgramEntry;  
    const D3D12_NODE_ID *pNewName;  
    const D3D12_NODE_ID *pShareInputOf;  
    const UINT *pDispatchGrid;  
    const UINT *pMaxDispatchGrid;  
    UINT NumOutputOverrides;  
    const D3D12_NODE_OUTPUT_OVERRIDES *pOutputOverrides;  
}D3D12_BROADCASTING_LAUNCH_OVERRIDES;  
  
typedef struct D3D12_COALESCING_LAUNCH_OVERRIDES  
{  
    const UINT *pLocalRootArgumentsTableIndex;  
    const BOOL *pProgramEntry;  
    const D3D12_NODE_ID *pNewName;  
    const D3D12_NODE_ID *pShareInputOf;  
    UINT NumOutputOverrides;  
    const D3D12_NODE_OUTPUT_OVERRIDES *pOutputOverrides;  
}D3D12_COALESCING_LAUNCH_OVERRIDES;  
  
typedef struct D3D12_THREAD_LAUNCH_OVERRIDES  
{  
    const UINT *pLocalRootArgumentsTableIndex;  
    const BOOL *pProgramEntry;  
    const D3D12_NODE_ID *pNewName;  
    const D3D12_NODE_ID *pShareInputOf;  
    UINT NumOutputOverrides;  
    const D3D12_NODE_OUTPUT_OVERRIDES *pOutputOverrides;  
}D3D12_THREAD_LAUNCH_OVERRIDES;  
  
typedef struct D3D12_COMMON_COMPUTE_NODE_OVERRIDES  
{  
    const UINT *pLocalRootArgumentsTableIndex;  
    const BOOL *pProgramEntry;  
    const D3D12_NODE_ID *pNewName;  
    const D3D12_NODE_ID *pShareInputOf;  
    UINT NumOutputOverrides;  
    const D3D12_NODE_OUTPUT_OVERRIDES *pOutputOverrides;  
}D3D12_COMMON_COMPUTE_NODE_OVERRIDES;  
  
typedef enum D3D12_NODE_OVERRIDES_TYPE  
{  
    D3D12_NODE_OVERRIDES_TYPE_NONE  = 0,  // 기본 그대로 사용
    D3D12_NODE_OVERRIDES_TYPE_BROADCASTING_LAUNCH   = 1,  // 
    D3D12_NODE_OVERRIDES_TYPE_COALESCING_LAUNCH = 2,  
    D3D12_NODE_OVERRIDES_TYPE_THREAD_LAUNCH = 3,  
    D3D12_NODE_OVERRIDES_TYPE_COMMON_COMPUTE    = 4  
}D3D12_NODE_OVERRIDES_TYPE;  
  
typedef struct D3D12_SHADER_NODE  
{  
    LPCWSTR Shader;  // Export 이름
    D3D12_NODE_OVERRIDES_TYPE OverridesType;  
    union   
	{  
        const D3D12_BROADCASTING_LAUNCH_OVERRIDES *pBroadcastingLaunchOverrides;  
        const D3D12_COALESCING_LAUNCH_OVERRIDES *pCoalescingLaunchOverrides;  
        const D3D12_THREAD_LAUNCH_OVERRIDES *pThreadLaunchOverrides;  
        
        // 공통 분모를 묶어 사용하기 깔끔한... 
        const D3D12_COMMON_COMPUTE_NODE_OVERRIDES *pCommonComputeNodeOverrides;  
    };    
}D3D12_SHADER_NODE;  
  
typedef enum D3D12_NODE_TYPE  
{  
    D3D12_NODE_TYPE_SHADER  = 0  
}D3D12_NODE_TYPE;  
  
typedef struct D3D12_NODE  
{  
    D3D12_NODE_TYPE NodeType;  
    union   
	{  
        D3D12_SHADER_NODE Shader;  
    };    
}D3D12_NODE;

typedef enum D3D12_WORK_GRAPH_FLAGS  
{  
    D3D12_WORK_GRAPH_FLAG_NONE  = 0,  
    
    //export 된 모든 노드를 포함
    D3D12_WORK_GRAPH_FLAG_INCLUDE_ALL_AVAILABLE_NODES   = 0x1 
}D3D12_WORK_GRAPH_FLAGS;

typedef struct D3D12_NODE_ID  
{  
    LPCWSTR Name;  // 노드 명
    UINT ArrayIndex;  // 노드가 배열로 선언 되었을때 인덱스
}D3D12_NODE_ID;
    
typedef struct D3D12_WORK_GRAPH_DESC  
{  
    LPCWSTR ProgramName;  // 워크 그래프 프로그램 이름
    D3D12_WORK_GRAPH_FLAGS Flags;  
    UINT NumEntrypoints;  // 진입점 개수 
    const D3D12_NODE_ID *pEntrypoints;  // 진입점으로 사용할 노드 배열
    UINT NumExplicitlyDefinedNodes;  
    const D3D12_NODE *pExplicitlyDefinedNodes; // 노드 실행 동작 재정의
}D3D12_WORK_GRAPH_DESC;
```
###### D3D12_STATE_SUBOBJECT_TYPE_BLEND
```
D3D12_BLEND_DESC
```
##### D3D12_STATE_SUBOBJECT_TYPE_SAMPLE_MASK
```
typedef struct D3D12_SAMPLE_MASK  
{  
	UINT SampleMask; 
}D3D12_SAMPLE_MASK;
```
##### D3D12_STATE_SUBOBJECT_TYPE_RASTERIZER
```
D3D12_RASTERIZER_DESC
```
##### D3D12_STATE_SUBOBJECT_TYPE_DEPTH_STENCIL
```
D3D12_DEPTH_STENCIL_DESC
```
##### D3D12_STATE_SUBOBJECT_TYPE_PRIMITIVE_TOPOLOGY
```
D3D12_PRIMITIVE_TOPOLOGY_TYPE
```
##### D3D12_STATE_SUBOBJECT_TYPE_RENDER_TARGET_FORMATS
```
struct D3D12_RT_FORMAT_ARRAY  
{  
    DXGI_FORMAT RTFormats[ 8 ];  
    UINT NumRenderTargets;  
};
```
##### D3D12_STATE_SUBOBJECT_TYPE_DEPTH_STENCIL_FORMAT
```
DXGI_FORMAT
```
##### D3D12_STATE_SUBOBJECT_TYPE_SAMPLE_DESC
```
DXGI_SAMPLE_DESC
```
##### D3D12_STATE_SUBOBJECT_TYPE_VIEW_INSTANCING
```
typedef struct D3D12_VIEW_INSTANCE_LOCATION  
{  
    UINT ViewportArrayIndex;  // 뷰 포트 지정
    UINT RenderTargetArrayIndex;  // 렌터 타겟 지정
}D3D12_VIEW_INSTANCE_LOCATION;

typedef enum D3D12_VIEW_INSTANCING_FLAGS  
{  
    D3D12_VIEW_INSTANCING_FLAG_NONE = 0,  
    D3D12_VIEW_INSTANCING_FLAG_ENABLE_VIEW_INSTANCE_MASKING = 0x1  
}D3D12_VIEW_INSTANCING_FLAGS;

typedef struct D3D12_VIEW_INSTANCING_DESC  
{  
    UINT ViewInstanceCount;  
    const D3D12_VIEW_INSTANCE_LOCATION *pViewInstanceLocations;  
    D3D12_VIEW_INSTANCING_FLAGS Flags;  // 뷰 마스크
}D3D12_VIEW_INSTANCING_DESC;
```
##### D3D12_STATE_SUBOBJECT_TYPE_GENERIC_PROGRAM
```
typedef struct D3D12_GENERIC_PROGRAM_DESC  
{  
    LPCWSTR ProgramName; 
    UINT NumExports;   
    LPCWSTR *pExports; // 프로그램에 사용되는 함수 
    UINT NumSubobjects;  
    
    //프로그램을 구성하는 서브 객체 포인터의 배열
    const D3D12_STATE_SUBOBJECT *ppSubobjects;  
}D3D12_GENERIC_PROGRAM_DESC;
```
---

###### 상태객체로부터 프로그램 식별자 얻기
```
typedef struct D3D12_PROGRAM_IDENTIFIER  
{  
    UINT64 OpaqueData[ 4 ];  
}D3D12_PROGRAM_IDENTIFIER;

ID3D12StateObjectProperties1* pSOProps = nullptr;
HRESULT hr = m_stateObject->QueryInterface(IID_PPV_ARGS(&pSOProps));
D3D12_PROGRAM_IDENTIFIER hWorkGraph = pSOProps->GetProgramIdentifier(L"ProgramName");
```
###### 백킹 메모리 크기 계산하기 

워크 그래프가 작동하는데 레코드필요한 내부 메모리의 크기를 얻어와 
UAV를 버퍼를 직접 할당해 제공해야한다.
```
ID3D12WorkGraphProperties* pWGProps = nullptr; 
m_stateObject->QueryInterface(IID_PPV_ARGS(&pWGProps));
UINT workGraphIndex = pWGProps->GetWorkGraphIndex(L"ProgramName");

D3D12_WORK_GRAPH_MEMORY_REQUIREMENTS memReqs = {}; 
pWGProps->GetWorkGraphMemoryRequirements(workGraphIndex, &memReqs); 
requiredSize = memReqs.MaxSizeInBytes; 
pWGProps->Release(); 
```
###### 프로그램 세팅하기 
```
typedef enum D3D12_PROGRAM_TYPE  
{  
	// 제네릭 프로그램은 그래프가 없는 기존의 파이프라인을 의미한다. 
	// 상태 객체로 통합함으로서 PSO를 더 유연하게 세팅 할 수 있게 되었다.
    D3D12_PROGRAM_TYPE_GENERIC_PIPELINE = 1,  
    D3D12_PROGRAM_TYPE_RAYTRACING_PIPELINE  = 4,  
    D3D12_PROGRAM_TYPE_WORK_GRAPH  = 5  
}D3D12_PROGRAM_TYPE;

typedef struct D3D12_SET_PROGRAM_DESC  
{  
    D3D12_PROGRAM_TYPE Type;  
    union   
	{  
        D3D12_SET_GENERIC_PIPELINE_DESC GenericPipeline;  
        D3D12_SET_RAYTRACING_PIPELINE_DESC RaytracingPipeline;  
        D3D12_SET_WORK_GRAPH_DESC WorkGraph;  
    };    
}D3D12_SET_PROGRAM_DESC;

void  ID3D12GraphicsCommandList::SetProgram(const D3D12_SET_PROGRAM_DESC *pDesc);  
```
###### D3D12_SET_WORK_GRAPH_DESC
```
typedef enum D3D12_SET_WORK_GRAPH_FLAGS  
{  
    D3D12_SET_WORK_GRAPH_FLAG_NONE  = 0,  
    D3D12_SET_WORK_GRAPH_FLAG_INITIALIZE = 0x1 // 최초에 백킹 메모리 초기화
}D3D12_SET_WORK_GRAPH_FLAGS;

typedef struct D3D12_GPU_VIRTUAL_ADDRESS_RANGE  
{  
    D3D12_GPU_VIRTUAL_ADDRESS StartAddress; // 시작 주소
    UINT64 SizeInBytes; // 크기 
}D3D12_GPU_VIRTUAL_ADDRESS_RANGE;

typedef struct D3D12_GPU_VIRTUAL_ADDRESS_AND_STRIDE  
{  
    D3D12_GPU_VIRTUAL_ADDRESS StartAddress; // 가상 GPU 주소
    UINT64 StrideInBytes;  // 구조체당 크기 
}D3D12_GPU_VIRTUAL_ADDRESS_AND_STRIDE;
    
typedef struct D3D12_SET_WORK_GRAPH_DESC  
{  
    D3D12_PROGRAM_IDENTIFIER ProgramIdentifier;  
    
    D3D12_SET_WORK_GRAPH_FLAGS Flags;
    D3D12_GPU_VIRTUAL_ADDRESS_RANGE BackingMemory; 
    D3D12_GPU_VIRTUAL_ADDRESS_RANGE_AND_STRIDE NodeLocalRootArgumentsTable; 
}D3D12_SET_WORK_GRAPH_DESC;
```
###### D3D12_SET_GENERIC_PIPELINE_DESC
```
typedef struct D3D12_SET_GENERIC_PIPELINE_DESC  
{  
    D3D12_PROGRAM_IDENTIFIER ProgramIdentifier;  
}D3D12_SET_GENERIC_PIPELINE_DESC;
```
###### D3D12_SET_RAYTRACING_PIPELINE_DESC
```

typedef struct D3D12_SET_RAYTRACING_PIPELINE_DESC  
{  
    D3D12_PROGRAM_IDENTIFIER ProgramIdentifier;  
}D3D12_SET_RAYTRACING_PIPELINE_DESC;
```
---
  
### 프로그램 실행 
CPU INPUT의 경우 드라이버가 직접 복사 해준다. 
```
UINT ID3D12WorkGraphProperties::GetWorkGraphIndex(LPCWSTR pProgramName);

UINT ID3D12WorkGraphProperties::GetEntrypointIndex(   
	UINT WorkGraphIndex,  
    D3D12_NODE_ID NodeID);
```

```
typedef enum D3D12_DISPATCH_MODE  
{  

	// 하나의 진입점 노드에  CPU 데이터를 입력값으로 넘김
    D3D12_DISPATCH_MODE_NODE_CPU_INPUT  = 0,  
    // 하나의 진입점 노드에 GPU 데이터를 입력값으로 넘김
    D3D12_DISPATCH_MODE_NODE_GPU_INPUT  = 1,  
    // 여러 개의 진입점 노드들에 CPU 데이터를 입력값으로 넘김
    D3D12_DISPATCH_MODE_MULTI_NODE_CPU_INPUT    = 2,  
    // 여러 개의 진입점 노드들에 GPU 데이터를 입력값으로 넘김
    D3D12_DISPATCH_MODE_MULTI_NODE_GPU_INPUT    = 3  
}D3D12_DISPATCH_MODE;

typedef struct D3D12_NODE_CPU_INPUT  
{  
    UINT EntrypointIndex;  
    UINT NumRecords;  // 입력 레코드 개수
    const void *pRecords;  //  CPU 상의 데이터 배열 시작 포인터
    UINT64 RecordStrideInBytes;  //데이터 한 개당 크기
}D3D12_NODE_CPU_INPUT;

typedef struct D3D12_NODE_GPU_INPUT  
{  
    UINT EntrypointIndex;  
    UINT NumRecords;  
    D3D12_GPU_VIRTUAL_ADDRESS_AND_STRIDE Records;  
}D3D12_NODE_GPU_INPUT;  
  
typedef struct D3D12_MULTI_NODE_CPU_INPUT  
{  
    UINT NumNodeInputs;  
    const D3D12_NODE_CPU_INPUT *pNodeInputs;  
    UINT64 NodeInputStrideInBytes;  
}D3D12_MULTI_NODE_CPU_INPUT;  
  
typedef struct D3D12_MULTI_NODE_GPU_INPUT  
{  
    UINT NumNodeInputs;  
    D3D12_GPU_VIRTUAL_ADDRESS_AND_STRIDE NodeInputs;  
}D3D12_MULTI_NODE_GPU_INPUT;

typedef struct D3D12_DISPATCH_GRAPH_DESC  
{  
    D3D12_DISPATCH_MODE Mode;  
    union   
	{  
        D3D12_NODE_CPU_INPUT NodeCPUInput;  
        D3D12_GPU_VIRTUAL_ADDRESS NodeGPUInput;  
        D3D12_MULTI_NODE_CPU_INPUT MultiNodeCPUInput;  
        D3D12_GPU_VIRTUAL_ADDRESS MultiNodeGPUInput;  
    };    
}D3D12_DISPATCH_GRAPH_DESC;

void ID3D12GraphicsCommandList::DispatchGraph(const D3D12_DISPATCH_GRAPH_DESC *pDesc);
```







