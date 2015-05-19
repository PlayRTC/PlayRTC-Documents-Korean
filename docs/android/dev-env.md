# 개발 환경 구성

## IDE 준비
안드로이드를 개발하기 위한 기본적인 준비가 필요합니다.

안드로이드 플렛폼을 만든 구글에서 권장하는 [안드로이드 스튜디오][Android Studio]나 Java 언어 개발에 널리 사용하는 [Eclipse][Eclipse]를 준비하면 됩니다.

[Eclipse][Eclipse]의 경우 기본 설치 후 안드로이드 개발 환경을 구성하려면 약간의 단계가 더 필요한데 다음을 통해 준비토록 합니다.

- [Installing the Stand-alone SDK Tools](https://developer.android.com/intl/ko/sdk/installing/index.html?pkg=tools)

첫 개발을 시작한다면 안드로이드 플렛폼 개발의 표준 도구인 [안드로이드 스튜디오][Android Studio]를 사용할것을 권장 합니다.

## PlayRTC Android SDK 준비하기
아래의 링크를 통해 다운로드 받습니다.

- <http://www.playrtc.com/download/>

다운로드 받고 압축을 풀면 3개의 파일이 나오는데, 원하는 프로젝트에 다음과 같이 배치 하면 됩니다.

### [안드로이드 스튜디오][Android Studio]에서 준비하기

아래와 같이 SDK 파일을 배치 합니다.

- [PROJECT-ROOT]/app/libs/sktplayrtc-client.x.x.x.jar
- [PROJECT-ROOT]/app/libs/libjingle_peerconnection.jar
- [PROJECT-ROOT]/app/src/main/jniLibs/libs/armeabi-v7a/ibgingle_peerconnection_so.so

그리고 그래들 빌드 파일에 라이브러리를 지정하여 빌드시 삽입 하기 위해 `[PROJECT_ROOT]/app/build.gradle`파일이 아래와 같은지 확인합니다.

~~~Gradle
dependencies {
    // 개별파일 지정
    compile files('libs/libjingle_peerconnection.jar')
    compile files('libs/sktplayrtc-client.2.0.0.jar')

    // 혹은 디렉토리 전체를 지정
    compile fileTree(dir: 'libs', include: ['*.jar'])
}
~~~

### [Eclipse][Eclipse]에서 준비하기

아래와 같이 SDK 파일을 배치 합니다.

- [PROJECT-ROOT]/libs/sktplayrtc-client.x.x.x.jar
- [PROJECT-ROOT]/libs/libjingle_peerconnection.jar
- [PROJECT-ROOT]/libs/armeabi-v7a/ibgingle_peerconnection_so.so

## 예제 앱 다운로드
이제 예제앱을 다운로드 받고 준비 해 봅시다.

예제앱은 아래를 통해 압축파일 형태로 다운로드 받을 수 있습니다.

- <http://www.playrtc.com/download/>
- <https://developers.sktelecom.com/develop/doc/sdk/>

혹은 [PlayRTC GitHub][PlayRTC Github] 저장소에서 소스코드 관리 방식인 [git][git]을 통해 클론받을 수 있습니다.

```Shell
$ git clone https://github.com/playrtc/PlayRTC-Android-Tutorial-SimpleChat
```

이로써 모든 튜토리얼을 개발하기 위한 모든 환경이 갖추어졌습니다. 다음장을 통해 본격적으로 영상/음성 웹앱을 만들어 보도록 하겠습니다.


[Android Studio]: https://developer.android.com/intl/ko/sdk/index.html
[Eclipse]: http://eclipse.org/
[git]: http://git-scm.com/
[PlayRTC Github]: https://github.com/playrtc
