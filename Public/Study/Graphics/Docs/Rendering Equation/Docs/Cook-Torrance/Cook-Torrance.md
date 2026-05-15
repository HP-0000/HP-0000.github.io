---
---


![](/Public/Study/Graphics/Docs/Rendering%20Equation/Docs/Cook-Torrance/Resource/2026년%207월%201일%20오후%2012_43_07-Photoroom.png)


**미세면 모델**
표면은 기울기를 갖는 무수한 미세 거울 집합으로 구성되며 
표면의 수직 방향으로는 단 한개의 미세 거울만이 존재함을 가정한다.
모든 임의 높이 구간에 대해, (미세 거울의 기울기 $\times$ 표면 투영 면적)의 총 합은 0 임을 가정한다.
모든 임의 높이 구간에 대해, 미세 거울의 기울기 분포 비율은 표면 전체의 기울기 분포와 동일함을 가정한다.


[Normal Distribution Function](/Public/Study/Graphics/Docs/Rendering%20Equation/Docs/Cook-Torrance/Normal%20Distribution%20Function)
[Geometry Block](/Public/Study/Graphics/Docs/Rendering%20Equation/Docs/Cook-Torrance/Geometry%20Block)

광원과 관찰자에 대해 유효 가시 미세 거울 면적은 
$\vec{i_s}$ 를 중심으로 $d\Omega^{h^*}$ 입체각 범위 안에 있는 법선을 가진 
모든 미세거울들이다.

단일 미세거울이 반사([Fresnel](/Public/Study/Graphics/Docs/Rendering%20Equation/Docs/Cook-Torrance/Fresnel))하는 미소 광속은 다음과 같다.
$$
d^2\Phi^{x^{\mu(\mu \mid v,l,h*)},e}_{\vec{o_x}} = F \times d^2\Phi^{s,x^{\mu(\mu \mid v,l,h*)}}_{\vec{i_s}} 
= F \times dI^s_{\vec{i_s}} \times  d\Omega^{s,x^{\mu(\mu \mid v,l,h*)}}_{\vec{i_s}} 
$$
$$
d\Omega^{s,x^{\mu(\mu \mid v,l,h*)}}_{\vec{i_s}} 
= \frac{dA^{\mu(\mu \mid v,l,h*)}_x(\vec{n_{h^*}} \cdot \vec{i_s})}{r^2}
= \frac{G_2(l,v)dA^{+\mu(+\mu \mid v,l,h^*)}_x(\vec{n_{h^*}} \cdot \vec{i_s})}{r^2}
$$
$$
dA^{+\mu(+\mu \mid v,l,h*)}_x=  D(h^*) \times d\Omega^{h^*} \times dA_x
$$
미세 표면 $x$에서 관찰자 e를 바라본 입체각이 $d\Omega^{x,e}_{\vec{o_e}}$ 이고
미세 거울의 법선 $\vec{n_{h^*}}$ 와 $\vec{i_s}$ 사이각이 $\theta$ 일때, $\vec{i_s}$ 와 $\vec{o_x}$ 사이각은 $2 \theta$ 이다

$d\Omega^{x,e}_{\vec{o_e}} =​ \sin(2\theta) \times 2d\theta \times d\phi$
$\sin(2 \theta) = 2\sin(\theta) \cos(\theta)$ 
$d\Omega^{x,e}_{\vec{o_e}} =​ 4\sin(\theta)\cos(\theta) \times d\theta \times d\phi =  4(\vec{n_{h^*}} \cdot \vec{i_s}) \big(\sin(\theta)\times d\theta \times d\phi\big) = 4(\vec{n_{h^*}} \cdot \vec{i_s}) d\Omega^{h^*}$

$$
dA^{+\mu(+\mu \mid v,l,h^*)}_x=  D(h^*) \times \frac{d\Omega^{x,e}_{\vec{o_e}}}{4(\vec{n_{h^*}} \cdot \vec{i_s})} \times dA_x
$$

표면에서 관찰자에게 방출한 광속은 다음과 같다.
$$d^2\Phi^{x,e}_{\vec{o_x}} = dI^{x}_{\vec{o_x}} \times  d\Omega^{x,e}_{\vec{o_x}} = d^2\Phi^{x^{\mu(\mu \mid v,l,h^*)},e}_{\vec{o_x}} $$
$$dI^{x}_{\vec{o_e}} \times  d\Omega^{x,e}_{\vec{o_e}} = F \times 
dI^s_{\vec{i_s}} \times \frac{G_2(\mathbf{l,v}) \times D \times \big(\frac{d\Omega^{x,e}_{\vec{o_e}}}{4(\vec{n_{h^*}} \cdot \vec{i_s})} \big)dA_x (\vec{n_{h^*}} \cdot \vec{i_s}) }{r^2}
$$
$$\frac{dI^x_{\vec{o_x}}}{dI^s_{\vec{i_s}}} = F  \times \frac{G_2(\mathbf{l,v})  \times D \times dA_x}{4r^2}$$
표면의 방출 휘도는 다음과 같다
$$L^{s,x,e}_{\vec{o_x}} = \frac{dI^x_{\vec{o_x}}}{dA_x(\vec{n_x} \cdot \vec{o_x})}$$

광원에서 미소 표면에 도달한 미소 광속은 다음과 같다.
$$
d^2\Phi^{s,x}_{\vec{i_s}} = dI^s_{\vec{i_s}} \times d\Omega^{s,x}_{\vec{i_s}}
= dI^s_{\vec{i_s}} \times \frac{\mathbf{dA_x(\vec{n_x} \cdot \vec{i_s})}}{r^2}
$$

표면의 조도는 다음과 같다.
$$
dE^x_{s,\vec{i_s}} 
= \frac{d^2\Phi^{s,x}_{\vec{i_s}}}{dA_x} 
= \frac{dI^s_{\vec{i_s}} \times \frac{\mathbf{dA_x(\vec{n_x} \cdot \vec{i_s})}}{r^2}}{dA_x} = \mathbf{\frac{dI^s_{\vec{i_s}}(\vec{n_x} \cdot \vec{i_s})}{r^2}}
$$

BRDF의 식은 다음과 같다.
$$
f_{r,\vec{i_s},\vec{o_e}} = \frac{L^{s,x,e}_{\vec{o_x}}}{dE^x_{s,\vec{i_s}}} 
= \frac{dI^x_{\vec{o_x}}}{dI^s_{\vec{i_s}}} \times 
\frac{r^2}{dA_x(\vec{n_x} \cdot \vec{o_x})(\vec{n_x} \cdot \vec{i_s})} 
= \frac{F  \times G \times D}{4(\vec{n_x} \cdot \vec{o_x})(\vec{n_x} \cdot \vec{i_s})}
$$