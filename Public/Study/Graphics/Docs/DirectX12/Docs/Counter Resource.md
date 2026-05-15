---
---

4바이트의 UAV buffer 리소스다.

버퍼를 다음의 타입으로 사용할 때
```
AppendStructuredBuffer<T>
ConsumeStructuredBuffer<T>
```
내부적으로 카운터 리소스를 사용해 offset 원자적 계산을 은닉화 해주어서
Append, Cosume 명령어만 간단하게 호출할 수 있게 한다.



