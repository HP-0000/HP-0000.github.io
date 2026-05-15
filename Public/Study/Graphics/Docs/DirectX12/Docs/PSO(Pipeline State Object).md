---
---

PSO는 셰이더 코드와 파이프라인 설정을 하나로 묶어, 드라이버가 GPU에서 실행할 수 있는 
네이티브 명령어와 상태"로 미리 변환해둔 객체

[Resource Binding](/Public/Study/Graphics/Docs/DirectX12/Docs/Resource%20Binding)
[Shader](/Public/Study/Graphics/Docs/DirectX12/Shader)
[Blending](/Public/Study/Graphics/Docs/DirectX12/Docs/Blending)
[MSAA](/Public/Study/Graphics/Docs/DirectX12/Docs/MSAA)
[Rasterizer](/Public/Study/Graphics/Docs/DirectX12/Docs/Rasterizer)
[DEPTH_STENCIL](/Public/Study/Graphics/Docs/DirectX12/Docs/DEPTH_STENCIL)

```
typedef enum D3D12_PIPELINE_STATE_FLAGS  
{  
    D3D12_PIPELINE_STATE_FLAG_NONE  = 0,  
    D3D12_PIPELINE_STATE_FLAG_TOOL_DEBUG    = 0x1,   
    D3D12_PIPELINE_STATE_FLAG_DYNAMIC_DEPTH_BIAS    = 0x4,  
    D3D12_PIPELINE_STATE_FLAG_DYNAMIC_INDEX_BUFFER_STRIP_CUT    = 0x8  
}D3D12_PIPELINE_STATE_FLAGS;

typedef struct D3D12_GRAPHICS_PIPELINE_STATE_DESC  
{  
    ID3D12RootSignature *pRootSignature;  // 루트 시그니처
    D3D12_SHADER_BYTECODE VS;   // 정점 셰이더
    D3D12_SHADER_BYTECODE PS;  // 픽셀 셰이더
    D3D12_SHADER_BYTECODE DS;  // 도메인 셰이더
    D3D12_SHADER_BYTECODE HS;  // 헐 셰이더
    D3D12_SHADER_BYTECODE GS;  // 지오메트리 셰이더
    D3D12_STREAM_OUTPUT_DESC StreamOutput;  // 스트림 출력 설정
    D3D12_BLEND_DESC BlendState;  // 렌더 타겟의 블렌딩(알파 혼합) 설정.
    UINT SampleMask;  //  uncovered 로 처리할 SampleDesc.Count 서브 샘플을
    D3D12_RASTERIZER_DESC RasterizerState;  // 래스터라이저 설정
    D3D12_DEPTH_STENCIL_DESC DepthStencilState;  // 깊이,스텐실 테스트 설정.
    D3D12_INPUT_LAYOUT_DESC InputLayout;  // 정점 버퍼의 레이아웃
    D3D12_INDEX_BUFFER_STRIP_CUT_VALUE IBStripCutValue;  //인덱스 버퍼에서 프리미티브 스트립 절단 값.
    D3D12_PRIMITIVE_TOPOLOGY_TYPE PrimitiveTopologyType;  // 프리미티브 토폴로지 타입
    UINT NumRenderTargets;  // 동시에 활성화할 렌더 타겟 개수
    DXGI_FORMAT RTVFormats[ 8 ];  // 각 렌더 타겟의 DXGI_FORMAT
    DXGI_FORMAT DSVFormat;  // 깊이, 스텐실 버퍼의 포맷
    DXGI_SAMPLE_DESC SampleDesc; // 멀티샘플링 설정
    UINT NodeMask;  // 다중 GPU 어댑터 마스크
    D3D12_CACHED_PIPELINE_STATE CachedPSO; //PSO 캐시 
    D3D12_PIPELINE_STATE_FLAGS Flags; // PSO 플래그
}D3D12_GRAPHICS_PIPELINE_STATE_DESC;

HRESULT ID3D12Device::CreateGraphicsPipelineState(   
	const D3D12_GRAPHICS_PIPELINE_STATE_DESC *pDesc,  
    REFIID riid,  
    void **ppPipelineState);
```

```
typedef struct D3D12_COMPUTE_PIPELINE_STATE_DESC  
{  
    ID3D12RootSignature *pRootSignature;  // 루트 시그니처
    D3D12_SHADER_BYTECODE CS;  // 컴퓨트 셰이더
    UINT NodeMask; // 다중 GPU 어댑터 마스크
    D3D12_CACHED_PIPELINE_STATE CachedPSO;  //PSO 캐시 
    D3D12_PIPELINE_STATE_FLAGS Flags;  
}D3D12_COMPUTE_PIPELINE_STATE_DESC;

HRESULT CreateComputePipelineState(   
	const D3D12_COMPUTE_PIPELINE_STATE_DESC *pDesc,  
    REFIID riid,  
    void **ppPipelineState) = 0;
```

```
void ID3D12GraphicsCommandList::SetPipelineState(ID3D12PipelineState *pPipelineState);
```

[Pipeline Stream](/Public/Study/Graphics/Docs/DirectX12/Docs/Pipeline%20Stream)
[Work Graphs](/Public/Study/Graphics/Docs/DirectX12/Work%20Graphs)