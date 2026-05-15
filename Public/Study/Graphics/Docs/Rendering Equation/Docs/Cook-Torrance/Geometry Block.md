---
---
![[Public/Study/Graphics/Docs/Rendering Equation/Docs/Cook-Torrance/Resource/Pasted image 20260817220901.png]]

미세 거울들(보라, 초록)은 미소 표면(검정)에 peak를 만든다.
시선(빨강)과 이루는 각에 따라 미세 거울들의 면적을 다음과 같이 분류한다.

$\mathbf{v} \cdot \mathbf{h} \geq 0$ 를 만족하는 미세 거울들(보라)는 가시 면적이다.
$\mathbf{v} \cdot \mathbf{h} < 0$ 를 만족하는 미세 거울들(초록)은 비가시 면적이다.

미세면 모델의 가정에 의해 
미소 표면의 시선 투영 면적(검정,양수)는 
가시 투영 면적(보라,양수)와 비가시 투영 면적(초록,음수)의 합과 일치한다.

가시 투영 면적(보라,양수)중 일부 유효 가시 투영 면적(보라,양수) 만이 실제 시선에서 보이며
유효 가시 투영 면적에 의해 시선에서 가려진 가시 투영 면적(보라,양수)은 
비가시 투영 면적(초록,음수)과 일치한다.

---

[Normal Distribution Function](/Public/Study/Graphics/Docs/Rendering%20Equation/Docs/Cook-Torrance/Normal%20Distribution%20Function)

