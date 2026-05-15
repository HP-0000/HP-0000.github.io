---
---


샘플러는 셰이더가 텍스처를 읽는 방식을 설정한다. 
루트 시그니처 생성시 정적으로 바인딩 할 수도, 아래와 같이 디스크립터 힙에 생성 후 동적으로 바인딩 할 수 있다.  

```

// MIN·MAG·MIP 순으로 타입을 적되, 뒷 축과 타입이 같으면 타입 표기를 생략.
// Point 또는 Linear 타입이 존재

###### MINIMUM
가져온 텍셀들 중에서 가장 어두운 값만 사용한다.
###### MAXIMUM
가져온 텍셀들 중에서 가장 밝은 값만 사용한다.
###### COMPARISON
가져온 텍셀들 값에 비교 연산을 통해 0 또는 1 가중치 값을 적용하여 사용한다

typedef enum D3D12_FILTER  
{  
    D3D12_FILTER_MIN_MAG_MIP_POINT  = 0,  
    D3D12_FILTER_MIN_MAG_POINT_MIP_LINEAR   = 0x1,  
    D3D12_FILTER_MIN_POINT_MAG_LINEAR_MIP_POINT = 0x4,  
    D3D12_FILTER_MIN_POINT_MAG_MIP_LINEAR   = 0x5,  
    D3D12_FILTER_MIN_LINEAR_MAG_MIP_POINT   = 0x10,  
    D3D12_FILTER_MIN_LINEAR_MAG_POINT_MIP_LINEAR    = 0x11,  
    D3D12_FILTER_MIN_MAG_LINEAR_MIP_POINT   = 0x14,  
    D3D12_FILTER_MIN_MAG_MIP_LINEAR = 0x15,  
    D3D12_FILTER_MIN_MAG_ANISOTROPIC_MIP_POINT  = 0x54,  
    D3D12_FILTER_ANISOTROPIC    = 0x55,  
    D3D12_FILTER_COMPARISON_MIN_MAG_MIP_POINT   = 0x80,  
    D3D12_FILTER_COMPARISON_MIN_MAG_POINT_MIP_LINEAR    = 0x81,  
    D3D12_FILTER_COMPARISON_MIN_POINT_MAG_LINEAR_MIP_POINT  = 0x84,  
    D3D12_FILTER_COMPARISON_MIN_POINT_MAG_MIP_LINEAR    = 0x85,  
    D3D12_FILTER_COMPARISON_MIN_LINEAR_MAG_MIP_POINT    = 0x90,  
    D3D12_FILTER_COMPARISON_MIN_LINEAR_MAG_POINT_MIP_LINEAR = 0x91,  
    D3D12_FILTER_COMPARISON_MIN_MAG_LINEAR_MIP_POINT    = 0x94,  
    D3D12_FILTER_COMPARISON_MIN_MAG_MIP_LINEAR  = 0x95,  
    D3D12_FILTER_COMPARISON_MIN_MAG_ANISOTROPIC_MIP_POINT   = 0xd4,  
    D3D12_FILTER_COMPARISON_ANISOTROPIC = 0xd5,  
    D3D12_FILTER_MINIMUM_MIN_MAG_MIP_POINT  = 0x100,  
    D3D12_FILTER_MINIMUM_MIN_MAG_POINT_MIP_LINEAR   = 0x101,  
    D3D12_FILTER_MINIMUM_MIN_POINT_MAG_LINEAR_MIP_POINT = 0x104,  
    D3D12_FILTER_MINIMUM_MIN_POINT_MAG_MIP_LINEAR   = 0x105,  
    D3D12_FILTER_MINIMUM_MIN_LINEAR_MAG_MIP_POINT   = 0x110,  
    D3D12_FILTER_MINIMUM_MIN_LINEAR_MAG_POINT_MIP_LINEAR    = 0x111,  
    D3D12_FILTER_MINIMUM_MIN_MAG_LINEAR_MIP_POINT   = 0x114,  
    D3D12_FILTER_MINIMUM_MIN_MAG_MIP_LINEAR = 0x115,  
    D3D12_FILTER_MINIMUM_MIN_MAG_ANISOTROPIC_MIP_POINT  = 0x154,  
    D3D12_FILTER_MINIMUM_ANISOTROPIC    = 0x155,  
    D3D12_FILTER_MAXIMUM_MIN_MAG_MIP_POINT  = 0x180,  
    D3D12_FILTER_MAXIMUM_MIN_MAG_POINT_MIP_LINEAR   = 0x181,  
    D3D12_FILTER_MAXIMUM_MIN_POINT_MAG_LINEAR_MIP_POINT = 0x184,  
    D3D12_FILTER_MAXIMUM_MIN_POINT_MAG_MIP_LINEAR   = 0x185,  
    D3D12_FILTER_MAXIMUM_MIN_LINEAR_MAG_MIP_POINT   = 0x190,  
    D3D12_FILTER_MAXIMUM_MIN_LINEAR_MAG_POINT_MIP_LINEAR    = 0x191,  
    D3D12_FILTER_MAXIMUM_MIN_MAG_LINEAR_MIP_POINT   = 0x194,  
    D3D12_FILTER_MAXIMUM_MIN_MAG_MIP_LINEAR = 0x195,  
    D3D12_FILTER_MAXIMUM_MIN_MAG_ANISOTROPIC_MIP_POINT  = 0x1d4,  
    D3D12_FILTER_MAXIMUM_ANISOTROPIC    = 0x1d5  
}D3D12_FILTER;

typedef enum D3D12_TEXTURE_ADDRESS_MODE  
{  
    D3D12_TEXTURE_ADDRESS_MODE_WRAP = 1,  
    D3D12_TEXTURE_ADDRESS_MODE_MIRROR   = 2,  
    D3D12_TEXTURE_ADDRESS_MODE_CLAMP    = 3,  
    D3D12_TEXTURE_ADDRESS_MODE_BORDER   = 4,  
    D3D12_TEXTURE_ADDRESS_MODE_MIRROR_ONCE  = 5  
}   D3D12_TEXTURE_ADDRESS_MODE;
    
typedef struct D3D12_SAMPLER_DESC  
{  
    D3D12_FILTER Filter;  // 텍스처 MIN(축소) MAG(확대) MIP(밉맵) 에 대한 픽셀 결정 방법 
    D3D12_TEXTURE_ADDRESS_MODE AddressU;  // 텍스처 좌표 범위 벗어날 때 처리 방식
    D3D12_TEXTURE_ADDRESS_MODE AddressV;  // 텍스처 좌표 범위 벗어날 때 처리 방식
    D3D12_TEXTURE_ADDRESS_MODE AddressW;  // 텍스처 좌표 범위 벗어날 때 처리 방식
    FLOAT MipLODBias;  // 밉맵 레벨 선택에 더할 오프셋
    UINT MaxAnisotropy;  // ANISOTROPIC의 이방성 필터링 최대 수행 회수
    D3D12_COMPARISON_FUNC ComparisonFunc;  // 비교 샘플링에 사용할 함수
    FLOAT BorderColor[ 4 ];   // 텍스처 좌표 범위 벗어날 때 BORDER 로 지정된 경우 색
    FLOAT MinLOD;  //밉맵 선택 레벨의 최소값
    FLOAT MaxLOD;  //밉맵 선택 레벨의 최대값
}D3D12_SAMPLER_DESC;

void CreateSampler(   
	const D3D12_SAMPLER_DESC *pDesc,  
    D3D12_CPU_DESCRIPTOR_HANDLE DestDescriptor);
```

