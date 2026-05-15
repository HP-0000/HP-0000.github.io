
**러셀의 역설**
러셀은 임의의 집합을  단순하게 말로 정의하는 것이 문제가 있음을 보였다.
$$R = \{ x \mid x \notin x \}$$
$R$ 집합을 자신의 원소가 아닌 원소 집합으로 두면 정의 되는 순간 
정의를 위반하게 됨으로 모순이 발생한다.

이에 따라 집합을 안전하고 엄밀하게 구성하는 형식 체계인 ZFC 공리계가 확립되었다

--- 

# 기본 공리

###### 외연 공리 (Axiom of Extensionality)
$$\forall A \, \forall B \, \Big[ \forall x \, (x \in A \iff x \in B) \implies A = B \Big]$$

---

###### 공집합 공리 (Axiom of Empty Set)
$$\exists! E \, \forall x \, (x \notin E)$$
- $\emptyset := E$
- $\exists!$ : 유일하게 존재함 (Unique existence)

---
###### 짝 공리 (Axiom of Pairing)
$$\forall a \, \forall b \, \exists! C \, \forall x \, \Big[ x \in C \iff (x = a \lor x = b) \Big]$$
- $\{a, b\} := C$
- $\{a\} := \{a, a\}$

---

###### 합집합 공리 (Axiom of Union)
$$\forall \mathcal{F} \, \exists! U \, \forall x \, \Big[ x \in U \iff \exists Y \, (Y \in \mathcal{F} \land x \in Y) \Big]$$
- $\bigcup \mathcal{F} := U$
- $A \cup B := \bigcup \{A, B\}$

---

###### 분리 공리꼴 (Axiom Schema of Separation)
$$\forall A \, \exists! B \, \forall x \, \Big[ x \in B \iff (x \in A \land \varphi(x)) \Big]$$
- $\{x \in A \mid \varphi(x)\} := B$
- $A \cap B := \{x \in A \mid x \in B\}$
- $A \setminus B := \{x \in A \mid x \notin B\}$
 - $\bigcap \mathcal{F} := \Big\{ x \in A_0 \;\Big|\; \forall Y \in \mathcal{F} \, (x \in Y) \Big\} \quad (A_0 \in \mathcal{F} \neq \emptyset)$


---
###### 정칙성 공리 (Axiom of Regularity)
$$\forall A \, \Big[ A \neq \emptyset \implies \exists x \in A \; \big( x \cap A = \emptyset \big) \Big]$$

---

# 무한 관련 공리

공리를 유한하게 전개하여 결론을 얻는 과정을 증명이라고 한다.
기본 공리로 증명되는 집합은 유한 집합이다.

유한한 증명 체계 안에서 무한 집합으로 확장하기 위해
아래의 공리가 도입 되었다.

---
###### 무한 공리 (Axiom of Infinity)
$$\exists I \, \Big[ \emptyset \in I \;\land\; \forall y \, (y \in I \implies y \cup \{y\} \in I) \Big]$$
---
###### 멱집합 공리 (Axiom of Power Set)
$$\forall A \, \exists! P \, \forall x \, \Big[ x \in P \iff \forall y \, (y \in x \implies y \in A) \Big]$$
- $\mathcal{P}(A) := P$
- $x \subseteq A :\iff \forall y \, (y \in x \implies y \in A)$

---
###### 치환 공리꼴 (Axiom Schema of Replacement)
$$\forall A \, \exists! B \, \forall y \, \Big[ y \in B \iff \exists x \in A \quad (y = F(x)) \Big]$$
- $\{F(x) \mid x \in A\} := B$

---
###### 선택 공리 (Axiom of Choice, AC)
$$\forall X \, \Bigg[ (\emptyset \notin X) \;\land\; \Big( \forall A, B \in X \; (A \neq B \implies A \cap B = \emptyset) \Big) \implies \exists C \, \forall Y \in X \; \exists! x \, (x \in Y \cap C) \Bigg]$$