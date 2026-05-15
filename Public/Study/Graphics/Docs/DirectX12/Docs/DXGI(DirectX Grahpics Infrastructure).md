---
---

DXGI는  운영체제/디스플레이 시스템과 DirectX12 사이에서 
인터페이스 역할을 제공한다.

IDXGIFactory 를 생성 후 인덱스를 1씩 올려가면서 EnumAdapters 를 호출해 어댑터를 얻거나
EnumAdapterByGpuPreference 를 호출하여 바로 적절한 어댑터를 얻는다.
어댑터에서 GetDesc 를 호출해 DXGI_ADAPTER_DESC 를 얻어 GPU의 관련 정보를 볼 수 있다.

어댑터에서 인덱스를 1씩 올려가면서 EnumOutputs 를 호출해  출력 장치를 찾는다.
출력 장치에서 GetDesc 를 호출해  DXGI_OUTPUT_DESC 를 얻어 출력 장치 관련 정보를 볼 수 있다.
