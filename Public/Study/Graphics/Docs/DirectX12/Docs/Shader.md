---
---
# Compile
DirectXShaderCompiler (DXC) 를 사용하여 컴파일 해야한다.
https://github.com/microsoft/DirectXShaderCompiler
컴파일 결과물은 DXIL(DirectX Intermediate Language)
GPU 하드웨어에 종속되지 않은 바이너리 덩어리다.
PSO가 생성될때 비로서 최종 기계어로 변환 된다.
```
#include <d3d12.h>
#include <dxcapi.h>
#include <wrl/client.h>

using Microsoft::WRL::ComPtr;

// 1. DXC 인스턴스 생성
ComPtr<IDxcUtils> pUtils;
DxcCreateInstance(CLSID_DxcUtils, IID_PPV_ARGS(&pUtils));

ComPtr<IDxcCompiler3> pCompiler;
DxcCreateInstance(CLSID_DxcCompiler, IID_PPV_ARGS(&pCompiler));

ComPtr<IDxcIncludeHandler> pIncludeHandler;
pUtils->CreateDefaultIncludeHandler(&pIncludeHandler);


// 2. HLSL 소스 코드 읽기
ComPtr<IDxcBlobEncoding> pSourceBlob;

pUtils->LoadFile(
    L"Shader.hlsl",
    nullptr,
    &pSourceBlob);


// 3. 컴파일 옵션 지정
LPCWSTR pszArgs[] =
{
    L"-E", L"MSMain",   // 진입점 함수
    L"-T", L"ms_6_8",   // 셰이더 프로파일
};


// 4. 컴파일 실행
DxcBuffer Source = {};

Source.Ptr      = pSourceBlob->GetBufferPointer();
Source.Size     = pSourceBlob->GetBufferSize();
Source.Encoding = DXC_CP_UTF8;

ComPtr<IDxcResult> pResult;

HRESULT hr = pCompiler->Compile(
    &Source,
    pszArgs,
    _countof(pszArgs),
    pIncludeHandler.Get(),
    IID_PPV_ARGS(&pResult));


// 5. 결과 확인
HRESULT CompileResult = S_OK;
pResult->GetStatus(&CompileResult);

if (FAILED(CompileResult))
{
    ComPtr<IDxcBlobUtf8> pErrorBlob;

    pResult->GetOutput(
        DXC_OUT_ERRORS,
        IID_PPV_ARGS(&pErrorBlob),
        nullptr);

    if (pErrorBlob)
    {
        printf("%s\n", pErrorBlob->GetStringPointer());
    }
}
else
{
	//----------------------------------------------------------  
	// DXIL Object (DXIL Container)  
	//----------------------------------------------------------

	ComPtr<IDxcBlob> pDXILObjectBlob;  
	  
	pResult->GetOutput(  
		DXC_OUT_OBJECT,  
		IID_PPV_ARGS(&pDXILObjectBlob),  
		nullptr);

    D3D12_SHADER_BYTECODE DXILByteCode =
    {
        pDXILObjectBlob->GetBufferPointer(),
        pDXILObjectBlob->GetBufferSize()
    };
    //----------------------------------------------------------
    // Embedded Root Signature
    //----------------------------------------------------------
    ComPtr<IDxcBlob> pRootSignatureBlob;
    hr = pResult->GetOutput(
        DXC_OUT_ROOT_SIGNATURE,
        IID_PPV_ARGS(&pRootSignatureBlob),
        nullptr);
    if (SUCCEEDED(hr))
    {
        ComPtr<ID3D12RootSignature> pRootSignature;
        pDevice->CreateRootSignature(
            0,
            pRootSignatureBlob->GetBufferPointer(),
            pRootSignatureBlob->GetBufferSize(),
            IID_PPV_ARGS(&pRootSignature));
            
        // 내용 분석
        ComPtr<ID3D12RootSignatureDeserializer> deserializer;
		D3D12CreateRootSignatureDeserializer(
		    pRootSignatureBlob->GetBufferPointer(),
		    pRootSignatureBlob->GetBufferSize(),
		    IID_PPV_ARGS(&deserializer));
		
		const D3D12_ROOT_SIGNATURE_DESC* desc = deserializer->GetRootSignatureDesc();
    }
}
```

