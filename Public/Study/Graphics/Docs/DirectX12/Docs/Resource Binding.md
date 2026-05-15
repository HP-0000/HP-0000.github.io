---
---

셰이더는 GPU 내에서 돌아가는 프로그램이다.
안전성과 보안 그리고 하드웨어 성능 최적화를 이유로, GPU 가상 주소에 대한 포인터 연산이나 역참조를 허용하지 않고 리소스에 대해 바인딩된 추상화된 셰이더 리소스 객체를 제공하여 접근을 제한한다.

# Root Parameter

루트 시그니처는 셰이더에 리소스를 바인딩 하는 방식을 정의한다.
256바이트 제한이 있다. 세가지 루트 파라미터 타입이 존재한다.

###### Root Constants
32비트 정수 값들을 직접 전달한다. 
DRAM 접근이 없기 때문에 빈번하게 변하는 데이터에 대해 명령을 빠르게 수행 가능하다.
###### Root Descriptor
리소스의 가상 주소를 직접 전달한다.
```
UINT64 GetGPUVirtualAddress(void); //리소스의 Base Address 주소 반환

void SetGraphicsRootShaderResourceView(
    UINT RootParameterIndex,  // 루트 파라미터의 인덱스
    D3D12_GPU_VIRTUAL_ADDRESS BufferLocation);
```
###### Descriptor Table
같은 리소스 뷰에 대해 배열을 GPU에 만들어 두고 그 시작 주소를 전달한다.

---
# Resource View
셰이더가 리소스를 최적화 해석 할 수 있게 정의한 설명자이며, 디스크립터를 의미한다.
같은 리소스에 여러 개의 뷰를 생성할 수 있다.

##### 디스크립터 힙 생성
Descriptor Table을 사용하는 루트 시그니처를 사용할 경우 먼저 GPU에 리소스 뷰를 담을 힙을 생성한다.

```
typedef enum D3D12_DESCRIPTOR_HEAP_TYPE  
{  
    D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV = 0,  
    D3D12_DESCRIPTOR_HEAP_TYPE_SAMPLER = (D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV + 1),  
    D3D12_DESCRIPTOR_HEAP_TYPE_RTV = (D3D12_DESCRIPTOR_HEAP_TYPE_SAMPLER + 1),  
    D3D12_DESCRIPTOR_HEAP_TYPE_DSV = (D3D12_DESCRIPTOR_HEAP_TYPE_RTV + 1),  
    D3D12_DESCRIPTOR_HEAP_TYPE_NUM_TYPES = (D3D12_DESCRIPTOR_HEAP_TYPE_DSV + 1) 
}D3D12_DESCRIPTOR_HEAP_TYPE;

typedef enum D3D12_DESCRIPTOR_HEAP_FLAGS  
{  
    D3D12_DESCRIPTOR_HEAP_FLAG_NONE = 0,  // 셰이더가 직접 접근 불가능.
    D3D12_DESCRIPTOR_HEAP_FLAG_SHADER_VISIBLE = 0x1  // 셰이더가 직접 접근 가능
}D3D12_DESCRIPTOR_HEAP_FLAGS;

typedef struct D3D12_DESCRIPTOR_HEAP_DESC  
{  
    D3D12_DESCRIPTOR_HEAP_TYPE Type;  
    UINT NumDescriptors;  
    D3D12_DESCRIPTOR_HEAP_FLAGS Flags;  
    UINT NodeMask;  // 다중 GPU 어댑터 마스크
}D3D12_DESCRIPTOR_HEAP_DESC;

HRESULT CreateDescriptorHeap(   
	D3D12_DESCRIPTOR_HEAP_DESC *pDescriptorHeapDesc,  
    REFIID riid,  
    void** ppvHeap); // ID3D12DescriptorHeap
```

디스크립터 힙의 핸들 얻기

```

D3D12_CPU_DESCRIPTOR_HANDLE ID3D12DescriptorHeap::GetCPUDescriptorHandleForHeapStart();
```

