---
---

두 도형의 충돌을 판별하는 알고리즘

A. 도형이 분리 되어 있다면 최소 이동 거리로 맞닿는 MTV(Minimum Translation Vector) 가 유일하다.
B. 맞닿아 있는 도형이 최소 이동 거리로 분리 되는 MTV는 다수 존재한다.
MTV의 방향벡터 만을 고려하면 B는 A의 반대 방향 벡터를 반드시 포함한다.

분리 축 검사는 도형을 분리축에 투영시 $Overlap < 0$ 를 보여 충돌하지 않았음을 보이는 것이다.
MTV는 분리 축이지만 A를 모르기에 검사를 진행하기 어렵다.

도형이 임의의 B.MTV에 의해 분리되어 있을 경우 그 B.MTV에 대해 분리 축 검사를 수행하여 충돌을 판별 할 수 있다.

이때, 분리 축 B.MTV 은 90 미만의 각도를 갖는 B.MTV 에 의한 분리 도형의 분리 축 검사를 대리할 수 있다.
왜냐하면 임의의 B.MTV 분리 축에 대해 Pojection 형태는 $<Mesh - MTV - Mesh>$ 임으로 B.MTV 직선이 한 점으로 투영되는 순간, 즉 $Overlap = 0$  전까지 충분히 분리 여부를 보일 수 있기 때문이다.

90 미만의 각을 기준으로 B.MTV 집합 전체를 대리 할 수 있는, B.MTV 리스트를 찾는다.
어떤 것이 A.MTV를 대리할 수 있는지 모르기에 각 리스트 모두에 대해 분리 축 검사를 진행한다.
# Convex 

볼록한 도형은 6가지 유형의 충돌 Feature 가 존재한다.
A.Face - B.Face
A.Face - B.Edge
A.Face - B.Vertex
A.Edge - B.Edge
A.Edge - B.Vertex
A.Vertex - B.Vertex

각 Feature 별로 B.MTV 부분 집합과 그것을 대리할 수 있는 분리 축을 찾을 것이다.
# 2D Convex

> 임의의 MTV가 대리 할 수 있는 각도는 90도 미만이기에 MTV 집합이 180도를 이루는 상황에 
> 유의해서 대표 MTV를 뽑아야 한다

#### A.Face - ( B.Face, B.Edge, B.Vertex)
Face Normal 은 유일한 MTV 이다.
#### A.Edge - B.Edge 

##### 내부의 단 한 점에서 만나는 경우
두 Edge의 방향이 다르다.(꼬인 위치)
두 Edge의 외적은 유일한 MTV 이다.
##### 두 점 이상 만나는 경우
두 Edge의 방향이 같다. 

A 도형은 A.Face 와 90 이상의 각도로 밀어내야 한다.
A 도형은 A.Edge 와 90의 각도로 밀어내야 한다.
B 도형은 B.Face 와 90 이하의 각도로 다가와야 한다. 
B 도형은 B.Edge 와 90 의 각도로 다가와야 한다.

이때 Face Normal이 180도를 이룰 수 있음으로, 90도를 나타내기 위해 
A.Face Normal x A.Edge 가 추가로 필요하다.

대표 MTV 
A.Face Normal 
B.Face Normal
A.Face Normal x A.Edge
#### A.Edge - B.Vertex

1.A 도형은 A.Face 와 90 이상의 각도로 밀어내야 한다.
2.A 도형은 A.Edge 와 90 의 각도로 밀어내야 한다.
3.B 도형은 B.Face 와 90 이하의 각도로 다가와야 한다. 
4.B 도형은 Vertex 주변 두 B.Edge 와 90 이하의 각도로 다가와야 한다. 

MTV 집합은 A의 제약에 따라 평면 P 위에 존재한다.
또한 B의 제약에 따라 B.Vertex를 지나며 B.Edge와 수직인 평면 두 개가 이루는 안쪽 영역에 존재한다. 

B의 제약 영역과 A의 제약 영역이 만드는 MTV 범위 경계선은
A.Edge X B.Edge, A.Face Normal, B.FaceNormal 조합으로 이루어 진다.

이때 B의 제약 영역이 P를 완전히 포함할 경우, MTV 범위는 A.Face Normal에 의한 180임으로 
A.Face Normal x A.Edge 가 추가로 필요하다.