# 셰이더 작성
https://github.com/microsoft/DirectXShaderCompiler/blob/main/tools/clang/lib/Sema/SemaHLSL.cpp
타입에 관해서는  ArBasicKind values 참조

https://github.com/microsoft/DirectXShaderCompiler/blob/main/tools/clang/include/clang/Basic/Attr.td
어트리뷰트에 관해서는 HLSL Change - HLSL Change End 구간 참조

#### SubobjectKind
[Work Graphs](/Public/Study/Graphics/Docs/DirectX12/Work%20Graphs)
셰이더 모델 6.3부터는 서브 오브젝트를 HLSL 코드 내에서 직접 정의할 수 있습니다
```
    "StateObjectConfig", // D3D12_STATE_OBJECT_CONFIG
    "GlobalRootSignature", // D3D12_GLOBAL_ROOT_SIGNATURE
    "LocalRootSignature",  // D3D12_LOCAL_ROOT_SIGNATURE
    "SubobjectToExportsAssociation", // D3D12_DXIL_SUBOBJECT_TO_EXPORTS_ASSOCIATION
    "RaytracingShaderConfig", // RaytracingShaderConfig
    "RaytracingPipelineConfig", // D3D12_RAYTRACING_PIPELINE_CONFIG
    
    // D3D12_HIT_GROUP_DESC ,Type 필드가 D3D12_HIT_GROUP_TYPE_TRIANGLES
    "TriangleHitGroup", 
    
    // D3D12_HIT_GROUP_DESC ,Type 필드가 D3D12_HIT_GROUP_TYPE_PROCEDURAL_PRIMITIVE
    "ProceduralPrimitiveHitGroup",
    "RaytracingPipelineConfig1", // D3D12_RAYTRACING_PIPELINE_CONFIG1 
```

##  1.HLSL Control Flow Attributes

###### branch, flatten
[GPU 구조](/Public/Study/컴퓨터%20구조/Docs/GPU/GPU%20구조)
분기 시 각 Path를 실행하는데 레인 활성화가 순차적으로 진행되기 때문에, 실행 시간이 길어짐으로
컴파일는 이를 최적화하여 워프가 모든 Path를 계산하고 조건에 따른 값을 선택하도록 Flatten 이 일어날 수 있다.

어트리뷰트를 작성함으로서 컴파일러에게 최적화 힌트를 제공한다.
```
// if / switch 위에 작성
[branch]
[flatten]
```

###### unroll , loop
반복문의 경우 반복 횟수 만큼 
종료 조건 비교, 회수 증가 연산, 명령어 점프를 수행해야한다.

컴파일러는 반복문을 최적화하여
```
[unroll]
for (int i = 0; i < 4; i++) 
{
	Execute()
}
```
아래와 같이 풀어낸다. 
```
Execute(1)
Execute(2)
Execute(3)
Execute(4)
```

실행 속도는 더 빠르지만, 명령 자체의 개수는 증가하였다.
만약 수만 번의 반복할 경우에는 풀어내면 명령어 개수가 그만큼 증가함으로
명령어 캐시를 넘어서게 된다. 그러한 경우에는 loop 힌트를 준다.

###### forcecase
switch 문의 경우, 컴파일러는 if else 문으로 해석하기도 하고
점프 테이블을 만들어 조건 비교를 위한 명령어 낭비를 최소화 하고 바로 실행할 수 있게 한다.

###### call
Switch 문이 최적화 되면서 실행되지 않을 분기의 변수들까지 모두 레지스터를 할당해야 함으로 낭비가 생긴다.
이 어트리뷰트를 사용하면 각 case 들을 서브루틴으로 만든다. 서브 루틴을 만듦으로서 분기의 손해를 감수하고, 
명령어 최적화에 제약을 걸어 할당에 필요한 레지스터를 줄인다.

###### fastopt
과도한 최적화 시뮬레이션을 하지 않고 빠르게 컴파일을 요구한다.

