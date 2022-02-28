# OSI 7 계층

## 목차
- [OSI 7계층이란](#OSI-7계층이란)
- [OSI 7계층 단계](#OSI-7계층-단계)


## OSI 7계층이란
* OSI 모형(Open Systems Interconnection Reference Model)은 국제표준화기구(ISO)에서 개발한 모델로, 컴퓨터 네트워크 프로토콜 디자인과 통신을 계층으로 나누어 설명한 것
* TCP/IP 모델과 달리 OSI 모델은 네트워크 프레임워크에서 발생하는 복잡한 상호 작용을 더 잘 이해하는 데 도움이 되는 개념적 프레임워크


## OSI 7계층 단계

|계층 | OSI 7계층     | 전송단위 |   장비       |   프로토콜            | 기능   |
---|------------------|---------|-------------|----------------|----------------------------------------------
7  | 응용(Application) 계층 | Message  | software  | HTTP, DHCP, DNS, FTP, SMTP          | 사용자가 네트워크에 접근할 수 있는 계층이다.
6  | 표현(Presentation) 계층 | Message | software  | JPEG, MPEG, SMB, AFP  | 운영체제의 한 부분으로 입력 또는 출력되는 데이터를 하나의 표현 형태로 변환한다.
5  | 세션(Session) 계층  | Message | software | SSH, TLS    | 통신 세션을 구성하는 계층으로, port연결이라고도 한다.
4  | 전송(Transport) 계층 | Segment(TCP} | Gateway( 게이트 웨이) | TCP, UDP    | 패킷들 전송이 유효한지 확인 후, 전송 실패한 패킷들 다시 전송(오류제어, 흐름제어)
3  | 네트워크(Network 계층 | Packet| Router(라우터), L3 switch |  IP주소, ICMP       |  라우팅 : 가장 중요한 기능은 데이터를 목적지까지 가장 안전하고 빠르게 전달
2  | 데이터(Data Link) 계층 | Frame | Switch(스위치), Bridge | MAC주소, PPP       | 물리계층을 통해 송수신되는 정보의 오류와 흐름을 관리
1  | 물리(Physical) 계층  | Bit | Hub(허브), Repeater |  Ethernet, RS-232C    | 주로 전기적 및 기계적인 특성으로 통신 케이블로 데이터를 전송(on/off)

- L1 장비 = 허브 hub (단순)
- L2 장비 = 스위치 switch (mac 테이블)
- L3 장비 = 라우터 Router (routing 테이블 : ip 정보가 있는)
- L4 장비 = 게이트웨이 Gateway

## 4계층 전송 Transport 계층
|프로토콜 종류 | TCP        |  UDP  |
---------|-----------------|-----------
연결방식 |  연결형 서비스            | 비연결형 서비스 (연결 설정 전 데이터 전송 시작)
패킷 교환 방식 | 가상 회선 방식           | 데이터그램 방식
전송 순서 | 순서 보장o           | 순서 변경 가능
수신 여부 확인 | 확인함           | 확인x
통신 방식 | 1:1통신           | 1:1 or 1:N or N:N 통신
신뢰성 | 높음              | 낮음
속도 | 느림 (전송 중 오류검사)         | 빠름
용도 | 고신뢰성 요구사항 (HTTP/HTTPS, FTP, 이메일)   | 고속, 및 효율성 요구사항 (비디오 스트리밍, DNS)


## 참고
- [TCP/IP vs OSI Model: What’s the Difference?](https://www.guru99.com/difference-tcp-ip-vs-osi-model.html)
- [What Are The 7 Layers Of The OSI Model?](https://www.webopedia.com/definitions/7-layers-of-osi-model/)
- [Transmission Control Protocol (TCP)](https://www.webopedia.com/definitions/tcp/)
