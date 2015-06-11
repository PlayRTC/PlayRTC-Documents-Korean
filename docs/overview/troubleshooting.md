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

이 로그 레벨을 설정하는 것은 다음의 각 SDK별 문서에 설명되어 있습니다.


## 에러 코드

PlayRTC 객체는 각 에러 상황별로 에러 코드를 가지고 있습니다. 코드는 다음과 같습니다.

Code  | Message                               | Description
----- | ------------------------------------- | ------------
M4001	| Unsupported media	                    | 사용자 단말기의 영상/음성 장치 미지원 또는 로컬미디어 생성 시 오류발생
M4002	| Don’t accept media	                  | 사용자가 브라우저 에서 미디어 사용여부를 거부하였다면
C4101	| Failed allocate channel	              | 서비스 서버에서 채널 할당시 에러가 발생하였다면
C4201	| Failed to connect channel’s server	  | 채널 서버와의 소켓 연결 실패
C4202	| Already disconnected channel’s server |	연결 이 닫혀 있는 상황에서 채널 서버로 요청
C4203	| Invalid authentication of channel	    | 채널 서버에서 사용자 인증 실패
C4204	| Invalid channel id	                  | 채널 서버의 채널 ID 인증 실패
C4205	| Channel error	                        | 채널 서버에서 발생하는 기타 모든 에러
S4401	| Invalid authentication of signal	    | 시그널 서버에서 사용자 인증 실패
S4402	| Failed to connect signal’s server	    | 시그널 서버와의 소켓 연결 실패
S4403	| Already disconnected signal’s server  | 연결 이 닫혀 있는 상황에서 시그널 서버로 요청
S4404	| Failed to create sdp	                | SDP 생성 실패
S4405	| Failed to register sdp	              | SDP 등록 실패
S4406	| Failed to register candidate	        | CANDIDATE 등록 실패
S4407	| Signal error	                        | 시그널 서버에서 발생하는 기타 모든 에러
S4408	| No answer	                            | Offer SDP를 전송하고 Answer SDP 응답을 받지 못 할 경우
P4501	| Failed P2P	                          | P2P 연결 실패

이러한 코드는 



## 에러 로그를 확인해보자

일단 에러는 어떤 것들이 있는지 정리해 보겠습니다. 아래는 크게 에러가 발생할 수 있는 상황들 입니다.

구분 설명
미디어	단말기의 영상/음상 미디어 장치가 존재하지 않을 때
사용자가 브라우저에서 미디어 사용을 거부하였을 때
채널 채널 서버와의 연결과 메시지 송수신시 에러
시그널 시그널 서버와의 연결과 메시지 송수신시 에러
P2P P2P 상황에서의 연결 실패


이제 브라우저의 개발자 도구를 열어 확인해 보겠습니다.

