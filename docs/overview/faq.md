# Frequently Asked Questions

자주 묻는 질문과 답변 입니다.


## 라이센스
### PlayRTC의 라이센스 정책은?
PlayRTC는 현재 개방형 API로 누구나 사용 가능합니다. 기타 자세한 이용약관은 [T Developers](https://developers.sktelecom.com/)를 통해 확인가능합니다.

### PlayRTC의 상용, 유료 라이센스 정책은?
현재 별도의 상용, 유료 라이센스 정책이 없으며 누구나 사용 가능합니다.

### 대규모 사용시 PlayRTC의 라이센스 정책은?
대규모 사용시의 별도의 라이센스 정책이 없으며 누구나 사용 가능합니다. 하지만, 대규모 사용을 위해서는 테스트 및 서비스 프로토타이핑이 필요할 수 있음으로 만약 대규모 사용이 예상된다면 `playrtc@sk.com`으로 별도 문의를 요청드립니다.

### 고신뢰 서비스 및 기술지원 라이센스 정책은?
PlayRTC는 기본적으로 안정적 서비스 품질을 유지하도록 운영되고 있습니다. 기본적인 기술지원 또한 홈페이지 내 게시판을 통해 운영되고 있습니다. 다만, 매우 높은 신뢰성과 높은 기술지원이 필요한 경우 `playrtc@sk.com`으로 별도 문의를 요청드립니다.

### PlayRTC의 하루 사용 최대량과 최대량 증설은?
기본은 API 호출 기준 1일당 1,000회 이며 [T Developers](https://developers.sktelecom.com/)의 My Project > Service > 사용한도 > 한도변경을 통해 추가로 한도를 설정할 수 있습니다.


## 프로젝트 키
### 프로젝트 키란 무엇입니까?
App등의 클라이언트가 PlayRTC 서비스에 접근하기 위한 키 입니다. 이 키값은 고유의 값으로 앱별/서비스별로 별도 발급받아 사용하게 됩니다.

### 프로젝트 키 발급, 사용방법은?
이 문서의 [Project Key](./project-key.md)를 발급방법을 확인하세요. 각 SDK별 Project Key 사용방법은 아래를 확인하세요.

- [Javascript Tutorial](../browser/simple-video-chat.md)
- [Android Tutorial](../android/simple-video-chat.md)
- [iOS Tutorial](../ios/simple-video-chat.md)

### Javascript SDK를 사용할 시 프로젝트키가 HTML 페이지에 노출됩니다. 안전하게 보호하는 방법은 무엇인가요?
PlayRTC의 프로젝트키 호출시 T Developers 보안 정책을 거쳐 호출하게 되어있습니다. T Developers에서 허용하는 URL 혹은 IP를 설정하면 되며 아래의 링크에 자세히 나와있습니다. 브라우저에서 Javascript SDK를 사용시 보안을 위한 필수적인 설정이니 꼭 설정을 하여야 합니다.

- [프로젝트 키를 통해 접속 제한 설정하기](./project-key.md)

참고로 iOS, Android SDK를 사용할 시에는 프로젝트키가 컴파일시 앱으로 묶여 노출되지 않으므로 해당사항이 없습니다.


## 보안
### PlayRTC의 영상/음성이나 Data Channel의 보안은?
PlayRTC는 WebRTC의 보안 규격을 그대로 이어받았습니다. WebRTC의 Media Stream과 Data Channel은 SSL에 준하는 전송계층 통신구간 암호화를 기본으로 하는 종단간 암호화가 구성되어 있습니다. PlayRTC는 WebRTC의 이러한 Media Stream의 SRTP, Data Channel에서는 SCTP를 기본으로 사용합니다. 기타 PlayRTC가 자체적으로 사용하는 HTTP RESTful이나 WebSocket은 SSL 구간 암호화가 적용되어 있습니다.

### 프로젝트 키를 안전하게 사용하는 방법은?
iOS, Android는 앱 컴파일로 묶일시에는 일반적인 방법으로 외부에 키가 노출되지 않습니다. 하지만 Browser에서 사용할시 HTML 페이지에 프로젝트 키가 쉽게 노출되며 이는 접속 허용 IP와 URL를 설정함으로써 안전하게 사용할 수 있습니다. 자세한 내용은 이 문서의 프로젝트 키 항목을 참고하세요.


## TURN, STUN, ICE
### TURN, STUN, ICE등은 무슨 뜻 인가요?
WebRTC는 전통적인 Real Time Commnnication의 기술과 개념을 응용하고 있습니다. TURN, STUN, ICE등의 용어는 RTC 기술에서 유래되거나 사용되고 있는 것으로 보다 자세한 내용은 별도의 용어집을 통해 확인이 가능합니다.

- [용어집](./glossary.md)

또한 PlayRTC 응용 서비스 개발자는 실질적으로 이러한 RTC기술의 이해없이 곧장 사용이 가능토록 구성되어 있음으로 이를 몰라도 PlayRTC를 사용하는데 지장이 없습니다.


## 서비스 서버, 서비스 헬퍼
### 서비스 서버, 서비스 헬퍼란?
이는 1.x 버전의 SDK에서 사용되던 개념입니다. 1.x에서는 별도의 서버를 두어 PlayRTC 서비스를 중계하게 하였으며 비즈니스 로직을 별도의 서버에 담아 둘 수 있도록 하였습니다. 지금은 더이상  사용되고 있지 않습니다.


## 연결
### 다자간 연결이 가능한가요?
WebRTC에서 한 Peer는 다수의 Peer를 가질 수 있습니다. 다만 이를 처리하는 기기의 성능적 한계가 있을 수 있습니다. 자세한 내용은 아래의 Known Limitation을 참고하세요.

- [Feature Map > Known Limitation](./feature-map.md)
