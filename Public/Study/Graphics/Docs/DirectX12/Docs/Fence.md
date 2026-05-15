---
---

# 펜스 생성
```
typedef enum D3D12_FENCE_FLAGS  
{  
    D3D12_FENCE_FLAG_NONE   = 0,  
    D3D12_FENCE_FLAG_SHARED = 0x1,  // 프로세스간 펜스 공유
    D3D12_FENCE_FLAG_SHARED_CROSS_ADAPTER = 0x2, // GPU 어댑터간 공유
    D3D12_FENCE_FLAG_NON_MONITORED  = 0x4  
}D3D12_FENCE_FLAGS;

HRESULT CreateFence(
    UINT64 InitialValue,  
    D3D12_FENCE_FLAGS Flags,  
    REFIID riid,  
	void **ppFence);
```
GPU가 펜스 값을 기다리게 하는 대기 명령을 큐에 추가한다.
```
HRESULT ID3D12CommandQueue::Wait(   
	ID3D12Fence *pFence,  
    UINT64 Value);
```
CPU 동기화를 위해 이벤트 객체와 펜스 값을 연결한다.
```
HRESULT ID3D12Fence::SetEventOnCompletion(   
	UINT64 Value,  
    HANDLE hEvent);
//WaitForSingleObject 을 이용해 신호를 받을 때 까지 해당 스레드를 블록시킨다.
```
펜스 값을 설정하는 명령을 큐에 추가한다.
```
HRESULT ID3D12CommandQueue::Signal(
  ID3D12Fence *pFence,
  UINT64      Value);
```
다음의 함수로 현재 펜스의 지정 값을 읽을 수 있다.
```
UINT64 GetCompletedValue(   
	ID3D12Fence *pFence);
```





