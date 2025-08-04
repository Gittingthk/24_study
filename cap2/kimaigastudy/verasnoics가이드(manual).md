---
id: -1
tags: ""
is_public: true
last_synced: ""
---
## Verasonics with Vantage configuration

Host Controller
Host Controller Operating System
Matlab
our software version 4.8.7 version
## Power on Procedure
1. Vantage system on
2. computer on -> fully power up the hardware
3. front panel probe connecstor led turn on
## Power Off Procedure
1. Shutdown computer.

### 소프트 웨어는 3.2.0이후 면 뭐든 상관이 없으니 support@verasonics.com에 문의

### .mat파일은 동일 소프트웨어에서 릴리즈 가능한데 .m파일은 재사용불가(다시 생성 권장)

### The term "Vantage project folder" is used throughout the Vantage system documentation to refer to the main direc tory containing an installation of the Vantage software, with a default name such as "Vantage 4.4.0-2012091800". When using the Vantage software, the Vantage project folder must be selected as the current Matlab working directory.

# Test

vatange시스템 완전 제거 후(호스트 컨트롤러를 완전히 전원 꺼짐 상태로로 만들기 이후 Vantage 시스템의 후면 패널에 있는 전원 스위치를 끈다.)

1. 호스트 컨트롤러를 재시작하고 로그인합니다. 
2. Matlab 응용 프로그램을 실행합니다.
3. Matlab 사용자 인터페이스가 준비되면, 4.4.0-2012091800 Vantage 프로젝트 폴더를 Matlab 작업 디렉토리로 선택한 후, Matlab 명령 프롬프트에서 activate 명령을 입력합니다.
4. 시스템 시작 중에 오류 메시지가 표시되지 않는지 확인합니다. 5. Matlab 명령 프롬프트에서 Version 명령을 입력하고 소프트웨어 릴리스 버전 번호가 올바르게 보고되며, 오류 또는 경고 메시지가 표시되지 않는지 확인합니다.

FPGA코드를 업데이트해야하는 경우가 발생한다

1. 호스트 컨트롤러를 제대로 종료하세요. Vantage 시스템은 이미 전원이 꺼져 있어야 합니다.
2. 호스트 컨트롤러와 Vantage 시스템이 모두 꺼진 상태에서, 호스트 컨트롤러와 Vantage 하드웨어 유닛 사이의 PCI Express 케이블이 제대로 연결되어 있는지 확인하세요. 
3. Vantage 하드웨어 유닛의 전원 스위치가 켜져 있는 위치로 설정되어 있는지 확인하세요. 이때 Vantage 하드웨어 유닛은 켜지지 않으며, 호스트 컨트롤러 시스템이 전원을 켤 때만 켜질 것입니다. 
4. 호스트 컨트롤러의 전원을 켜고, 호스트 컨트롤러와 Vantage 하드웨어 유닛이 모두 켜지는지 관찰하세요. Vantage 하드웨어 유닛이 켜질 때 팬 소리를 들을 수 있어야 합니다. 
5. 호스트 컨트롤러에 로그인하고 Matlab 애플리케이션을 실행하세요.
6. 새로 설치된 Vantage 프로젝트 폴더를 Matlab 작업 디렉터리로 선택하고, activate를 실행한 후, Matlab 명령 프롬프트에서 Version 명령을 입력하세요.

라이센스 관련

1. Matlab 환경을 실행하십시오.
2. Matlab을 열고 Vantage SW가 설치된 디렉토리로 이동하십시오. 
3. 명령 창에 activate를 입력하고 Enter를 누르십시오.
4. Matlab 콘솔 프롬프트에서 systemInfo를 입력하고 Enter를 누르십시오.
5. 이 명령은 현재 디렉토리에 systemInfo.txt라는 텍스트 파일을 생성합니다.
6. systemInfo.txt 파일을 Verasonics 라이센싱(licensing-request@verasonics.com)으로 메시지에 첨부하십시오.

self test

1. 테스트를 시행하기 전에 시스템 전원을 끄고 재시작하여 시스템이 알려진 작동 상태에 있는지 확인한다. 
2. Matlab 명령 창 프롬프트에서 Vantage 프로젝트 폴더로 이동하여 필요 시 activate를 실행한다.
3. 명령창에 VerasonicsVerificationTest를 입력하고 enter를 누른다. 
4. 나타나는 GUIㅍ창에서 '시스템검증 테스트' 버튼을 클릭한 다음 Matlab 명령 창에 나타나는 프롬프트에 응답한다. 테스트는 몇 분 정도 소요되며 최종 결과는 명령 창에 '모든 테스트 통과'라고 표시되어야 한다. 트스트 중 하나라도 실패하면 '테스트 실패' 메시지가 나타난다. VerasonicsVerificationTest는 로그 파일을 하위 폴더 HardwareTest/TestResultLogs에 저장합니다.

Vantage Sequence Programming Tutorial pdf 읽어보기
Vantage Sequence Programming Manual
AppNote Tools Summary

명령어 

Version -> 'Version x' 모든 주요 하드웨어 모듈의 부품 번호 및 일련 번호, FPGA 코드 수정 수준 등이 포함 된다.

이래도 안된다 FPGA 파일 하나가 손상된경우다. 이때 reprogramHardware를 사용 복구 방법이 될수있다. 끝나면 전원을 꺼라는 명령이 나온다.

ProbeConnectorStatus -> 커넥터 유형, 프로브가 연결되어 있는지 여부, 연결되어 있다면 트랜스듀서의 ID와 이름

VerasnoicsVerificationTest->전체 시스템에 대한 자기 테스트로 

SystemInfo -> SW설치 및 라이선스 상태, 열결된후 Vatage HW 시스템 보고서를 제공

SwitchesInfo ->PCIe 장치 엔드포인트 간의 링크 보고서를 생성


time tag for RF Data
- 트랜스듀서가 한번 쏨 (Tx)
    
- RF 수신: 128채널, 채널당 2048 샘플 수집
    
- 이 때, **전체 Receive 데이터의 맨 앞 2 샘플에 time tag가 박힘**

TX → delay → RX 시작 → 그 순간을 찍은 시간 값

boolean result = Hardware.enableAcquisitionTimeTagging(enable); %타임태그 활성화 함수 enable은 boolean value이고 restult가 반환 값이고 

time tag 활성화시 주의점은 recon(영상생성) 함수가 이 time tag값도 샘플 값으로 인식해서 처리해 버리기 떄문에 주의 해야한다

result = setTimeTaggingAttributes(wrap24, reset); %  24시간을 최대로 설정후 0으로 리셋

시스템 계층

|계층|대표 요소|역할|네가 다루는가?|
|---|---|---|---|
|1. 사용자 계층|`.m`, `VSX`|시퀀스 정의, 제어|✅ 직접 작성|
|2. 제어 계층|`VSX.m`, `.mat`|설정 전달|⚙️ 간접적으로|
|3. 실행 계층|`runAcq.mex`|실행 트리거|❌ 직접 수정 안함|
|4. HAL|HAL API|하드웨어 추상화|❌ 내부 구조|
|5. 하드웨어|FPGA, 송수신기|실제 초음파 송수신|❌ 물리 장치|
사용법

1. 매트랩을 킨다. 그리고 vantage프로젝트 폴더로 이동한다.
2. 활성화 명령을 발행하여 vantage 프로젝트 폴더를 활성화한다.