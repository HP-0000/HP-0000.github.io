---
---
![](/Public/Study/Graphics/Docs/DirectX12/Docs/GraphicsPipeLine/Resource/Pasted%20image%2020260417141642.png)
#### Input Assembler 

메모리에서 Index 를 보고 Vertex 정보를 가져와 Vertex ID, Instance ID를 붙여서 
Vertex Shader로 넘긴다. 그리고 Primitive 조립 규칙을 Primitive Assembly 에 저장한다.
#### Vertex Shader
병렬로 정점에 대한 프로그래밍 코드를 실행한다. Vertex ID 가 같다면 결과도 같아, 연산 최적화가 진행된다.
연산이 끝나면 IA에서 지정된 주소로 결과를 저장한다.
#### Primitive Assembly
Primitive를 구성하는 정점을 모아 래스터 라이저로 넘겨준다.
#### HullShader
IA 가 패치 조립 규칙을 적용할 경우, 버텍스 쉐이더 이후 **Primitive Assembly** 를 통해 패치 덩어리를 입력 받는다.
LOD(Level of Detail) 프로그래밍 로직을 수행하여 선,삼각형,사각형 중 하나의 모양을 택하고 밀도를 고른 뒤 테셀레이터에 전달한다. 그리고 도메인 쉐이더에 패치를 넘긴다.
#### Tessellator
하드웨어 동작을 통해 헐 셰이더가 지정한 모양에 따라 
정삼각형을 쪼개 UVW 좌표를 만들거나 정 사각형을 쪼개 UV 좌표를 만든다. 
결과물을 도메인 셰이더에 넘긴다.
#### Domain Shader 
테셀레이터의 결과물 좌표 값과 패치 정점들을 받아 3D 좌표를 계산한다.
#### Geometry Shader 
모양에 따라 얻은 Primitive에 대해 프로그래밍 코드를 실행한 뒤 출력 스트림 종류 선언 후,
다시 일일이 정점 목록을 작성하고 Primitive Assembly 로 보낸다.
#### Stream Output (opt)
계산된 정점들을 VRAM에 저장한다. 

### 비 효율성
일단 단계가 나눠질 수록 자유도는 증가한 것처럼 보이나, 병렬 연산 되더라도 데이터가 묶여서 넘어가야 하기 때문에
병목이 생기고, 지오메트리 셰이더는 출력 크기를 모르기 때문에 메모리를 미리 할당하지 못해 순차적으로 처리를 해야한다.
#### Rasterizer
[Rasterizer](/Public/Study/Graphics/Docs/DirectX12/Docs/Rasterizer)
#### Pixel Shader 
병렬로 픽셀에 대한 프로그래밍 코드를 실행한다. 
만약 이곳에서 DIscard 명령을 수행한다면 GPU는 Early - Z 를 수행하지 않아 서 성능이 낮아진다.
#### Output Merger
- Stencil Test / Write : 특정 픽셀만 통과 하거나 배제한다.
- Depth Test / Write : 픽셀을 결정하고 최종 깊이 값을 쓴다
- Blending : 색상을 혼합하고 최종적으로 쓴다.



# New Graphics PipeLine
![](/Public/Study/Graphics/Docs/DirectX12/Docs/GraphicsPipeLine/Resource/Pasted%20image%2020260417165403.png)

GPU가 진화하면서 병목을 일으키던 단계들을 통합하여 병렬 컴퓨트 할 수 있도록 바꾸었다. 
[Work Graphs](/Public/Study/Graphics/Docs/DirectX12/Work%20Graphs)