#### 뷰 생성
SRV는 읽기 전용 리소스이다.
```
typedef struct D3D12_SHADER_RESOURCE_VIEW_DESC  
{  
    DXGI_FORMAT Format;  
    D3D12_SRV_DIMENSION ViewDimension;  
    UINT Shader4ComponentMapping;  
    union   
	{  
        D3D12_BUFFER_SRV Buffer;  
        D3D12_TEX1D_SRV Texture1D;  
        D3D12_TEX1D_ARRAY_SRV Texture1DArray;  
        D3D12_TEX2D_SRV Texture2D;  
        D3D12_TEX2D_ARRAY_SRV Texture2DArray;  
        D3D12_TEX2DMS_SRV Texture2DMS;  
        D3D12_TEX2DMS_ARRAY_SRV Texture2DMSArray;  
        D3D12_TEX3D_SRV Texture3D;  
        D3D12_TEXCUBE_SRV TextureCube;  
        D3D12_TEXCUBE_ARRAY_SRV TextureCubeArray;  
        D3D12_RAYTRACING_ACCELERATION_STRUCTURE_SRV RaytracingAccelerationStructure;  
    };    
}D3D12_SHADER_RESOURCE_VIEW_DESC;

void CreateShaderResourceView(   
    ID3D12Resource *pResource,  
    const D3D12_SHADER_RESOURCE_VIEW_DESC *pDesc,  
    D3D12_CPU_DESCRIPTOR_HANDLE DestDescriptor);
```

UAV는 읽기/쓰기 리소스다. 
[Counter Resource](/Public/Study/Graphics/Docs/DirectX12/Docs/Counter%20Resource)
```
typedef struct D3D12_UNORDERED_ACCESS_VIEW_DESC  
{  
    DXGI_FORMAT Format;  
    D3D12_UAV_DIMENSION ViewDimension;  
    union   
	{  
        D3D12_BUFFER_UAV Buffer;  
        D3D12_TEX1D_UAV Texture1D;  
        D3D12_TEX1D_ARRAY_UAV Texture1DArray;  
        D3D12_TEX2D_UAV Texture2D;  
        D3D12_TEX2D_ARRAY_UAV Texture2DArray;  
        D3D12_TEX2DMS_UAV Texture2DMS;  
        D3D12_TEX2DMS_ARRAY_UAV Texture2DMSArray;  
        D3D12_TEX3D_UAV Texture3D;  
    };    
}D3D12_UNORDERED_ACCESS_VIEW_DESC;

void CreateUnorderedAccessView(   
	ID3D12Resource *pResource,  
	
	//버퍼 타입의 경우 카운터 리소스를 뷰에 같이 묶을 수 있다. 
	ID3D12Resource *pCounterResource, 
    const D3D12_UNORDERED_ACCESS_VIEW_DESC *pDesc,  
    D3D12_CPU_DESCRIPTOR_HANDLE DestDescriptor);
```

CBV는 읽기 전용이며 빈번하게 변경되며 크기가 작은 데이터이다.

```
typedef struct D3D12_CONSTANT_BUFFER_VIEW_DESC  
{  
    D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;  
    UINT SizeInBytes;  
}D3D12_CONSTANT_BUFFER_VIEW_DESC;
    
void CreateConstantBufferView(   
	const D3D12_CONSTANT_BUFFER_VIEW_DESC *pDesc,  
    D3D12_CPU_DESCRIPTOR_HANDLE DestDescriptor);
```
RTV와 DSV는 출력 병합기에 지정한다.
```
void CreateRenderTargetView(   
	ID3D12Resource *pResource,  
	const D3D12_RENDER_TARGET_VIEW_DESC *pDesc,  
	D3D12_CPU_DESCRIPTOR_HANDLE DestDescriptor);  
  
void CreateDepthStencilView(   
	ID3D12Resource *pResource,  
    const D3D12_DEPTH_STENCIL_VIEW_DESC *pDesc,  
    D3D12_CPU_DESCRIPTOR_HANDLE DestDescriptor);  
```

[Sampler](/Public/Study/Graphics/Docs/DirectX12/Docs/Sampler/Sampler)

