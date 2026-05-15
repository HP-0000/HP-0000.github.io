---
---

픽셀의 한계로 모습이 계단식으로 보이는 현상을 막기 위해 Rasterization 단계에서 다음의 기법을 사용한다.
[Rasterizer](/Public/Study/Graphics/Docs/DirectX12/Docs/Rasterizer)

# 초과 표본화(SSAA)
해상도를 증가시켜 출력하면 픽셀 당 샘플이 더 생기는데 이를 평균 내어 결정한다.
정확한 결과를 낸다.

# 다중 표본화(MSAA)
해상도는 그대로 두지만 래스터 라이저가 
픽셀 내부에 여러 샘플 위치를 두어 삼각형이 각 샘플 위치를 지나는지 더 면밀하게 판단하여
픽셀 당 커버리지 마스크를 생성한다.
```
typedef struct DXGI_SAMPLE_DESC  
{  
    UINT Count;  //픽셀 당 추출한 표본 개수
    UINT Quality; // 표본의 위치 패턴
} DXGI_SAMPLE_DESC;
```

출력 병합기 OM 단계에서 픽셀 셰이더에서 계산한 
값을 각 커버리지 샘플에 복사하여 MSAA 텍스처를 생성한다.

예를 들어 한 픽셀에 빨간 삼각형과 초록 삼각형이 절반씩 차지하고 있는 경우
빨간 삼각형의 커버리지 마스크는 1100 
초록 삼각형의 커버리지 마스크는 0011
픽셀은 (Red, Red, Green, Green) 이 된다.

화면에 출력하기 위해 이를 평균 내어 단일 텍스처 리소스로 변환하는 명령은 다음과 같다.
```
void ID3D12GraphicsCommandList::ResolveSubresource(
	ID3D12Resource *pDstResource, // 기본 힙에 위치한 텍스처 리소스
	UINT            DstSubresource,
	ID3D12Resource *pSrcResource, 
	UINT            SrcSubresource,
	DXGI_FORMAT     Format); 
```