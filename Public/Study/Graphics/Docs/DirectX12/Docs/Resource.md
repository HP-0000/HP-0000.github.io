---
---

DIRECT12 부터 GPU 가상 주소와 실제 물리 메모리를 제어권이 개발자에게 넘어 왔다.

#### 리소스 정의하기
[MSAA](/Public/Study/Graphics/Docs/DirectX12/Docs/MSAA)
```
typedef enum D3D12_RESOURCE_DIMENSION  
{  
    D3D12_RESOURCE_DIMENSION_UNKNOWN  = 0,  
    D3D12_RESOURCE_DIMENSION_BUFFER = 1,  
    D3D12_RESOURCE_DIMENSION_TEXTURE1D  = 2,  
    D3D12_RESOURCE_DIMENSION_TEXTURE2D  = 3,  
    D3D12_RESOURCE_DIMENSION_TEXTURE3D  = 4  
}D3D12_RESOURCE_DIMENSION;

typedef enum D3D12_TEXTURE_LAYOUT  
{  
    D3D12_TEXTURE_LAYOUT_UNKNOWN    = 0,  // 자동 레이아웃
    D3D12_TEXTURE_LAYOUT_ROW_MAJOR  = 1,  // 행 우선 순서로 저장, CPU 접근용 
    D3D12_TEXTURE_LAYOUT_64KB_UNDEFINED_SWIZZLE = 2, // 타일로 분할하되, 내부의 텍셀 순서는 불투명
    D3D12_TEXTURE_LAYOUT_64KB_STANDARD_SWIZZLE  = 3  // 타일로 분할하되, 패턴이  텍셀 배열 표준화
}D3D12_TEXTURE_LAYOUT;

typedef enum D3D12_RESOURCE_FLAGS  
{  
    D3D12_RESOURCE_FLAG_NONE    = 0,  
    D3D12_RESOURCE_FLAG_ALLOW_RENDER_TARGET = 0x1,  
    D3D12_RESOURCE_FLAG_ALLOW_DEPTH_STENCIL = 0x2,  
    D3D12_RESOURCE_FLAG_ALLOW_UNORDERED_ACCESS  = 0x4,  
    D3D12_RESOURCE_FLAG_DENY_SHADER_RESOURCE    = 0x8,  
    D3D12_RESOURCE_FLAG_ALLOW_CROSS_ADAPTER = 0x10,  
    D3D12_RESOURCE_FLAG_ALLOW_SIMULTANEOUS_ACCESS   = 0x20,  
    D3D12_RESOURCE_FLAG_VIDEO_DECODE_REFERENCE_ONLY = 0x40,  
    D3D12_RESOURCE_FLAG_VIDEO_ENCODE_REFERENCE_ONLY = 0x80,  
    D3D12_RESOURCE_FLAG_RAYTRACING_ACCELERATION_STRUCTURE   = 0x100  
}D3D12_RESOURCE_FLAGS;
    
typedef struct D3D12_RESOURCE_DESC  
{  
    D3D12_RESOURCE_DIMENSION Dimension;  // 리소스 차원
    UINT64 Alignment;   // 리소스 메모리 정렬 요구 사항
    UINT64 Width;  // 버퍼의 경우 사이즈, 텍스처의 경우 너비
    UINT Height;  // 버퍼의 경우 1, 텍스처 높이
    UINT16 DepthOrArraySize; // 텍스처 리소스 해당
    UINT16 MipLevels;  // 밉맵 수준 개수
    DXGI_FORMAT Format;  // 픽셀 포맷
    DXGI_SAMPLE_DESC SampleDesc;  // 멀티 샘플링 개수와 품질 
    D3D12_TEXTURE_LAYOUT Layout;  // 텍스처 메모리 레이아웃
    D3D12_RESOURCE_FLAGS Flags;  // 리소스의 용도. 뷰 타입 제한 
}D3D12_RESOURCE_DESC;
```
서브 리소스 개수를 구하는 다음의 헬퍼 함수가 있다.
```
constexpr UINT D3D12CalcSubresource( UINT MipSlice, UINT ArraySlice, UINT PlaneSlice, UINT MipLevels, UINT ArraySize ) noexcept  
{  
    return MipSlice + ArraySlice * MipLevels + PlaneSlice * MipLevels * ArraySize;  
}
```

