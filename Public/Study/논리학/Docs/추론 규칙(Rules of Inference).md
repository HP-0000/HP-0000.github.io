---
---
# 연역

참으로 주어진 전제들로부터 논리적 규칙만을 사용하여, 
반드시 참일 수밖에 없는 결론을 도출해 내는 사유 방식을 말한다.

# 자연 연역(Natural Deduction) 

### 1. 연언 ($\land$) 규칙
*   **연언 도입 ($\land I$)**
    *   입력: $P$, $Q$
    *   출력: $P \land Q$
*   **연언 제거 ($\land E$)**
    *   입력: $P \land Q$
    *   출력: $P$ (또는 $Q$)

### 2. 선언 ($\lor$) 규칙
*   **선언 도입 ($\lor I$)**
    *   입력: $P$
    *   출력: $P \lor Q$ (임의의 $Q$)
*   **선언 제거 ($\lor E$)**
    *   입력: $P \lor Q$가 참이고, 임시 가정 $[P]$ 그리고 $[Q]$로부터 동시에 $R$이 도출된다면,
    *   출력: $R$ (가정 $P$와 $Q$는 해제됨)

### 3. 조건문 ($\rightarrow$) 규칙
*   **조건문 도입 ($\rightarrow I$, 조건문 증명법)**
    *   입력: 임시 가정 $[P]$로부터 $Q$가 도출된다면,
    *   결론: $P \rightarrow Q$ (가정 $P$는 해제됨)
*   **조건문 제거 ($\rightarrow E$, 전건긍정식)**
    *   입력: $P \rightarrow Q$, $P$
    *   출력: $Q$

### 4. 부정 ($\neg$) 규칙
*   **부정 도입 ($\neg I$, 귀류법)**
    *   입력: 임시 가정 $[P]$로부터 모순($\bot$)이 도출된다면,
    *   출력: $\neg P$ (가정 $P$는 해제됨)
*   **부정 제거 ($\neg E$, 이중부정 제거)**
    *   입력: $\neg\neg P$
    *   출력: $P$

### 5. 쌍조건문 ($\leftrightarrow$) 규칙
*   **쌍조건문 도입 ($\leftrightarrow I$)**
    *   입력: $P \rightarrow Q$, $Q \rightarrow P$
    *   출력: $P \leftrightarrow Q$
*   **쌍조건문 제거 ($\leftrightarrow E$)**
    *   입력: $P \leftrightarrow Q$
    *   출력: $P \rightarrow Q$ (또는 $Q \rightarrow P$)

# 10가지 동치 규칙 (Rules of Replacement)

1. **드모르간의 법칙 (De Morgan's Laws, DM)**
   * $\neg(P \land Q) \equiv \neg P \lor \neg Q$
   * $\neg(P \lor Q) \equiv \neg P \land \neg Q$
2. **교환법칙 (Commutation, Com)**
   * $(P \lor Q) \equiv (Q \lor P)$
   * $(P \land Q) \equiv (Q \land P)$
3. **결합법칙 (Association, Assoc)**
   * $[P \lor (Q \lor R)] \equiv [(P \lor Q) \lor R]$
   * $[P \land (Q \land R)] \equiv [(P \land Q) \land R]$
4. **분배법칙 (Distribution, Dist)**
   * $[P \land (Q \lor R)] \equiv [(P \land Q) \lor (P \land R)]$
   * $[P \lor (Q \land R)] \equiv [(P \lor Q) \land (P \lor R)]$
5. **이중부정 (Double Negation, DN)**
   * $P \equiv \neg\neg P$
6. **이위법칙 / 대우 (Transposition, Trans)**
   * $(P \rightarrow Q) \equiv (\neg Q \rightarrow \neg P)$
7. **실질함의 (Material Implication, Impl)**
   * $(P \rightarrow Q) \equiv (\neg P \lor Q)$
8. **실질동치 (Material Equivalence, Equiv)**
   * $(P \leftrightarrow Q) \equiv [(P \rightarrow Q) \land (Q \rightarrow P)]$
   * $(P \leftrightarrow Q) \equiv [(P \land Q) \lor (\neg P \land \neg Q)]$
9. **수출입법칙 (Exportation, Exp)**
   * $[(P \land Q) \rightarrow R] \equiv [P \rightarrow (Q \rightarrow R)]$
10. **멱등법칙 / 항진법칙 (Tautology, Taut)**
    * $P \equiv (P \lor P)$
    * $P \equiv (P \land P)$

---

