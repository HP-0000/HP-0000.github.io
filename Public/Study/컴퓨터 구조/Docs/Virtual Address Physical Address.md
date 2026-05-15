---
---

PTE(Page Table Entry)
    - Present Bit :  페이지가 실제 물리 RAM에 올라와 있는지 여부 
    - PFN (Physical Frame Number)  : 물리 RAM의 페이지 프레임 번호

#### Physical Address
RAM에 적재해둔 PTE 장부를 통해 MMU(Memory Management Unit)가 
실제 물리 주소로 변환한다.
12 비트를 통해 4KB 페이지 단위로 물리 메모리가 관리되며 
PFN 제공 비트 수에 따라 물리 주소 범위가 결정된다. 
#### Virtual Address
CPU는 64비트 범용 레지스터를 사용하여 가상 메모리 주소로 
장부에서 PTE 위치를 찾아 메모리 읽기 쓰기 명령을 한다. 

실제 하드웨어는 48비트만 가상 주소로 사용한다.
* 최상위 16비트 : 사용 안 함
* 중간 36비트 (9 + 9 + 9 + 9) 
  4단계 계층 장부를 거친 최종 PTE의 위치 인덱스
* 하위 12비트 
  4KB 페이지 내부에서 접근할 바이트 오프셋 

--- 

CPU와 Ram은 메모리 버스로 연결되어있다.
CPU는 PCle 버스를 통해 여러 하드웨어 자원과 연결된다.
#### Base Address Register
PCle 버스와 연결된 각 장치 내부에 있는 레지스터.
자신이 필요한 크기를 요청하여 그 결과, CPU의 물리 주소를 저장하고
이에 대응되는 GPU의 PA 기준점을 GPU 내부의 레지스터에 저장한다.

CPU의 PFN이 BAR 영역 내 물리 주소를 가리키도록 PTE를 설정하면,  
CPU가 해당 주소에 접근할 때 GPU가 내부 기준점을 이용해 적절한 VRAM PA로 변환하므로 MMIO가 성공적으로 수행된다

#### Resizable Bar
CPU의 물리 주소 공간이 넓어진 현재는, Bar의 요청 크기를 VRAM 전체 크기로 잡는다.
원한다면 유동적으로 크기를 변경할 수 있다.

#### MMIO(Memory-Mapped I/O)
CPU가 IO 장치의 레지스터나 메모리를 메모리 주소 공간에 매핑 하여 일반적인 명령어로 
접근하는 기법이다. 메모리 컨트롤러는 물리 주소 공간의 일정 범위를 각 장치에 할당한다.
CPU의 MMU가 변환한 물리 주소는 메모리 컨트롤러가 주소 범위를 해석하여 메모리 버스로 가야 하는지 PCle 버스로 가야 하는지 판단해 명령을 전달한다. 장치는 Bar 기준점과 오프셋을 이용해 실제 자신의 PA를 계산한다.

#### DMA(Direct Memory Access)
장치 내부의 DMA 컨트롤러는 데이터를 CPU 레지스터를 거치지 않고 메모리 버스 혹은 PCle 버스를 통해 이동 시키는 방식

#### SSD 
SSD 내에서 물리 주소 PBA는 논리 주소 LBA 블록으로 표현된다.
RAM에는 LBA의 사용 상태를 나타내는 스왑 테이블이 적재 되어 있다.

MMU가 PTE를 읽었을 때 Present Bit 가 0 인 경우, 예외가 발생한다.
OS는 이때  나머지 비트 값을 SSD의 LBA로 판단하고 SSD 드라이버에 데이터를 요청한다. 명령에 직접 RAM의 물리 주소를 적어주어 DMA 방식으로, SSD 컨트롤러가 CPU 코어를 거치지 않고 메모리 컨트롤러로 데이터를 보내 RAM에 데이터를 쓸 수 있게 한다.

#### Graphics Translation Table
GPU 드라이버가 RAM에 올려둔 장부(GTT)는 GPU의 가상 주소(VA)와 VRAM의 물리 주소(PA)를 매핑 한다. GPU의 MMU는 RAM에서 이 장부를 읽어와서 주소 변환을 한다.

#### GPUDirect Storage
SSD 에서 Ram 으로 그리고 VRAM으로 복사 하는 것은 오버헤드가 있다.
따라서 GPU의 Bar 의 물리 주소를 SSD 컨트롤러가 알고 있으면 DMA를 보내 
CPU 코어를 지나지 않고 SSD에서 VRAM 으로 데이터 이동을 할 수 있다.

#### IOMMU
CPU 내부에서 IO 장치들이 RAM을 향해 접근할 때 그들의 가상 주소를 
RAM 의 물리 주소로 변환 하는 장치. 관련 매핑 정보를 담은 변환 테이블은 
Ram에 저장된다.

[통합 가상 메모리](/Public/Study/컴퓨터%20구조/Docs/통합%20가상%20메모리)