#### 리소스 크기 구하기
밉맵을 고려한 리소스 할당에 필요한 정보를 제공한다.
```
typedef struct D3D12_RESOURCE_ALLOCATION_INFO  
{  
    UINT64 SizeInBytes;  
    UINT64 Alignment;  
}D3D12_RESOURCE_ALLOCATION_INFO;

D3D12_RESOURCE_ALLOCATION_INFO GetResourceAllocationInfo(   
	UINT visibleMask,  
	UINT numResourceDescs,  
    const D3D12_RESOURCE_DESC *pResourceDescs);
```

#### 힙 할당하기
[GPU 구조](/Public/Study/컴퓨터%20구조/Docs/GPU/GPU%20구조)
[Virtual Address Physical Address](/Public/Study/컴퓨터%20구조/Docs/Virtual%20Address%20Physical%20Address)

```
enum D3D12_HEAP_TYPE  
{  
    D3D12_HEAP_TYPE_DEFAULT = 1,  
    D3D12_HEAP_TYPE_UPLOAD  = 2,  
    D3D12_HEAP_TYPE_READBACK    = 3,  
    D3D12_HEAP_TYPE_CUSTOM  = 4,  
    D3D12_HEAP_TYPE_GPU_UPLOAD  = 5  
}D3D12_HEAP_TYPE;
```
###### D3D12_HEAP_TYPE_DEFAULT
위치 : VRAM
동작 : GTT 를 통해 GPU의 VA가 PA로 변환됨
용도 : GPU 가 빈번히 읽고 쓰는 데이터
###### D3D12_HEAP_TYPE_UPLOAD
위치 : RAM
동작 : CPU가 데이터를 쓰고, GPU의 DMA를 통해 DEFAULT 힙으로 복사
용도 : CPU가 자주 갱신할 대규모 데이터
제한 : D3D12_RESOURCE_DIMENSION_BUFFER 만 허용
###### D3D12_HEAP_TYPE_READBACK
위치 : RAM
동작 : GPU가 DMA로 데이터를 쓰고, CPU가 읽음
용도 : GPU 연산 결과를 CPU가 읽어야 할 때
제한 : D3D12_RESOURCE_DIMENSION_BUFFER 만 허용
###### D3D12_HEAP_TYPE_GPU_UPLOAD
위치 : VRAM
동작 : Resizable Bar를 통해 CPU가 직접 쓰기
용도 : CPU가 자주 갱신하고 GPU가 바로 읽어야 하는 소규모 데이터

```
typedef struct D3D12_HEAP_PROPERTIES  
{  
    D3D12_HEAP_TYPE Type;  // 힙의 용도와 접근 패턴 지정
    D3D12_CPU_PAGE_PROPERTY CPUPageProperty; // D3D12_HEAP_TYPE_CUSTOM 의 경우 캐시 정책 지정
    D3D12_MEMORY_POOL MemoryPoolPreference;  // D3D12_HEAP_TYPE_CUSTOM 의 경우 메모리 풀 지정
    UINT CreationNodeMask;  // 힙을 소유하는 GPU 어뎁터 지정
    UINT VisibleNodeMask;  // 힙에 접근 가능한 GPU 어뎁터 지정
}D3D12_HEAP_PROPERTIES;
    
typedef struct D3D12_HEAP_DESC  
{  
    UINT64 SizeInBytes;  // 힙의 크기 (페이지 크기인 64KB 배수)
    D3D12_HEAP_PROPERTIES Properties;  
    UINT64 Alignment;   // 정렬 (64KB 배수)
    D3D12_HEAP_FLAGS Flags; // 힙의 제한사항 명시 - 용도에 따른 최적화 
}D3D12_HEAP_DESC;

HRESULT CreateHeap(
	D3D12_HEAP_DESC *pDesc,  
    REFIID riid,  
    void **ppvHeap); // 힙 인터페이스
```
#### 힙에 리소스 생성하기
힙에 리소스를 정의 및 초기 상태를 설정해 배치한다.
```
typedef struct D3D12_DEPTH_STENCIL_VALUE  
{  
    FLOAT Depth;  
    UINT8 Stencil;  
}D3D12_DEPTH_STENCIL_VALUE;  
  
typedef struct D3D12_CLEAR_VALUE  
{  
    DXGI_FORMAT Format;  
    union   
	{  
	    FLOAT Color[ 4 ];  
	    D3D12_DEPTH_STENCIL_VALUE DepthStencil;  
    }   ;    
}D3D12_CLEAR_VALUE;

HRESULT CreatePlacedResource(   
	ID3D12Heap *pHeap,  // 힙 인터페이스 주소
    UINT64 HeapOffset,  // 리소스를 배치할 위치
    const D3D12_RESOURCE_DESC *pDesc, //리소스 정의
    D3D12_RESOURCE_STATES InitialState, // 리소스의 상태 정의
    const D3D12_CLEAR_VALUE *pOptimizedClearValue,  // 리소스 초기화 값
    REFIID riid,  
    void **ppvResource); // 리소스 인터페이스
```
힙의 크기 정렬과 오프셋 입력을 알아서 계산 하는 다음의 편의 함수가 존재한다.
```
HRESULT CreateCommittedResource(   
	const D3D12_HEAP_PROPERTIES *pHeapProperties,  
    D3D12_HEAP_FLAGS HeapFlags,  
    const D3D12_RESOURCE_DESC *pDesc,  
    D3D12_RESOURCE_STATES InitialResourceState,  
    const D3D12_CLEAR_VALUE *pOptimizedClearValue,  
    REFIID riidResource,  
    void **ppvResource);
```
---
# 리소스 복사

