# 개발 환경 구성


## IDE 준비

iOS 개발을 하기 위해서는 기본적으로 맥 하드웨어와 OSX 운영체제 및 애플 계정 및 애플에서 만든 [Xcode](Xcode) [IDE](IDE)이 필요합니다.

애플계정 생성 및 [Xcode](Xcode) 설치는 아래의 애플 공식문서를 참고하여 생성 및 설치토록 합니다.

- [Start Developing iOS Apps Today](https://developer.apple.com/library/ios/referencelibrary/GettingStarted/RoadMapiOS/index.html#//apple_ref/doc/uid/TP40011343)

설치를 완료했


## PlayRTC iOS SDK 준비하기

아래의 링크를 통해 다운로드 받습니다.

- <http://www.playrtc.com/download/>

다운로드 받고 압축을 풀면 1개의 `a` 확장자 파일, 1개의 `dat`확장자 파일과 다수개의 `h`확장자를 가지는 헤더파일이 나오는데, 원하는 프로젝트에 다음과 같이 배치 하면 됩니다.

- [PROJECT-ROOT]/PlayRTC/libs/icudtl.dat
- [PROJECT-ROOT]/PlayRTC/libs/libsktplayrtc.x.x.x.a
- [PROJECT-ROOT]/PlayRTC/public/PlayRTC.h 및 기타 `h` 확장자 파일

### Objective-C로 개발 준비 하기

PlayRTC SDK는 현재 [Objective-C](Objective-C) 언어로 구성되어있으며 곧바로 다음장인 XCode 준비하기로 넘어가면 됩니다.

### Swift로 개발 준비

PlayRTC를 [Swift](Swift) 언어로 개발하기 위해서는 다음과 같이 `MyAppProjectName-Bridging-Header.h`파일을 작성하고, PlayRTC iOS SDK의 헤더파일을 임포트 하여야 합니다. 이 파일은 아래의 디렉토리에 위치시키도록 합니다.

- [PROJECT-ROOT]/MyApp/MyAppProjectName-Bridging-Header.h

그리고 실제 파일에 들어갈 내용은 아래와 같습니다.

```
#ifndef MyAppProjectName_Bridging_Header_h
#define MyAppProjectName_Bridging_Header_h

#import "PlayRTC.h"
#import "PlayRTCData.h"
#import "PlayRTCDataHeader.h"
#import "playRTCDataObserver.h"
#import "PlayRTCDataSendObserver.h"
#import "PlayRTCDefine.h"
#import "PlayRTCFactory.h"
#import "PlayRTCMedia.h"
#import "PlayRTCObserver.h"
#import "PlayRTCSettings.h"
#import "PlayRTCVideoView.h"

#endif
```

보다 더 자세한 내용은 아래의 링크를 통해 확인합니다.

- [Swift and Objective-C in the Same Project](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)


## [Xcode](Xcode) 빌드환경 준비하기

원하는 프로젝트를 [Xcode](Xcode)에서 만들고 위와 같이 SDK를 프로젝트 폴더에 복사하고 필요하다면 PlayRTC iOS SDK 브릿징 헤더 파일을 만들고 난 다음 빌드 환경을 구성하도록 하여야 합니다.

### Linked Frameworks and Libraries

MyApp > Targets > MyApp > General > Linked Frameworks and Libraries에서 다음과 같이 링크된 라이브러리를 설정 합니다.

- UIKit.frameworl
- Foundation.framework
- OpenGLES.framework
- Securuty.framework
- AudioToolbox.framework
- CoreFoundation.framework
- AVFoundation.framework
- CoreVideo.framework
- GLKit.framework
- CoreAudio.framework
- CoreMedia.framework
- MobileCoreService.framework
- SystemConfiguration.framework
- CFNetwork.framework
- CoreGraphics.framework
- QuartCore.framework
- libm.dylib
- libc++.dylib
- libicucore.dylib
- libsqlite3.dylib


### Build Settings

MyApp > Targets > MyApp > Build Settings에서 다음과 같이 설정합니다.

- Architecture
    - Architecture : $(ARCHS_STANDARD), $(ARCHS_STANDARD_INCLUDING_64_BIT)
    - Build Active Architecture Only : NO
    - Valid Architecture : armv7
- Linking
    - Other Linker FLags : -ObjC
- Apple LLVM 6.0 – Language
    - C Language Dialect : C99[-std=c99]
- Apple LLVM 6.0 – Language – C
    - C++ Language Dialect : C++11[-std=c++11]

### Build Phases

MyApp > Targets > MyApp > Build Phases에서 다음과 같이 설정합니다.

- Copy Bundle Resource
    - icudtl.dat ...in PlayRTC/libs

## 예제 앱 다운로드

이제 예제앱을 다운로드 받고 준비 해 봅시다.

예제앱은 아래를 통해 압축파일 형태로 다운로드 받을 수 있습니다.

- <http://www.playrtc.com/download/>
- <https://github.com/playrtc/>
- <https://developers.sktelecom.com/develop/doc/sdk/>

혹은 [PlayRTC GitHub][PlayRTC Github] 저장소에서 소스코드 관리 방식인 [git][git]을 통해 클론받을 수 있습니다.

```Shell
$ git clone https://github.com/playrtc/PlayRTC-iOS-Tutorial-SimpleChat
```

이로써 모든 튜토리얼을 개발하기 위한 모든 환경이 갖추어졌습니다. 다음장을 통해 본격적으로 영상/음성 웹앱을 만들어 보도록 하겠습니다.


[IDE]: http://ko.wikipedia.org/wiki/%ED%86%B5%ED%95%A9_%EA%B0%9C%EB%B0%9C_%ED%99%98%EA%B2%BD
[git]: http://git-scm.com/
[PlayRTC Github]: https://github.com/playrtc
[Swift]: https://developer.apple.com/swift/
[Objective-C]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html
[Xcode]: https://developer.apple.com/kr/xcode/
