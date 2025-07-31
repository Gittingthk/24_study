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
P.statDepth = 20; % 단위는 파장이다 이건 20람다 로 6.16mm부터 수신 시작하겠다는 뜻이다. 계산법(속도/중심주파수 * 원하는 깊이{여기서 20을 의미})
P.endDepth = 192; % 최대 깊이를 의미 128의 배술 설정하는게 좋다 이유는 Verasonics의 데이터 처리가 128-sample 단위로 최적화 되어 있기 때문이다.? 여기는 더 공부가 필요하다

%시스템 파라미터 정의
Resource.Parameters.numTransmit = 128 % 송신 채널 수를 의미한다. 이때 L7-4트랜스 듀서는 128 elements가 있으며 모두 사용해서 한번에 전체 영역에 plane wave를 쏜다.
Resource.Parameters.numRcvChannels = 64; %수신 채널 수를 의미한다. Verawsonics 시스템은 보통 동시에 64채널까지 수신가능 이것때문에 right, left가 존재 synthtic aperture을 사용하기 위함
Resource.Parameters.speedOfSound = 1540; % 초음파의 인체 조직 내 속도로 1540m/s로 설정한다. 나중에 computeTrans()라는 함수로 초점이나 delay 계산
Resource.Parameters.verbose = 2; % 이건 출력 디버깅 정보 수준 0은 출력 없음, 1은 간단한 정보 출력, 2는 자세한 정보 출력
Resoure.Parameters.initializeOnly = 0; % 1로 설정함녀 시퀀스를 메모리에 로밍만 하고 실행하지 않음. 0이면 로딩 후 즉시 실행
Resource.Parameters.simulateMode =0; % 0은 실제 하드웨어로 작동, 1은 시뮬레이션 모드(개발 디버깅용), 2 계속해서 수신데이터를 처리만 함
Trans.name = 'L7-4'; 중심 주파수 대역은 5.5MHz이고 리니어 탐촉자
Trnas.units = 'wavelengths'; 이후 좌표 값이나 깊이 값들ㄹ이 파장 단위로 해석된다
Trans = computeTrans(Trans); 트랜스듀서 정보 초기화 함수
Trans.maxHighVoltage = 50; 송신 펄스에 사용할 최대 전압 단위는 볼트이며 50V이하에서 안전하게 사용가능하다(L7-4)
PData(1).PDelta = [Trans.spacing, 0, 0.5]; [dx, dy, dz]는 x축 방향 해상도 (요소 간격), y축 방향 은(0), z축 방향 해상도( 깊이 간격) Trans.spacing은 탐촉자 요소 간의 거리로 0.3mm 파장이라면 0.3을 입력한다? 이거 다시 알아봐야할 듯
PData(1).Size(1) = ceil((P.endDepth - P.startDepth) / PData(1).PDelta(3)); Z축(깊이) 방향의 영상 크기(픽셀 수) 를 계산한다 전체 깊이 범위를 dz 간격으로 나눈 것.
