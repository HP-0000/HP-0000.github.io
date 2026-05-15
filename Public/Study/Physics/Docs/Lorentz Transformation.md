---
---













속도는 단위 시간 당 이동한 거리를 의미한다.

원점에서 




































관성계 $S'$의 원점을 $S$로 평행 이동 후 강체 회전을 통해 축을 일치 시키면 
동일한 관성계가 된다.
- 관성계 : 정지해 있거나 등 속도 운동하는 좌표계

$S'$는 $x$축 방향으로 빛의 속도 $c$와 근접한 속도 $v$로 움직이고 있다.

두 가지 특수한 가정을 하자.
- 상대성 원리 : 모든 관성계의 물리 법칙이 동일하다.
- 광속 불변의 법칙 : 빛의 속도는 관찰자와 상관없이 동일하다 

광속이 불변하지 않는다면, 





$S$ 입장에서 $t$ 시간 뒤 원점으로부터 $S'$ 좌표계에서 빛의 속도 $c$와 근접한 
$v'_\mu$ 속도로 움직이는 점을 관찰할 때 그 속도가 $(v'_\mu + v)$ 즉  $2c$ 에 가까울 것 같지만,
앞서 특수한 가정에 의해서는 여전히 $c$에 가까운 속도로 보여야 한다. 
속도의 정의는 다음과 같았다.
$$\text{속도} = \frac{\text{거리}}{\text{시간}}$$
이는 $S$ 가 관찰한 $S'$의 거리는 수축하고 시간은 팽창 해야 한다는 결론이 나온다.
관성계가 동등함으로 역으로 생각하면 $S'$가 관찰한 $S$ 는 거리는 수축하고 시간은 팽창해야한다.















$S`$ 좌표계의 정지한 점 $x'$ 는 $S$ 의 $x$ 기준으로 

이때 $S'$가 $S$에 대해 $x$축 방향으로 속도 $v$로 움직일 때, 
$t = t'$ 라면, 
$S$ 좌표계의 점을 $S'$ 좌표계로 변환하면  $x' = (x - vt)$ 
$S`$ 좌표계의 점을 $S$ 좌표계로 변환하면$x = (x' + vt')$ 

그러나 실제로는 관성계는 저마다의 
이때 $S'$가 $S$에 대해 $x$축 방향으로 빛의 속도 $c$에 가깝게 움직일 때, 
$t \neq t'$ 가 되며 

시공간이 균일하다는 조건 하에  $S$와 $S'$ 사이의 좌표 변환은 선형 변환으로 정의된다 
$$x' = Ax + Bt \quad$$
$$t' = Cx + Dt \quad$$

* $S'$의 원점($x' = 0$)은 $S$에서 $x = vt$로 움직인다.
  $$0 = A(vt) + Bt$$
  $$\mathbf{x' = A(x - vt)}$$

원점에서 방출된 양방향 빛을 고려합니다.

* **$+x$ 방향 ($x=ct, x'=ct'$):**
  $$ct' = A(c-v)t \implies t' = A\left(1 - \frac{v}{c}\right)t$$
  $$A\left(1 - \frac{v}{c}\right) = Cc + D$$

* **$-x$ 방향 ($x=-ct, x'=-ct'$):**
  $$-ct' = A(-c-v)t \implies t' = A\left(1 + \frac{v}{c}\right)t$$
  $$A\left(1 + \frac{v}{c}\right) = -Cc + D$$

* **두 식 연립:**
  * **합:** $2A = 2D \implies \mathbf{D = A}$
  * **차:** $2A\frac{v}{c} = -2Cc \implies \mathbf{C = -\frac{Av}{c^2}}$
* **시간 변환식 정리:**
  $$\mathbf{t' = A\left(t - \frac{vx}{c^2}\right)} \quad \text{--- (6)}$$

* 두 관성계는 동등하므로 역변환은 속도 부호만 반대($-v$)다.
  $$x = A(x' + vt')$$
  $$x = A \left[ A(x-vt) + vA\left(t - \frac{vx}{c^2}\right) \right]$$
  $$x = A^2 \left( x - vt + vt - \frac{v^2}{c^2}x \right)$$
  $$x = A^2 x \left( 1 - \frac{v^2}{c^2} \right)$$
  $$1 = A^2 \left( 1 - \frac{v^2}{c^2} \right) \implies \mathbf{A = \frac{1}{\sqrt{1 - \frac{v^2}{c^2}}} = \gamma}$$

### 최종 결과

$$x' = \gamma (x - vt)$$
$$t' = \gamma \left(t - \frac{vx}{c^2}\right)$$

$$x = \gamma (x' + vt')$$
$$t = \gamma \left(t' + \frac{vx'}{c^2}\right)$$

$$\gamma = \frac{1}{\sqrt{1 - \frac{v^2}{c^2}}}$$


---

아래 식에 대입해보면
$$(ct')^2 - x'^2$$

$$(ct')^2 - x'^2 = \left[ \gamma\left(ct - \frac{vx}{c}\right) \right]^2 - \left[ \gamma(x - vt) \right]^2$$
$$= \gamma^2 \left[ \left(ct - \frac{vx}{c}\right)^2 - (x - vt)^2 \right]$$

$$= \gamma^2 \left[ \left( c^2 t^2 - 2vxt + \frac{v^2 x^2}{c^2} \right) - \left( x^2 - 2vxt + v^2 t^2 \right) \right]$$

$$= \gamma^2 \left[ c^2 t^2 - v^2 t^2 - x^2 + \frac{v^2 x^2}{c^2} \right]$$

$$= \gamma^2 \left[ t^2 (c^2 - v^2) - x^2 \left( 1 - \frac{v^2}{c^2} \right) \right]$$

$$= \gamma^2 \left[ c^2 t^2 \left( 1 - \frac{v^2}{c^2} \right) - x^2 \left( 1 - \frac{v^2}{c^2} \right) \right]$$
$$= \gamma^2 \left( 1 - \frac{v^2}{c^2} \right) \left[ c^2 t^2 - x^2 \right]$$

$$= 1 \cdot \left[ (ct)^2 - x^2 \right] = (ct)^2 - x^2$$

$$(ct')^2 - x'^2 = (ct)^2 - x^2$$
