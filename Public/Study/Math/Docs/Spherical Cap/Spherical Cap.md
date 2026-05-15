---
---


# 원뿔의 옆 넓이
![](/Public/Study/Math/Docs/Spherical%20Cap/Resource/Pasted%20image%2020260704203833.png)
원뿔에서 윗부분을 잘라낸 원뿔대의 옆넓이 공식은 
큰 부채꼴 넓이에서 작은 부채꼴을 뺀 것과 같음으로

- (R) 원뿔대 아랫면(큰 원) 반지름
- (l) 원뿔대의 모선 길이 
- (r) 원뿔대 윗면(작은 원) 반지름
- (x) 잘려 나간 작은 원뿔의 모선 길이

닯음에 의해 
$\frac{r}{R} = \frac{x}{x+l}$
$x = \frac{rl}{R-r}$

원뿔의 옆면 넓이 : $\frac{1}{2}\times (x+l) 2 \pi R = \pi R(x+l)$
작은 원뿔의 옆면 넓이 : $\frac{1}{2}\times x 2 \pi r = \pi rx$
이 둘을 뺀 $\pi (R - r)x  + \pi Rl$ 식에 x 식을 정리하면
$$\pi (r+R)l$$


# Archimedes' Hat-Box Theorem

![](/Public/Study/Math/Docs/Spherical%20Cap/Resource/Pasted%20image%2020260704204342.png)

구를 완전히 감싸는 원통의 옆 넓이를 생각해보자. 높이는 $2r$, 둘레는 $2\pi r$, 따라서 넓이는 
$S_2 = 4 \pi r^2$ 이는 구의 면적과 같다.

이처럼 임의의 높이로 자른 경우, 원기둥 슬라이스의 옆넓이와 구 슬라이스의 겉 옆넓이는 같다.

![](/Public/Study/Math/Docs/Spherical%20Cap/Resource/Pasted%20image%2020260704211508.png)

#### 1. 기호 정의
*   $R$: 구의 반지름
*   $\overline{RP}$: 자른 구간의 높이 ($h$)
*   $\overline{SP}$: 원뿔대의 모선 길이 (현의 길이)
*   $\overline{QP}, \overline{TS}$: 원뿔대의 아랫면 및 윗면 반지름

#### 2. 닮음비 유도
두 직각삼각형의 닮음 관계($\triangle OQP \sim \triangle RSP$)에 의해 다음이 성립합니다.

$$\frac{\overline{RP}}{\overline{SP}} = \frac{\overline{QP}}{R} \implies \overline{SP} = \frac{\overline{RP}}{\overline{QP}} R$$

#### 3. 겉넓이 비교

**① 구 슬라이스 넓이 ($S_1$)**
원뿔대 옆넓이 공식에 위에서 구한 $\overline{SP}$를 대입합니다.

$$S_1 \approx \pi (\overline{TS} + \overline{QP}) \overline{SP} = \pi (\overline{TS} + \overline{QP}) \frac{\overline{RP}}{\overline{QP}} R$$

**② 극한 적용 ($\overline{RP} \to 0$)**
슬라이스 높이가 줄어들면 윗면 반지름은 아랫면 반지름과 같아집니다 ($\overline{TS} \to \overline{QP}$).

$$S_1 \to \pi (2\overline{QP}) \frac{\overline{RP}}{\overline{QP}} R = 2\pi R \overline{RP}$$

**③ 원기둥 슬라이스 넓이 ($S_2$)**
반지름 $R$, 높이 $\overline{RP}$인 원기둥 영역의 옆넓이는 다음과 같습니다.

$$S_2 = 2\pi R \overline{RP}$$

구와 원기둥의 슬라이스 넓이는 서로 같습니다. ($S_1 = S_2$)

![](/Public/Study/Math/Docs/Spherical%20Cap/Resource/Pasted%20image%2020260704191312.png)


# Spherical Cap

$h = r - r \cos\theta = r(1 - \cos\theta)$
구대의 넓이는 다음과 같다.
$A=2πrh$

이를 입체각([각도](/Public/Study/Math/Docs/각도/각도))으로 나타내면
$\Omega =\frac{2\pi r^2 (1 - \cos\theta)}{r^2}​= ​2\pi(1 - \cos\theta)$