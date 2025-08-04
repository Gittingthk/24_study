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

1. 호스트 컨트롤러를 재시작하고 로그인합니다. 2. Matlab 응용 프로그램을 실행합니다. 3. Matlab 사용자 인터페이스가 준비되면, 4.4.0-2012091800 Vantage 프로젝트 폴더를 Matlab 작업 디렉토리로 선택한 후, Matlab 명령 프롬프트에서 activate 명령을 입력합니다. 4. 시스템 시작 중에 오류 메시지가 표시되지 않는지 확인합니다. 5. Matlab 명령 프롬프트에서 Version 명령을 입력하고 소프트웨어 릴리스 버전 번호가 올바르게 보고되며, 오류 또는 경고 메시지가 표시되지 않는지 확인합니다.
