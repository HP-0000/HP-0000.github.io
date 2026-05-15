---
---

# GGX

###### Tangent Space
미소 표면을 $X$,$Y$ , 그 법선을 $Z$ 로 하는 좌표계

탄젠트 공간에서 단위 법선 벡터 $\vec{h}$를 구면 좌표계로 표현하면 다음과 같다. 
- $h_x = \sin\theta_m \cos\phi_m$  (X 성분)
- $h_y = \sin\theta_m \sin\phi_m$  (Y 성분)
- $h_z = \cos\theta_m$  (Z 성분)
* $\theta_m$ : 미소 표면 법선 $\vec{n}$과 미세 거울 법선 $\vec{h}$ 사이각 $\left(0 \le \theta_m < \frac{\pi}{2}\right)$

경사 공간 확률 밀도 함수를 $P(s_x,s_y)$로 정의하자.
$s_x = \frac{h_x}{h_z} = \tan\theta_m \cos\phi_m$
$s_y = \frac{h_y}{h_z} = \tan\theta_m \sin\phi_m$

함수 값은 $h(s_x,s_y)$가 존재할 확률이다.
$$
\iint_{-\infty}^{\infty}P(s_x,s_y) \, ds_x \, ds_y = 1
$$

현실의 물질 표면에 존재하는 미세 거울들의 경사각 확률 분포 그래프를 
근사하는 함수를 찾았더니 아래와 같았다.

$$f(\mathbf{h}) = \frac{1}{(s_x^2 + s_y^2 + \alpha^2)^2}$$
- $\alpha$ : Roughness
- 각 법선의 확률은 대칭성을 만족 

$$P(s_x,s_y) = C \times f(\mathbf{h})$$ 

$$
\iint_{-\infty}^{\infty}C \times \frac{1}{(s_x^2 +s_y^2 + \alpha^2)^2} \, ds_x \, ds_y = 1
$$


$$
C \int_{-\infty}^{\infty} \left[ \int_{-\infty}^{\infty} \frac{1}{(s_y^2 + (s_x^2 +\alpha^2))^2} \, ds_y \right] \, ds_x = 1.0
$$

$\int_{-\infty}^{\infty} \frac{1}{(x^2 + a^2)^2} \, dx = \mathbf{\frac{\pi}{2 a^3}}$
임으로 
$$
\int_{-\infty}^{\infty} \frac{1}{(s_y^2 + (s_x^2 + \alpha^2))^2} \, ds_y = \mathbf{\frac{\pi}{2 (s_x^2 + \alpha^2)^{3/2}}}
$$


$\int_{-\infty}^{\infty} \frac{1}{(x^2 + a^2)^{3/2}} \, dx = \mathbf{\frac{2}{a^2}}$
임으로

$$
C \int_{-\infty}^{\infty} \frac{\pi}{2 (s_x^2 + \alpha^2)^{3/2}} \, ds_x = C \cdot \frac{\pi}{2} \left[ \int_{-\infty}^{\infty} \frac{1}{(s_x^2 + \alpha^2)^{3/2}} \, ds_x \right]
= C \cdot \frac{\pi}{2} \cdot \mathbf{\frac{2}{\alpha^2}} = \mathbf{C \cdot \frac{\pi}{\alpha^2}}
$$

$\therefore C = \frac{\alpha^2}{\pi}$

$$P(s_x,s_y) = \frac{\alpha^2}{\pi(s_x^2 + s_y^2 + \alpha^2)^2}$$
---

### GTR2 (GGX)
자코비안 행렬식을 구한다.
[미분](/Public/Study/Math/Docs/미적분/미분)
* 입력 벡터: $(\theta_m, \phi_m)$
* 출력 벡터 $(s_x, s_y)$
$$
J = \begin{pmatrix} \frac{\partial s_x}{\partial \theta_m} & \frac{\partial s_x}{\partial \phi_m} \\ \frac{\partial s_y}{\partial \theta_m} & \frac{\partial s_y}{\partial \phi_m} \end{pmatrix}
$$

 $\frac{d}{d\theta_m}\tan\theta_m = \sec^2\theta_m = \frac{1}{\cos^2\theta_m}$)

1. $\frac{\partial s_x}{\partial \theta} = \frac{\partial}{\partial \theta}(\tan\theta \cos\phi) = \mathbf{\frac{\cos\phi}{\cos^2\theta}}$
2. $\frac{\partial s_x}{\partial \phi} = \frac{\partial}{\partial \phi}(\tan\theta \cos\phi) = \mathbf{-\tan\theta \sin\phi}$
3. $\frac{\partial s_y}{\partial \theta} = \frac{\partial}{\partial \theta}(\tan\theta \sin\phi) = \mathbf{\frac{\sin\phi}{\cos^2\theta}}$
4. $\frac{\partial s_y}{\partial \phi} = \frac{\partial}{\partial \phi}(\tan\theta \sin\phi) = \mathbf{\tan\theta \cos\phi}$

