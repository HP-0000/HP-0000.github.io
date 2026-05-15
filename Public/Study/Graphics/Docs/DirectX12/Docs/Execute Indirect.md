---
---

CPU에서 명령의 종류와 순서를 고정한 템플릿(Command Signature)을 미리 정의하고
GPU에서 인자 버퍼(Argument Buffer)에 해당 명령들의 매개 변수 그리고 레코드(고정된 명령 목록)의 반복 회수를 결정한 뒤 실행한다.

```
typedef enum D3D12_INDIRECT_ARGUMENT_TYPE  
{
    D3D12_INDIRECT_ARGUMENT_TYPE_DRAW = 0,  // DrawInstanced 실행
    D3D12_INDIRECT_ARGUMENT_TYPE_DRAW_INDEXED, //DrawIndexedInstanced 실행
    D3D12_INDIRECT_ARGUMENT_TYPE_DISPATCH,  // Dispatch 실행
    D3D12_INDIRECT_ARGUMENT_TYPE_VERTEX_BUFFER_VIEW ,  // Vertex Buffer 변경
    D3D12_INDIRECT_ARGUMENT_TYPE_INDEX_BUFFER_VIEW,  // Index Buffer 변경
    D3D12_INDIRECT_ARGUMENT_TYPE_CONSTANT ,   // Root 32비트 상수 설정
    D3D12_INDIRECT_ARGUMENT_TYPE_CONSTANT_BUFFER_VIEW ,  // Root CBV 설정
    D3D12_INDIRECT_ARGUMENT_TYPE_SHADER_RESOURCE_VIEW,  // Root SRV 설정
    D3D12_INDIRECT_ARGUMENT_TYPE_UNORDERED_ACCESS_VIEW ,  // Root UAV 설정
    D3D12_INDIRECT_ARGUMENT_TYPE_DISPATCH_RAYS ,  // DispatchRays 실행
    D3D12_INDIRECT_ARGUMENT_TYPE_DISPATCH_MESH ,  // DispatchMesh 실행
    D3D12_INDIRECT_ARGUMENT_TYPE_INCREMENTING_CONSTANT // 자동 증가하는 상수를 Root Constant에 설정
}D3D12_INDIRECT_ARGUMENT_TYPE;  

typedef struct D3D12_INDIRECT_ARGUMENT_DESC
{  
    D3D12_INDIRECT_ARGUMENT_TYPE Type;  // 실행 명령 
    union   
	{  
        struct   
		{  
            UINT Slot;  // Input Assembler 의 버퍼 슬롯
        }VertexBuffer;  
        
        struct   
		{  
            UINT RootParameterIndex;  
            UINT DestOffsetIn32BitValues;  // 루트 상수 버퍼 오프셋
            UINT Num32BitValuesToSet;  // 값 개수
	    }Constant;  
	    
        struct   
		{  
            UINT RootParameterIndex;  
        }ConstantBufferView;  
        
        struct   
		{  
            UINT RootParameterIndex;  
        }ShaderResourceView;  
        
        struct   
		{  
            UINT RootParameterIndex;  
        }UnorderedAccessView;  
        
        struct   
		{  
            UINT RootParameterIndex;  // 상수를 받을 루트 파라미터 인덱스
            UINT DestOffsetIn32BitValues;  //  루트 상수 버퍼 오프셋
        }IncrementingConstant;  
    };    
}D3D12_INDIRECT_ARGUMENT_DESC;    

typedef struct D3D12_COMMAND_SIGNATURE_DESC  
{  
    UINT ByteStride;  // 레코드 크기  
    UINT NumArgumentDescs;  // 레코드에 들어있는 명령 요소 개수
    const D3D12_INDIRECT_ARGUMENT_DESC *pArgumentDescs; // 레코드를 정의하는 명령 목록
    UINT NodeMask;  
}D3D12_COMMAND_SIGNATURE_DESC;

HRESULT ID3D12Device::CreateCommandSignature(
    const D3D12_COMMAND_SIGNATURE_DESC *pDesc,  
    ID3D12RootSignature *pRootSignature,  
    REFIID riid,  
    void **ppvCommandSignature);

void ID3D12GraphicsCommandList::ExecuteIndirect(
	ID3D12CommandSignature *pCommandSignature,  
    UINT MaxCommandCount, // 레코드 최대 반복 회수
    ID3D12Resource *pArgumentBuffer,  // 인자 버퍼 
    UINT64 ArgumentBufferOffset,  // ArgumentBuffer 시작 위치
    ID3D12Resource *pCountBuffer,  // 레코드 반복회수
    UINT64 CountBufferOffset);
```

