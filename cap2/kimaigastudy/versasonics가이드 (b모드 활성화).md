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
PData(1).Size(2) = ceil((Trnas.numelements * Trnas.spacing /2 ) / PData(1).PDelta(1)); /2를 한 이유는 Verasonics 영상이 탐촉자 중심 기준 반폭만을 영상화 하기 때문이다.
PData(1).Size(3) = 1; 영상 페이지 수 1장이면 2d영상, **3D 영상은 여러 페이지 필요**
PData(1).Origin = [0, 0, P.startDepth]; 영상의 기준 좌표를 설정한다. (x, y, z) = 왼쪽 위 꼭짓점
시뮬레이션
pt1; 이 파일을 실행해서 point scatterers(점 산란체) 위치를 불러온다.
Media.attenuation = -0.5; 매질의 감쇠를 설정한다. 여기선 시뮬레이션이므로 약하게 -0.5로 설정
Media.function = 'movePoints';

리소스 구조체 설정

Resource.RcvBuffer(1).datatype = 'int16' % 수신 버퍼
Resource.RcvBuffer(1).rowPerFrame = 4096*2; % 1프레임당 샘플 개수(길이 방향), 8192개의 샘플 저장 가능
Resource.RcvBuffer(1).colsPerFrame = Resource.Paramters.numTransmit; % 프레임당 열 수 = 송신 횟수
Resource.RcvBuffer(1).numFrames = 100; %

Resource.InterBuffer(1).numFrames = 1; %프레임 간 누적이 필요 없는 경우
Resource.ImageBuffer(1).numFrames = 10; 최대 10장의 이미지가 저장될 공간
Resource.DisplayWindow(1).Title = 'L7-4Flash_LE' ;
Resource.DisplayWindow(1).pdelta = 0.35;
ScrnSze = get(0, 'ScreenSize');
DwWidth = ceil(PData(1).Size(2)*PData(1)/Resource.DisplayWindow(1).pdelta);
DwHeight = ceil(PData(1).Size(1)*PData(1).PDelta(3)/Resource.DisplayWindow(1).pdelta);
Resource.DisplyWindow(1).Position = [250,(ScrnSize(4)-(DwHeight+150))/2, DwWidth, DwHeight];
Resource.DisplayWindow(1).ReferencePt = [PData(1).Origin(1),0,PData(1).Origin(3)];
Resource.DisplayWindow(1).Type = 'Verasonics';
Resource.DisplyaWindow(1).numFrames = 20;
Resource.DisplayWindow(1).AxesUnits = 'mm';
Resource.DisplayWindow(1).Colormap = gray(256);
TW(1).type = 'parametric';
TW(1).Parameters = [Trans.frequency, 0.67, 2, 1]; % TW(1)은 1번째 송신 파형 정의, parametric파형 수식으로 정의, parameters = [중심주파수, 대역폭, 사이클 수, 인벌롭] 이벌롭이 1이면 Gaussian
TX(1).waveform = 1;
TX(1).Origin = [0.0, 0.0, 0.0]; %중심에서 시작하는 flash transmit(평면파)
TX(1).focus = 0;  % 0이면 비집중형 송신(plane wave)
TX(1).Steer = [0.0, 0.0]; % 빔 조향 강도 [theta, alpha] 조향 없이 정직한 파형
TX(1).Apod = ones(1, Trnas.numelements);
TX(1).Delay = computeTXDelays(TX(1)); 초점화, 조향, 평면파 송신 등에 필요한 delay 설정

초음파 수신부

TGC.CntrlPts = [0,244,476,699,883,1023,1023,1023]; %이득의 조절점 8개
TGC.rangeMax = P.endDepth; %TGC적용 깊이의 최대 범위
TGC.Waveform = computeTGWaveform(TGC); % 위설정을 바탕으로 실제 TGC 곡성 생성

Receive = repmat(struct('Apod', zeros(1, Trans.numelements),'startDepth', P.startDepth, 'endDepth', amxAcqLength,'TGC, 1, 'bufnum', 1, 'framenum', 1, 'acqNum', 1, 'sampleMode', 'NS200BW', 'mode', 0, 'callMediaFunc', 0),1,2*Resource.RcvBuffer(1).numFrames);

for i = 1:Resource.RcvBuffer(1).numFrames
	Receive(i).Apod(Resource.Parameters.numRcvChannels+1:Trnas.numelements) = 1.0; 오른쪽 반 수신 채널 활성화
	Recieve(i).framenum = i; %현재 프레임 번호 설정 
	Recieve(i).acqNum = 1; %각 프레임의 첫 번째 수신 이벤트
	Recieve(i).callMediaFunc = 1; %시뮬레이션에서 media 객체 업데이트
end

Recon = struct('senscutoff', 0.6, ... % 감도 컷오프 값
				'pdatanum', 1, ... % 사용할 PData 구조 번호
				'rcvBufframe', -1, ... % 가장 최근 수신한 프레임 사용
				'IntBufDest', [1,1], ... % 중간 버퍼에 저장할 위치
				'ImgBufDest', [1,-1], ... % 이미지 버퍼 1번에 저장, 프레임 번호는 자동 증가 -1
				'RINums', 1:2); % ReconInfo의 1번과 2번을 순서대로 사용해 재구성
ReconInfo = repmat(struct('mode', 'replaceIQ', ...
					'txnum', 1, ... % 송신 파형 1번 사용
					'rcvnum', 1, ... % 수신 횟수 구분
					'regionnum', 1),1,2);
ReconInfo(2).mode = 'accumIQ_replacentensity';
ReconInfo(2).rcvnum = 2;

pres = 20;
Process(1).classname = 'Image';
Process(1),method = 'imageDisplay';
Process(1).Parameters = {'imgbufnum' ,1, ... % 1번 ImageBuffer 사용
						'framenum', -1, ...% 마지막 프레임 표시
						'pdatanum', 1,...
						'pgain',1.0,...
						'reject',2,000 % reject level 노이즈 억제
						'persistLevel', 20 %20프레임까지 잔상 유지
						'interpMethod', '4pt' % 4포인트 interpolation 사용
						'compressMethod', 40 압축 세기 조절(40dB)
						'displayWindow', 1번 display 창에 표시
						'display', 1 실제화면 띄움
						
						};
% SeqControl structure arrays
SeqControl(1).command = 'jump';
SeqControl(1).argument = 1;
SeqControl(2).command = 'timeToNextAcq';
SeqControl(2).argument = 200;
SeqControl(3).command = 'timeToNextAcq';
SeqControl(3).argument = 9800;
SeqControl(4).command = 'returnToMatlab;
nsc = 5;

n=1; %이벤트 인덱스 시작

for i =1 


