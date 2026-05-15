---
---

퍼포스 서버가 유니코드가 아닌 경우 클라이언트의 요청을 해석 못할때 이와 같은 오류가 발생한다.
해결 방법은 다음과 같다.

`p4 set -S Perforce `  : P4ROOT 경로를 확인한다.
`net stop Perforce`  : 서버를 종료한다
`cd "<P4ROOT>\Server"`
.\p4d.exe -r "C:\MyFolder\Perforce" -xi`: 유니코드로 서버를 전환한다.
`net start Perforce`  : 서버를 시작한다
`p4 set -s P4CHARSET=utf8`  : 클라이언트의 문자를 유니코드로 전환한다.