# ID3D12Resource ArgumentBuffer
###### D3D12_INDIRECT_ARGUMENT_TYPE_DRAW
```
typedef struct D3D12_DRAW_ARGUMENTS  
{  
    UINT VertexCountPerInstance;  
    UINT InstanceCount;  
    UINT StartVertexLocation;  
    UINT StartInstanceLocation;  
}D3D12_DRAW_ARGUMENTS;
```
###### D3D12_INDIRECT_ARGUMENT_TYPE_DRAW_INDEXED
```
typedef struct D3D12_DRAW_INDEXED_ARGUMENTS  
{  
    UINT IndexCountPerInstance;  
    UINT InstanceCount;  
    UINT StartIndexLocation;  
    INT BaseVertexLocation;  
    UINT StartInstanceLocation;  
}D3D12_DRAW_INDEXED_ARGUMENTS;
```
###### D3D12_INDIRECT_ARGUMENT_TYPE_DISPATCH
```
typedef struct D3D12_DISPATCH_ARGUMENTS  
{  
    UINT ThreadGroupCountX;  
    UINT ThreadGroupCountY;  
    UINT ThreadGroupCountZ;  
}D3D12_DISPATCH_ARGUMENTS;
```
###### D3D12_INDIRECT_ARGUMENT_TYPE_VERTEX_BUFFER_VIEW
```
typedef struct D3D12_VERTEX_BUFFER_VIEW  
{  
    D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;  
    UINT SizeInBytes;  
    UINT StrideInBytes;  
}D3D12_VERTEX_BUFFER_VIEW;
```
###### D3D12_INDIRECT_ARGUMENT_TYPE_INDEX_BUFFER_VIEW
```
typedef struct D3D12_INDEX_BUFFER_VIEW  
{  
    D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;  
    UINT SizeInBytes;  
    DXGI_FORMAT Format;  
}D3D12_INDEX_BUFFER_VIEW;
```

###### D3D12_INDIRECT_ARGUMENT_TYPE_CONSTANT
```
UINT32 array
```
###### D3D12_INDIRECT_ARGUMENT_TYPE_CONSTANT_BUFFER_VIEW
```
D3D12_GPU_VIRTUAL_ADDRESS
```

###### D3D12_INDIRECT_ARGUMENT_TYPE_SHADER_RESOURCE_VIEW
```
D3D12_GPU_VIRTUAL_ADDRESS
```
###### D3D12_INDIRECT_ARGUMENT_TYPE_UNORDERED_ACCESS_VIEW
```
D3D12_GPU_VIRTUAL_ADDRESS
```
###### D3D12_INDIRECT_ARGUMENT_TYPE_DISPATCH_RAYS
```
typedef struct D3D12_DISPATCH_RAYS_DESC  
{  
    D3D12_GPU_VIRTUAL_ADDRESS_RANGE RayGenerationShaderRecord;  
    D3D12_GPU_VIRTUAL_ADDRESS_RANGE_AND_STRIDE MissShaderTable;  
    D3D12_GPU_VIRTUAL_ADDRESS_RANGE_AND_STRIDE HitGroupTable;  
    D3D12_GPU_VIRTUAL_ADDRESS_RANGE_AND_STRIDE CallableShaderTable;  
    UINT Width;  
    UINT Height;  
    UINT Depth;  
}D3D12_DISPATCH_RAYS_DESC;
```

###### D3D12_INDIRECT_ARGUMENT_TYPE_DISPATCH_MESH
```
typedef struct D3D12_DISPATCH_MESH_ARGUMENTS  
{  
    UINT ThreadGroupCountX;  
    UINT ThreadGroupCountY;  
    UINT ThreadGroupCountZ;  
}D3D12_DISPATCH_MESH_ARGUMENTS;
```
