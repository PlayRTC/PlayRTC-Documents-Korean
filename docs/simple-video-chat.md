# 영상/음성 통화 웹앱 만들기
개선된 PlayRTC 2.0버전대의 SDK를 사용하면 Html 파일 하나만 있어도 간단히 영상/음성 통화 웹앱을 만들 수 있습니다. 아래의 내용은 자바스크립트에 대한 매우 기초적인 이해를 필요로 합니다. 자바스크립트에 대해 좀 더 알아보고 싶으면, 아래를 참고 하세요.

- [자바스크립트 수업 - 생활코딩](http://opentutorials.org/course/743)

## 바로 실행해 보기
이전 장을 통해 필요한 환경을 갖춥니다.

예제앱을 다운로드 받거나 아래와 같이 PlayRTC의 Git 저장소 클론을 통해 준비합니다.

- Windows :
```dos
c:\> cd %UserProfile%
c:\> mkdir www
c:\> cd www
c:\> git clone https://github.com/playrtc/example-simple-chat-webapp
```

- OSX, unix-like :
```sh
$ cd ~ && mkdir www && cd www
$ git clone https://github.com/playrtc/example-simple-chat-webapp
```

그리고, 다운로드 압축을 푼 디렉토리에서 명령 프롬프트/파워쉘/터미널을 실행시키고 다음과 같이 웹 서버를 실행 시킵니다.

- Windows :
```dos
c:\> cd %UserProfile%/www
c:\> harp server -p 8000
```

- OSX, unix-like :
```sh
$ cd ~/www && harp server -p 8000
```

이제 브라우저의 주소 입력란에 `http://localhost:8000/example-simple-chat-webapp/index.html`를 실행시키면 다음과 같은 화면이 나타나고 직접 웹앱을 실행 해 볼 수 있습니다.

![](DraggedImage.png)

## 구성 살펴 보기
이 웹앱은 실제로는 `index.html`파일 하나로 이루어져 있습니다.

영상/음성 통화를 하기위해 방을 만들고 만들어진 방에 참여하는데에 어떠한 서버 프로그래밍이나 복잡한 작업이 없이 간단한 html 파일 하나면 가능한 것 입니다!

그러면, 기능과 함께 화면 구성을 살펴보고 사용해 봅시다.

<img uml="
actor Caller
control PlayRTCServer
actor Callee
Caller -> PlayRTCServer: 채널 생성을 요청
PlayRTCServer -> Caller: 채널 접속 및 생성된 채널의 ID를 알려줌
Callee -> PlayRTCServer: 채널의 ID를 입력하고 채널 접속을 시도
PlayRTCServer -> Callee: 채널 접속
Caller <-> Callee: P2P 연결
">

이 웹앱은 `Caller`라는 사용자가 채널 생성이라는 버튼을 눌러 채널을 생성하면, `Callee`란 사용자가 해당 채널 번호를 확인하여 채널 입장을 하는 기능을 수행합니다.

![](DraggedImage-1.png)

여기서 채널이란, 이야기 방을 의미합니다. `Caller`와 `Callee`가 PlayRTC를 통해 통화 하기 위해서는 `Caller`가 직접 `Callee`에게 전화를 거는 것이 아닌 `Caller`가 PlayRTC 서버에 이야기방을 생성하고, 생성된 이야기 방의 식별자를 `Callee`에게 알려주면 `Callee`가 PlayRTC의 이야기 방에 접속하여 `Caller`와 통화를 할 수 있는 구성입니다.

## 코드 살펴 보기
그러면 실재로 코드를 살펴 보도록 하겠습니다. HTML은 [Twitter](https://github.com/twitter)의 [Bootstrap](http://bootstrapk.com/)란 UI 프레임워크를 사용하고 있습니다. Bootstrap를 사용하면 간편하게 깔끔하고 웹표준과 모바일에 적합한 페이지를 만들 수 있습니다.

- [Bootstrap - 생활코딩](http://opentutorials.org/course/477)

HTML 마크업에는 다음과 같은 Video 태그가 있습니다.
```html
<video class="remote-video center-block" id="callerRemoteVideo"></video>
<video class="local-video pull-right" id="callerLocalVideo"></video>
```
이 Video 태그는 실제로 `Caller`와 `Callee`의 얼굴 영상이 나타날 곳 입니다. 이 Video 태그에 PlayRTC 자바스크립트 라이브러리의 객체가 영상을 연결(bind)합니다. 자세한 내용은 아래의 자바스크립트 코드를 같이 살펴보며 알아보겠습니다.

### Caller
우선 Caller의 코드는 다음과 같습니다.

```js
'use strict';

var createChannelButton = document.querySelector('#createChannel');
var createChannelId = document.querySelector('#createChannelId');
var appCaller;

appCaller = new PlayRTC({
  projectKey: "aee5f87e-c376-4f7e-b470-5c4ba725186d",
  localVideoTarget: "callerLocalVideo",
  remoteVideoTarget: "callerRemoteVideo"
});

appCaller.on('connectChannel', function(channelId) {
  createChannelId.value = channelId;
});

createChannelButton.addEventListener('click', function(e) {
  e.preventDefault();
  appCaller.createChannel();
}, false);
```

우선은 `use strict`을 통해 모던 자바스크립트의 엄격한 오류 검사 기능을 활용하도록 합니다. 이 문서의 모든 예제 자바스크립트는 엄격한(strict) 오류 검사에 맞추어 작성되어 있습니다.
- [Strict 모드 - MSDN](https://msdn.microsoft.com/ko-kr/library/ie/br230269(v=vs.94).aspx)

```js
var createChannelButton = document.querySelector('#createChannel');
var createChannelId = document.querySelector('#createChannelId');
```

이 세줄의 코드는 UI상의 버튼과 Input 폼 DOM을 가리키고 있습니다. 이제 이 UI요소에 이벤트를 부여하여 사용자가 버튼(`createChannelButton`)을 클릭하면, 서버에 채널을 만들어 줄것을 요청하고, 만들어진 채널의 방 번호를 Input 폼(`createChannelId`) 안에 넣도록 해 보겠습니다.

```js
var appCaller;
appCaller = new PlayRTC({
  projectKey: "aee5f87e-c376-4f7e-b470-5c4ba725186d",
  localVideoTarget: "callerLocalVideo",
  remoteVideoTarget: "callerRemoteVideo"
});
```
appCaller란 이름으로 새로운 PlayRTC 객체(`new PlayRTC`)를 가리키도록 합니다. 새로운 PlayRTC 객체를 생성할때, 설정 [객체를 리터럴 형태](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Working_with_Objects#.EA.B0.9D.EC.B2.B4_.EC.83.9D.EC.84.B1.ED.95.98.EA.B8.B0)의 인수로 넣어 두어 각종 설정을 할 수 있습니다. [설정 객체](#)에는 PlayRTC 플렛폼에서 제공하는 프로젝트 키와 Caller와 Callee의 영상이 표출될 Video 태그를 지정해 줍니다.

예제상의 프로젝트 키, `aee5f87e-c376-4f7e-b470-5c4ba725186d`는 개발용으로 공개된 것으로 잠깐의 테스트용도입니다. 만약 본격적으로 개발을 하거나 격리된 환경을 원한다면 [여기](https://developers.sktelecom.com/community/faq/)를 참고하여 자신만의 PlayRTC 프로젝트를 생성하고, 프로젝트 생성시 제공되는 프로젝트키를 이곳에 입력하면 됩니다.

- [T Developers 프로젝트 생성 및 키 발급 방법](https://developers.sktelecom.com/community/faq/)

```js
appCaller.on('connectChannel', function(channelId) {
  createChannelId.value = channelId;
});

createChannelButton.addEventListener('click', function(e) {
  e.preventDefault();  //폼에 적용된 브라우저 동작을 사용하지 않음
  appCaller.createChannel();
}, false);
```

실질적인 작동 기능이 담겨있는 코드 입니다. PlayRTC 자바스크립트 라이브러리는 자바스크립트 사용 방식중 널리 사용되고 있는 [이벤트 중심(event driven)](http://ko.wikipedia.org/wiki/%EC%9D%B4%EB%B2%A4%ED%8A%B8_(%EC%BB%B4%ED%93%A8%ED%8C%85))으로 사용하게 되어 있습니다.

`PlayRTC`의 [인스턴스 객체](https://developer.mozilla.org/ko/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript)인 `appCaller`의 내장 기능중 이벤트를 다루는 `on`을 사용하여 `connectChannel`이벤트가 발생했으때, `connectChannel`이벤트의 결과물인 `channelID`를 `Input 폼(createChannelId)`에 넣어 사용자가 지금 만들어지고 접속한 채널의 ID를 알 수 있도록 합니다.

`채널생성(createChannelButton)버튼`을 클릭하면, 좀전에 생성한 appCaller객체를 통해 서버로 채널을 만들것을 요청합니다. 그리고 성공적으로 채널이 생성되면 자동으로 서버의 채널에 접속하게 되고 이때 채널 번호를 `Input 폼(createChannelId)`에 넣도록 하는 코드입니다.

`addEventListener`는 자바스크립트에서 이벤트를 다룰때 사용하는 기능입니다.
- [addEventListener - 생활코딩](http://opentutorials.org/module/904/6761)
`e.preventDefault();`는 폼에 기본적으로 적용된 브라우저 동작을 사용하지 않도록 하는 기능입니다.
- [preventDefault - MDN](https://developer.mozilla.org/ko/docs/Web/API/Event/preventDefault)

### Callee

```js
'use strict';

var connectChannelId = document.querySelector('#connectChannelId');
var connectChannelButton = document.querySelector('#connectChannel');
var appCallee;

appCallee = new PlayRTC({
  projectKey: "aee5f87e-c376-4f7e-b470-5c4ba725186d",
  localVideoTarget: "calleeLocalVideo",
  remoteVideoTarget: "calleeRemoteVideo"
});

connectChannelButton.addEventListener('click', function(e) {
  e.preventDefault();
  var channelId = connectChannelId.value;
  if (!channelId) { return };
  appCallee.connectChannel(channelId);
}, false);
```

`Callee`의 코드 대부분은 앞에서 설명한 바와 같습니다.

사용자가 접속하고자 하는 채널의 ID를 Input 폼(`connectChnnelID`)에 입력하고 `채널 입장`버튼(`connectChannelButton`)을 클릭하면 `PlayRTC`의 인스턴스 객체인 `appCallee`의 `connectChannel`기능을 통해 서버에 있는 채널로 접속합니다.

이때, Input 폼(`connectChnnelID`)이 비어있을 경우 `appCallee.connectChannel`을 호출하면 비어있는 값으로 채널에 접속하려 시도하기때문에 오류가 발생합니다. 이 오류가 발생하는 것을 막기 위해서 Input 폼이 비어있으면 정의되지 않은 값(`undefined`)을 반환(`return`)하여 다음 행의 명령인 채널 접속 시도를 하지 않게 됩니다.

- [undefined와 null - 자바스크립트 가든](http://bonsaiden.github.io/JavaScript-Garden/ko/#core.undefined)

`Callee`의 코드도 실질적으로 `Caller`의 코드와 크게 다르지 않습니다. 사실상 `Caller`가 `Callee`가 될수도 있고 `Callee`가 `Caller`도 될수 있지만 여기서는 전체적인 흐름을 간단히 설명하기 위해 이렇게 분리 해 놓았습니다.

## 복습
단순한 코드이지만 한편으로 처음 사용해 보는 개발자를 위해 차근차근 살펴 보았습니다. 이 코드를 살펴보면서 다루어진 PlayRTC의 주요 개념에 대해 다시한번 살펴 보겠습니다.

- PlayRTC 자바스크립트 라이브러리는 이벤트 중심의 구성을 가진다.
	- 따라서 “버튼을 누를때”, “채널을 만들때”와 같이 특정한 순간을 중심으로 구성을 하면 한결 수월하게 작성이 가능합니다.
- PlayRTC 서버는 채널이라는 방 개념을 가지고 있고 매우 중요하다.
	- 사용자(Peer)와 사용자(Peer)가 만나기 위해서는 PlayRTC서버의 채널을 통해 중계(Signaling) 합니다.
	- “상대방의 e-mail과 같은 ID를 통해 전화하기”, “홈페이지에 PlayRTC로 상담요청하기”등 좀더 다양한 기능 구성을 갖는 서비스를 만들려면 채널과 채널의 접속상태인 세션을 동적으로 다루는 역할이 필요합니다. 이 채널을 어떻게 다루느냐에 따라 다양한 기능과 상황의 서비스를 만들 수 있는 것 입니다. 이 부분은 별도의 문서를 통해 살펴보겠습니다.

## 마무리
이렇게 간단하게 영상/음성 통화 웹앱을 만들어 보았습니다. 아주 쉽죠? WebRTC를 사용하는데 필요한 복잡한 내용은 PlayRTC에 맞기고, 멋지고 세련된 서비스를 만드는데 집중하세요! PlayRTC는 쉽고 편리하게 영상/음성 커뮤니케이션 기능을 제공합니다.
