---
id: -1
tags:
  - 공부
is_public: true
last_synced:
---
## 1단계
Verasnoics 시스템 활성화:
MATLAB명령창에 아래를 입력한다
>[!note] activate()


정상 실행되면 아무 에러없이 조용히 넘어간다
>[!note] vsv.apps.runQuickScan() 

B-mode GUI 실행을 위한 실행 명령이다. **트랜스듀서 선택하고 실시간 B-mode 영상 확인 가능**


이건 시퀀스 파일을 다 만들었을때 실행하는거다


먼저 시퀀스 파일을 만들어보자

>[!todo] 코드리뷰
>```
>
```
test_bmode_right.m

clear all
P.statDepth = 20; % 단위는 파장이다 이건 20람다 로 6.16mm부터 수신 시작하겠다는 뜻이다. 계산법(속도/중심주파수)


