---
---

$$\int_{a}^{b} f(x) \, dx = \int_{a}^{b} f(a + b - x) \, dx$$

$u = a + b - x$
$du = -dx$


$x = a$ 일 때 $u = a + b - a = \mathbf{b}$
$x = b$ 일 때 $u = a + b - b = \mathbf{a}$

   $$\int_{a}^{b} f(x) \, dx = \int_{\mathbf{b}}^{\mathbf{a}} f(a + b - u) \mathbf{(-du)}= \int_{a}^{b} f(a + b - u) \, du$$


---

#### 활용

$$I = \int_{0}^{\frac{\pi}{2}} \frac{e^{\sin x}}{e^{\sin x} + e^{\cos x}} \, dx$$


$u = 0 + \frac{\pi}{2} - x$ 
$$I = \int_{0}^{\frac{\pi}{2}} \frac{e^{\cos x}}{e^{\cos x} + e^{\sin x}} \, dx$$

$$2I = \int_{0}^{\frac{\pi}{2}} \frac{e^{\sin x} + e^{\cos x}}{e^{\sin x} + e^{\cos x}} \, dx$$

$$2I = \int_{0}^{\frac{\pi}{2}} 1 \, dx = \frac{\pi}{2}$$
$$\therefore I = \frac{\pi}{4}$$
