# PlayRTC 소개

PlayRTC는 영상, 음성, 데이터를 전송하는 웹 표준 기술 WebRTC 기반의 플랫폼으로, 표준 기술을 활용하여 새로운 비즈니스를 손쉽게 만들고 빠른 개발이 가능하도록 합니다. 아울러 다양한 단말 환경 지원과, 고가의 서버 구축이 필요없이 곧바로 원하는 서비스를 제작 가능합니다.

PlayRTC를 잘 활용하면 다양한 형태의 영상, 음성 미디어 전송 서비스를 개발할 수 있습니다. 또한 굳이 미디어 통신이 아니더라도 다양한 형태의 콘텐트를 Peer to Peer로 전송하는 P2P 서비스도 구현할 수 있습니다.

예상 응용 서비스로는 아래와 같은 것이 있을 수 있으며 개발자의 창의적인 아이디어와 PlayRTC의 빠르고 쉬운 개발 및 운영 환경은 새로운 비즈니스를 만드는데 있어 경쟁력 될 것 입니다.

- 영상 통신 기반의 B2B, B2C 웹/앱 서비스 (화상 채팅, 원격 상담, 원격 진단 등)
- 영상을 기반으로 하는 Light Weight IoT서비스 (원격 감시 CCTV 등)
- 데이터 통신 기반의 P2P/Grid 응용 웹/앱 서비스 (P2P 모바일 데이터 파일 전송, P2P 공동 Memo 등)


## WebRTC와 PlayRTC

PlayRTC는 WebRTC기술을 실재 시장 환경에서 활용하기 편리하도록 만든 플랫폼 입니다.

일반적으로 WebRTC는 브라우저 내 미디어 엔진과 데이터에 대한 내용을 담고 있으며 아래와 같습니다.

- Peer-to-Peer Connections (RTCPeerConnection)
- Peer-to-Peer Data API (RTCDataChannel)
- Media Stream (GetUserMedia) 등

하지만, 이 미디어 엔진과 데이터에 대한 내용을 실재 사용하기 위해서는 보통 Signaling 이라 부르는 통신 과정을 직접 구현하여야 하며 아래와 같습니다.

- SDP
- ICE, STUN, TURN for NAT Traversal
- DTLS 등


## PlayRTC 서비스 주요 기능

위와 같이 WebRTC가 실재로 잘 작동하기 위해서는 브라우저 지원 이외에도 상호간 연결을 중계하는 서버기술이 필요함을 알 수 있으며, 이는 상당한 비용과 기술적 역량을 필요로 하게 됩니다. PlayRTC는 다양한 사업자, 개발자가 WebRTC 기술을 보다 쉽게 활용 할 수 있도록 플랫폼으로 제공 하고 있으며, 다음을 주요기능으로 삼고 있습니다.

- Android, iOS의 모바일 플랫폼 지원
- Internet Explorer 지원
- TURN / STUN 서버 제공
- 품질 높은 영상 통신 인프라
- 서비스 개발 및 운영 수준 향상을 위한 개발자 커뮤니티 및 운영 환경
