---
---


픽셀 셰이더가 출력한 색상과 이미 렌더 타겟에 저장된 색상을 어떻게 혼합할지 결정
한번에 듀얼 소스 블렌딩 가능
```

typedef enum D3D12_LOGIC_OP  
{  
    D3D12_LOGIC_OP_CLEAR    = 0,  
    D3D12_LOGIC_OP_SET  = (D3D12_LOGIC_OP_CLEAR + 1) ,  
    D3D12_LOGIC_OP_COPY = (D3D12_LOGIC_OP_SET + 1) ,  
    D3D12_LOGIC_OP_COPY_INVERTED = (D3D12_LOGIC_OP_COPY + 1),  
    D3D12_LOGIC_OP_NOOP = (D3D12_LOGIC_OP_COPY_INVERTED + 1),  
    D3D12_LOGIC_OP_INVERT = (D3D12_LOGIC_OP_NOOP + 1),  
    D3D12_LOGIC_OP_AND  = (D3D12_LOGIC_OP_INVERT + 1),  
    D3D12_LOGIC_OP_NAND = (D3D12_LOGIC_OP_AND + 1),  
    D3D12_LOGIC_OP_OR   = (D3D12_LOGIC_OP_NAND + 1),  
    D3D12_LOGIC_OP_NOR  = (D3D12_LOGIC_OP_OR + 1),  
    D3D12_LOGIC_OP_XOR  = (D3D12_LOGIC_OP_NOR + 1),  
    D3D12_LOGIC_OP_EQUIV = (D3D12_LOGIC_OP_XOR + 1),  
    D3D12_LOGIC_OP_AND_REVERSE  = (D3D12_LOGIC_OP_EQUIV + 1),  
    D3D12_LOGIC_OP_AND_INVERTED = (D3D12_LOGIC_OP_AND_REVERSE + 1),  
    D3D12_LOGIC_OP_OR_REVERSE   = (D3D12_LOGIC_OP_AND_INVERTED + 1), 
    D3D12_LOGIC_OP_OR_INVERTED  = (D3D12_LOGIC_OP_OR_REVERSE + 1)   
}D3D12_LOGIC_OP;

typedef enum D3D12_BLEND_OP  
{  
    D3D12_BLEND_OP_ADD  = 1,  // Src * SrcBlend + Dst * DestBlend
    D3D12_BLEND_OP_SUBTRACT = 2,  // Src * SrcBlend - Dst * DestBlend
    D3D12_BLEND_OP_REV_SUBTRACT = 3,  // Dst * DestBlend - Src * SrcBlend
    D3D12_BLEND_OP_MIN  = 4,  // min(Src * SrcBlend, Dst * DestBlend)
    D3D12_BLEND_OP_MAX  = 5  // max(Src * SrcBlend, Dst * DestBlend)
}D3D12_BLEND_OP;


typedef enum D3D12_BLEND  
{  
    D3D12_BLEND_ZERO    = 1,  // 0 
    D3D12_BLEND_ONE = 2,  // 1
    D3D12_BLEND_SRC_COLOR   = 3, 
    D3D12_BLEND_INV_SRC_COLOR   = 4,  
    D3D12_BLEND_SRC_ALPHA   = 5,  
    D3D12_BLEND_INV_SRC_ALPHA   = 6,  
    D3D12_BLEND_DEST_ALPHA  = 7,  
    D3D12_BLEND_INV_DEST_ALPHA  = 8,  
    D3D12_BLEND_DEST_COLOR  = 9,  
    D3D12_BLEND_INV_DEST_COLOR  = 10,  
    D3D12_BLEND_SRC_ALPHA_SAT   = 11,   // min(출처 알파, 1 - 목적지 알파)
    D3D12_BLEND_BLEND_FACTOR    = 14,  // OMSetBlendFactor 로 설정
    D3D12_BLEND_INV_BLEND_FACTOR    = 15,  
    D3D12_BLEND_SRC1_COLOR  = 16,  
    D3D12_BLEND_INV_SRC1_COLOR  = 17,  
    D3D12_BLEND_SRC1_ALPHA  = 18,  
    D3D12_BLEND_INV_SRC1_ALPHA  = 19,  
    D3D12_BLEND_ALPHA_FACTOR    = 20,  
    D3D12_BLEND_INV_ALPHA_FACTOR    = 21  
}D3D12_BLEND;

typedef struct D3D12_RENDER_TARGET_BLEND_DESC  
{  
    BOOL BlendEnable;  
    BOOL LogicOpEnable;  // 블렌딩 대신 논리 연산 사용
    D3D12_BLEND SrcBlend;   
    D3D12_BLEND DestBlend;  
    D3D12_BLEND_OP BlendOp;  // 블렌딩 연산
    D3D12_BLEND SrcBlendAlpha;  
    D3D12_BLEND DestBlendAlpha;  
    D3D12_BLEND_OP BlendOpAlpha;  
    D3D12_LOGIC_OP LogicOp;  // 비트 연산
    UINT8 RenderTargetWriteMask; // 출력할 채널 지정
}D3D12_RENDER_TARGET_BLEND_DESC;

typedef struct D3D12_BLEND_DESC  
{  
    BOOL AlphaToCoverageEnable;  // 알파 값을 MSAA 커버리지에 사용할 것인가
    BOOL IndependentBlendEnable; //  렌더 타겟마다 독립적인 블렌딩 설정
    D3D12_RENDER_TARGET_BLEND_DESC RenderTarget[ 8 ]; //렌더 타겟들의 블렌딩
}D3D12_BLEND_DESC;
```

빛에는 투명도(알파)라는 개념이 없다. 투명한 재질의 경우 빛의 투과를 직접 계산하는 대신 
단순히 비율로 섞어버리는 방법으로 알파 블렌딩이 사용된다.