대표 MTV 
A.Face Normal 
B.FaceNormal
A.Edge X B.Edge (Vertex 주변 Edge)
A.Face Normal x A.Edge 
#### A.Vertex - B.Vertex

1.A 도형은 A.Face 와 90 이상의 각도로 밀어내야 한다.
2.A 도형은 Vertex 주변 두 A.Edge 와 90 이상의 각도로 밀어내야 한다.
3.B 도형은 B.Face 와 90 이하의 각도로 다가와야 한다. 
4.B 도형은 Vertex 주변 두 B.Edge 와 90 이하의 각도로 다가와야 한다. 

MTV 집합은 A의 제약에 따라 A.Vertex를 지나며 A.Edge와 수직인 평면 두 개가 이루는 안쪽 영역에 존재한다. 
또한 B의 제약에 따라 B.Vertex를 지나며 B.Edge와 수직인 평면 두 개가 이루는 안쪽 영역에 존재한다. 

B의 제약 영역과 A의 제약 영역이 만드는 MTV 범위 경계선은
A.Edge X B.Edge, A.Face Normal , B.FaceNormal 조합으로 이루어 진다.

이때 A.Face Normal과 B.Face Normal 이 같은 경우, MTV 범위는 180도 임으로
Face Normal X Edge 가 추가로 필요하다.

대표 MTV 
A.Face Normal 
B.Face Normal
A.Edge(Vertex 주변 Edge) X B.Edge(Vertex 주변 Edge)
A.Face Normal x A.Edge
B.Face Normal x B.Edge
# 3D Convex
#### A.Face - ( B.Face, B.Edge, B.Vertex)
Face Normal 은 유일한 MTV 이다.
#### A.Edge - B.Edge 

##### 내부의 단 한 점에서 만나는 경우
두 Edge의 방향이 다르다.(꼬인 위치)
두 Edge의 외적은 유일한 MTV 이다.
##### 두 점 이상 만나는 경우
1.A 도형은 Edge 주변 A.Face 와 90 이상의 각도로 밀어내야 한다.
2.A 도형은 A.Edge 와 90 의 각도로 밀어내야 한다.
3.B 도형은 Edge 주변 B.Face 와 90 이하의 각도로 다가와야 한다. 
4.B 도형은 B.Edge 와 90 의 각도로 다가와야 한다. 

대표 MTV 
A.Face Normal 
B.Face Normal

#### A.Edge - B.Vertex 

1.A 도형은 A.Face 와 90 이상의 각도로 밀어내야 한다.
2.A 도형은 Vertex 주변 두 A.Edge 와 90 이상의 각도로 밀어내야 한다.
3.B 도형은 Vertex 주변 B.Face 와 90 이하의 각도로 다가와야 한다. 
4.B 도형은 Vertex 주변 B.Edge 와 90 이하의 각도로 다가와야 한다. 

MTV 집합은 A의 제약에 따라 평면 P 위에 존재한다.
또한 B의 제약에 따라 B.Vertex를 지나며 B.Edge 와 수직인 평면들이 만드는 영역 안에 존재한다.

B의 제약 영역과 A의 제약 영역이 만드는 MTV 범위 경계선은
A.Edge X B.Edge, A.Face Normal , B.FaceNormal 조합으로 이루어 진다.

대표 MTV 
A.Face Normal 
B.Face Normal
A.Edge X B.Edge (Vertex 주변 Edge)

#### A.Vertex - B.Vertex

1.A 도형은 Vertex 주변 A.Face 와 90 이상의 각도로 밀어내야 한다.
2.A 도형은 Vertex 주변 A.Edge 와 90 이상의 각도로 밀어내야 한다.
3.B 도형은 Vertex 주변 B.Face 와 90 이하의 각도로 다가와야 한다. 
4.B 도형은 Vertex 주변 B.Edge 와 90 이하의 각도로 다가와야 한다. 


즉 MTV 집합은 같은 Vertex를 지나는 평면들 내부가 만드는 영역이다.

B의 제약 영역과 A의 제약 영역이 만드는 MTV 범위 경계선은
A.Edge X B.Edge, A.Face Normal , B.FaceNormal 조합으로 이루어 진다.

대표 MTV 
A.Face Normal 
B.Face Normal
A.Edge (Vertex 주변 Edge) X B.Edge (Vertex 주변 Edge)