$$
J = \begin{pmatrix} \frac{\cos\phi_m}{\cos^2\theta_m} & -\tan\theta_m \sin\phi_m \\ \frac{\sin\phi_m}{\cos^2\theta_m} & \tan\theta_m \cos\phi_m \end{pmatrix}
$$

$\det \begin{pmatrix} a & b \\ c & d \end{pmatrix} = ad - bc$ 적용

$$
\det(J) = \left( \frac{\cos\phi_m}{\cos^2\theta_m} \right) (\tan\theta_m \cos\phi_m) - \left( -\tan\theta_m \sin\phi_m \right) \left( \frac{\sin\phi_m}{\cos^2\theta_m} \right)
$$

$$
\det(J) = \frac{\tan\theta_m \cos^2\phi_m}{\cos^2\theta_m} + \frac{\tan\theta _m\sin^2\phi_m}{\cos^2\theta_m}
$$

$$
\det(J) = \frac{\tan\theta_m}{\cos^2\theta_m} (\cos^2\phi_m + \sin^2\phi_m) = \frac{\tan\theta_m}{\cos^2\theta_m}
$$

$$
\det(J) = \mathbf{\frac{\sin\theta_m}{\cos^3\theta_m}}
$$
$$
ds_x \, ds_y =  |\det(J)| \, d\theta_m \, d\phi_m = \frac{1}{\cos^3\theta_m} \cdot \mathbf{(\sin\theta_m \, d\theta_m \, d\phi_m)} = \frac{d\Omega^\vec{h}}{\cos^3\theta}
$$
$$
\mathbf{d\Omega^\vec{h} = \cos^3\theta_m \, ds_x \, ds_y}
$$

$\vec{h}$ 법선을 갖는 미세 거울의 총 면적을 $dA^{\mu',h}_x$ 라고 하자.
$$
P(s_x,s_y) \, ds_x \, ds_y = \frac{dA^{\mu',h}_x(n \cdot h)}{dA_x}
= \frac{dA^{\mu',h}_x\cos\theta_m}{dA_x}
$$
$$
\frac{P(s_x,s_y) \, ds_x \, ds_y}{d\Omega^\vec{h}}
= \frac{dA^{\mu',h}_x\cos\theta_m}{dA_x d\Omega^\vec{h}}
$$
$$D(h) = \frac{dA^{\mu',h}_x}{dA_x d\Omega^\vec{h}} = \frac{P(s_x, s_y)}{\cos^4\theta_m} $$
$$
\iint_{-\infty}^{\infty}P(s_x,s_y) \, ds_x \, ds_y 
= \int_{\vec{n} \cdot \vec{h} > 0} \frac{P(s_x, s_y)}{\cos^3\theta_m} \, d\Omega^\vec{h} = 1
$$

$$
\int_{\Omega} D(h)(n \cdot h) \, d\Omega^\vec{h} = 1  
$$
---

$s_x^2 + s_y^2  = \tan^2\theta_m$
$\tan^2\theta_m = \frac{\sin^2\theta_m}{\cos^2\theta_m} = \frac{1 - \cos^2\theta_m}{\cos^2\theta_m}$

$$
\begin{aligned}
(s_x^2 + s_y^2 + \alpha^2) &= \left(\frac{1 - \cos^2\theta_m}{\cos^2\theta_m} + \alpha^2\right) \\
&= \frac{1 - \cos^2\theta_m + \alpha^2\cos^2\theta_m}{\cos^2\theta_m} \\
&= \frac{1 + (\alpha^2 - 1)\cos^2\theta_m}{\cos^2\theta_m}
\end{aligned}
$$

$$(s_x^2 + s_y^2 + \alpha^2)^2 = \frac{\left(1 + (\alpha^2 - 1)\cos^2\theta_m\right)^2}{\cos^4\theta_m}$$


$$P(s_x, s_y) = \frac{\alpha^2 \cos^4\theta_m}{\pi \left(1 + (\alpha^2 - 1)\cos^2\theta_m\right)^2}$$

$$D(h) = \frac{\alpha^2}{\pi (1 + (\alpha^2 - 1)(\vec{n} \cdot \vec{h})^2)^2}$$
