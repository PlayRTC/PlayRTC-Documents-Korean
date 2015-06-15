# PlayRTC Support Environment
PlayRTC가 지원하는 환경입니다.


## Browser Support

Browser       | Platform      | Minimum       | Recommend
------------- | ------------- | ------------  | -------------
Chrome        | Desktop       | Over 23.x     | Latest Stable
Chrome        | Android       | Over 36.x     | Latest Stable
Firefox       | Desktop       | Over 22.x     | Latest Stable
Firefox       | Android       | Over 31.x     | Latest Stable
Opera         | Desktop       | Over 18.x     | Latest Stable
Opera         | Android       | Over 22.x     | Latest Stable
Safari        | Desktop       | Not Available | Not Available
Safari        | iOS           | Not Available | Not Available
Explorer      | Desktop       | Not Available | Not Available

- 크롬, 파이어폭스, 오페라의 경우 Windows, OSX, Linux 모두 최신 버전의 브라우저를 사용할 것을 권장합니다.
- 크롬, 파이어폭스, 오페라의 경우 안드로이드에서 최신 버전의 브라우저를 사용할 것을 권장합니다.
- Explorer는 현재 브라우저에서 WebRTC를 지원하지 않고 있으며, Explorer에서 사용하고자 하면 PlayRTC ActiveX Plugin을 사용하여야 합니다.
- 사파리는 현재 브라우저에서 WebRTC를 지원하지 않고 있습니다.


## SDK Support

Platform             | OS API                                                   | Device
-------------------- | -------------------------------------------------------- | ------------------------------
Native Browser       | See Above                                                | See Above
Android              | Over API Level 15, Version 4.0.4, Ice Cream Sandwich     | Over Ice Cream Sandwich Device
iOS                  | Over iOS 7.1.2                                           | Over iPhone 5, iPad Air, iPad Mini2
Windows Explorer     | Over Windows 7, Explorer 10                              | Over Windows 7 Desktop Device


## Codec Support

Item             | Codec
---------------- | -----
Video and Audio  | VP8
Audio            | Opus

- WebRTC는 기본적으로 미디어 코덱을 지정하고 있지는 않지만, 현재 PlayRTC는 VP8과 Opus기준으로 작동하고 있습니다.


## Network Environment
