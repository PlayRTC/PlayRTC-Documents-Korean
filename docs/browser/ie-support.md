# PlayRTC for IE

Windows에 기본으로 설치되어 있는 Internet Explorer 브라우저는 현재 버전 11까지 WebRTC를 지원하지 않고 있습니다.

- [브라우저 WebRTC 지원현황](http://caniuse.com/#feat=rtcpeerconnection)

이는 여러 시장상황과 맞물려서 앞으로도 IE 브라우저는 WebRTC를 지원하지 않을 가능성이 높습니다. 다만 IE뒤를 잇는 차세대 브라우저인 [Spartan, Edge](http://windows.microsoft.com/en-us/windows/preview-microsoft-edge-pc)에서는 WebRTC혹은 WebRTC의 차세대 규격인 ORTC를 지원할 것이라는 기대를 모으고 있습니다.

PlayRTC는 IE 브라우저에서도 WebRTC기술을 사용할 수 있도록 추가적인 ActiveX 플러그인을 제공하고 있습니다.

## ActiveX 배포

PlayRTC ActiveX는 `playrtc.com`으로 부터 다운로드 받을 수 있습니다. 현재는 `playrtc.com`에서만 ActiveX 파일을 배포하고 있습니다.

```
app = new PlayRTC({
     localVideoTarget: localStream_ie,
     remoteVideoTarget: remoteStream_ie,
     cabUrl: cab;
});
```

## PlayRTC 객체 생성

PlayRTC 객체 생성은 위에서 확인할 수 있었습니다. 여기서는 상세하게 크롬브라우저와 상호 통신 하기 위해서 PlayRTC 객체 생성시 무엇을 변경해야하는지 알아보겠습니다.

- 명칭	설명
  - localVideoTarget	 WebRTC 가 지원되는 브라우저에서는 video 태그를 지정하던 것을 IE 에서는 div 를 지정하는 형태로 변경되었습니다.
  - remoteVideoTarget	 WebRTC 가 지원되는 브라우저에서는 video 태그를 지정하던 것을 IE 에서는 div 를 지정하는 형태로 변경되었습니다.
  - cabUrl	 IE 에서 추가된 옵션으로 ActiveX 가 놓여 있는 경로를 지정합니다.

localVideoTarget와 remoteVideoTarget 을 IE 까지 포함하려고 하면 약간의 코딩을 더 해주어야 합니다. 일단 HTML 코드의 변화입니다. HTML 은 playrtc.com 에서 배포하고 있는 sample 중 videochat.html 을 기준으로 변경하였습니다.

``` HTML
<div id="main">
  <button id="channelLeave">채널나가기</button>
  <div class="another" id="remoteStream_ie">
    <video id="remoteStream"></video>
  </div>
  <div class="action-box">
    <div class="message">
      <div class="messageList">
        <div></div>
      </div>
      <form id="sendTextForm" style="display:block;">
        <input id="sendTextInput" placeholder="메시지를 입력하세요." value="" />
        <button>Text Send</button>
      </form>
    </div>
    <div class="my" id="localStream_ie">
      <video id="localStream" width="320" height="240"></video>
    </div>
  </div>
</div>
```

위에서도 확인할 수 있듯이 video 태그를 감싸는 div 에 id 속성을 부여했습니다. 해당 id 속성은 IE 브라우저에서 사용될 것입니다.

```
var local = "localStream",
     remote = "remoteStream";

if(PlayRTC.utils.browser.name === "ie"){
     local = "localStream_ie";
     remote = "remoteStream_ie";
}

conn = new PlayRTC({
     localVideoTarget: local,
     remoteVideoTarget: remote,
     cabUrl: "/cab"
});
```

위 소스에서 localVideoTarget, remoteVideoTarget 옵션을 IE 의 경우 video 태그를 감싸고 있는 상위 div 를 지정하고 있음을 확인 할 수 있습니다. 이제 크롬에서는 기존과 같이 video 태그에 영상을 표시할 것이며 IE 에서는 지정된 div 밑으로 영상이 들어갑니다. 초기화 이후 이벤트를 할당하는 과정은 기존과 동일합니다.


### 채널에 접속하기

IE 에서도 역시 채널에 접속하는 방법은 동일합니다. 그러나 채널에 접속하기 위해서는 크롬브라우저와 마찬가지로 웹 캠의 사용에 대한 허용 여부를 묻는 UI 가 생성됩니다. 여기에서 반드시 허용을 클릭해야만 정상적으로 채널을 접속하여 영상 통신을 준비할 수 있습니다.

### IE와 그렇지 않은 페이지 동시 지원하기