###### allow_uav_condition
UAV의 경우,  다른 워프가 종료 조건을 write 해야 하지만 현재 루프를 도는 워프로 인해
스케줄링 되지 않는 경우 무한 루프의 위험이 있다. 따라서 hlsl에서 루프 종료 조건에 UAV에서 읽은 데이터를 사용하면 컴파일 오류를 낸다. 이를 끌 수 있는 어트리뷰트다.

##  2.HLSL Function Attributes
### 2.1 셰이더 진입점 및 단계 제어 (Shader Stages & Entry Points)
###### Shader
진입점을 가리키기 위해  함수 위에 작성합니다.
```
[shader("Pixel")]
[shader("Vertex")]
[shader("Geometry")]
[shader("Hull")]
[shader("Domain")]
[shader("Compute")]
[shader("RayGeneration")]
[shader("Intersection")]
[shader("AnyHit")]
[shader("ClosestHit")]
[shader("Miss")]
[shader("Callable")]
[shader("Mesh")]
[shader("Amplification")]
[shader("Node")]
```
###### earlydepthstencil
픽셀 셰이더 함수 위에 작성하며 Early-Z 를 강제로 수행하는 일을 한다.

Z write On 상태에서 Early-Z 의 경우 픽셀 셰이더 에서 깊이 값을 수정하거나 clip, discard를 호출하면
존재하지도 않는 픽셀의 z값이 z 버퍼에 채워질 수 있음으로 하드웨어에 의해 기능이 꺼지게 된다.

픽셀 셰이어에 UAV 버퍼에 값을 쓰는 코드가 있으면 깊이 테스트 결과와 무관하게 실행되어야 한다고 
판단됨으로 하드웨어에 의해 Early-Z  기능이 꺼지게 된다. 

###### clipplanes
버텍스 셰이더 함수 위에 작성하며 
컴파일러가 셰이더 코드 끝에 정점 위치와 클립 평면 간의 거리 계산 코드를 추가한다. 
평면 외부의 정점들에 대해서 클리핑 단계가 수행된다.

```
cbuffer ClipPlaneBuffer : register(b0) 
{ 
	float4 g_ClipPlane0; 
	float4 g_ClipPlane1; 
};

//최대 6개 까지의 평면을 제공할 수 있다.
[clipplanes(g_ClipPlane0,g_ClipPlane1, ...)]
```

현대적인 방법으로는 float4 출력값 에 대해 SV_ClipDistance0, SV_ClipDistance1 시멘틱을 
사용해 최대 8개의 평면과의 거리를 직접 계산해 넘겨주는 방법이 있다.

###### numthreads
메시 셰이더나 컴퓨트 셰이더 함수 위에 작성.
연산을 수행할 스레드 개수를 지정한다.
```
[numthreads(X, Y, Z)]
```
###### GroupSharedLimit
메시 셰이더나 컴퓨트 셰이더 함수 위에 작성.
```
[GroupSharedLimit(<bytes>)]
```
사용 가능한 공유 메모리를 명시함

###### wavesize
numthreads를 실행할 때 
Warp 또는 Wave 라고 부르는 실제 집행 단위를 요청한다.
```
[wavesize(Min, Max, Preferred)]
```
###### waveopsincludehelperlanes
픽셀 셰이더 위에 선언된다.
[Rasterizer](/Public/Study/Graphics/Docs/DirectX12/Docs/Rasterizer)

웨이브 명령어는 활성화된 스레드가 실행하는 코드이다. 
가상 픽셀을 수행하는 셰이더의 경우 웨이브 연산에 빠지게 되는데
이때 같이 수행되도록 한다.

###### RootSignature
진입점 함수 위에 작성한다.
```
[RootSignature(...)]
```

