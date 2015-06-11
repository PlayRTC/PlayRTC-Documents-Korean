# PLAYRTC 디버깅 도구
## 크롬개발자도구 활용하기

크롬 개발자 도구는 F12 또는 Ctrl + Shift + i 단축키로 활성화 시킬 수 있습니다.

크롬개발자도구1

Element : 웹사이트의 HTML 문서 구조를 확인하거나 엘리먼트 편집기를 이용하여 마음대로 추가, 수정, 삭제 할 수 있습니다.
Network : 웹사이트에서 사용되고 있는 정적 리소스들에 대한 네트워크 상황을 확인해볼 수 있습니다. 또한, Ajax(XHR) 요청/응답 및 WebSocket 요청/응답을 확인해 볼 수 있습니다. PlayRTC 에서는 이것을 활용하여 Helper 와의 연동 및 채널 서버와의 websocket 연결 시그널 서버와의 websocket 연결을 확인해 볼 수 있습니다.
Source : 웹사이트에서 사용하고 있는 css, javascript 의 내용을 확인해볼 수 있습니다. 또한 Javascrpt 디버깅을 할 수 있습니다. Breakpoint 를 설정할 수 있으며 trace를 통해 변수와 call stack 을 확인할 수 있습니다.
Timeline : 웹사이트의 성능을 나타내는 고급 내역을 타임라인으로 표시합니다.
Profile : 웹사이트의 메모리 사용 및 분포 내역을 표시합니다.
Resource : 웹사이트의 모든 리소스를 표시합니다. 표시하는 리소스 종류는 WebSQL, IndexedDB, Local Storage, Sesstion Storage, Cookies, Application Cache 입니다.
Console : 콘솔 명령어로 자바스크립트, HTML DOM 등에 접근하여 조작할 수 있습니다. PlayRTC 로그는 Console 창에서 확인해 볼 수 있습니다.


## Network 활용하기

Network 에서는 Helper 와의 연동 및 WebSocket 연동을 확인해 볼 수 있습니다.

Helper 와의 연동 확인해 보기

PlayRTC JS SDK API 중 getChannelList 를 활용하여 생성된 모든 채널 목록을 가져와 보겠습니다. 그리고 이때의 Network 창에서 해당 요청이 어떤식으로 표현되는지 확인해 보겠습니다.

getChannelList API 사용법은 다음과 같습니다.

```
/*
 * 채널 리스트 조회 후 화면에 표시
 * */
 conn.getChannelList(function(data){
      var channels = data.channels,
          channel = null,
          html = "";

      for(var i=0; i<channels.length; i++){
           channel = channels[i];
           html = html + '<li>' + (channel.channelName || channel.channelId)
              + '<button data-channelId="' + channel.channelId +'" >입장</button></li>';
      }

      $("#channelList").html(html);
 });
```
그리도 이때의 Network 창에 나타나는 모습입니다.

크롬개발자도구2

왼쪽 목록에서 해당 요청을 확인 할수 있습니다.
그에 따른 상세 내역을 가운데 내역에서 확인 할 수 있습니다. Header 영역에서는 요청/응답에 관한 Http Header 내용을 볼 수 있습니다.

크롬개발자도구3

Preview 영역과 Response 영역에서는 Helper 에서 반환한 값을 확인 해볼 수 있습니다.
위와 같이 Header창과 Preview, Response 영역 창을 통해 HTTP 상태는 어떠하며 어떠한 값을 반환하였는지, 에러일 경우 어떻게 상태를 나타내는지 등을 확인 할 수 있습니다.


## WebSocket 와의 연동 확인해 보기

Channel 서버와 Signal 서버에 각각 WebSocket 이 연결되거나 연결을 시도했다면 Network 창에서 이를 확인해 볼 수 있습니다.크롬개발자도구4

왼쪽 목록에서 해당 요청을 확인 할수 있습니다.
그에 따른 상세 내역을 가운데 내역에서 확인 할 수 있습니다. Frames 에서 주고받은 데이터를 확인해 볼 수 있으며 Close 상태도 알 수 있다.


## Console 활용하기

Console 창에서는 PlayRTC JS SDK 에서 출력하는 로그를 확인해 볼 수 있습니다.

크롬콘솔

상단 filter와 에러 레벨을 선택함으로써 원하는 로그만 확인해 볼 수 있습니다.
출력된 로그를 확인해 볼 수 있습니다.


## 파이어폭스 개발자도구 활용하기

파이어폭스 개발자 도구는 F12 또는 Ctrl + Shift + i 단축키로 활성화 시킬 수 있습니다.

파이어폭스개발자도구1

검사기 : 웹사이트의 HTML 문서 구조를 확인하거나 엘리먼트 편집기를 이용하여 마음대로 추가, 수정, 삭제 할 수 있습니다.
콘솔 : 콘솔 명령어로 자바스크립트, HTML DOM 등에 접근하여 조작할 수 있습니다. PlayRTC 로그는 Console 창에서 확인해 볼 수 있습니다.
디버거 : 웹사이트에서 사용하고 있는 javascript 의 내용을 확인해볼 수 있습니다. 또한 Javascrpt 디버깅을 할 수 있습니다. Breakpoint 를 설정할 수 있으며 trace를 통해 변수와 call stack 을 확인할 수 있습니다.
스타일편집기 : 웹사이트의 스타일시트를 확인해 볼 수 있습니다. 또한 스타일 시트를 편집하여 적용해 볼 수 있습니다.
프로파일 : 웹사이트의 메모리 사용 및 분포 내역을 표시합니다.
네트워크 : 웹사이트에서 사용되고 있는 정적 리소스들에 대한 네트워크 상황을 확인해볼 수 있습니다. 또한, Ajax(XHR) 요청/응답 확인해 볼 수 있습니다. 그러나 크롬개발자도구와는 다르게 WebSocket 에 관해서는 확인할 수 없습니다. PlayRTC 에서는 이것을 활용하여 Helper 와의 연동을 확인해 볼 수 있습니다.


## Network 활용하기

Network 에서는 Helper 와의 연동을 확인해 볼 수 있습니다.

Helper 와의 연동 확인해 보기

크롬개발자도구에서와 마찬가지로 PlayRTC JS SDK API 중 getChannelList 를 활용하여 생성된 모든 채널 목록을 가져와 보겠습니다. 그리고 이때의 네트워크 창에서 해당 요청이 어떤식으로 표현되는지 확인해 보겠습니다.

getChannelList API 사용법은 크롬개발자도구에서 설명했던 코드와 동일합니다. 그리도 이때의 Network 창에 나타나는 모습입니다.파이어폭스개발자도구2

왼쪽 목록에서 해당 요청을 확인 할수 있습니다.
그에 따른 상세 내역을 가운데 내역에서 확인 할 수 있습니다. 헤더 영역에서는 요청/응답에 관한 Http Header 내용을 볼 수 있습니다.
파이어폭스개발자도구3

응답 영역에서는 Helper 에서 반환한 값을 확인 해볼 수 있습니다.
위와 같이 헤더 영역창과 응답 영역 창을 통해 HTTP 상태는 어떠하며 어떠한 값을 반환하였는지, 에러일 경우 어떻게 상태를 나타내는지 등을 확인 할 수 있습니다.


## Console 활용하기

파폭콘솔

출력된 로그를 확인해 볼 수 있습니다.