#### 버퍼 리소스 간 복사
```
void ID3D12GraphicsCommandList::CopyBufferRegion(   
	ID3D12Resource *pDstBuffer,  
    UINT64 DstOffset,  
    ID3D12Resource *pSrcBuffer,  
    UINT64 SrcOffset,  
    UINT64 NumBytes) = 0;
```

#### 버퍼 리소스와 텍스처 리소스간 복사
```
typedef struct D3D12_SUBRESOURCE_FOOTPRINT  
{  
    DXGI_FORMAT Format;  // 픽셀 포맷
    UINT Width;  // 텍스처 너비
    UINT Height;  // 텍스처 높이
    UINT Depth;   // 
    UINT RowPitch;  //GPU 정렬 값(256)을 반영해 패딩을 포함한 행의 실제 바이트수
}D3D12_SUBRESOURCE_FOOTPRINT;  

typedef struct D3D12_PLACED_SUBRESOURCE_FOOTPRINT  
{  
    UINT64 Offset;  // 버퍼의 오프셋
    D3D12_SUBRESOURCE_FOOTPRINT Footprint;  
}D3D12_PLACED_SUBRESOURCE_FOOTPRINT;  

typedef enum D3D12_TEXTURE_COPY_TYPE  
{  
    D3D12_TEXTURE_COPY_TYPE_SUBRESOURCE_INDEX   = 0,  // 텍스처
    D3D12_TEXTURE_COPY_TYPE_PLACED_FOOTPRINT    = 1   // 버퍼 
}D3D12_TEXTURE_COPY_TYPE;

typedef struct D3D12_TEXTURE_COPY_LOCATION  
{  
    ID3D12Resource *pResource;  // 대상 리소스
    D3D12_TEXTURE_COPY_TYPE Type;  // 텍스처인가 버퍼인가
    union   
	{  
        D3D12_PLACED_SUBRESOURCE_FOOTPRINT PlacedFootprint; //버퍼에 배치된 텍스처의 메모리 레이아웃
        UINT SubresourceIndex; // 텍스처의 하위 리소스 인덱스
    };    
}D3D12_TEXTURE_COPY_LOCATION;

typedef struct D3D12_BOX  
{  
    UINT left;  
    UINT top;  
    UINT front;  
    UINT right;  
    UINT bottom;  
    UINT back;  
}D3D12_BOX; // 복사될 원본 텍스처의 범위. 버퍼일 경우 nullptr 

void ID3D12GraphicsCommandList::CopyTextureRegion(   
	D3D12_TEXTURE_COPY_LOCATION *pDst,  
    UINT DstX,  //목적지 픽셀 좌표 , 버퍼의 경우 0
    UINT DstY,  //목적지 픽셀 좌표 , 버퍼의 경우 0
    UINT DstZ,  //목적지 픽셀 좌표 , 버퍼의 경우 0
    D3D12_TEXTURE_COPY_LOCATION *pSrc,  
    const D3D12_BOX *pSrcBox); // nullptr 이면 해당 하는 서브리소스 전체 픽셀 복사
```
D3D12_RESOURCE_DESC 을 통해 D3D12_PLACED_SUBRESOURCE_FOOTPRINT를 만들어주는
다음의 편의 함수가 존재한다.
```
void  ID3D12Device::GetCopyableFootprints(   
	const D3D12_RESOURCE_DESC *pResourceDesc, // 리소스 설명자
    UINT FirstSubresource,  // 시작 서브리소스 인덱스 
    UINT NumSubresources,  // 처리할 서브 리소스 개수
    UINT64 BaseOffset,  // 버퍼 내 오프셋
    _Out_writes_opt_ D3D12_PLACED_SUBRESOURCE_FOOTPRINT *pLayouts, 
    _Out_writes_opt_ UINT *pNumRows,  // 서브리소스의 행 개수, 편의적으로 제공
    _Out_writes_opt_ UINT64 *pRowSizeInBytes,   // 편의적으로 제공 
    _Out_opt_ UINT64 *pTotalBytes); // 편의적으로 제공
```