```
RootFlags( FLAG1 | FLAG2 | ... )
CBV/SRV/UAV(레지스터, [space, visibility])
RootConstants(num32BitConstants = 값_개수, b레지스터, [space, visibility])
DescriptorTable(범위1, 범위2, ... , [visibility] )
StaticSampler( sReg, [filter, addressU, addressV, addressW , mipLODBias, maxAnisotropy , comparisonFunc, borderColor, minLOD, maxLOD,space, visibility])

DescriptorTable( Range1, Range2, ... , [visibility] )

#### Range
CBV( bReg, [ numDescriptors, space, offset, flags ] )
SRV( tReg, [ numDescriptors, space, offset, flags] )
UAV( uReg, [ numDescriptors, space, offset, flags] )
Sampler(sReg, [ numDescriptors, space, offset] )
```
###### export
함수 정의 앞에 붙여 선언한다.
라이브러리로 셰이더 파일을 컴파일 하는 경우, 외부에서 호출 가능한
공개용 API 함수를 제공하는 역할이다.

### 2.2 매개변수 한정자 및 데이터 전달 (Parameters & Data Flow)

###### in
매개 변수 타입 앞에 선언
내부에서 값을 변경하더라도 함수가 끝난 뒤 호출부의 원래 변수 값은 변하지 않습니다.

###### out
매개 변수 타입 앞에 선언
결과를 밖으로 꺼내기 위한 용도.
함수가 시작될 때 변수 안의 이전 값은 무시되므로 내부에서 값을 할당해야한다.

###### inout
매개 변수 타입 앞에 선언
원래 변수가 가진 값을 함수 내부로 가져와서 사용하고, 함수 내부에서 값을 수정한 뒤 그 결과물을 다시 호출부의 변수로 되돌려줍니다

######  vertices, primitives, indices 
메시 셰이더는 vertices, primitives(opt), indices 정보를 담은 구조체를 인자로 보내야한다.
이때 출력 종류를 나타내기 위해 사용한다.
```
[out] [vertices / primitives / indices ]  [데이터 타입] [변수명] [최대 개수 배열]
```

###### outputtopology
메시 진입점 코드 위에 작성한다.
```
[outputtopology(line)]
[outputtopology(triangle)]
```
토폴로지를 지정한다.

###### raypayload
DXR 파이프라인에서 입력 구조체 정의부에 선언한다.

액세스 한정자를 도입해 셰이더 마다 접근 할 데이터를 레지스터 최적화한다.
```
struct [raypayload] MyPayload 
{
    // color는 ClosestHit, Miss가 쓰고, 호출자가 읽습니다.
    float4 color  : read(caller) : write(closesthit, miss);
};
```

### 2.3 매개변수 보간 방식 (Interpolation Qualifiers)
픽셀 셰이더의 입력 변수 앞에 선언하여 래스터 라이저의 보간 방식을 정의한다.
기본 적으로 linear이다.

###### linear 
원근 보정 선형 보간을 수행한다. 
###### noperspective 
원근 보정을 적용하지 않고 화면 공간에서 선형 보간을 수행한다.
###### nointerpolation 
보간을 수행하지 않고 첫 번째 정점이 대표하는 값을 그대로 적용한다.
###### centroid 
MSAA 에서 활성화된 커버리지 샘플 중 하나를 선택해 보간을 수행한다. 
###### center 
MSAA 에서 픽셀 중심 위치를 기준으로 보간을 수행한다.
###### sample 
MSAA 에서 활성화된 커버리지 샘플 마다 보간을 수행한다. 

### 2.4 변수 저장소, 정밀도 및 배치 (Storage, Precision & Memory Layout)
###### precise
변수 앞에 선언한다.
부동 소수점 반올림 오차로 인해 계산 순서에 따라 결과가 달라진다.
따라서 변수에 값을 저장하는 연산 단계에 대해 최적화 및 순서 재배치를 막는다.
###### column_major / row_major
행렬 자료형인 float4x4 타입 앞에 선언한다.
###### unorm / snorm
float 타입 앞에 선언한다.

1바이트를 사용하여 255 정수 데이터를 
unorm은 0~1,  snorm은 -1 ~ 1 값으로 
Vram에 압축하여 저장하거나 압축을 해제 하여 읽는다.

###### groupshared
동일한 스레드 그룹에 속한 워프들이 공유할 변수 앞에 선언한다.