브라우저의 개발자 도구를 엽니다.
Sample 1 의 videochat 샘플(http://localhost:5400/videochat) 을 실행합니다.
채널을 생성하기 위해 채널 이름을 입력하고 채널 입장 버튼을 클릭합니다.
브라우저가 미디어 사용 유무를 물으면 “거부”를 클릭합니다.
이제 개발자 도구 콘솔 창에 나타나는 로그를 확인해 봅시다.
만약 사용자가 미디어 사용 유무에서 거부를 눌렀다면 아래와 같이 에러 로그가 발생합니다.

에러로그



## 서비스 서버의 에러

튜토리얼 4. 영상 채팅 서비스 서버 구현하기 와 튜토리얼 5. 영상 채팅 방 서비스 서버 확장하기 에서 서비스 서버와 서비스 헬퍼를 살펴봤었습니다.
여러분의 서비스는 여러분의 서비스 서버에 존재하게 되며 Client 와 채널 서버와의 사이에서 Biz Logic을 수행합니다. 이 과정에서 다양한 에러 사황이 발생할수 있으며 이러한 에러는 SDK 와 Client(서비스를 이용하는 사용자) 에게까지 전달되어 어져야 합니다.
이러한 상황을 가정하여 서버스 서버에서 에러를 반환했다면 어떻게 나타날까요! 서비스 서버와 서비스 헬퍼에 관한 샘플인 sample2 의 일부 소스를 변경해 보겠습니다.

service 폴더 내의 intercept.js 를 열어 createChannel 의 intercept 내용을 아래와 같이 변경합니다.

```
exports.init = function(app, projectKey){
     helper = new PlayRTCHelper.Helper({
          app: app,
          projectKey: projectKey
     });

     helper.intercept("getChannelList", requestIntercept);
     helper.intercept("createChannel", function(req, res){
          res.statusCode = 401;
          res.json({
               error: true,
               message: "사용자가 존재하지 않습니다."
          });
          return false;
     });
     helper.intercept("connectChannel", requestIntercept);
     helper.intercept("dicconnectChannel", requestIntercept);
};
```
이제 createChannel 요청 시  이를 가로챌 함수는 무조건 에러를 발생시켜 요청을 수행하지 않습니다. 로그는 어떻게 출력 될까요! 확인해 보겠습니다.

errorlog

정의한 응답 코드와 메시지가 SDK 까지 전달되어 로그로 출력되는 것을 확인해 볼 수 있습니다.


## 에러 이벤트

에러가 발생하면 SDK 는 내부적으로 Recovery 하여 초기 상태로 돌려놓습니다. 그리고 에러 이벤트 핸들러가 존재한다면, 이를 호출하여 줍니다. 해당 에러 이벤트 핸들러에서 에러 상황에 적절한 대응 조치를 취할 수 있습니다. 에러 이벤트는 다음과 같이 정의 합니다.
```
conn.on("error", function(code, desc, payload){
     console.log(code, desc, payload);
});
```


## 에러 이벤트가 취하는 파라미터

이름	설명
code	SDK 에서 정의되어 있는 해당 에러를 대표하는 코드
desc	에러에 관한 상세한 메시지
payload	버(서비스 서버, 채널 서버, 시그널 서버)에서 에러를 응답하였다면 해당 에러 원문이 전달됩니다. 만약 전달되지 않으면 undefined 입니다.


## code 및 desc

code	desc	설명
M4001	 Unsupported media	 사용자 단말기의 영상/음성 장치 미지원 또는 로컬미디어 생성 시 오류발생
M4002	 Don’t accept media	 사용자가 브라우저 에서 미디어 사용여부를 거부하였다면
C4101	 Failed allocate channel	 서비스 서버에서 채널 할당시 에러가 발생하였다면
C4201	 Failed to connect channel’s server	 채널 서버와의 소켓 연결 실패
C4202	 Already disconnected channel’s server	 연결 이 닫혀 있는 상황에서 채널 서버로 요청
C4203	 Invalid authentication of channel	 채널 서버에서 사용자 인증 실패
C4204	 Invalid channel id	 채널 서버의 채널 ID 인증 실패
C4205	 Channel error	 채널 서버에서 발생하는 기타 모든 에러
S4401	 Invalid authentication of signal	 시그널 서버에서 사용자 인증 실패
S4402	 Failed to connect signal’s server	 시그널 서버와의 소켓 연결 실패
S4403	 Already disconnected signal’s server	 연결 이 닫혀 있는 상황에서 시그널 서버로 요청
S4404	 Failed to create sdp	 SDP 생성 실패
S4405	 Failed to register sdp	 SDP 등록 실패
S4406	Failed to register candidate	 CANDIDATE 등록 실패
S4407	Signal error	 시그널 서버에서 발생하는 기타 모든 에러
S4408	No answer	 Offer SDP를 전송하고 Answer SDP 응답을 받지 못 할 경우
P4501	Failed P2P	 P2P 연결 실패
대표적인 코드를 선별하여 사용자에게 alert 메시지를 출력해보도록 하겠습니다.

```
conn.on("error", function(code, desc, payload){
     if(code === "M4001"){
          alert("사용자 브라우저는 미디어 장치를 지원하지 않습니다.");
     }
     else if(code === "M4002"){
          alert("해당 서비스는 반드시 영상/음성을 필요로 합니다. 브라우저의 미디어 사용여부를 반드시 '사용'으로 해주세요.");
     }
     else if(code === "P4501"){
          alert("P2P 연결이 실패하였습니다.");
     }
     else{
          alert("에러가 발생했습니다.");
     }
});
조금 전 서비스 서버에서 발생한 에러는 에러 이벤트 핸들러에서 어떻게 잡아낼까요. 다음 코드를 확인해 보겠습니다.

conn.on("error", function(code, desc, payload){
     if(code === "C4101" && payload){
          if(payload.error){
               alert(payload.message); //서비스 권한이 존재하지 않습니다.
          }
      }
});
```
서비스 서버와의 연동에서 채널 생성시 에러는 C4101 에러로 정의되어 있습니다. 그리고 서비스 서버에서 반환한 에러 전문은 payload 로 전달되어 집니다. 예시에서는 message 정도만 담고 있지만 해당 에러 전문에 에러에 관한 좀더 자세한 사항을 담아 에러 이벤트에서 처리할 수 있습니다.
