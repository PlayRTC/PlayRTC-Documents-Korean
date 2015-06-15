# PlayRTC Architecture


## Structure

PlayRTC는 크게 Client SDK와 Backend Service로 구성되어 있습니다. Client SDK는 개발자 여러분이 직접 앱을 개발하는데 사용하게 되며, Backend Service는 Client SDK의 작동을 지원합니다.

![Structure](https://docs.google.com/drawings/d/1DdL1798xUbbPbDsWn0048TNgSoPBBu0OTrjc-llauII/pub?w=760&h=685)


### Client SDKs
PlayRTC는 WebRTC를 네이티브로 지원하는 Chrome, Firefox등의 브라우저에서 Javascript SDK를 제공하고 있습니다. 안드로이드, iOS의 네이티브 환경에서 WebRTC를 구현하여 SDK를 제공하고 있으며, 이를 통해 안드로이드 앱, iOS앱에 WebRTC를 적용할 수 있습니다. WebRTC를 지원하지 않는 IE에도 마찬가지로 ActiveX Plugin과 SDK를 제공하고 있습니다.

각 SDK는 최대한 일치하는 동작과 기능, 사용방법을 가지고 있습니다. 동시에 플랫폼과 언어의 용법에 충실하게 구성되어 있으며 이러한 부분에서 일부 다른 형태가 있을 수 있습니다. 그 외에 플렛폼의 상이점 때문에 일부 기능지원이 다른 부분이 있으며 이 부분은 다음을 참고 합니다.

[SDK 지원여부](./feature-map.md)


### Backend Service
Backend Service는 SDK의 기능을 수행하기 위한 서버자원으로 핵심적으로는 Signaling, Channel을 관리해 줍니다. 그 외에 프로젝트 인증, 관리, 사용을 위한 [T Developers](T Developers)가 있습니다.


## Flow

WebRTC 기반 서비스를 만들고자 한다면 다음의 두가지 부분을 해결하여야 합니다.

- 다수의 Peer간의 연결/설정
- 연결된 Peer간의 통신.

기본적으로 연결된 Peer간의 통신은 브라우저에서 제공하는 기능입니다. 하지만 다수의 Peer간의 연결은 직접 구현이 필요하며 난이도와 기술 복잡도가 높은편에 속합니다. PlayRTC에서는 이 과정을 내부적으로 구현하여 제공하고 있습니다.


### Basic Flow

![Flow](https://docs.google.com/drawings/d/1jXCy-IcHo52mXH-x0IBJB6BhXIhF3O3LdLKAbiN4Dfs/pub?w=870&h=641)

PlayRTC는 채널 이라는 개념을 도입하여 Peer와 Peer간 연결을 다룰 수 있습니다.

위와 같이 각 Peer는 PlayRTC 서비스에 채널을 만들고, 요청하고, 접속한 상태에서 각 Peer에 대한 정보를 주고 받고 이를 토대로 실제 P2P 연결을 하게 됩니다.

이때 서로에 대한 정보를 주고받는 것을 Signaling 이라 부르며 접속환경, 방화벽 및 사설 IP 환경등의 정보를 공유하여 필요시 PlayRTC에서 제공하는 TURN, STUN의 릴레이 서버를 경유하여 Peer간 접속이 가능토록 합니다.

PlayRTC 채널은 다음과 같은 기능을 수행합니다.

- 채널에 대한 생성과 유지와 삭제 과정 전반을 관리
- 서비스 서버와 Peer에 대한 인증 처리
- 시그널링과 미디어 변환, 미디어 Relay(STUN, TURN) 기능 제공
- 모니터링 및 로그 기능 제공


### Basic Sequence

![Sequence](https://docs.google.com/drawings/d/1tIuFc4d6R__PlBNwsBsp6yrdmVLZdaWwQYftknenos0/pub?w=674&h=556)

개략적으로 순서를 간단하게 살펴봤습니다. 실제로 Peer-to-Peer 채널 연결 및 통신은 이보더 더 복잡하고 적지않은 과정을 거쳐야 합니다.

[T Developers]:https://developers.sktelecom.com/
