---
---

```
typedef enum D3D12_FILL_MODE  
{  
    D3D12_FILL_MODE_WIREFRAME   = 2,  // 테두리만
    D3D12_FILL_MODE_SOLID   = 3  //  채움
}   D3D12_FILL_MODE;

typedef enum D3D12_CULL_MODE  
{  
    D3D12_CULL_MODE_NONE    = 1,  
    D3D12_CULL_MODE_FRONT   = 2,  
    D3D12_CULL_MODE_BACK    = 3  
}D3D12_CULL_MODE;

typedef enum D3D12_CONSERVATIVE_RASTERIZATION_MODE  
{  
    D3D12_CONSERVATIVE_RASTERIZATION_MODE_OFF   = 0,  
    D3D12_CONSERVATIVE_RASTERIZATION_MODE_ON    = 1  //조금이라도 침범시 무조건 해당 픽셀을 그립니다
}D3D12_CONSERVATIVE_RASTERIZATION_MODE;  
  
typedef struct D3D12_RASTERIZER_DESC  
    {  
    D3D12_FILL_MODE FillMode;  
    D3D12_CULL_MODE CullMode;  
    BOOL FrontCounterClockwise;  // 전면 결정
    INT DepthBias;  //깊이 값에 더할 상수 오프셋
    FLOAT DepthBiasClamp;  // 최종 DepthBias 제한 값
    FLOAT SlopeScaledDepthBias;
    BOOL DepthClipEnable;  // TRUE면 near/far 기준으로 제거, FALSE면 각각 0,1 깊이로 쓰기
    BOOL MultisampleEnable;  //선 안티에일리어싱 알고리즘 선택
    BOOL AntialiasedLineEnable;  // 선 앤티앨리어싱을 사용할지 여부
    UINT ForcedSampleCount;  // 픽셀 당 픽셀 셰이더를 여러 번 실행할지 결정하는 값
    D3D12_CONSERVATIVE_RASTERIZATION_MODE ConservativeRaster;  
}D3D12_RASTERIZER_DESC;
```


래스터라이저는 다음을 수행한다.

#### 클리핑 
NDC 경계선 그리고 버텍스 셰이더에서 clipplanes를 출력으로 제공한 경우 해당 평면 경계에 대해서
기존의 삼각형을 잘라낸 후  다시 삼각형으로 분할 한다.
#### 컬링
컬링 설정에 따라 면을 제거한다.

#### 뷰포트 변환
세 꼭지점의 NDC 좌표를 픽셀 좌표로 변환한다.
#### 스캔 변환
삼각형 내부의 픽셀에 대해 UV 및 Z값을 보간 계산한다.

픽셀마다 MSAA를 위한 커버리지 마스크를 생성한다.
[MSAA](/Public/Study/Graphics/Docs/DirectX12/MSAA)

밉맵 계산을 위해 UV 좌표의 변화량을 계산하기 위해 
[Sampler](/Public/Study/Graphics/Docs/DirectX12/Docs/Sampler/Sampler)
2X2 쿼드를 완전히 구성해야 한다. 즉 활성화 된 픽셀이 하나라도 있다면 
나머지 쿼드를 이루는 픽셀들이 삼각형 바깥의 픽셀일지라도 가상 픽셀로서 
UV 및 Z값을 보간 계산한다.

#### Early DepthStencil
각 픽셀에 대해 Depth/Stencil Test 를 수행한다.
Z Write 가 설정에 따라 수행 된다. 

#### Texture Sampler
래스터 라이저의 결과물에 대해 2x2의 쿼드 단위로 픽셀 셰이더가 병렬 실행되며  
Sample 명령어에 대해 텍스처 샘플러는 변화량을 계산하고 밉맵 레벨을 결정하고 
텍셀 읽기 및 보간을 통해 최종 색상 값을 계산한다.

가상 픽셀인 경우 변화량 계산에 도움을 준 뒤, 출력으로 이어지지 않고 버려진다.

---

삼각형의 한 점에서 카메라가 같은 거리에 있더라도 바라보는 각도에 따라
한 픽셀이 대표하는 삼각형의 면적이 달라지며 이는 대표 깊이 값 간의 비교가
부정확해지는 결과가 생긴다.

픽셀의 단위 변화량이 큰 경우, 즉 삼각형의 기울기가 큰 만큼
SlopeScaledDepthBias는 깊이 값을 좀 더 보정해 제공하여 보수적인 깊이를 제공한다.