#### 출발지와 목적지가 완전히 동일하다면
아래의 함수를 사용한다.
```
void ID3D12GraphicsCommandList::CopyResource(
    ID3D12Resource *pDstResource,  
    ID3D12Resource *pSrcResource);
```

#### CPU 가상 주소 접근하기
Upload 힙에 Cpu가 데이터를 쓸 때, ReadBack 힙에서 Cpu가 데이터를 읽어올 때 사용한다.
```
typedef struct D3D12_RANGE {
  SIZE_T Begin;  // 시작 오프셋
  SIZE_T End;    // 끝 오프셋 
}D3D12_RANGE;

HRESULT ID3D12Resource::Map(
  UINT                   Subresource, // 서브리소스 인덱스
  const D3D12_RANGE*     pReadRange, // 해당 범위에 대해 CPU 캐시 무효화 발생(업로드 힙인 경우 무시 됨)
  void**                 ppData); 가상 주소 

void ID3D12Resource::Unmap(
  UINT                   Subresource,
  const D3D12_RANGE*     pWrittenRange//해당 범위에 대해 플러시 최적화(ReadBack 힙인 경우 무시)
);
```

# 리소스 타일링
리소스의 서브 리소스 인덱스는 고정되며 각 서브 리소스는 여러 개의 타일로 구성된다.
거대한 리소스에 가상 주소를 미리 예약 해 둔 뒤 , 사용하는 일부의 타일에 대해 실제 물리 주소를 할당하여
사용한다.
#### 타일 정보 얻기
```
typedef struct D3D12_PACKED_MIP_INFO  
{  
    UINT8 NumStandardMips;  // 밉맵 개수
    UINT8 NumPackedMips;  // 오름 차순으로, 묶을 밉맵 개수
    UINT NumTilesForPackedMips;  // 묶은 밉맵에 사용할 타일 개수
    UINT StartTileIndexInOverallResource; //  패킹된 밉맵의 타일 시작 위치
}D3D12_PACKED_MIP_INFO;

typedef struct D3D12_TILE_SHAPE  
{  
    UINT WidthInTexels;  
    UINT HeightInTexels;  
    UINT DepthInTexels;  
}D3D12_TILE_SHAPE;

typedef struct D3D12_SUBRESOURCE_TILING  
{     
    UINT WidthInTiles;  // 타일 개수
    UINT16 HeightInTiles; // 타일 개수  
    UINT16 DepthInTiles;  // 타일 개수
    UINT StartTileIndexInOverallResource;  // 타일 시작 위치
}D3D12_SUBRESOURCE_TILING;
   
void GetResourceTiling(
  [in] ID3D12Resource  *pTiledResource,
  [out, optional] UINT  *pNumTilesForEntireResource, // 필요한 타일 개수
  [out, optional] D3D12_PACKED_MIP_INFO  *pPackedMipDesc, // 패킹된 밉맵의 타일
  [out, optional] D3D12_TILE_SHAPE *pStandardTileShapeForNonPackedMips, // 일반 밉맵의 타일 모양
  [in, out, optional] UINT *pNumSubresourceTilings, // 정보를 받을 서브 리소스 개수 
  [in]  UINT FirstSubresourceTilingToGet, // 시작 서브 리소스 인덱스
  [out] D3D12_SUBRESOURCE_TILING  *pSubresourceTilingsForNonPackedMips);//일반 밉맵의 서브리소스 타일 수
```
#### GPU 가상 주소 예약하기
```
HRESULT CreateReservedResource(   
	const D3D12_RESOURCE_DESC *pDesc,  
    D3D12_RESOURCE_STATES InitialState,  
    const D3D12_CLEAR_VALUE *pOptimizedClearValue,  
    REFIID riid,  
    void **ppvResource);
```
#### 가상 주소와 물리 주소 연결
```
typedef struct D3D12_TILED_RESOURCE_COORDINATE  
{  
    UINT X;  
    UINT Y;  
    UINT Z;  
    UINT Subresource;  
}D3D12_TILED_RESOURCE_COORDINATE;

typedef struct D3D12_TILE_REGION_SIZE  
{  
    UINT NumTiles;  // 타일이 선형 일때 타일 개수
    BOOL UseBox;  // 타일이 선형 인가
    UINT Width;  // 타일 가로 개수
    UINT16 Height;  // 타일 세로 개수
    UINT16 Depth;  // 타일 깊이 개수
}D3D12_TILE_REGION_SIZE;

typedef enum D3D12_TILE_RANGE_FLAGS  
{  
    D3D12_TILE_RANGE_FLAG_NONE  = 0,  // 매핑
    D3D12_TILE_RANGE_FLAG_NULL  = 1,  // 연결 하지 않음
    D3D12_TILE_RANGE_FLAG_SKIP  = 2,  // 현재 매핑 상태 유지
    D3D12_TILE_RANGE_FLAG_REUSE_SINGLE_TILE = 4  // 여러 타일을 같은 물리타일 매핑
}D3D12_TILE_RANGE_FLAGS;

void ID3D12CommandQueue::UpdateTileMappings(
	ID3D12Resource *pResource,
	UINT NumResourceRegions, // 매핑을 변경할 
	const D3D12_TILED_RESOURCE_COORDINATE *pResourceRegionStartCoordinates, // 타일 시작 좌표
	const D3D12_TILE_REGION_SIZE *pResourceRegionSizes, // 타일 영역 
	ID3D12Heap *pHeap, // nullptr 시 NULL 매핑 
	UINT NumRanges, // 범위 개수
	const D3D12_TILE_RANGE_FLAGS *pRangeFlags, // 각 범위의 매핑 플래그 배열
	const UINT *pHeapRangeStartOffsets, // 각 범위의 물리 힙 내 시작 타일 인덱스 배열
	const UINT *pRangeTileCounts, // 각 범위의 타일 개수 배열
	D3D12_TILE_MAPPING_FLAGS Flags);
```