$$
\text{미소 시선 투영 면적 비} = 
\frac{\text{미세 거울 시선 투영 면적}}{\text{표면 시선 투영 면적}} 
$$
$$=\frac{\mathbf{dA^{\mu'_,h}_x  (\mathbf{v} \cdot \mathbf{h})}}{\mathbf{dA_x  (\mathbf{v} \cdot \mathbf{n}})} 
= D(h)\frac{\vec{v} \cdot \vec{h}}{\vec{v}\cdot \vec{n}}d\Omega^\vec{n_h}
$$

탄젠트 공간에서 표면에서 시작하는 시선 벡터의 단위 벡터는 다음과 같다.
$\mathbf{v} = (\sin\theta_v \cos\phi_v,\sin\theta_v \sin\phi_v,\cos\theta_v)$
- $\theta_v$ : 미소 표면 법선 $\vec{n}$과 시야 벡터 $\vec{v}$ 사이각

탄젠트 공간에서 단위 법선 벡터 $\vec{h}$를 구면 좌표계로 표현하면 다음과 같다. 
- $h_x = \sin\theta_m \cos\phi_m$  (X 성분)
- $h_y = \sin\theta_m \sin\phi_m$  (Y 성분)
- $h_z = \cos\theta_m$  (Z 성분)
- $\theta_m$ : 미소 표면 법선 $\vec{n}$과 미세 거울 법선 $\vec{h}$ 사이각

$s_x = \frac{h_x}{h_z} = \tan\theta_m \cos\phi_m$
$s_y = \frac{h_y}{h_z} = \tan\theta_m \sin\phi_m$
$\vec{h} =\cos\theta_m (s_x,s_y,1)$

$\mathbf{v} \cdot \mathbf{h} =(\cos\theta_m) \times ((\sin\theta_v \cos\phi_v)(s_x) + (\sin\theta_v \sin\phi_v)(s_y) + (\cos\theta_v)(1) = (s_x \cos\phi_v + s_y \sin\phi_v) \sin\theta_v + \cos\theta_v)$
$\mathbf{v} \cdot \mathbf{n} = \cos\theta_v$

$$
\mathbf{\frac{\mathbf{v} \cdot \mathbf{h}}{\mathbf{v} \cdot \mathbf{n}}} = \frac{\cos\theta_m \cos\theta_v \Big[ 1 + (s_x \cos\phi_v + s_y \sin\phi_v)\tan\theta_v \Big]}{\cos\theta_v} = \mathbf{\cos\theta_m \Big[ 1 + (s_x \cos\phi_v + s_y \sin\phi_v)\tan\theta_v \Big]}
$$

$$
\begin{aligned}
\text{미소 시선 투영 면적 비} &= \left( D(\mathbf{h}) \, d\Omega^{\vec{n}_h} \right) \times \left( \frac{\mathbf{v} \cdot \mathbf{h}}{\mathbf{v} \cdot \mathbf{n}} \right) \\
&= \left( \frac{P(s_x, s_y)}{\mathbf{\cos\theta_m}} \, ds_x \, ds_y \right) \times \mathbf{\cos\theta_m} \Big[ 1 + (s_x \cos\phi_v + s_y \sin\phi_v)\tan\theta_v \Big] \\
&= \mathbf{P(s_x, s_y) \Big[ 1 + (s_x \cos\phi_v + s_y \sin\phi_v)\tan\theta_v \Big] \, ds_x \, ds_y}
\end{aligned}
$$

$$P(s_x,s_y) = \frac{\alpha^2}{\pi(s_x^2 + s_y^2 + \alpha^2)^2}$$

---

$$\text{미소 시선 투영 면적 비} = \mathbf{P(s_x, s_y) \Big[ 1 + (s_x \cos\phi_v + s_y \sin\phi_v)\tan\theta_v \Big] \, ds_x \, ds_y}$$

$\mathbf{v_xy} = (\sin\theta_v \cos\phi_v,\sin\theta_v \sin\phi_v)$

$$
|\mathbf{v}_{xy}| = \sqrt{(\sin\theta_v \cos\phi_v)^2 + (\sin\theta_v \sin\phi_v)^2} = \sqrt{\sin^2\theta_v (\cos^2\phi_v + \sin^2\phi_v)} = \mathbf{\sin\theta_v}
$$

$$
\hat{\mathbf{v}}_{xy} = \frac{\mathbf{v}_{xy}}{|\mathbf{v}_{xy}|} = \frac{(\sin\theta_v \cos\phi_v, \; \sin\theta_v \sin\phi_v)}{\sin\theta_v} = \mathbf{(\cos\phi_v, \; \sin\phi_v)}
$$

$$
\vec{s} \cdot \hat{\mathbf{v}}_{xy} = (s_x, s_y) \cdot (\cos\phi_v, \sin\phi_v) = \mathbf{s_x \cos\phi_v + s_y \sin\phi_v}
$$

$\mathbf{t_x} = s_x \cos\phi_v + s_y \sin\phi_v$
수직인 벡터는 다음과 같다.
$\mathbf{t_y} = s_x \cos(\phi_v+\frac{\pi}{2}) + s_y \sin(\phi_v+\frac{\pi}{2}) = -s_x \sin\phi_v + s_y \cos\phi_v$


$$
\begin{pmatrix} t_x \\ t_y \end{pmatrix} = \begin{pmatrix} \cos\phi_v & \sin\phi_v \\ -\sin\phi_v & \cos\phi_v \end{pmatrix}\begin{pmatrix} s_x \\ s_y \end{pmatrix}
$$

- $\mathbf{t_x} =s_x \cos\phi_v + s_y \sin\phi_v$
- $\mathbf{t_y} = -s_x \sin\phi_v + s_y \cos\phi_v$
- $s_x^2 + s_y^2 = \mathbf{t_x^2 + t_y^2} = \tan^2\theta_m$
- $ds_x \, ds_y = \mathbf{dt_x \, dt_y}$

$$P(s_x, s_y) = \frac{\alpha^2}{\pi (s_x^2 + s_y^2 + \alpha^2)^2} = \frac{\alpha^2}{\pi (t_x^2 + t_y^2 + \alpha^2)^2} = P(t_x, t_y)$$

$$
\text{미소 시선 투영 면적 비} = P(t_x, t_y) \Big( 1 + t_x \tan\theta_v \Big) \, dt_x \, dt_y
$$
$$
\begin{aligned}
\iint_{-\infty}^{\infty} P(t_x, t_y) \Big( 1 + t_x \tan\theta_v \Big) \, dt_x \, dt_y 
&= \underbrace{\iint_{-\infty}^{\infty} P(t_x, t_y)  \, dt_x \, dt_y}_{= \, \mathbf{1}} +  \tan\theta_v \underbrace{\iint_{-\infty}^{\infty} t_x P(t_x, t_y) \, dt_x \, dt_y}_{\text{대칭성(기함수 적분)에 의해 } = \, \mathbf{0}} \\
&= 1 + \tan\theta_v \times 0 \\
&= \mathbf{1}
\end{aligned}
$$

가시 구간에 대해 적분한다. 
$\mathbf{v} \cdot \mathbf{h} = \mathbf{t_x \sin\theta_v + \cos\theta_v}$
$t_x \sin\theta_v + \cos\theta_v \ge 0$
$\mathbf{t_x \ge -\cot\theta_v}$

$$
 \int_{-\cot\theta_v}^{\infty} (t_x \tan\theta_v + 1) \cdot \left[ \mathbf{\int_{-\infty}^{\infty} \frac{\alpha^2}{\pi (t_x^2 + t_y^2 + \alpha^2)^2} \, dt_y} \right] \, dt_x
$$

$\int_{-\infty}^{\infty} \frac{1}{(x^2 + a^2)^2} \, dx = \mathbf{\frac{\pi}{2 a^3}}$
임으로 

$$
\int_{-\infty}^{\infty} \frac{\alpha^2}{\pi (t_x^2 + t_y^2 + \alpha^2)^2} \, dt_y = \mathbf{\frac{\alpha^2}{2 (t_x^2 + \alpha^2)^{3/2}}}
$$


이를 원래 식에 대입한다.
$$
\mathbf{ \int_{-\cot\theta_v}^{\infty} (t_x \tan\theta_v + 1) \cdot \frac{\alpha^2}{2 (t_x^2 + \alpha^2)^{3/2}} \, dt_x}
$$

$$
\underbrace{ \tan\theta_v \int_{-\cot\theta_v}^{\infty} \frac{\alpha^2 t_x}{2 (t_x^2 + \alpha^2)^{3/2}} \, dt_x }_{I_1} \;+\; \underbrace{ \int_{-\cot\theta_v}^{\infty} \frac{\alpha^2}{2 (t_x^2 + \alpha^2)^{3/2}} \, dt_x }_{I_2}
$$

---

###### $I_1$ 
$u = t_x^2 + \alpha^2$ 
$du = 2 t_x dt_x$ 

$$
I_1 = \tan\theta_v \cdot \frac{\alpha^2}{4} \int_{\cot^2\theta_v + \alpha^2}^{\infty} u^{-3/2} \, du
$$
$$
I_1 = \tan\theta_v \cdot \frac{\alpha^2}{4} \left[ -2 u^{-1/2} \right]_{\cot^2\theta_v + \alpha^2}^{\infty} = \frac{\alpha^2 \tan\theta_v}{2 \sqrt{\cot^2\theta_v + \alpha^2}}
$$

$$
I_1 = \mathbf{\frac{\alpha^2 \tan^2\theta_v}{2 \sqrt{1 + \alpha^2 \tan^2\theta_v}}}
$$

###### $I_2$ 
$t_x = \alpha \tan u$ 
$dt_x = \alpha \sec^2 u \, du$
$\tan(u_{lower}) = \frac{-\cot\theta_v}{\alpha} = \frac{\text{높이}}{\text{밑변}}$

$$
I_2 = \int_{u_{lower}}^{\pi/2} \frac{\alpha^2}{2 (\alpha^3 \sec^3 u)} \cdot (\alpha \sec^2 u \, du) = \frac{1}{2} \int_{u_{lower}}^{\pi/2} \cos u \, du
$$

$$
I_2 = \frac{1}{2} \Big[ \sin u \Big]_{u_{lower}}^{\pi/2} = \frac{1}{2} \left( \sin\left(\frac{\pi}{2}\right) - \sin(u_{lower}) \right) = \mathbf{\frac{1}{2} \Big( 1 - \sin(u_{lower}) \Big)}
$$

삼각비에 의해
$\sin(u_{lower}) = \frac{-\cot\theta_v}{\sqrt{\cot^2\theta_v + \alpha^2}}$

$$
I_2 = \mathbf{\frac{1}{2} + \frac{1}{2 \sqrt{1 + \alpha^2 \tan^2\theta_v}}}
$$

---
가시 투영 면적을 다음과 같이 정의한다.
$$
dA^{+\mu_v}_x = \int_{v \cdot h \geq 0} dA^{\mu',h}_x (\mathbf{v} \cdot \mathbf{h})
$$
$$
\begin{aligned}
\frac{dA^{+\mu_v}_x}{\mathrm{d}A_x (\mathbf{v} \cdot \mathbf{n})} 
= I_1 + I_2 = \frac{\sqrt{1 + \alpha^2 \tan^2\theta_v} + 1}{2}
\end{aligned}
$$
비가시 투영 면적을 다음과 같이 정의한다.
$$
dA^{-\mu_v}_x = \int_{v \cdot h < 0} dA^{\mu',h}_x (\mathbf{v} \cdot \mathbf{h})
$$
$$
\begin{aligned}
\Lambda(v) = \frac{dA^{-\mu_v}_x}{\mathbf{dA_x (\mathbf{v} \cdot \mathbf{n}})} = \frac{dA^{+\mu_v}_x}{\mathrm{d}A_x (\mathbf{v} \cdot \mathbf{n})}  - \frac{\mathrm{d}A_x (\mathbf{v} \cdot \mathbf{n})}{\mathrm{d}A_x (\mathbf{v} \cdot \mathbf{n})} 
=\frac{\sqrt{1 + \alpha^2 \tan^2\theta_v}}{2}  - \frac{1}{2}
\end{aligned}
$$
#### Smith Model
유효 가시 투영 면적을 $dA^{\mu_v}_x$ 로 정의한다.
$$
\begin{aligned}
\frac{\mathbf{dA^{\mu_v}_x}}{\mathbf{dA_x  (\mathbf{v} \cdot \mathbf{n}})}  = 1
\end{aligned}
$$
가시 투영 면적 대비 유효 가시 투영 면적의 비는 다음과 같다. 
$$
G_1(v) = \frac{dA^{\mu_v}_x}{dA^{+\mu_v}_x} = \frac{1}{1 + \Lambda(v)} 
$$

###### 섀도잉($G_1(\mathbf{l})$)
광원에서 온 빛의 일부가 표면을 향할때 다른 거울에 의해 가려지는 현상 
###### 마스킹($G_1(\mathbf{v})$)
표면에서 관찰자로 반사되는 빛의 일부가 다른 거울에 의해 가려지는 현상 

## Height-Correlated Smith

차폐 현상을 매우 단순하게 근사 하면 다음과 같다.
시선이 진행하며 높이 구간 $[z +dz , z+2dz]$를 통과하면서 마주한 비가시 투영 면적 크기 만큼 
하부 높이 구간 $[-\infty, z +dz ]$ 에 존재하는 가시 투영 면적을 가린다.
발생하는 비가시 차폐 면적은 하부의 각 높이 구간이 가진 유효 가시 투영 면적
비율에 따라 분배한다.

구간$[z , z+dz]$에 존재하는 미세거울에 대한 표기를 다음과 같이 정리한다.

미세 거울 면적 :  $dA^{\mu'}_{x_{z_1}}$ 
미세 표면 거울 면적 :  $dA^{\mu'_n}_{x_{z_1}}$

시선이 높이 $z_2$에 도달 했을때 표기를 다음과 같이 정리한다.

가시 거울 면적 : $dA^{+\mu(+\mu \mid v_{z_1})}_{x_z}$ 
가시 투영 면적 : $dA^{+\mu_v(+\mu \mid v_{z_1})}_{x_z}$ 

비가시 거울 면적 : $dA^{-\mu(-\mu \mid v_z)}_{x_{z_1}}$ 
비가시 투영 면적 : $dA^{-\mu_v(-\mu \mid v_z)}_{x_{z_1}}$ 

유효 가시 거울 면적 : $dA^{\mu(\mu \mid v_{z_2})}_{x_{z_1}}$ 
유효 가시 투영 면적 : $dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}}$ 


미세면 모델의 가정에 의해 미세거울의 기울기 분포가 높이와 상관 없이 일정함으로

$$
\frac{dA^{+\mu_v(+\mu \mid v)}_{x_{z_1}}}{dA^{\mu'_{n}}_{x_{z_1}}(v \cdot n)} = \frac{dA^{+\mu_v}_x}{dA_x (v \cdot n)} = 1 + \Lambda(v) 
$$
$$
\frac{dA^{-\mu_v(-\mu \mid v)}_{x_{z_1}}}{dA^{\mu'_{n}}_{x_{z_1}}(v \cdot n)} 
= \frac{dA^{-\mu_v}_x}{dA_x (v \cdot n)} =  \Lambda(v)
$$

$dA^{+\mu_v(+\mu \mid v)}_{x_{z_1}} =(1 + \Lambda(v)) (v \cdot n)dA^{\mu'_n}_{x_{z_1}}$
$dA^{-\mu_v(-\mu \mid v)}_{x_{z_1}} = \Lambda(v)(v \cdot n) dA^{\mu'_n}_{x_{z_1}}$

시선이 $dz$ 하강하여 $z_2$가 되었을 때 구간 $dz$의 비가시 투영 면적에 의해
하부 구간 $[z_1, z_1 + dz_1]$ 의 미세 거울의 유효 가시 투영 면적이 감쇠하는 면적량은 다음과 같다.
$$
dA^{\mu_v(\mu \mid v_{z_2+dz_2})}_{x_{z_1}} - dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}} = dA^{-\mu_v(-\mu \mid v)}_{x_{z_2}}  \times \frac{dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}}}{\int_{-\infty}^{z_2} dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z}}}$$
유효 가시 투영 면적은 표면의 가시 투영 면적과 일치해야한다.
$$ 
\int_{-\infty}^{z_2} dA^{\mu_v(\mu \mid v_{z_2})}_{x_z} = (v \cdot n)\int_{-\infty}^{z_2} dA^{\mu'_n}_{x_z} 
$$

$$
dA^{\mu_v(\mu \mid v_{z_2+dz_2})}_{x_{z_1}} - dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}}
=   \Lambda(v)\frac{dA^{\mu'_n}_{x_{z_2}}dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}}}{\int_{-\infty}^{z_2} dA^{\mu'_n}_{x_z}}
$$
$$
\frac{dA^{\mu_v(\mu \mid v_{z_2+dz_2})}_{x_{z_1}} - dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}}}{dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}}}
=   \Lambda(v)\frac{dA^{\mu'_n}_{x_{z_2}}}{\int_{-\infty}^{z_2} dA^{\mu'_n}_{x_z}}
$$
$$G_1(v,z_1,z_2) = \frac{dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}}}{dA^{+\mu_v(\mu \mid v)}_{x_{z_1}}}$$
$$
u(z_2) = \int_{-\infty}^{z_2} dA^{\mu'_n}_{x_z}
$$
$$
\frac{G_1(v,z_1,z_2+dz_2) -G_1(v,z_1,z_2)}{G_1(v,z_1,z_2)}
= \frac{dG_1(v,z_1,z_2)}{G_1(v,z_1,z_2)}
= \Lambda(v) \frac{du(z_2)}{u(z_2)}
$$
양식을 적분한다.
$$
\ln G_1(v,z_1,z_2) = \Lambda(\mathbf{v})\ln u(z_2) + C
$$
$z_2 = \infty$ 를 대입하면 $G_1(v,z_1,\infty)=1$ $u(\infty) = dA_x$ 임으로
$$
G_1(v,z_1,z_2) = [\frac{u(z_2)}{dA_x}]^{\Lambda(\mathbf{v})}
$$
---

$$
G_1(v) = \frac{dA^{\mu_v}_x}{dA^{+\mu_v}_x} 
=\frac{\int_{-\infty}^{\infty} \mathbf{dA^{\mu_v(\mu\mid v_{z_1})}_{x_{z_1}}}}{\int_{-\infty}^{\infty} \mathbf{dA^{+\mu_v(\mu \mid v)}_{x_{z_1}}}}
$$

$$
\int_{-\infty}^{\infty} \mathbf{dA^{\mu_v(\mu\mid v_{z_1})}_{x_{z_1}}}
=  \int_{-\infty}^{\infty}  G_1(v,z_1,z_1)dA^{+\mu_v}_{x_{z_1}}
= \int_{-\infty}^{\infty} [\frac{u(z_1)}{dA_x}]^{\Lambda(\mathbf{v})} \Big[ (1 + \Lambda(v))(v \cdot n) dA^{\mu'_n}_{x_{z_1}} \Big]
$$

$$
\int_{-\infty}^{\infty} \mathbf{dA^{+\mu_v(\mu \mid v)}_{x_{z_1}}} 
= (1 + \Lambda(v))(v \cdot n) \underbrace{\int_{-\infty}^{\infty} dA^{\mu'_n}_{x_{z_1}}}_{A_x}
$$
$$
G_1(v) = \frac{1}{A_x} \int_{-\infty}^{\infty} \frac{[u(z_1)]^{\Lambda(v)}}{A_x^{\Lambda(v)}} \, dA^{\mu'_n}_{x_{z_1}} = \mathbf{\frac{1}{A_x^{\Lambda(v) + 1}} \int_{-\infty}^{\infty} [u(z_1)]^{\Lambda(v)} \, dA^{\mu'_n}_{x_{z_1}}}
$$
* $du = dA^{\mu'_n}_{x_{z_1}}$
* $z_1 = -\infty \implies \mathbf{u = 0}$ 
* $z_1 = +\infty \implies \mathbf{u = A_x}$

$$
G_1(v) = \frac{1}{A_x^{\Lambda(v) + 1}} \int_{\mathbf{0}}^{\mathbf{A_x}} u^{\Lambda(v)} \, \mathbf{du}
$$
$$
G_1(v) = \frac{1}{A_x^{\Lambda(v) + 1}} \left[ \frac{u^{\Lambda(v) + 1}}{\Lambda(v) + 1} \right]_0^{A_x} = \frac{1}{1 + \Lambda(v)}
$$
---

$$
dA^{\mu_v(\mu \mid v_{z_2+dz_2})}_{x_{z_1}} - dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}}
=   \Lambda(v)\frac{dA^{\mu'_n}_{x_{z_2}}dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}}}{\int_{-\infty}^{z_2} dA^{\mu'_n}_{x_z}}
$$
$$
dA^{\mu_v(\mu \mid v_{z_2+dz_2})}_{x_{z_1}}  = dA^{\mu_v(\mu \mid v_{z_2})}_{x_{z_1}} \times  \big [ 1 + \Lambda(v)\frac{dA^{\mu'_n}_{x_{z_2}}}{\int_{-\infty}^{z_2} dA^{\mu'_n}_{x_z}} \big] 
$$
$$
dA^{\mu(\mu \mid v_{z_2+dz_2})}_{x_{z_1}}  = dA^{\mu(\mu \mid v_{z_2})}_{x_{z_1}} \times  \big [ 1 + \Lambda(v)\frac{dA^{\mu'_n}_{x_{z_2}}}{\int_{-\infty}^{z_2} dA^{\mu'_n}_{x_z}} \big] 
$$
섀도잉이 마스킹과 상관 없이 독립적으로 작용한다고 가정한다.
$l \approx v$ 일때 미세 거울이 섀도잉과 마스킹에 동시에 참여하지만 
독립으로 취급하여 중복으로 차폐를 적용하는 문제를 안고 가야한다.


$$
dA^{\mu(\mu \mid v_{z_2+ dz_2},l_{z_2+ dz_2})}_{x_{z_1}}
= 

dA^{\mu(\mu \mid v_{z_2},l_{z_2})}_{x_{z_1}}  \times  \big [ 1 + \Lambda(v)\frac{dA^{\mu'_n}_{x_{z_2}}}{\int_{-\infty}^{z_2} dA^{\mu'_n}_{x_z}} \big] \times  \big [ 1 + \Lambda(l)\frac{dA^{\mu'_n}_{x_{z_2}}}{\int_{-\infty}^{z_2} dA^{\mu'_n}_{x_z}} \big] 
$$

$$
\begin{aligned}
dA^{\mu(\mu \mid v_{z_2+ dz_2},l_{z_2+ dz_2})}_{x_{z_1}} 
&= dA^{\mu(\mu \mid v_{z_2},l_{z_2})}_{x_{z_1}} \left[ 1 + \Lambda(v)\frac{du}{u} + \Lambda(l)\frac{du}{u} + \Lambda(v)\Lambda(l)\left(\frac{du}{u}\right)^2
\right]
\end{aligned}
$$

$$
\frac{dA^{\mu(\mu \mid v_{z_2+ dz_2},l_{z_2+ dz_2})}_{x_{z_1}}
- dA^{\mu (\mu \mid v_{z_2},l_{z_2})}_{x_{z_1}}}{dA^{\mu(\mu \mid v_{z_2},l_{z_2})}_{x_{z_1}}} 
= (\Lambda(v) + \Lambda(l))\frac{dA^{\mu'_n}_{x_{z_2}} }{\int_{-\infty}^{z_2} dA^{\mu'_n}_{x_{z}}} + \Lambda(v)\Lambda(l)\frac{(du)^2}{u^2}
$$
$$G_2(l,v,z_1,z_2) = \frac{dA^{\mu(\mu \mid v_{z_2},l_{z_2})}_{x_{z_1}}}{dA^{+\mu(+\mu \mid v,l)}_{x_{z_1}}}$$
$$
\frac{dG_2(l,v,z_1,z_2)}{G_2(l,v,z_1,z_2)}
= (\Lambda(l) + \Lambda(v)) \frac{du(z_2)}{u(z_2)} + \Lambda(v)\Lambda(l)\frac{(du(z_2))^2}{u^2(z_2)}
$$

양식을 적분한다.
$$
\ln G_1(v,z_1,z_2) = (\Lambda(\mathbf{l})+\Lambda(\mathbf{v}))\ln u(z_2) - \Lambda(v)\Lambda(l)\frac{du(z_2)}{u(z_2)} + C
$$
$z_2 = \infty$ 를 대입하면 $G_1(v,z_1,\infty)=1$ $u(\infty) = dA_x$
$du(\infty) = dA^{\mu'_n}_{x_\infty} = 0$ 임으로
$$
\ln G_1(v,z_1,z_2) = (\Lambda(\mathbf{l})+\Lambda(\mathbf{v}))\ln u(z_2)
$$
$$
G_2(l,v,z_1,z_2) = [\frac{u(z_2)}{dA_x}]^{\Lambda(l) + \Lambda(v)}
= G_1(\mathbf{l},z_1,z_2) \times G_1(\mathbf{v},z_1,z_2) 
$$
$$
G_2(l,v) = \frac{dA^{\mu(\mu \mid v,l)}_x}{dA^{+\mu(+\mu \mid v,l)}_x} 
= \frac{\int_{-\infty}^{\infty} 
G_2(l,v,z_1,z_1)dA^{+\mu(+\mu \mid v,l)}_{x_{z_1}}
}{\int_{-\infty}^{\infty} dA^{+\mu(+\mu \mid v,l)}_{x_{z_1}}}
$$

모든 임의 높이 구간에 대해 미세 거울의 기울기 분포 비율이 표면 전체와 동일함으로 
$$
\frac{dA^{+\mu(+\mu \mid v, l)}_{x_{z_1}}}{dA^{\mu'_n}_{x_{z_1}}} 
=\frac{dA^{+\mu(+\mu \mid v, l)}_x}{dA_x} = C(v, l) 
$$
* $C(v, l)$은 $z_1$과 무관)
$$
\begin{aligned}
G_2(l,v) &= \frac{\int_{-\infty}^{\infty} G_2(l,v,z_1,z_1) \Big[ \mathbf{C(v,l)} \, dA^{\mu'_n}_{x_{z_1}} \Big]}{\int_{-\infty}^{\infty} \Big[ \mathbf{C(v,l)} \, dA^{\mu'_n}_{x_{z_1}} \Big]} \\
&= \frac{\mathbf{C(v,l)} \int_{-\infty}^{\infty} G_2(l,v,z_1,z_1) \, dA^{\mu'_n}_{x_{z_1}}}{\mathbf{C(v,l)} \underbrace{\int_{-\infty}^{\infty} dA^{\mu'_n}_{x_{z_1}}}_{A_x}} \\
&= \frac{1}{A_x} \int_{-\infty}^{\infty} \mathbf{G_2(l,v,z_1,z_1)} \, dA^{\mu'_n}_{x_{z_1}}
\end{aligned}
$$

$$
\begin{aligned}
G_2(l,v) &= \frac{1}{A_x} \int_{-\infty}^{\infty} \left[ \frac{u(z_1)}{A_x} \right]^{\Lambda(l) + \Lambda(v)} dA^{\mu'_n}_{x_{z_1}} \\
&= \frac{1}{A_x^{\Lambda(l) + \Lambda(v) + 1}} \int_{-\infty}^{\infty} [u(z_1)]^{\Lambda(l) + \Lambda(v)} \, dA^{\mu'_n}_{x_{z_1}}
\end{aligned}
$$
* $\mathbf{du = dA^{\mu'_n}_{x_{z_1}}}$
* $z_1 = -\infty \implies \mathbf{u = 0}$
* $z_1 = +\infty \implies \mathbf{u = A_x}$
$$
G_2(l,v) = \frac{1}{A_x^{\Lambda(l) + \Lambda(v) + 1}} \int_{\mathbf{0}}^{\mathbf{A_x}} u^{\Lambda(l) + \Lambda(v)} \, \mathbf{du}
$$
$$
\begin{aligned}
G_2(l,v) &= \frac{1}{A_x^{\Lambda(l) + \Lambda(v) + 1}} \left[ \frac{u^{\Lambda(l) + \Lambda(v) + 1}}{\Lambda(l) + \Lambda(v) + 1} \right]_0^{A_x} \\
&= \frac{1}{A_x^{\Lambda(l) + \Lambda(v) + 1}} \left( \frac{A_x^{\Lambda(l) + \Lambda(v) + 1}}{\Lambda(l) + \Lambda(v) + 1} - 0 \right) \\
&= \mathbf{\frac{1}{1 + \Lambda(\mathbf{l}) + \Lambda(\mathbf{v})}}
\end{aligned}
$$
$$1 + \Lambda(\mathbf{l}) + \Lambda(\mathbf{v})=  \frac{\sqrt{1 + \alpha^2 \tan^2\theta_l} + \sqrt{1 + \alpha^2 \tan^2\theta_v}}{2}$$
- $\tan^2\theta = \frac{1 - \cos^2\theta}{\cos^2\theta}$
- $\cos\theta_l = (N \cdot L)$, $\cos\theta_v = (N \cdot V)$ 

$$G_2(\mathbf{l}, \mathbf{v}) = \frac{2}{\displaystyle \frac{\sqrt{\alpha^2 + \cos^2\theta_l(1 - \alpha^2)}}{\cos\theta_l} + \frac{\sqrt{\alpha^2 + \cos^2\theta_v(1 - \alpha^2)}}{\cos\theta_v}}$$

$$G_2(\mathbf{l}, \mathbf{v}) = \mathbf{\frac{2 \cos\theta_l \cos\theta_v}{\cos\theta_v \sqrt{\alpha^2 + \cos^2\theta_l(1 - \alpha^2)} + \cos\theta_l \sqrt{\alpha^2 + \cos^2\theta_v(1 - \alpha^2)}}}$$
$$G_2(\mathbf{l}, \mathbf{v}) = \mathbf{\frac{2(N \cdot L)(N \cdot V)}{(N \cdot V)\sqrt{\alpha^2 + (1 - \alpha^2)(N \cdot L)^2} + (N \cdot L)\sqrt{\alpha^2 + (1 - \alpha^2)(N \cdot V)^2}}}$$


최적화 방법과 수치적분을 정리한다.
언리얼의 환경광에 대해 공부한다.
Karis의 면광원 공식 에대해
면광원??? 흠냐리이이잉
알파가 0일때 디렉 델타가 분모로 나눠지는 이유?













# 최적화

$G_2$ 분모의 루트 식을 계산 효율을 위해 근사 한다 
$$f(x) = \sqrt{\alpha^2 + (1 - \alpha^2)x^2}$$

변곡점이 한 개임으로 직선의 방정식으로 근사한다.
직선 $ax + b$와 원래 곡선 $f(x)$ 사이의 전체 누적 오차는 다음과 같다.
$$S(a, b) = \int_{0}^{1} \Big( f(x)  - (ax + b) \Big)^2 dx$$
$$\frac{\partial S}{\partial a} = \int_{0}^{1} 2\Big( f(x) - (ax + b) \Big) \cdot (-x) \, dx = 0$$
$$\int_{0}^{1} x f(x) \, dx - a \int_{0}^{1} x^2 \, dx - b \int_{0}^{1} x \, dx = 0$$

* $\int_{0}^{1} x^2 \, dx = \left[ \frac{1}{3}x^3 \right]_0^1 = \mathbf{\frac{1}{3}}$
* $\int_{0}^{1} x \, dx = \left[ \frac{1}{2}x^2 \right]_0^1 = \mathbf{\frac{1}{2}}$

$$ 
I_1 = \int_{0}^{1} x f(x) \, dx = \frac{1}{3}a + \frac{1}{2}b 
$$
$$ I_1 = \int_{0}^{1} x \sqrt{\alpha^2 + (1 - \alpha^2)x^2} \, dx $$

$u = \alpha^2 + (1 - \alpha^2)x^2$
$du = 2(1 - \alpha^2)x \, dx$
$$ I_1 = \frac{1}{2(1 - \alpha^2)} \int_{\alpha^2}^{1} u^{1/2} \, du $$
$$ 
I_1 = \frac{1 - \alpha^3}{3(1 - \alpha^2)} = \frac{1 + \alpha + \alpha^2}{3(1 + \alpha)}
$$

$$\frac{\partial S}{\partial b} = \int_{0}^{1} 2\Big( f(x) - (ax + b) \Big) \cdot (-1) \, dx = 0$$
$$\int_{0}^{1} f(x) \, dx - a \int_{0}^{1} x \, dx - b \int_{0}^{1} 1 \, dx = 0$$
* $\int_{0}^{1} x \, dx = \mathbf{\frac{1}{2}}$
* $\int_{0}^{1} 1 \, dx = [x]_0^1 = \mathbf{1}$
$$
I_0 = \int_{0}^{1} f(x) \, dx = \frac{1}{2}a + b
$$

$$
I_0 = \int_{0}^{1} \sqrt{\alpha^2 + (1 - \alpha^2)x^2} \,dx
$$
- $u = \sqrt{1 - \alpha^2} \cdot x$
* $dx = \frac{du}{\sqrt{1 - \alpha^2}}$

$$ I_0 = \frac{1}{\sqrt{1 - \alpha^2}} \int_{0}^{\sqrt{1 - \alpha^2}} \sqrt{\alpha^2 + u^2} \, du = \frac{1}{2} + \frac{\alpha^2}{2\sqrt{1 - \alpha^2}} \ln\left( \frac{1 + \sqrt{1 - \alpha^2}}{\alpha} \right) $$
누적 오차가 최소가 되는 $(a,b)$ 는 다음과 같다.
$$ 
a = 12 I_1 - 6 I_0 = \frac{1 + \alpha + 4\alpha^2}{1 + \alpha} - \frac{3\alpha^2}{\sqrt{1 - \alpha^2}} \ln\left( \frac{1 + \sqrt{1 - \alpha^2}}{\alpha} \right)
$$
$$ 
b = 4 I_0 - 6 I_1 = -\frac{2\alpha^2}{1 + \alpha} + \frac{2\alpha^2}{\sqrt{1 - \alpha^2}} \ln\left( \frac{1 + \sqrt{1 - \alpha^2}}{\alpha} \right) 
$$

이를 계산 효율 편의를 위해 근사한다.
각각 변곡점이 0개 임으로, 직선으로 근사해도 좋지만 
$\alpha$의 경우 제곱 연산을 미리 계산해 둘 수 있음으로 오차를 줄이기 위해 
2차 다항식으로 근사한다.


근사식 $P_a(\alpha) = c_2\alpha^2 + c_1\alpha + c_0$  에 대하여

$$S(c_2, c_1, c_0) = \int_{0}^{1} \Big( a(\alpha) - (c_2\alpha^2 + c_1\alpha + c_0) \Big)^2 d\alpha$$

오차가 최소가 되기 위한 극소 조건은 각 계수에 대한 편미분이 $0$이어야 합니다.

$$\frac{\partial S}{\partial c_2} = 0, \quad \frac{\partial S}{\partial c_1} = 0, \quad \frac{\partial S}{\partial c_0} = 0$$

1. $\frac{\partial S}{\partial c_2} = \int_{0}^{1} 2\Big( a(\alpha) - c_2\alpha^2 - c_1\alpha - c_0 \Big)(-\alpha^2) \, d\alpha = 0$
2. $\frac{\partial S}{\partial c_1} = \int_{0}^{1} 2\Big( a(\alpha) - c_2\alpha^2 - c_1\alpha - c_0 \Big)(-\alpha) \, d\alpha = 0$
3. $\frac{\partial S}{\partial c_0} = \int_{0}^{1} 2\Big( a(\alpha) - c_2\alpha^2 - c_1\alpha - c_0 \Big)(-1) \, d\alpha = 0$

$$
\begin{pmatrix}
\int_{0}^{1} \alpha^4 \, d\alpha & \int_{0}^{1} \alpha^3 \, d\alpha & \int_{0}^{1} \alpha^2 \, d\alpha \\[6pt]
\int_{0}^{1} \alpha^3 \, d\alpha & \int_{0}^{1} \alpha^2 \, d\alpha & \int_{0}^{1} \alpha \, d\alpha \\[6pt]
\int_{0}^{1} \alpha^2 \, d\alpha & \int_{0}^{1} \alpha \, d\alpha & \int_{0}^{1} 1 \, d\alpha
\end{pmatrix}
\begin{pmatrix}
c_2 \\[6pt]
c_1 \\[6pt]
c_0
\end{pmatrix}
=
\begin{pmatrix}
\int_{0}^{1} \alpha^2 a(\alpha) \, d\alpha \\[6pt]
\int_{0}^{1} \alpha a(\alpha) \, d\alpha \\[6pt]
\int_{0}^{1} a(\alpha) \, d\alpha
\end{pmatrix}
$$

* $\int_0^1 \alpha^4 d\alpha = \frac{1}{5}, \quad \int_0^1 \alpha^3 d\alpha = \frac{1}{4}, \quad \int_0^1 \alpha^2 d\alpha = \frac{1}{3}, \quad \int_0^1 \alpha d\alpha = \frac{1}{2}, \quad \int_0^1 1 d\alpha = 1$
$$M = \begin{pmatrix} \frac{1}{5} & \frac{1}{4} & \frac{1}{3} \\[4pt] \frac{1}{4} & \frac{1}{3} & \frac{1}{2} \\[4pt] \frac{1}{3} & \frac{1}{2} & 1 \end{pmatrix}$$
$$M^{-1} = \begin{pmatrix} 180 & -180 & 30 \\ -180 & 192 & -36 \\ 30 & -36 & 9 \end{pmatrix}$$

수치 적분을 통해 우변의 값을 계산한다.

* $A_2 = \int_{0}^{1} \alpha^2 a(\alpha) \, d\alpha \approx \mathbf{0.0838}$
* $A_1 = \int_{0}^{1} \alpha a(\alpha) \, d\alpha \approx \mathbf{0.1731}$
* $A_0 = \int_{0}^{1} a(\alpha) \, d\alpha \approx \mathbf{0.5212}$

역행렬을 곱하여 계수를 구한다.
* $c_2 = 180(0.0838) - 180(0.1731) + 30(0.5212) = 15.084 - 31.158 + 15.636 = \mathbf{-0.108}$
* $c_1 = -180(0.0838) + 192(0.1731) - 36(0.5212) = -15.084 + 33.235 - 18.763 = \mathbf{-0.887}$
* $c_0 = 30(0.0838) - 36(0.1731) + 9(0.5212) = 2.514 - 6.232 + 4.691 = \mathbf{+0.996}$

$$\mathbf{a(\alpha) \approx -0.108\alpha^2 - 0.887\alpha + 0.996}$$
근사식 $P_b(\alpha) = d_2\alpha^2 + d_1\alpha + d_0$에 대하여
$$S(d_2, d_1, d_0) = \int_{0}^{1} \Big( b(\alpha) - (d_2\alpha^2 + d_1\alpha + d_0) \Big)^2 d\alpha$$
1. $\frac{\partial S}{\partial d_2} = \int_{0}^{1} 2\Big( b(\alpha) - d_2\alpha^2 - d_1\alpha - d_0 \Big)(-\alpha^2) \, d\alpha = 0$
2. $\frac{\partial S}{\partial d_1} = \int_{0}^{1} 2\Big( b(\alpha) - d_2\alpha^2 - d_1\alpha - d_0 \Big)(-\alpha) \, d\alpha = 0$
3. $\frac{\partial S}{\partial d_0} = \int_{0}^{1} 2\Big( b(\alpha) - d_2\alpha^2 - d_1\alpha - d_0 \Big)(-1) \, d\alpha = 0$

$$
\begin{pmatrix}
\int_{0}^{1} \alpha^4 \, d\alpha & \int_{0}^{1} \alpha^3 \, d\alpha & \int_{0}^{1} \alpha^2 \, d\alpha \\[6pt]
\int_{0}^{1} \alpha^3 \, d\alpha & \int_{0}^{1} \alpha^2 \, d\alpha & \int_{0}^{1} \alpha \, d\alpha \\[6pt]
\int_{0}^{1} \alpha^2 \, d\alpha & \int_{0}^{1} \alpha \, d\alpha & \int_{0}^{1} 1 \, d\alpha
\end{pmatrix}
\begin{pmatrix}
d_2 \\[6pt]
d_1 \\[6pt]
d_0
\end{pmatrix}
=
\begin{pmatrix}
\int_{0}^{1} \alpha^2 b(\alpha) \, d\alpha \\[6pt]
\int_{0}^{1} \alpha b(\alpha) \, d\alpha \\[6pt]
\int_{0}^{1} b(\alpha) \, d\alpha
\end{pmatrix}
$$

* $\int_0^1 \alpha^4 d\alpha = \frac{1}{5}, \quad \int_0^1 \alpha^3 d\alpha = \frac{1}{4}, \quad \int_0^1 \alpha^2 d\alpha = \frac{1}{3}, \quad \int_0^1 \alpha d\alpha = \frac{1}{2}, \quad \int_0^1 1 d\alpha = 1$

$$M = \begin{pmatrix} \frac{1}{5} & \frac{1}{4} & \frac{1}{3} \\[4pt] \frac{1}{4} & \frac{1}{3} & \frac{1}{2} \\[4pt] \frac{1}{3} & \frac{1}{2} & 1 \end{pmatrix}$$

$$M^{-1} = \begin{pmatrix} 180 & -180 & 30 \\ -180 & 192 & -36 \\ 30 & -36 & 9 \end{pmatrix}$$

수치 적분을 통해 우변의 값을 계산한다.

* $B_2 = \int_{0}^{1} \alpha^2 b(\alpha) \, d\alpha \approx \mathbf{0.2495}$
* $B_1 = \int_{0}^{1} \alpha b(\alpha) \, d\alpha \approx \mathbf{0.3269}$
* $B_0 = \int_{0}^{1} b(\alpha) \, d\alpha \approx \mathbf{0.4788}$

역행렬을 곱하여 계수를 구한다.

* $d_2 = 180(0.2495) - 180(0.3269) + 30(0.4788) = 44.910 - 58.842 + 14.364 = \mathbf{+0.285}$
* $d_1 = -180(0.2495) + 192(0.3269) - 36(0.4788) = -44.910 + 62.765 - 17.237 = \mathbf{+0.709}$
* $d_0 = 30(0.2495) - 36(0.3269) + 9(0.4788) = 7.485 - 11.768 + 4.309 = \mathbf{+0.006}$

$$\mathbf{b(\alpha) \approx 0.285\alpha^2 + 0.709\alpha + 0.006}$$
$$f(x) \approx \mathbf{(-0.108\alpha^2 - 0.887\alpha + 0.996)x + (0.285\alpha^2 + 0.709\alpha + 0.006)}$$