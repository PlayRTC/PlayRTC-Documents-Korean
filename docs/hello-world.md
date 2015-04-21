# Hello World!
안녕하세요. 아무런 지식 없이, 곧바로 PlayRTC를 체험해 보도록 합시다.

## HTML 코딩
HTML 코딩 시간입니다.
- `playrtc.min.js` 라이브러리 파일 include
- `<video>` 태그를 2개 생성
- 자바스크립트로 PlayRTC 클래스의 인스턴스 생성 후
- 그 클래스의 call 메소드 호출
이 필요합니다. 다음의 샘플 코드를 직접 타이핑 내지 Copy & Paste 하면 코딩 완료 입니다. 이렇게 PlayRTC 서비스의 장점인 ‘Easy & Simple’ 입니다.

```html
	<!DOCTYPE html>
	<html lang="ko">

	<head>
	  <title>PlayRTC Hello World!</title>
	  <meta charset="utf-8">
	  <script src="http://www.playrtc.com/sdk/js/playrtc.min.js"></script>
	</head>

	<body>
	  <div>
    	<input type="text" id="channelId" placeholder="ChannelId를 입력하세요." value="">
    	<button id="connectChannel">채널 입장</button>
    	<button id="createChannel">채널 생성 및 입장</button>
	  </div>
	  <video id="localVideo" style="width: 320px;height: 240px;"></video>
	  <video id="remoteVideo" style="width: 320px;height: 240px;"></video>
	  <script type="text/javascript">
    	conn = new PlayRTC({
    	  localVideoTarget: "localVideo",
    	  remoteVideoTarget: "remoteVideo"
    	});
    	conn.on("connectChannel", function(channelId) {
    	  document.getElementById("channelId").value = channelId;
    	});
    	document.getElementById("createChannel").onclick = function(e) {
    	  conn.createChannel();
    	  return false;
    	};
    	document.getElementById("connectChannel").onclick = function(e) {
    	  var channelId = document.getElementById("channelId").value;
    	  if (!channelId) { return };
    	  conn.connectChannel(channelId);
    	  return false;
    	};
	  </script>
	</body>

	</html>
```

## Hello.html 확인 하기

이제 웹 서버를 돌리고 확인해 봅시다. 최신버전의 `php`나 `python`이 있으면 아래와 같이 간단하게 웹 서버를 생성하고 확인이 가능합니다.

- php
	- `$ php -S localhost:8080`
- python
	- `$ python -m SimpleHTTPServer 8000`

이제 브라우저(크롬이나 파이어 폭스 최신버전)에서 결과를 확인하기 위해 URL 창에 다음 URL을 입력합니다.
 
http://localhost:8000/

샘플 실행 환경과 샘플 코드 작성에 이상이 없다면 다음과 같은 화면이 출력되어야 합니다.

`채널 생성 및 입장` 버튼을 누르면 다음과 같이 브라우저 상단에 미디어 사용을 허용할 것인지 여부를 묻습니다.

혹시라도 WebRTC 코드가 사용자가 모르는 사이에 웹 캠 등을 사용하면 프라이버시 등에 문제가 발생할 수 있으므로 브라우저에서 웹 캠을 사용하게 되면 항상 허용 여부를 묻게 됩니다.

`허용` 버튼을 클릭하면, ‘Room(방)’이라 할 수 있는 설정된 Channel ID가 리턴되며 현재 페이지에 당신의 화면이 보이게 됩니다.

영상 연결 서비스를 테스트하기 위해서, 사용하는 브라우저의 새로운 탭 하나를 만들어 동일한 URL (http://localhost:8000/)로 접속한 후, 리턴 받은 Channel Id를 `채널 입장` 입력창에 넣고 버튼을 누르면 됩니다. 개발자 자신의 얼굴이 두개 비칠 것 입니다.
 
자 이제 여러분은 PlayRTC 개발자로 입문하셨습니다. :-)
