# PlayRTC for IE

Windows에 기본으로 설치되어 있는 Internet Explorer 브라우저는 현재 버전 11까지 WebRTC를 지원하지 않고 있습니다.

- [브라우저 WebRTC 지원현황](http://caniuse.com/#feat=rtcpeerconnection)

이는 여러 시장상황과 맞물려서 앞으로도 IE 브라우저는 WebRTC를 지원하지 않을 가능성이 높습니다. 다만 IE뒤를 잇는 차세대 브라우저인 [Spartan, Edge](http://windows.microsoft.com/en-us/windows/preview-microsoft-edge-pc)에서는 WebRTC혹은 WebRTC의 차세대 규격인 ORTC를 지원할 것이라는 기대를 모으고 있습니다.

PlayRTC는 IE 브라우저에서도 WebRTC기술을 사용할 수 있도록 추가적인 ActiveX 플러그인을 제공하고 있습니다.


## ActiveX 배포

PlayRTC ActiveX는 `playrtc.com`으로 부터 다운로드 받을 수 있습니다. 현재는 `playrtc.com`에서만 ActiveX 파일을 배포하고 있습니다.

playrtc.js 를 화면에 include 하여 playrtc 객체를 선언하면 자동으로 `playrtc.com` 에서 ActiveX 를 내려받아 설치합니다.


## PlayRTC 객체 생성

localVideoTarget와 remoteVideoTarget 을 IE 까지 포함하려고 하면 약간의 코딩을 더 해주어야 합니다. 우리가 Hello World 에서 실행 했던 코드를 기반으로 작성해 보도록 합니다.

```HTML
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
    <input type="text" id="channelIdInput" placeholder="Enter the channel ID" value="">
  	<button id="connectChannelButton">Connect Channel</button>
  	<button id="createChannelButton">Create and Connect Channel</button>
  </div>

  <div class="my" id="localStreamIe">
    <video class="video" id="localVideo"></video>
  </div>
  <div class="my" id="remoteStreamIe">
    <video class="video" id="remoteVideo"></video>
  </ie>

	<script src="http://www.playrtc.com/sdk/js/playrtc.min.js"></script>
	<script>
    'use strict';

    var local = 'localStream';
    var remote = 'remoteStream';
    var app;

    if(PlayRTC.utils.browser.name === 'ie'){
         local = 'localStreamIe';
         remote = 'remoteStreamIe';
    }

  	app = new PlayRTC({
			projectKey: '60ba608a-e228-4530-8711-fa38004719c1',
			localVideoTarget: 'local',
  	  remoteVideoTarget: 'remote'
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

위에서도 확인할 수 있듯이 video 태그를 감싸는 div 에 id 속성을 부여했습니다. 해당 id 속성은 IE 브라우저에서 사용될 것입니다.

localVideoTarget, remoteVideoTarget 옵션을 IE 의 경우 video 태그를 감싸고 있는 상위 div 를 지정하고 있음을 확인 할 수 있습니다. 이제 크롬에서는 기존과 같이 video 태그에 영상을 표시할 것이며 IE 에서는 지정된 div 밑으로 영상이 들어갑니다. 초기화 이후 이벤트를 할당하는 과정은 기존과 동일합니다.


## 채널에 접속하기

IE 에서도 역시 채널에 접속하는 방법은 동일합니다. 그러나 채널에 접속하기 위해서는 크롬브라우저와 마찬가지로 웹 캠의 사용에 대한 허용 여부를 묻는 UI 가 생성됩니다. 여기에서 반드시 허용을 클릭해야만 정상적으로 채널을 접속하여 영상 통신을 준비할 수 있습니다.


## 그 외 차이점

IE 버전과 일반 playrtc 버전은 거의 모든 인터페이스가 동일합니다. 이 곳에서는 그 중 인터페이스 사용법에 차이가 나는 레코드에 대해 설명합니다.


### 기존 레코딩 방법

```
// The local stream record
app.getMedia().record("video");

app.getMedia().recordStop(function(blob){
     PlayRTC.utils.fileDownload(blob, 'local-video.webm');
});

// The remote stream record
app.getAllPeer()[0].record("video");

app.getAllPeer()[0].recordStop(function(blob){
     PlayRTC.utils.fileDownload(blob, 'remote-video.webm');
});
```

### IE 레코딩 방법

```Javasciprt
// The IE local stream record
app.getLocalMedia().record('%USERPROFILE%\Downloads\localVideo');
app.getLocalMedia().recordStop();

// The IE remote stream record
app.getRemoteMedia().record('%USERPROFILE%\Downloads\remoteVideo');
app.getRemoteMedia().recordStop();
```

기존 local Stream 과 remote Stream 을 관장하는 객체를 얻는 방법은 각각 playrtc 객체와 playrtc 에 저장된 하위 peer 객체를 가져오는 형태였습니다. IE 에서는 이것이 playrtc 객체의 메소드인 `getLocalMedia` 와 `getRemoteMedia` 로 변경되었습니다.

또한 기존 record 메소드는 무엇을 레코딩할지에 대한 인자를 문자열로 받습니다. 예를 들어 `conn.getMedia().record("video")` 혹은 `conn.getMedia().record("audio")` 와 같이 video 나 audio 를 인자로 받아 해당 미디어에 대해 레코딩을 진행합니다.
IE 에서는 레코딩할 경로와 파일 이름을 full path 로 전달 받습니다. Audio 만 별도로 레코딩하는 방법은 별도로 제공하지 않고 video 만을 레코딩합니다.

레코딩을 끝내는 방벙에도 차이가 있습니다. 기존의 recordStop 메소드는 함수를 인자로 넘겨 해당 함수의 인자로 취해지는 blob 을 저장하는 형태입니다. 허나 IE 에서는 단순히 recordStop 을 호출하여 레코딩을 중지합니다. 이렇게 레코딩을 중지하면 명시한 경로에 파일이 저장됩니다.
