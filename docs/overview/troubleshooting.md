# PlayRTC Troubleshooting

PlayRTC의 문제 해결 방법입니다. PlayRTC는 다양한 프로토콜, 서버, 기술들을 복합적으로 사용하고 있으며 여기에서는 문제가 생겼을때 살펴보아야 할 부분을 설명합니다.


## 기본 사항
문제가 생겼을때 기본적으로 다음의 항목을 살펴보는 것을 권장합니다.

- PlayRTC 프로젝트 설정
    - T Developers로 부터 발급받은 API Key를 올바로 삽입 했는지 여부
    - T Developers에서 접속 허용 URL 이나 IP를 별도로 설정 했는지 여부 등
- PlayRTC 응용 앱 설정
    - API Key를 올바로 설정했는지 여부
    - PlayRTC 라이브러리인 jar, so파일들을 올바르게 복사하고 코드상에서 임포팅 했는지 여부
    - PlayRTC.js가 올바른 버전이 로딩 됬는지, playrtc.js가 크로스 도메인 정책에 의하여 로딩이 안된것은 아닌지 여부
    - PlayRTC 객체를 인스턴스화 할때 알맞은 설정값을 넣었는지 여부
- PlayRTC 서버와의 연결 및 채널링
    - 인터넷 연결 여부와 망의 상태가 정상적인지 여부
    - T Developers 서버의 정상작동 여부
    - PlayRTC 서버의 정상작동 여부
- Peer 연결
    - Peer의 인터넷 연결 여부와 망의 상태 여부
- Peer의 단말 기능과 상태
    - 단말기의 영상/음상 미디어 장치가 존재 여부
    - 사용자가 미디어 사용을 거부/승낙 하였는지 여부

기본적으로 이런내용을 확인해보면 상당수의 기초적 문제를 해결 할 수 있습니다. 또한 여기서 얻은 정보를 통해 게시판 문의를 하면 올바른 답변을 받을 가능성을 높일 수 있습니다.


## 로그 레벨

PlayRTC SDK는 다음과 같은 로그 레별을 가지고 있으며 필요에 따라 사용가능합니다.

Item  | Description
----- | -----------
Trace | 일반적인 모든 로그를 뜻합니다. PlayRTC 객체 생성부터 각 단계별 진행이 될때마다 모든 로그를 출력합니다. 또한 warn, error 로그도 출력합니다.
Warn  | 삭제 예정 기능을 사용하거나 필수 파라미터가 아닌 부가적인 파라미터를 넘기지 않았을 때 이를 출력합니다. 또한 error 로그도 출력합니다.
Error | 모든 에러 상황을 출력합니다. 필수 파라미터가 빠졌다거나 서버와의 연동에서 서버가 에러를 반환했다던지 또한 P2P 연결이 성사되지 못 하는 등 PlayRTC 상에서 일어날 수 있는 모든 에러는 에러로 출력됩니다.
None  | 어떠한 로그도 출력하지 않습니다.

로그 레벨을 설정은 각 SDK별 API 문서를 참고합니다.


##  State Change Event

PlayRTC는 서버와의 통신간 상태를 가지게 되며 아래의 상태 변경 이벤트를 디버그 코드로 확인할 수 있습니다.

- CHANNELING : 채널링 시작
- CHECKING : p2p 체킹
- SUCCESS : p2p 성공
- CONNECTED : P2P 연결
- DISCONNECTED : P2P 종료


## Error Code

PlayRTC 객체는 각 에러 상황별로 에러 코드를 가지고 있습니다.

### Javascript SDK Error Code

아래의 에러코드를 통해 디버그 시 도움을 받을 수 있습니다.

Code  | Message                               | Description
----- | ------------------------------------- | ------------
M4001	| Unsupported media	                    | 단말기의 영상/음성 장치 미지원 또는 로컬미디어 생성 시 오류발생
M4002	| Don't accept media	                  | 브라우저 에서 미디어 사용 거부
C4001	| Failed allocate channel	              | T Developers에서 채널 할당시 에러
C4002	| Failed to connect channel's server	  | 채널 소켓 실패
C4003	| Already disconnected channel's server	| 연결 이 닫혀 있는 상황에서 채널서버로 요청
C4004	| Invalid authentication of channel	    | 채널 서비스에서 사용자 인증 실패(토큰, PeerId)
C4005	| Invalid channel id	                  | 채널 서비스의 채널 ID 인증실패
C4006	| Channel error	                        | 채널 기타 모든 에러(전문 에러 포함)
C4007	| Channel socket error	                | 채널 소켓 에러
C4008 |	Failed to create sdp	                | SDP 생성 실패
C4009 |	Failed to register                    |	SDP 등록 실패
C4010 |	Failed to register candidate	        | CANDIDATE 등록 실패
P4001 |	Failed P2P	                          | 연결 실패