---
# D3D12_FILTER

필터는 픽셀 수가 텍셀 수 보다 많은 경우 축소(MIN) 적은 경우 확대(MAG)하여
UV 좌표에 대응하는 픽셀 값을 가져오는 방식을 지정한다.

###### POINT 
UV 좌표와 가까운 텍셀 1개를 가져온다.
###### LINEAR
UV 좌표와 가까운 텍셀 
- 1D 텍스처 : 2개를 가져옴.
- 2D 텍스처 : 4개를 가져옴.
- 3D 텍스처 : 8개를 가져옴.

기본 연산은 가져온 UV 좌표값으로 각 텍셀에 가중치를 곱해 평균을 낸다.

###### MIP
MIP_POINT 의 경우 계산된 밉맵 레벨에서 반올림하여, 가장 가까운 밉맵 이미지 1장 선택함
MIP_LINEAR 의 경우 계산된 밉맵 레벨의 위아래 이미지 2장을 모두 가져온 뒤 비율대로 부드럽게 섞는다.

**밉맵 레벨**
화면상의 픽셀 1개가 이동할 때, 텍스처(UV) 변화량을 계산한다. 
이때 텍스처의 실제 해상도(Width, Height)를 곱해서 픽셀 1개가 가로/세로 방향으로 
텍셀 몇 개를 덮는지를 구한다.

* 화면 X방향 픽셀 1개가 덮는 텍셀 수 ($L_x$): $\sqrt{(\frac{\partial u}{\partial x} \times Width)^2 + (\frac{\partial v}{\partial x} \times Height)^2}$
* 화면 Y방향 픽셀 1개가 덮는 텍셀 수 ($L_y$): $\sqrt{(\frac{\partial u}{\partial y} \times Width)^2 + (\frac{\partial v}{\partial y} \times Height)^2}$

$L_x$와 $L_y$ 중 **더 큰 값**을 선택한다.
$\rho = \max(L_x, L_y)$
Mipmap Level (LOD) = $\log_2(\rho)$ // 밉맵 단계 마다 해상도는 절반이 된다.

###### Anisotropic
![](/Public/Study/Graphics/Docs/DirectX12/Docs/Sampler/Resource/Pasted%20image%2020260619174212.png)

카메라가 표면을 비스듬하게 바라볼 때, 특히 바닥을 바라 보면 
한 픽셀이 텍스처에서 차지하는 영역이 정사각형이 아닌 사다리꼴이 된다.

이방성 필터링은 짧은 축 방향의 해상도가 선명하도록 
$L_x$와 $L_y$ 중 **더 작은 값**을 선택해 밉맵을 정한다.
$\rho = \min(L_x, L_y)$

이때 발생하는 밉맵 차이는 다음과 같다.
$\log_2(\max(L_x, L_y)) - \log_2(\min(L_x, L_y))$
따라서 긴 축 방향으로  다음의 텍셀 만큼을 더 샘플링 해야 한다.
$\min( \max(L_x, L_y) / \min(L_x, L_y) , MaxAnisotropy)$ 

픽셀의 UV 좌표를 기준으로 긴 축 방향을 따라 서브 UV 좌표를 보간 계산 한 뒤
각각에 대해 LINEAR(샘플링)을 거쳐 평균을 계산한다.

