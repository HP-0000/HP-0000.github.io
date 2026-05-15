---
---

VRAM 역시 한정적인 자원이기 때문에 Ram 과의 메모리 스왑이 필요하다.
```
HRESULT MakeResident(UINT NumObjects, // 배열 크기
ID3D12Pageable** ppObjects); //리소스 배열

HRESULT Evict(UINT NumObjects, // 배열 크기
ID3D12Pageable** ppObjects); //리소스 배열
```



