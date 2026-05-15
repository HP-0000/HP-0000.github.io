---
---


루트 파라미터들은 레지스터 번호로 구분되며
HLSL 작성시 Register 문법으로 바인딩을 선언함으로서
값에 접근 할 수 있다.

```
Texture2D    g_texture : register(t0,space0);
```

```
typedef enum D3D12_ROOT_PARAMETER_TYPE  
{  
    D3D12_ROOT_PARAMETER_TYPE_DESCRIPTOR_TABLE = 0,  
    D3D12_ROOT_PARAMETER_TYPE_32BIT_CONSTANTS = (D3D12_ROOT_PARAMETER_TYPE_DESCRIPTOR_TABLE + 1),  
    D3D12_ROOT_PARAMETER_TYPE_CBV = (D3D12_ROOT_PARAMETER_TYPE_32BIT_CONSTANTS + 1),   
    D3D12_ROOT_PARAMETER_TYPE_SRV = (D3D12_ROOT_PARAMETER_TYPE_CBV + 1),  
    D3D12_ROOT_PARAMETER_TYPE_UAV = (D3D12_ROOT_PARAMETER_TYPE_SRV + 1)   
}D3D12_ROOT_PARAMETER_TYPE;
typedef enum D3D12_SHADER_VISIBILITY  
{  
    D3D12_SHADER_VISIBILITY_ALL = 0,  
    D3D12_SHADER_VISIBILITY_VERTEX  = 1,  
    D3D12_SHADER_VISIBILITY_HULL    = 2,  
    D3D12_SHADER_VISIBILITY_DOMAIN  = 3,  
    D3D12_SHADER_VISIBILITY_GEOMETRY    = 4,  
    D3D12_SHADER_VISIBILITY_PIXEL   = 5,  
    D3D12_SHADER_VISIBILITY_AMPLIFICATION   = 6,  
    D3D12_SHADER_VISIBILITY_MESH    = 7  
}D3D12_SHADER_VISIBILITY;

typedef struct D3D12_ROOT_PARAMETER  
{  
    D3D12_ROOT_PARAMETER_TYPE ParameterType;  
    union   
	{  
        D3D12_ROOT_DESCRIPTOR_TABLE DescriptorTable;  
        D3D12_ROOT_CONSTANTS Constants;  
        D3D12_ROOT_DESCRIPTOR Descriptor;  
    };  
    D3D12_SHADER_VISIBILITY ShaderVisibility;  
}D3D12_ROOT_PARAMETER;
```

# 디스크립터 테이블

```
typedef enum D3D12_DESCRIPTOR_RANGE_TYPE  
{  
    D3D12_DESCRIPTOR_RANGE_TYPE_SRV = 0,  
    D3D12_DESCRIPTOR_RANGE_TYPE_UAV = ( D3D12_DESCRIPTOR_RANGE_TYPE_SRV + 1 ) ,  
    D3D12_DESCRIPTOR_RANGE_TYPE_CBV = ( D3D12_DESCRIPTOR_RANGE_TYPE_UAV + 1 ) ,  
    D3D12_DESCRIPTOR_RANGE_TYPE_SAMPLER = ( D3D12_DESCRIPTOR_RANGE_TYPE_CBV + 1 )   
}D3D12_DESCRIPTOR_RANGE_TYPE;  
  
typedef struct D3D12_DESCRIPTOR_RANGE  
{  
    D3D12_DESCRIPTOR_RANGE_TYPE RangeType;  // 디스크립터의 종류
    UINT NumDescriptors;  // 디스크립터 개수, 차례로 레지스터 번호가 매핑된다.
    UINT BaseShaderRegister;  // 첫 번째 디스크립터가 매핑될 셰이더 레지스터 번호
    UINT RegisterSpace;  // 레지스터 스페이스
    UINT OffsetInDescriptorsFromTableStart;  // 디스크립터 테이블 시작점에서 이 범위까지의 오프셋
}D3D12_DESCRIPTOR_RANGE;  

typedef struct D3D12_ROOT_DESCRIPTOR_TABLE  
{  
    NumDescriptorRanges;  
    const D3D12_DESCRIPTOR_RANGE *pDescriptorRanges;
}D3D12_ROOT_DESCRIPTOR_TABLE;
```

# 디스크립터
```
typedef struct D3D12_ROOT_DESCRIPTOR  
{  
    UINT ShaderRegister;  
    UINT RegisterSpace;  
}D3D12_ROOT_DESCRIPTOR;
```

# 루트 상수 
```
typedef struct D3D12_ROOT_CONSTANTS  
{  
    UINT ShaderRegister;  
    UINT RegisterSpace;  
    UINT Num32BitValues;  
}D3D12_ROOT_CONSTANTS;
```