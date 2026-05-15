---
---

### Scanline
CPU가 폰트의 벡터곡선 정보를 이용하여 필요한 해상도의 비트맵을 생성한다. 각 픽셀의 중심이 글리프 내부에 포함되는지를 판정하여 픽셀 값을 기록한 뒤 GPU에 업로드하여 렌더링한다.

비트맵 자체가 특정 해상도에 맞게 생성되므로 확대하면 계단 현상(Aliasing)이 발생하며, 다른 크기로 출력하려면 일반적으로 새로운 해상도의 비트맵을 다시 생성해야 한다.

### SDF (Signed Distance Field)

CPU가 일정 해상도의 비트맵을 생성하면서 각 픽셀에 글리프 경계까지의 부호 있는 거리를 저장한다.

생성 시 Pixel Range를 지정하며, 글리프 경계에서는 약 0.5의 값을 갖도록 정규화한다. 경계에서 바깥쪽으로 Pixel Range만큼 떨어질수록 값은 0에 가까워지고, 안쪽으로 Pixel Range만큼 떨어질수록 1에 가까워진다.

GPU에서는 이 SDF 텍스처를 원하는 크기로 스케일링하면서 일반적인 텍스처 보간(Linear Filtering)을 적용한다. 거리 값은 선형적으로 보간되므로 단순 비트맵보다 경계가 자연스럽게 유지되며, 픽셀 셰이더에서 0.5를 경계값으로 사용하여 글리프 내부와 외부를 판정하면 확대·축소 시에도 비교적 선명한 결과를 얻을 수 있다.

### MSDF (Multi-channel Signed Distance Field)
일반 SDF 방식은 모서리 주변에서 서로 다른 두 Edge 에 대해 MinDistance 로 표현 되기 때문에
뭉툭하게 표현 되는 한계가 있다.

![](/Public/Study/Graphics/Docs/Font/Docs/MSDF/Resource/Pasted%20image%2020260702183035.png)


이를 해결하기 위해 Sharp Corner를 기준으로 글리프의 윤곽선을 여러 Edge로 분할한 뒤, 인접한 Edge들이 같은 채널에 들어가지 않도록 RGB의 최대 3개 채널에 각각 독립적인 SDF를 저장한다.

GPU의 픽셀 셰이더에서는 세 채널의 값을 이용하여 Median(R, G, B)연산을 수행해 실제 거리장을 복원한다. 이렇게 하면 서로 다른 Edge의 거리 정보가 섞이지 않아 날카로운 모서리를 정확하게 유지할 수 있으며, 확대 시에도 일반 SDF보다 훨씬 선명한 코너를 표현할 수 있다.

#### 구현 
https://github.com/Chlumsky/msdfgen
https://dcgi.fel.cvut.cz/wp-content/wpallimport-dist/publications/pdf/publications-2018-sloup-cgf-msdf-paper.pdf

모서리가 되는 Vertex에 인접한 두 Edge의 접선이 이루는 각도를 비교한다.
Sharp Corner를 기준으로 인접한 Edge들을 다른 채널에 배정한다.

// 논문 읽고 정리 해야 함 
###### Edge Coloring
어떻게 인접하지 않게 배정할 것인가.

###### Pseudo-distance
픽셀에서 가장 가까운 Edge를 찾고 그 Edge 방향 직선과의 거리와 부호를 기록한다.
이후 Median 연산은 모서리의 SDF 기울기 값의 불연속을 보장한다.

