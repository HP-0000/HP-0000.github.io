---
---


P4 Admin 에서 다음과 같이 유저를 생성하였다.

[관리자 - SuperUser 권한 부여] : ServerMaster 

[협업 - Write 권한 부여] : Master , HP  


[P4 Admin - ServerMaster]

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250903134725.png)
또한 Depot을 Stream Type으로 생성하였다.
이는 프로젝트의 메인 저장소이며 수직으로  부모 - 자식 Stream을 생성해 
분기를 만들고 불안정한 자식 Stream을 안정한 부모 Stream로 통합하며 개발을 진행한다.

Depot은 P4 Admin 에서만 삭제가 가능하며 Obliterate Files (히스토리 삭제) 이후 Delete 할 수 있다.

[P4V - Master]

MainLine Stream을 생성한다.
해당 Stream에 대한 WorkSpace를 생성한다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904124511.png)

WorkSpace는 서버로부터 파일을 받을 로컬 저장소이다. 
삭제는  WorkSpace -> Stream -> Depot 순으로 해야 한다.


![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904114914.png)

다음과 같이 파일을 생성하고 Add를 누른다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904125325.png)

빨간 십자가로 Add 표시가 뜨고 ChangeList 가 Pending 목록에 추가된 것이 보인다.
기본적으로 파일이 변경되면 Default ChangeList 에 묶인다.
원한다면 새 ChangeList를 만들어 선별적으로 변경 파일을 묶어 서버에 제출할 수 있다.

Submit 한다.
![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904125926.png)

Depot에 변경 파일 및 History가 추가되었다.
파일의 저장된 상태를 Revision이라고 한다.
파일 옆에 표시된 숫자는 각각 [현재/최신] Revision 을 보인다.

Histroy에서 Undo Revision을 클릭해보겠다. 
특정 Revision으로 되돌리며 서버에 반영되며 이력이 남는다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904131214.png)


Histroy에서 Get This Revision을 통해 파일을 추가했던 시점을 가져와 보겠다.
로컬 작업 공간을 특정 Revision으로 되돌리며 Depot에는 영향을 주지 않는다.


![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904131530.png)

파일을 보면 최신 Revision은 2 이며 현재 보고 있는 버전은 1임을 알 수 있다.
Depot에는 변화 없이 파일이 삭제된 상태이다.
노란색 세모 표시가 뜨고 이는 Get Latest를 통해 최신 리비전으로 파일이 동기화 될 필요가 있음을 나타낸다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904132209.png)

파일 복구한다는 느낌으로 Add 후 Submit 해서 서버에 반영해보았다.


[P4V - HP]

MainLine Stream의 자식으로 Development Stream을 생성한다.
해당 Stream에 대한 WorkSpace를 생성한다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904134624.png)

리비전 기록은 자식 스트림에서 새로이 시작하는 것으로 보인다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904135309.png)
Checkout 을 하면 빨간 체크 마크가 생긴다. 
이는 파일 속성을 읽기 전용에서 쓰기 권한을 설정하는 것으로  
원치 않는 파일 수정을 막기 위한 중간 단계라고 생각한다.

Checkout하지 않고 직접 파일을 덮어쓰자, 퍼포스는 로컬 변경 사항을 알아차리지 못했다.
이때 Reconcile Offline Work를 실행하면 정상적으로 수정된 파일들을 Checkout 상태로 만들어준다.


[Master] 역시 부모 스트림의 파일을 수정했다고 하자.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904143733.png)

Stream 그래프를 확인하면 화살표에 불이 들어와있다. 부모의 수정사항을 Merge 하고 Copy Up 할 작업이 있다는 것이다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904144254.png)

Pending List 에 Merge 작업이 추가되었다. 빨간 물음표는 충돌이 났음을 의미한다.
Resolve를 눌러 충돌을 해결해야한다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904150116.png)


- ![파일1](https://help.perforce.com/helix-core/server-apps/p4merge/current/Content/Images/p4merge-file1_15x15.png) :  다른 사용자가 수정한 상대방 파일과 연결됩니다. 
- ![파일2](https://help.perforce.com/helix-core/server-apps/p4merge/current/Content/Images/p4merge-file2_15x15.png) : 사용자가 수정한 내 파일과 연결됩니다. 
- ![기본 파일](https://help.perforce.com/helix-core/server-apps/p4merge/current/Content/Images/p4merge-basefile_15x15.png) : 기준 파일과 연결됩니다. 


자 그냥 텍스트를 다 합치는 것으로 충돌을 해결하였다.
![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904150019.png)

[Master] 쪽에서도 CopyFiles 한 뒤 Pending 에 추가된 작업을 Submit 해주자.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904150621.png)
올바르게 스트림 작업이 완료되었다.

이번엔 [Master] 가 [HP] 와 같은 스트림에서 작업한다고 가정하고
Feature 스트림의 워크 스페이스를 하나 만든다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904152012.png)


[Master]가 Lock을 걸었다. 

[Master]의 화면에서는 왼쪽 잠긴 좌물쇠가 보이고
[HP]는 오른쪽에 좌물쇠가 잠긴것을 확인 할 수 있다.
![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904152200.png)
[HP]는 해당 파일을 Lock 할 수도, 수정사항에 대해 Submit 할 수도 없다.






Shelve 하면 수정 사항이 PendingList 에 추가된다.

![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904154408.png)
이는 히스토리에 남지 않는 임시 리비전을 만들어 사용하는 것으로 임시 작업을 유용하게 할 수 있다.

또한 [Master] 는 Pending 에서 [HP] 의 Shelve 목록을 자유롭게 Unshelve 할 수 있다.
![](/Public/Study/Perforce/Docs/Perforce%20협업%20시뮬레이션/Resource/20250904154910.png)

Submit은 서버에 이력이 남아 철저한 리뷰 이후 수행해야한다면
Shelve - Unshelve를 통해 팀장과 팀원간 유용한 협업을 할 수 있다.