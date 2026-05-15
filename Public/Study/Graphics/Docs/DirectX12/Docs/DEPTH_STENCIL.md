---
---

스텐실은 픽셀 단위로 특정 영역만 그리거나 제외하는 마스킹 버퍼이다.
참조값과 비교하여 실패하면 픽셀을 폐기한다.

스텐실 테스트를 통과한 픽셀은 깊이 테스트를 수행한다.


```

typedef enum D3D12_STENCIL_OP  
{  
    D3D12_STENCIL_OP_KEEP   = 1,  
    D3D12_STENCIL_OP_ZERO   = 2,   
    D3D12_STENCIL_OP_REPLACE   = 3,  // 참조 값 대체
    D3D12_STENCIL_OP_INCR_SAT  = 4, // 1 증가  
    D3D12_STENCIL_OP_DECR_SAT  = 5,  // 1 감소
    D3D12_STENCIL_OP_INVERT = 6,  // 비트 반전
    D3D12_STENCIL_OP_INCR   = 7,  // 1증가 (래핑 발생, 255→0)
    D3D12_STENCIL_OP_DECR   = 8  //1 감소 (래핑 발생, 0→255)
}D3D12_STENCIL_OP;  

typedef struct D3D12_DEPTH_STENCILOP_DESC  
{  
    D3D12_STENCIL_OP StencilFailOp;  
    D3D12_STENCIL_OP StencilDepthFailOp;  
    D3D12_STENCIL_OP StencilPassOp;  
    D3D12_COMPARISON_FUNC StencilFunc;  
}D3D12_DEPTH_STENCILOP_DESC;
    
typedef struct D3D12_DEPTH_STENCIL_DESC  
{  
    BOOL DepthEnable;  
    D3D12_DEPTH_WRITE_MASK DepthWriteMask;   
    D3D12_COMPARISON_FUNC DepthFunc;  // 깊이 비교 함수
    BOOL StencilEnable;  
    UINT8 StencilReadMask;  
    UINT8 StencilWriteMask;  
    D3D12_DEPTH_STENCILOP_DESC FrontFace;  
    D3D12_DEPTH_STENCILOP_DESC BackFace;  
}D3D12_DEPTH_STENCIL_DESC;
```
