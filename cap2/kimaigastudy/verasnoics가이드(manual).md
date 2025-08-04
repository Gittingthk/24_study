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
4. 나타나는 GUIㅍ창에서 '시스템검증 테스트' 버튼을 클릭한 다음 Matlab 명령 창에 나타나는 프롬프트에 응답한다. 테스트는 몇 분 정도 소요되며 최종 결과는 명령 창에 '모든 테스트 통과'라는 