# Hello World!
안녕하세요. 아무런 지식 없이, 곧바로 PlayRTC를 체험해 보도록 합시다.

<a class="jsbin-embed" href="http://jsbin.com/fegupesodi/3/embed?output">PlayRTC Hello World!</a>
<script src="http://static.jsbin.com/js/embed.js"></script>
<br>

`채널 생성 및 입장` 버튼을 누르면 브라우저 상단에 웹캠과 마이크 미디어 사용을 허용할 것인지 여부를 묻습니다. 프라이버시를 위해 절차이며 `허용` 버튼을 클릭하면, ‘Room(방)’이라 할 수 있는 설정된 Channel ID가 리턴되며 카메라로 내 모습이 보이게 됩니다!

이제 1:1 영상/음성 대화를 시작해 보도록 하겠습니다. 위 페이지에서 생성된 Channel ID를 아래의 페이지에 `ChannelID를 입력하세요.`라고 적힌 Input 태그에 입력하고 `입장` 버튼을 누릅니다.

<a class="jsbin-embed" href="http://jsbin.com/fegupesodi/3/embed?output">PlayRTC Hello World!</a>
<script src="http://static.jsbin.com/js/embed.js"></script>
<br>

내 모습이 보이시나요? 쉽습니다! 어떠한 서버도 없이 HTML과 Javascript를 통해서 작동이 가능합니다.

## 코드 확인하기
이제 한번 코드를 확인해 보도록 합시다.

```html
<!DOCTYPE html>
<html lang="ko">

<head>
	<meta charset="utf-8">
  <title>PlayRTC Hello World!</title>
	<style>
		.video {
			width: 320px;
			height: 240px;
		}
	</style>
</head>

<body>
  <div>
	  <label for="channelIdInput">Channel ID</label>
		<input type="text" id="channelIdInput" placeholder="ChannelId를 입력하세요." value="">
  	<button id="connectChannelButton">채널 입장</button>
  	<button id="createChannelButton">채널 생성 및 입장</button>
  </div>

  <video class="video" id="localVideo"></video>
  <video class="video" id="remoteVideo"></video>

	<script src="http://www.playrtc.com/sdk/js/playrtc.min.js"></script>
	<script>
  	app = new PlayRTC({
			projectKey: '60ba608a-e228-4530-8711-fa38004719c1',
			localVideoTarget: 'localVideo',
  	  remoteVideoTarget: 'remoteVideo'
  	});

		app.on('connectChannel', function(channelId) {
  	  document.getElementById('channelIdInput').value = channelId;
  	});

		document.getElementById('createChannelButton').onclick = function(event) {
  	  app.createChannel();
  	  return false;
  	};

		document.getElementById('createChannelButton').onclick = function(event) {
  	  app.createChannel();
  	  return false;
  	};

  	document.getElementById('connectChannelButton').onclick = function(event) {
  	  var channelId = document.getElementById('channelIdInput').value;

			if (!channelId) { return; }
  	  app.connectChannel(channelId);
  	  return false;
  	};
  </script>
</body>

</html>
```

- `playrtc.min.js` 라이브러리 파일 include
- `video` 태그를 2개 생성
- PlayRTC 객체 생성

크게 이렇게 세 단계를 통해 곧바로 시작이 가능합니다.

## 로컬에서 직접 실행해 보기

만약 직접 웹 서버를 통해 확인해 보고 싶다면 다음장 [브라우저 개발 환경 설정하기](browser/dev-env.md)를 참고하여 웹서버를 설치 하거나, 최신버전의 [php][php]나 [python][python]이 있으면 아래와 같이 간단하게 웹 서버를 생성하고 확인이 가능합니다.

``` Shell
# php
$ php -S localhost:8080

# python
$ python -m SimpleHTTPServer 8000
```
이제 브라우저(크롬이나 파이어 폭스 최신버전)에서 결과를 확인하기 위해 URL 창에 다음 URL을 입력합니다.
 
- <http://localhost:8000/>

자 이제 여러분은 PlayRTC 개발자로 입문하셨습니다. :-)

[php]: http://php.net/
[python]: https://www.python.org/
