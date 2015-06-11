# Glossary
PlayRTC에서 자주 사용되는 용어 입니다. PlayRTC에서 정의된 용어는 명칭 옆에 PlayRTC라 표기하였습니다.

## Channel - PlayRTC
Peer의 여부를 관리하는 상태 관리에 대한 PlayRTC의 개념 입니다. 특히 양 Peer가 상호 PlayRTC에 연결되어 있는지를 판별하는데 자주 사용됩니다.


## Channeling - PlayRTC

Peer가 서로 다른 Peer와 통신을 수행하고 유지하고 종료하기 위해 수행하는 모든 작업입니다. 이를 위해 Peer는 보통 채널서버와 통신을 하며 채널 서버에서 제공하는 시그널링 서버 및 릴레이 서버등과도 통신을 수행해야 합니다.


## Channel Server - PlayRTC

채널링을 담당하는 서버입니다.

- 채널에 대한 생성과 유지와 삭제 과정 전반을 관리
- 서비스 서버와 Peer에 대한 인증 처리
- 시그널링과 미디어 변환, 미디어 Relay(STUN, TURN) 기능 제공
- 모니터링 및 로그 기능 제공

## Data Channel

WebRTC에서 Media Stream이 영상/음성을 전송한다면, Data Channel은 데이터를 전송하는 채널입니다. 이 채널을 통해 문자열이나 바이트 배열을 보낼 수 있으며 Media Stream과 다르게 SCTP라는 프로토콜을 사용합니다.

- [WebRTC data channels - HTML5 Rocks](http://www.html5rocks.com/en/tutorials/webrtc/datachannels/)


## ICE (Interactive Connectivity Establishment)

ICE는 웹 브라우저 간에 피어 투 피어 접속을 할 수 있게 해 주는 프레임워크입니다. A에서 B로 직접적인 피어 투 피어 접속이 쉽게 동작 하지 않는데는 많은 이유가 있습니다. 공개된 접속을 막는 방화벽을 통과해야 하기도 하고, 공인 IP 주소를 갖지 않은 기기들에 유일한 주소를 부여해야 하며, 라우터가 직접적인 피어 투 피어 연결을 지원하지 않는 경우 서버를 통해 데이터를 중계 해야 할 수도 있습니다. 이러한 이유들을 해결하기 위해 ICE는 아래에 기술된 기술들을 사용합니다.

- [ICE IETF RFC Document 5245](http://tools.ietf.org/html/rfc5245)


## ICE Candidate

미디어에 대한 정보를 교환하는 것 뿐만 아니라, 피어 투 피어 통신을 하려는 양쪽은 네트워크 연결에 관한 정보도 교환해야 합니다. 이 정보는 ICE 후보와 통신 가능한 방법(직접 혹은 TRUN 서버를 통한 방법)에 대한 상세 내용입니다.


## NAT (Network Address Translation)

NAT은 기기에 공인 IP 주소를 부여하는데 사용합니다. 라우터는 공인 IP 주소를 가지고 있고, 라우터에 연결되는 모든 기기는 사설 IP 주소를 갖습니다. 기기가 통신 요청을 하면 기기의 사설 IP 주소는 라우터 공인 IP 주소의 유일한 포트로 연계됩니다. 이런 방법 덕분에 각각의 모든 기기가 공인 IP 주소를 갖지 않아도 인터넷 상에 노출될 수 있습니다.

일부 라우터들은 네트워크를 통해 기기에 접속 하려는 경우에 제약을 가하는 경우가 있습니다. 이런 경우 STUN 서버가 찾은 공인 IP를 가졌다 하더라도 연결을 할 수 없습니다. 이런 상황에서는 TURN으로 턴해야합니다.

- [NAT - Wikipedia](http://en.wikipedia.org/wiki/Network_address_translation)


## NAT Traversal

NAT Gateway 를 통과하는 TCP/IP 세션 네트워크 또는 UDP 커넥션을 생성하고 관리하는 기술의 통칭.


## Peer - PlayRTC

통신을 원하는 사람 혹은 소프트웨어 혹은 클라이언트 프로그램을 의미합니다. PlayRTC에서는 사람이나 클라이언트 프로그램을 혼용해서 쓰는 편입니다.


## Peer ID - PlayRTC

PlayRTC가 Peer에게 발급합니다. Peer 채널에 접속시에 자동으로 PlayRTC로부터 부여 받으며, 고유한 값입니다.


## SDP (Session Description Protocol)

SDP는 연결된 멀티미디어 데이터의 해상도, 형식, 코덱, 암호화 방법 등등을 기술하는 표준으로써, 피어 투 피어로 연결된 서로 간에 전송될 데이터를 이해하는데 사용됩니다. SDP는 미디어 자체라기 보다는 메타데이터라고 볼 수 있습니다.

- [SDP IETF RFC Document 4566](https://tools.ietf.org/html/rfc4566)


## Signaling - PlayRTC

![signaling diagram](https://mdn.mozillademos.org/files/6119/webrtc-complete-diagram.png)

시그널링은 상호간에 통신을 원하는 Peer가 자신의 통신을 위한 프로파일 문서(SDP)를 Peer간에 주고받고 P2P 통신에 필요한 모든 일을 수행하는 행위를 의미합니다. 보통 시그널링을 위해 Peer는 시그널링 서버를 필요로 합니다. 이 과정에서 NAT Traversing이나 미디어 변환 및 릴레이 등의 과정이 요구될 수 있습니다. 위의 다이어그램은 WebRTC에서 일어나는 시그널링을 잘 설명해 주고 있으며 PlayRTC도 위와 유사한 형태로 시그널링이 진행됩니다.


## STUN (Session Traversal Utilities for NAT)

STUN은 사용자의 공인 주소를 찾고 피어 투 피어로 직접적인 연결을 막는 요소가 라우터 내에 있는지 알아내는 프로토콜입니다.
클라이언트는 자신의 공인 주소와 라우터의 NAT 뒤에 있는 자신이 접속이 가능한지 여부를 알기 위해 인터넷 상의 STUN 서버에 요청(request)을 보내 묻습니다.

- [TURN, STUN IETF RFC Document 5766](http://tools.ietf.org/html/rfc5766)


## TURN (Traversal Using Relays around NAT)

NAT를 사용하는 일부 라우터들은 ‘대칭형 NAT(Symmetric NAT)’이라는 제약 사항을 사용합니다. 이 규칙은 이전에 피어 투 피어로 연결했던 대상에 대해서만 연결을 허용합니다.

TURN은 TURN 서버를 통한 공개된 연결에 대해 대칭형 NAT 제약을 적용하지 않고, TURN 서버를 통해 모든 정보를 중계하는 것입니다. TURN 서버와 연결을 한 후 피어 투 피어로 통신하려는 모든 대상에게 “TURN 서버로 패킷을 보내면 자신에게 포워딩 된다”고 알려 주면 됩니다. 보통 이 방법은 오버헤드 있음으로 다른 대안이 전혀 없는 경우메만 사용하는 것이 좋습니다.

- [STUN IETF RFC Document 3489](http://tools.ietf.org/html/rfc3489)