#### 타일 리소스와 버퍼 리소스간 복사
```
typedef enum D3D12_TILE_COPY_FLAGS  
{  
    D3D12_TILE_COPY_FLAG_NONE  = 0,  
    D3D12_TILE_COPY_FLAG_NO_HAZARD  = 0x1,  // 
    D3D12_TILE_COPY_FLAG_LINEAR_BUFFER_TO_SWIZZLED_TILED_RESOURCE  = 0x2,  // 선형 버퍼에서 업로드
    D3D12_TILE_COPY_FLAG_SWIZZLED_TILED_RESOURCE_TO_LINEAR_BUFFER  = 0x4  // 선형 버퍼로 저장
}   D3D12_TILE_COPY_FLAGS;

void ID3D12GraphicsCommandList::CopyTiles(   
	_In_  ID3D12Resource *pTiledResource,  // 대상 타일 리소스
    _In_  const D3D12_TILED_RESOURCE_COORDINATE *pTileRegionStartCoordinate, // 타일 영역 시작 좌표
    _In_  const D3D12_TILE_REGION_SIZE *pTileRegionSize, // 복사할 타일 영역 
    _In_  ID3D12Resource *pBuffer,    // 일반 버퍼 리소스
    UINT64 BufferStartOffsetInBytes, // 버퍼 내 시작 오프셋
    D3D12_TILE_COPY_FLAGS Flags); // 복사 방향
```
[Residency](/Public/Study/Graphics/Docs/DirectX12/Docs/Residency)
[ResourceBarrier](/Public/Study/Graphics/Docs/DirectX12/Docs/ResourceBarrier)
[Resource Binding](/Public/Study/Graphics/Docs/DirectX12/Docs/Resource%20Binding)
