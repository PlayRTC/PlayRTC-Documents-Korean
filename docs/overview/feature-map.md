# Feature Map

각 SDK는 최대한 일치하는 동작과 기능, 사용방법을 가지고 있습니다. 동시에 플랫폼과 언어의 용법에 충실하게 구성되어 있으며 이러한 부분에서 일부 다른 형태가 있을 수 있습니다. 그 외에 플렛폼의 상이점 때문에 일부 기능지원이 다른 부분이 있으며 아래를 참고합니다.


## Feature Map

Feature                  | Browser           | Android | iOS | Explorer ActiveX Plugin
------------------------ | ----------------- | ------- | --- | -----------------------
Media Strem              | O                 | O       | O   | O
Peer Connection          | O                 | O       | O   | O
Data Channel             | O                 | O       | O   | O
Data Channel - String    | O                 | O       | O   | O
Data Channel - File      | O                 | O       | O   | X
Recode Video - Local     | Depend on Browser | X       | X   | O
Recode Video - Remote    | Depend on Browser | X       | X   | O
Recode Audio - Local     | Depend on Browser | X       | X   | X
Recode Audio - Remote    | Depend on Browser | X       | X   | X
Screen Cast              | X                 | O       | X   | X


### Browser Recode Feature Support

Broswer | Platform | Local Video | Local Audio | Remote Video | Remote Audio
------- | -------- | ----------- | ----------- | ------------ | ------------
Chrome  | Desktop  | O           | O           | X            | X
Chrome  | Android  | X           | X           | X            | X
Firefox | Desktop  | O           | O           | O            | O
Firefox | Android  | X           | X           | X            | X
Opera   | Desktop  | O           | O           | X            | X
Opera   | Android  | X           | X           | X            | X


## Known Limitation

WebRTC의 구현체는 아직도 빠른 속도로 개발이 되고 있으며, PlayRTC도 이러한 구현체의 영향을 받습니다. 동시에 방화벽이나 공유기(NAT)뒤에 있는 Peer를 위한 릴레이 서버인 TURN/STUN의 여러 상황과 구현에 영향을 받으며 아래의 알려진 사항들은 이들과 밀접한 연관이 있습니다.

- 안드로이드, iOS에서 스피커폰 모드에서 하울링(스피커의 소리가 마이크로 입력되어 소리가 반복적으로 울리는 현상)이 있을 수 있음. Ear Piece 모드(귀에 폰을 대고 사용하는 일반 통화 모드), 이어폰 모드에서는 정상적 작동.
- 브라우저에서 Data Channel을 이용한 파일전송시 이론적인 한계는 기기의 메모리 한계와 같으나 실사용에서는 20~50M 전후의 크기를 안정적으로 보낼 수 있음.
- 안드로이드에서 Data Channel을 이용한 파일전송시 이론적인 한계는 기기의 저장 한계와 같으나 실사용에서는 10M 전후의 크기를 안정적으로 보낼 수 있음.
- iOS에서는 H264 동영상 코덱만 하드웨어 가속이 되어 PlayRTC의 VP8 코덱의 경우 하드웨어 가속을 받지 못해 안드로이드, 데스크탑에 비해 품질이 일부 낮음.
- Peer Connection에 있어서 브라우저가 가질수 있는 피어의 수는 이론적으로 제한이 없음. 단 데스크탑의 성능상의 문제로 실제 사용가능한 최대 피어의 연결수는 4 인것으로 파악됨.
- Peer Connection에 있어서 iOS, Android, Explorer에서 성능상의 문제로 PlayRTC에서는 사용가능한 최대 피어의 연결수는 1로 제한되어 있음.
