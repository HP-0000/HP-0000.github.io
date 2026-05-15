---
---

$\Phi$ 는 광속을 나타내며 단위는 루멘(lm) 을 사용한다.
$I$는 광도를 나타내며 단위는 칸델라(cd) 를 사용한다.

---
광원 $s$과 표면 $x$ 에 대하여 
광원을 이루는, 미소 면적 $dA_s$를 갖는 미소 광원 각각이 사방으로 빛을 방출할 때,
고유 방향 $\vec{i_s}$으로 입체각 $d\Omega^{s,x}_{i_s}$ 범위로 방출한 광속 $d^2\Phi^{s,x}_{i_s}$ 이 미소 표면 $dA_x$에 입사한다.

[각도](/Public/Study/Math/Docs/각도/각도)
$dA_s$ 에서 바라본 $dA_x$의 입체각은 다음과 같다.
$$d\Omega^{s,x}_{\vec{i_s}} = \frac{dA_x(\vec{n_x} \cdot \vec{i_s})}{r^2}$$
광도를 다음과 같이 정의한다.
$$dI^s_{\vec{i_s}} = \frac{d^2\Phi^{s,x}_{\vec{i_s}}}{d\Omega^{s,x}_{\vec{i_s}}}$$
미소 표면의 단위 면적 당 입사 광속 $d^2\Phi^{s,x}_{i_s}$ 을 조도로 정의한다.
$$dE^{s,x}_{\vec{i_s}} = \frac{d^2\Phi^{s,x}_{\vec{i_s}}}{dA_x} = \frac{dI^s_{\vec{i_s}}(\vec{n_x} \cdot \vec{i_s})}{r^2}$$

$dA_x$ 에서 바라본 $dA_s$의 입체각은 다음과 같다.
$$d\Omega^{x,s}_{\vec{-i_s}} = \frac{dA_s(\vec{n_s} \cdot \vec{i_s})}{r^2}$$

조도식은 다음과 같이 정리할 수 있다.
$$dE^{s,x}_{\vec{i_s}} =  \frac{dI^s_{\vec{i_s}}(\vec{n_x} \cdot \vec{i_s})}{dA_s(\vec{n_s} \cdot \vec{i_s})} d\Omega^{x,s}_{\vec{-i_s}}$$

입사 휘도를 다음과 같이 정의한다.
$$L^{s,x}_{\vec{i_s}} = \frac{dI^s_{\vec{i_s}}}{dA_s(\vec{n_s} \cdot \vec{i_s})}$$

$$dE^{s,x}_{\vec{i_s}} = L^{s,x}_{\vec{i_s}} \times (\vec{n_x} \cdot \vec{i_s}) d\Omega^{x,s}_{\vec{-i_s}}$$

--- 
#### BRDF
광원 $ds$ 로부터 $\vec{i_s}$ 방향으로 빛을 받은 표면 $dx$가  
$\vec{o_x}$ 방향으로 관찰자 $e$ 에게 광속을 제공할 때 
방출 휘도를 다음과 같이 정의한다.
$$L^{s,x,e}_{\vec{o_x}} = \frac{dI^x_{\vec{o_x}}}{dA_x(\vec{n_x} \cdot \vec{o_x})}$$
조도와 방출 휘도의 비를 $BRDF(f_{r,\vec{i_s},\vec{o_x}})$ 로서 다음과 같이 정의한다.
$$
f_{r,\vec{i_s},\vec{o_x}} = \frac{L^{s,x,e}_{\vec{o_x}}}{dE^{s,x}_{\vec{i_s}}} 
$$
#### Render Eqaution
광원 $s$ 로부터 빛을 받은 표면 $x$가  
$\vec{o_x}$ 방향으로 관찰자 $e$ 에게 광속을 제공할 때 
방출 휘도는 다음과 같다.

$$
L^{x,e}_{\vec{o}_x}  = \int_{\vec{i_s}} L^{s,x,e}_{\vec{o_x}} = \int_{\vec{i_s}} f_{r,\vec{i_s},\vec{o_x}} \times dE^{s,x}_{\vec{i_s}}
= \int_{\vec{i_s}} f_{r,\vec{i_s},\vec{o_x}} \times L^{s,x}_{\vec{i_s}} (\vec{n_x} \cdot \vec{i_s}) d\Omega^{x,s}_{\vec{-i_s}}
$$

광원 $s$ 로부터 조도식은 다음과 같다.
$$
E^{s,x} = \int_{\vec{i_s}} dE^{s,x}_{\vec{i_s}} = \int_{\vec{i_s}}  L^{s,x}_{\vec{i_s}} \times (\vec{n_x} \cdot \vec{i_s}) d\Omega^{x,s}_{\vec{-i_s}}
$$
#### 일루미네이션의 계산
$$
L^{x,e}_{\vec{o}_x} = L^{x,e}_{\vec{o}_x}(Diffuse) + L^{x,e}_{\vec{o}_x}(specular)
$$
$specular$ 는 물체 표면에서 즉시 산란된 빛이다. 
[Cook-Torrance](/Public/Study/Graphics/Docs/Rendering%20Equation/Docs/Cook-Torrance/Cook-Torrance)

$Diffuse$ 는 물체 내부로 침투 한 뒤 산란되어 방출된 빛이다.

**로컬 일루미네이션**
표면과 광원을 분리하여 직접 광만을 계산한다.
**글로벌 일루미네이션**
로컬 일루미네이션을 계산 한 뒤, 표면들의 간접 광을 추가로 더한다.

![](/Public/Study/Graphics/Docs/Rendering%20Equation/Docs/Resource/Pasted%20image%2020260701123628.png)


####  LTC
#### Split-Sum