```
// groupshared 변수의 쓰기가 완료될때가지 대기하고 완료된 워프는 스케줄링 대기열에서 제외
// 동일한 그룹의 모든 워프가 장벽에 도달시 대기 해제 신호를 보내 스케줄링되도록 한다. 
Barrier(GROUP_SHARED_MEMORY, GROUP_SCOPE | GROUP_SYNC)
```

###### globallycoherent
UAV 리소스 앞에 선언한다.

```
// globallycoherent 리소스의 쓰기가 완료될때가지 대기하고 
// 완료된 워프는 스케줄링 대기열에서 제외
// 동일한 그룹의 모든 워프가 장벽에 도달시 대기 해제 신호를 보내 스케줄링되도록 한다. 
Barrier(UAV_MEMORY, DEVICE_SCOPE | GROUP_SYNC)
```

어떤 SM이 리소스에 접근할지 모르기 때문에 전역 SM 으로 플러시가 일어난다.

###### reordercoherent
UAV 리소스 앞에 선언한다.
```
// reordercoherent 변수의 쓰기가 완료될때가지 대기하고 완료된 워프는 스케줄링 대기열에서 제외 
// 동일한 그룹의 모든 워프가 장벽에 도달시 대기 해제 신호를 보내 스케줄링되도록 한다.  
Barrier(UAV_MEMORY, REORDER_SCOPE | GROUP_SYNC)
```
DXR에서 충돌체에 따라 실행 코드가 분기 되는데 이때 각 노드들로 스레드 그룹이 재정렬 되어 
실행된다. 이때 어떤 SM으로 이동되는지 알고 있음으로 해당하는 SM 들 에게만 플러시가 일어난다.


### 2.5 Work Graphs / Node Shader 제어 (작업 그래프)

###### nodelaunch
노드의 진입점 함수 위에 선언한다.
```
[NodeLaunch("thread")] // 레코드 당 스레드가 실행된다.
void MyBroadNode(ThreadNodeInputRecord<MyData> input) 
{ 
	// 스레드 전체가 이 하나의 레코드 데이터를 공유해서 읽습니다. 
	MyData data = input.Get(); 
}

[NodeLaunch("broadcasting")] // 레코드당 스레드 그룹이 실행된다
[NodeDispatchGrid(16, 1, 1)]  // 생성할 그룹 개수를 정의
[numthreads(8, 8, 1)] // 스래드 그룹당 스레드 개수 정의
void MyBroadNode(DispatchNodeInputRecord<MyData> input) 
{ 
	// 그룹 전체가 이 하나의 레코드 데이터를 공유해서 읽습니다. 
	MyData data = input.Get(); 
}

[NodeLaunch("mesh")]
[outputtopology("triangle")]
[NodeDispatchGrid(16, 1, 1)]  // 생성할 그룹 개수를 정의
[numthreads(8, 8, 1)] // 스래드 그룹당 스레드 개수 정의
void MyMeshNode( 
	DispatchNodeInputRecord<MyData> input, 
	out vertices MyDataVert verts[64], 
	out primitives MyDataPrim prim[64], 
	out indices uint3 MyDataidx[64])
{ 	
	MyData record = input.Get();
	SetMeshOutputCounts(uint numVertices, uint numPrimitives);
	if (localID < record.numVertices) 
	{ 
		
	}
	if (localID < record.numPrimitives) 
	{ 
		
	}
}

[numthreads(64, 1, 1)]
[NodeLaunch("coalescing")] // 스케줄러가 레코드를 적절히 쌓은 뒤 스레드 그룹 하나를 실행한다.
void MyCoalesceNode([MaxRecords(32)] GroupNodeInputRecords<MyData> inputs) 
{ 
	uint recordCount = inputs.Count(); 
	if (localID < recordCount) 
	{ 
		MyData record = inputs[localID]; 
	}
}
```
###### nodeisprogramentry
워크 그래프의 최초 프로그램 진입점 함수 위에 선언한다.
```
[NodeIsProgramEntry]
```
###### nodeid
// 진입점 함수 위에 선언한다.
```
[NodeID("노드이름")]
[NodeID("노드이름", 배열인덱스)]
```
노드의 출력 변수들에, 출력 노드의 ID를 사용함으로서 노드의 연결을 나타낸다. 
워크 그래프는 서로 다른 셰이더 코드를 실행해야할때 분기가 아닌, 재 정렬을 선택해
성능을 높인다. 서로 다른 노드는 같은 이름 다른 인덱스로 묶여 출력으로 지정될 수 있다.