###### 루트 시그니처 생성
[D3D12_ROOT_PARAMETER](/Public/Study/Graphics/Docs/DirectX12/Docs/D3D12_ROOT_PARAMETER)

```
typedef enum D3D12_ROOT_SIGNATURE_FLAGS  
{  
    D3D12_ROOT_SIGNATURE_FLAG_NONE  = 0,  
    D3D12_ROOT_SIGNATURE_FLAG_ALLOW_INPUT_ASSEMBLER_INPUT_LAYOUT    = 0x1,  
    D3D12_ROOT_SIGNATURE_FLAG_DENY_VERTEX_SHADER_ROOT_ACCESS    = 0x2,  
    D3D12_ROOT_SIGNATURE_FLAG_DENY_HULL_SHADER_ROOT_ACCESS  = 0x4,  
    D3D12_ROOT_SIGNATURE_FLAG_DENY_DOMAIN_SHADER_ROOT_ACCESS    = 0x8,  
    D3D12_ROOT_SIGNATURE_FLAG_DENY_GEOMETRY_SHADER_ROOT_ACCESS  = 0x10,  
    D3D12_ROOT_SIGNATURE_FLAG_DENY_PIXEL_SHADER_ROOT_ACCESS = 0x20,  
    D3D12_ROOT_SIGNATURE_FLAG_ALLOW_STREAM_OUTPUT   = 0x40,  
    D3D12_ROOT_SIGNATURE_FLAG_LOCAL_ROOT_SIGNATURE  = 0x80,  
    D3D12_ROOT_SIGNATURE_FLAG_DENY_AMPLIFICATION_SHADER_ROOT_ACCESS = 0x100,  
    D3D12_ROOT_SIGNATURE_FLAG_DENY_MESH_SHADER_ROOT_ACCESS  = 0x200,  
    D3D12_ROOT_SIGNATURE_FLAG_CBV_SRV_UAV_HEAP_DIRECTLY_INDEXED = 0x400,  // Bindless
    D3D12_ROOT_SIGNATURE_FLAG_SAMPLER_HEAP_DIRECTLY_INDEXED = 0x800  // Bindless
}D3D12_ROOT_SIGNATURE_FLAGS;

typedef struct D3D12_ROOT_SIGNATURE_DESC  
{  
    UINT NumParameters;   // 루트 파라미터 개수
    const D3D12_ROOT_PARAMETER *pParameters; // 루트 파라미터 배열
    UINT NumStaticSamplers;  // 정적 샘플러 개수 - 동적 샘플러는 디스크립터 힙에 생성
    const D3D12_STATIC_SAMPLER_DESC *pStaticSamplers;  
    D3D12_ROOT_SIGNATURE_FLAGS Flags;  // 루트 시그니처 동작 제어
}D3D12_ROOT_SIGNATURE_DESC;

HRESULT WINAPI D3D12SerializeRootSignature(  
     const D3D12_ROOT_SIGNATURE_DESC* pRootSignature,  
     D3D_ROOT_SIGNATURE_VERSION Version,  
     ID3DBlob** ppBlob,  
     ID3DBlob** ppErrorBlob);

HRESULT CreateRootSignature(   
	UINT nodeMask,  //다중 GPU 어댑터 마스크
    const void *pBlobWithRootSignature,  // 직렬화된 루트 시그니처 바이트코드 포인터
    SIZE_T blobLengthInBytes,  // 바이트코드의 크기
    REFIID riid,  
    void **ppvRootSignature); // ID3D12RootSignature
```

#### Bindless
전역 힙은 루트 시그니처 생성시 따로 루트 파라미터를 바인딩하지 않는다.
셰이더 코드에서 각각 ResourceDescriptorHeap , SamplerDescriptorHeap 으로 접근한다.

```
void SetDescriptorHeaps(
    UINT NumDescriptorHeaps,  
    ID3D12DescriptorHeap* ppDescriptorHeaps);
```

[Shader](/Public/Study/Graphics/Docs/DirectX12/Docs/Shader)
셰이더에서도 루트 시그니처를 정의할 수 있다.
[Execute Indirect](/Public/Study/Graphics/Docs/DirectX12/Docs/Execute%20Indirect)