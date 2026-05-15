언리얼의 셰이더 타입은 

```
EShaderTypeForDynamicCast
├─ Global
├─ Material
├─ MeshMaterial
├─ Niagara
├─ OCIO
├─ ComputeKernel
└─ NNERuntimeIREE
```





###### FShader







######  FShaderType
- FGlobalShaderType
- FMaterialShaderType
- FMeshMaterialShaderType
- FNiagaraShaderType
- FOpenColorIOShaderType

C++ 클래스에서 아래와 같이 등록한다.
```
IMPLEMENT_SHADER_TYPE(
    ,
    FSDFMeshShader,
    TEXT("SDFMesh"),
    TEXT("MainMS"),
    SF_Mesh
);

이는 .usf 파일과 entry point 그리고 shader frequency를 매핑한다

```
`FShader`는 셰이더가 컴파일된 내용을 담는다
이것들은 Shader Map에 담기고 

RHICmdList.SetComputeShader 등으로 셰이더를 연결한다.

Render Dependency Graph

Global Shader는 
FGlobalShader ↓ FGlobalShaderType ↓ FGlobalShaderMap 라는 경로를 쓰고 

FMaterialShader ↓ FMaterialShaderType ↓ Material Shader Map  와 같이 경로가 쓰인다

FSDFWorkGraphShader
        ↓
FGlobalShaderType
        ↓
SDFWorkGraph.usf
        ↓
Shader Compiler
        ↓
FGlobalShaderMap
        ↓
TShaderMapRef
        ↓
FRHIWorkGraphShader
        ↓
FRHIWorkGraphPipelineState
        ↓
DX12 Work Graph
        ↓
Mesh Shader
        ↓
SDF