###### nodelocalrootargumentstableindex 
진입점 함수 위에 작성한다.

```
[NodeLocalRootArgumentsTableIndex(상수)]
```
로컬 루트파라미터 테이블의 인덱스를 명시한다.

###### nodeshareinputof
진입점 함수 위에 작성한다.

```
[NodeShareInputOf(Node ID)]
```

노드가 출력 매개 변수로 따로 명시하지 않아도 
Node ID로의 출력 레코드에 대해 자신도 실행된다.

###### nodedispatchgrid 
노드 진입점 위에 선언한다.
```
[NodeDispatchGrid(x, y, z)]
```
노드가 실행될 때 실행할 스레드 그룹의 개수를 지정한다.

###### nodemaxdispatchgrid
노드 진입점 위에 선언한다.
```
struct MyData 
{ 
	uint3 GridSize : SV_DispatchGrid;
};

[NodeMaxDispatchGrid(x, y, z)]
```
입력 데이터로 동적으로 읽어와 스레드 그룹을 생성할 때, 최대 한도를 지정한다.

###### nodemaxrecursiondepth
재귀적으로 자기 자신을 다시 호출하는 노드 함수에 선언한다.
```
// 노드의 최대 실행 길이는 32로 제한된다.
[NodeMaxRecursionDepth(N)]
```
워크 그래프에서 노드 간의 상호 재귀는 불가하지만
예외적으로 자기 자신을 호출하는 것은 가능하다.

###### nodetrackrwinputsharing
입력 레코드 구조체의 정의부에 선언한다.

구조체에 스레드 그룹의 작업을 추적하기 위한 4바이트의 원자적 카운터가 추가 된다.

```
RWDispatchNodeInputRecord<T> inputRecord // RW가 붙은 Input Record 를 입력 받는다.

if (inputRecord.FinishedCrossGroupSharing()) 
{ 
	각 스레드 그룹의 대표 스레드가 이 값을 1 씩 올리고
	마지막으로 1을 올린 스레드 그룹은 True 가 된다 
}
```
동일한 스레드 그룹의 작업은 배리어로 동기화 할 수 있다.

# 3. HLSL Parameter Attributes (매개변수 속성)
###### MaxRecords
Coalescing 노드에서 입력 레코드 매개변수 바로 앞에 선언 하여
최대로 모일 수 있는 레코드를 지정하거나 출력  매개변수 앞에 붙여 
최대 몇 개의 레코드를 내보낼지 지정한다.
```
[MaxRecords(상수)]
```
###### maxrecordssharedwith
출력 인자 앞에 선언한다.
```
[Shader("node")] 
void MyNode( 
[MaxRecords(상수)] NodeOutput<MyData> outRecordA, 
[MaxRecordsSharedWith(outRecordA)] NodeOutput<MyData> outRecordB)
```
출력 인자에 해당하는 레코드 메모리를 공유하여 사용한다.

###### allowsparsenodes
출력 인자에 선언한다.
```
[AllowSparseNodes]
```
연결될 노드가 존재하지 않더라도 D3D12 런타임이 파이프라인 컴파일 에러를 내지 않게 한다.
###### NodeArraySize
NodeOutputArray 출력 매개변수 앞에 선언한다.
```
[Shader("node")] void MyNode( 
[MaxRecords(64)] [NodeArraySize(3)] NodeOutputArray<MyData> shader )
```
타겟 노드 배열의 크기를 지정한다.

###### unboundedsparsenodes
출력 노드 배열에 선언한다.
```
[UnboundedSparseNodes]
```
D3D12 런타임이 실제 연결 상태를 스캔하여 NodeArraySize를 알아서 계산한다.
연결될 노드가 존재하지 않더라도 컴파일 에러를 내지 않게 한다.