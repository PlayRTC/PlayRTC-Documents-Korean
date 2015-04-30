# 영상/음성 통화 안드로이드 앱 만들기

본 튜토리얼을 진행하려면 안드로이드용 PlayRTC SDK와 샘플을 다운로드받아야 합니다.

## Android SDK 사용 환경

PlayRTC SDK를 이용하기 위한 환경은 다음과 같습니다.

- Android SDK: Android SDK Version 11 이상

Application Project의 libs폴더에 다운로드받은 SDK 안에 포함된 library파일을 추가하고, Class-Path에 추가한 라이브러리를 등록합니다.

- [PROJECT-ROOT]/libs/sktplayrtc-client.x.x.x.jar
- [PROJECT-ROOT]/libs/libjingle_peerconnection.jar
- [PROJECT-ROOT]/libs/armeabi-v7a/ibgingle_peerconnection_so.so

## SDK 사용을 위한 Application Manifest 작업

Application Manifest에 아래와 같은 Permissions 항목 추가합니다.

```XML
<!-- PlayRTC SDK Android-SDK -->
<uses-sdk android:minSdkVersion="11" android:targetSdkVersion="19" />

<!-- PlayRTC SDK permission START -->
<uses-feature android:name="android.hardware.camera" />
<uses-feature android:name="android.hardware.camera.autofocus" />
<uses-feature android:glEsVersion="0x00020000" android:required="true" />
<uses-permission android:name="android.permission.CAMERA"/>
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
<!-- PlayRTC SDK permission END -->
```

## PlayRTC Application  주요 개발 단계

com.sktelecom.playrtc.PlayRTC는 PlayRTC SDK에서 가장 중요한 Class입니다. PlayRTC 클래스를 이용하여 P2P 연결에 필요한 각종 작업을 수행하고 명령을 내립니다. 또한 PlayRTCObserver 인터페이스를 구현한 클래스를 등록하여 내부의 CallBack 이벤트를 처리할 수 있습니다. 아래는 PlayRTC 클래스를 이용하여 작업하게 될 대부분의 일을 간단하게 정리하였습니다.

1. PlayRTC 객체 생성
    - 먼저 PlayRTC 객체를 생성하려면 PlayRTCFactory의 newInstance 메소드로 객체를 생성합니다.

2. 서비스 설정
    - PlayRTC 인터페이스를 통해서 기본 값이 설정되어 있는 PlayRTCSettings 개체를 얻어와 구현하는 서비스에 맞게  설정 값을 지정할 수 있으며, PlayRTCSettings 객체에 지정한 값은 내부적으로 PlayRTC 플랫폼 서비스에서 조회한 서비스 설정에 우선하여 적용됩니다.

3. PlayRTC 플랫폼 서비스 채널 입장
    - PlayRTC 플랫폼 채널 서비스에 새로운 채널을 생성하고 입장을 하거나, 기존의 만들어진 채널에 입장하여 P2P연결 수립을 수행합니다.  한 사용자는 채널을 생성하여야 하고, 다은 사용자는 만들어진 채널의 아이디로 채널에 입장을 합니다. 채널의 아이디는 PlayRTCObserver의 onConnectChannel에서 확인 할 수 있습니다.

4. 로컬미디어스트림 출력
    - 채널 서버에 연결 되면 SDK는 로컬 영상/음성 스트림을 제어를 위한 개체를 생성하여 PlayRTCObserver의 onAddLocalStream 을 통해 PlayRTCMedia인터페이스를 전달하며, 전달 받은 PlayRTCMedia에 영상 출력 뷰의 렌더러를 전달하여 영상을 출력하도록 해야 합니다.

5. 상대방과의 연결수락 과정진행
    - PlayRTCSetting의 channel.ring을 true로 설정하면 상호간에 연결수락 과정을 진행하며, PlayRTCObserver를통해서 상대방으로부터 연결수락을 받은경우와상대방으로부터의 연결 수락 의사를 수신한 경우등  두개의상황을처리해야합니다.  

6. 연결수립 후 원격미디어스트림 출력
    - P2P연결 수립 후 상대방의 영상/음성 스트림을 제어를 위한 개체를 생성하여 PlayRTCObserver의 onAddRemoteStream을 통해 PlayRTCMedia인터페이스를 전달하며, 전달 받은 PlayRTCMedia에 영상 출력 뷰의 렌더러를 전달하여 영상을 출력하도록 해야 합니다.

7. 데이터 통신을 위한 PlayRTCData 인터페이스
    - 상대방과 P2P가 연결 되면 데이터 스트림 통신을 위한 개체를 생성하고 PlayRTCObserver의 onAddDataStream인터페이스를 통해 전달받습니다. 데이터 스트림 객체의 상태와 오류 정보 및 데이터 수신을 처리 하기 위한 PlayRTCDataObserver 인터페이스 구현체를 등록하고, 데이터 송신 시 에는 메소드 인자로 playRTCSendDataObserver인터페이스 구현체를 전달해야 합니다.

8. PlayRTC의 상태 변경 정보와 오류 정보 처리
    - PlayRTC의 주요상태 변화와 오류 정보를 전달 받아 처리합니다.

9. 상대방과의연결종료처리
    - PlayRTC의 disconnectChannel을 호출하여 채널에서 퇴장하거나 deleteChannel을 호출하여 채널을 종료하여 상대방과의 연결을 종료하도록합니다.

이제 좀 더 자세하게 PlayRTC 클래스를 이용하는 방법을 차근차근히 살펴보겠습니다.

## PlayRTC 객체 생성

PlayRTC SDK의 PlayRTCFactory Class의 newInstance 메소드를 이용하여 PlayRTC 객체를 생성할 수 있습니다. newInstance의 파라미터값은 보통 2개입니다.

첫번째 파라미터값은 서비스 서버의 RESTful API 주소입니다.

두번째 파라미터값은 Callback 이벤트 처리를 위한 observer 클래스를 등록합니다. PlayRTCObserver 인터페이스를 구현한 클래스가 필요합니다. 이 클래스를 통해서 통신 상태에 대한 여러가지 이벤트를 수신할 수 있습니다.

보통의 소스 코드는 다음과 같습니다.

Sample 소스의  com.playrtc.sample.handler.PlayRTCHandler 생성자 를 참고하세요.
```Android
/* PlayRTC 멤버 변수 */
private PlayRTC playRTC = null;

/* PlayRTC 생성
* - url String, 서비스 서버 URL
* - playRTCObserver, PlayRTCOberver 인터페이스 구현 리스너
*/

try {

  this.playRTC = PlayRTCFactory.newInstance((PlayRTCObserver)new PlayRTCObserverImpl());

} catch (UnsupportedPlatformVersionException e) {
  // Android SDK 11 지원합니다.
  e.printStackTrace();

} catch (RequiredParameterMissingException e) {
  // SERVICE_URL과 PlayRTCObserver 구현개체를 생성자에 전달해야 한다.
  e.printStackTrace();

}
```

## PlayRTCSetting 개체를 이용한 서비스 설정

PlayRTC 객체를 생성하고 나면 getSettings() 메소드를 호출하여 PlayRTC에 설정해둔 기본적인 세팅값들을 가져올 수 있습니다. 즉 getSettings 메소드를 호출하면 PlayRTCSettings 객체를 얻을 수 있습니다. createChannel이나 connectChannel등의 메소드를 호출하는 채널작업을 하기 전에 이 객체를 수정하게 되면 수정된 값을 이용하여 채널 작업을 수행할 수 있게 됩니다.

PlayRTCSetting의 주요 설정 항목은 다음과 같습니다.

- Android Application Context
- PlayRTC 미디어 서비스 관련 설정 – 사용자 재지정
- PlayRTC 로깅 설정 – 사용자 지정

### PlayRTCSetting 개체 구하기

PlayRTC 구현 객체를 생성한 후 PlayRTC의 getSettings() 메소드를 호출하여 PlayRTCSetting 개체를 얻어와 구현할 서비스에 맞게 설정 항목을 설정할 수 있습니다. PlayRTCSetting 개체는 기본값으로 초기화 되어 있는 상태입니다.

```Android
/* PlayRTC 서비스 설정 구하기*/
PlayRTCSettings settings = playRTC.getSettings();
```

### 서비스 설정하기

Sample 소스의 com.playrtc.sample.handler.PlayRTCHandler.setConfiguration를 참고하세요.

#### Android Application Context 전달

PlayRTC SDK는 Android 단말기의 카메라 등 시스템 자원에 접근하기 위해 Application 의 Context가 반드시 필요합니다.

```Android
/* this는 Activity Class 인스턴스 입니다. */
android.setContext(this.getApplicationContext());
```

#### T-Developers 프로젝트 키 지정하기
T-Developers에서 내 프로젝트를 위해 생성한 키를 지정합니다. 1.x버전에서는 서비스 서버에서 지정하던 부분이 2.x버전에서 서비스 서버가 제거됨으로써 클라이언트에 지정하도록 되어있습니다.

```Android
settings.setTDCProjcetID("나의 프로젝트 키");
```

#### 영상 및 음성 스트리밍 사용 설정

PlayRTCSettings의 videoEnable과 audioEnable 메소드로  Boolean 값을 지정하며, 각 항목에 false를 설정하면 자신의 미디어 스트림은 상대에게 전송이 되지 않으나 상대방이 사용하도록 설정 한 경우에는 상대방의 미디어 스트림을 수신하게 됩니다.  SDK 서비스 설정에 정의한 값을 다음과 같이 재 지정 할 수 있습니다.

```Android
settings.setAudioEnable(true or false);   /* 음성 전송 사용 여부 */
settings.setVideoEnable(true or false);   /* 영상 전송 사용 여부 */

settings.video.setFrontCameraEnable(true);  /* 전면 카메라 사용 여부 */
settings.video.setBackCameraEnable(true);   /* 후면 카메라 사용 여부 */

```

#### 데이터 통신 스트림  사용 설정

PlayRTCSettings의 dataEnable 메소드로 true를 설정하면 데이터 통신 채널을 생성 하여 텍스트 및 Binary,파일 데이터를 송수신 할 수 있는 통신 스트림 개체가 생성이 됩니다. 데이터 통신 스트림만  사용하려면 영상/음성 설정을 false로 지정하면 됩니다. SDK 서비스 설정에 정의한 값을 다음과 같이 재 지정 할 수 있습니다.

```Android
settings.setDataEnable(true or false);   /* 데이터 스트림 사용 여부 */
```

#### 상호 연결 승인 과정 설정

서비스 이용자 상호간에 통신 연결 여부를 확인하는 과정을 설정할 수 있습니다.
channel 설정에서 setRing 메소드에 true를 설정하면 연결과정에서 채널에 나중에 입장하는 사용자는 먼저 입장한 사용자에게 연결 확인을 받고 연결 과정을 수행하게 됩니다.

```Android
/* ring, 연결 수립 여부를 상대방에게 묻는지 여부를 지정, true면 상대의 수락이 있어야 연결 수립 진행 */
settings.channel.setRing(true or false);
```

#### SDK 로깅 설정

PlayRTC SDK는 PlayRTC 로그를 Eclipse 로그 Console에 출력하고, 단말기에 파일로 저장 할 수 있으며 주요 실행 로그 및 오류 사항을 PlayRTC 플랫폼 로그 서비스에 전달 합니다.
console, file 로그는 각각의 로그레벨(INFO, DEBUG, WARN, ERROR)을 설정할 수 있습니다. 로그 설정 예는 다음과 같습니다.

```Android
/* SDK Console 로그 레벨 지정 */
settings.log.console.setLevel(PlayRTCSettings.DEBUG);

/* SDK 파일 로깅을 위한 로그 파일 경로, 파일 로깅을 사용하지 않는다면 Pass */
File logPath = new File(Environment.getExternalStorageDirectory().getAbsolutePath() + "/Android/data/" + this.getPackageName() + "/files/log");

/* 파일 로그를 남기려면 로그파일 폴더 지정 . [PATH]/yyyyMMdd.log  */ 
settings.log.file.setRolling(10); /* 10일간 보존 */
settings.log.file.setLogPath(logPath.getAbsolutePath());

/* SDK 파일 로그 레벨 지정 */
settings.log.file.setLevel(PlayRTCSettings.DEBUG);

/* 서버 로그 전송 실패 시 임시 로그 DB 저장 폴더 */
File cachePath = new File(Environment.getExternalStorageDirectory().getAbsolutePath() + "/Android/data/" + this.getPackageName() + "/files/cache");
settings.log.setCachePath(cachePath.getAbsolutePath());

/* 서버 로그 전송 실패 시 재 전송 지연 시간, msec */
settings.log.setRetryQueueDelays(1000);

/* 서버 로그 재 전송 실패시 로그 DB 저장 후 재전송 시도 지연 시간, msec */
settings.log.setRetryCacheDelays(10 * 1000);
```

## PlayRTC P2P 연결을 위한 채널 서비스 입장 및 이벤트처리

먼저 채널에 입장히는 사용자가 채널을 생성해야 하며 다른 사용자는 생성된 채널의 아이디로 채널에 입장을 하여 P2P연결을 합니다.

### 신규 채널을 생성하고 입장하기 – USER A

PlayRTC 플랫폼 채널 서비스에 채널을 새로 생성하고 입장하는 메소드는 PlayRTC.createChannel 입니다. 이 메소드를 호출하면 USER A를 위한 채팅방이 채널서버에 만들어진다고 보면 됩니다. 이 요청은 서비스서버를 거쳐서 채널서버로 전달됩니다. 채널서버는 채널id와 USER A에게 할당된 토큰값등을 반환해줄 것입니다. 가장 기본적인 사용법은 아무런 입력값 없이 null을 입력하여 createChannel(null) 메소드 호출하여 채널을 생성하는 방법이 있습니다.

하지만 PlayRTC는 부가적인 채널이나 peer정보등을 이 createChannel 메소드에 실어서 채널서버에  저장할 수 있도록 하고 있습니다. 즉, createChannel 호출 시 채널 및 채널 사용자의 부가 정보를 같이 전달하여 채널/사용자 정보 조회 시 받아볼 수 있습니다.

createChannel 메소드를 호출하면 PlayRTC는 새로 생성한 채널의 id를 PlayRTCObserver의 onConnectChannel 메소드를 통해 전달해 줍니다. 이때 전달 받은 채널id는 USER B가 connectChannel로 채널에 입장할 때 사용해야 하는  채널 아이디입니다.

createChannel과 관련된 예제는 다음과 같습니다.

Sample 소스의  com.playrtc.sample.handler.PlayRTCHandler.createChannel을 참고하세요.

```Android
JSONObject parameters = new JSONObject();

// 채널정보를 정의한다.
JSONObject channel = new JSONObject();
try {

  // 채널에 대한 이름을 임의로 혹은 사용자에게 입력받아서 지정한다.
  channel.put("channelName", CHANNEL_NAME);

  // 필요하다면 값을 더 지정한다
  .....

  parameters.put("channel", channel);
} catch (JSONException e) {
  e.printStackTrace();
}

// 채널 사용자에 대한 정보를 정의한다.
JSONObject peer = new JSONObject();
try {

  // application에서 사용하는 사용자 아이디를 지정
  peer.put("uid", USER_ID);


  // 사용자에 대한 별칭을 지정한다.
  peer.put("userName", USER_NAME);

  // 필요하다면 값을 더 지정한다.
  .....
  parameters.put("peer", peer);
} catch (JSONException e) {

  e.printStackTrace();
}

try {

  playRTC.createChannel(parameters);

} catch (RequiredConfigMissingException e) {

  // application context가 없음
  e.printStackTrace();
}
```

### 만들어진 채널에 입장하기 – USER B

이미 생성된 채널에 입장히는 메소드는 connectChannel 입니다.

connectChannel  호출 시  해당 채널 아이디가 필요합니다.  채널 사용자의 부가 정보를 같이 전달하여 채널/사용자  정보 조회 시 받아볼 수 있습니다. connectChannel가 잘 수행되면 onConnectChannel 메소드를 통해 보안등을 위한 채널 사용자 토큰정보등을 받을 수 있으므로 잘 보관해둬야 합니다.

connectChannel에 대한 소스코드는 다음과 같습니다.

Sample 소스의 com.playrtc.sample.handler.PlayRTCHandler.connectChannel 를 참고하세요.

```Android
JSONObject parameters = new JSONObject();

// 채널 사용자에 대한 정보를 정의한다.
JSONObject peer = new JSONObject();

try {
  // application에서 사용하는 사용자 아이디를 지정
  peer.put("uid", userId);

  // 사용자에 대한 별칭을 지정한다.
  peer.put("userName", userName);

  // 필요하다면 값을 더 지정한다
  ....
  parameters.put("peer", peer);

} catch (JSONException e) {

  e.printStackTrace();

}

try {
  playRTC.connectChannel(channelId, parameters);
} catch (RequiredConfigMissingException e) {
  // application context가 없음
  e.printStackTrace();
}
```



### 채널 입장 이벤트 받기

createChannel, connectChannel을 호출하여 채널에 입장하면  PlayRTCObserver의 onConnectChannel 이 호출이되며, 입장한 채널의 아이디를 전달하게 됩니다.  onConnectChannel이 호출 될때 reason의 문자열 값으로 채널 입장 시 사용한 createChannel, connectChannel을 구분할 수 있습니다.

Sample 소스의 com.playrtc.sample.handler.PlayRTCObserverImpl.onConnectChannel 메소드를 참고하세요.

```Android
@Override
public void onConnectChannel(final PlayRTC obj, final String channelId, final String reason) {
  // createChannel을 호출하여 채녈에 입장한 경우 - USER A
  if(reason.equals("create")) {
    //channelID를 상대방에게 알려줘야 함.
  }
  // connectChannel을 호출하여 채널에 입장한 경우 - USER B
  else if(reason.equals("connect")) {
    ...
  }
}
```

## P2P 연결 승인과정 적용하기

PlayRTCSettings 객체의 channel 의 ring 을 true로 설정 하면 PlayRTC SDK는 사용자간에 연결 여부를 확인하는 과정을 수행 하게 됩니다. ring 옵션을 false로 설정하면 이 과정을 수행하지 않습니다.

- 채널에 나중에 입장한 사용자는 먼저 입장한 연결 대상자에게 연결 승인을 물러본다.
- 채널에 먼저 입장한 사용자는 연결 상대방에게 연결 승인을 통보한다.

Sample 소스의 com.playrtc.sample.handler.PlayRTCObserverImpl 클래스를 참고하세요.

### 채널에 먼저 입장한 경우 – USER A

채널에 먼저 입장한 사용자는 연결 과정 시작 전에 상대방으로부터 연결 의사를 묻는  onRing을 받게 됩니다.

연결 수락 의사는 PlayRTC의 accept를 호출하며, 거부 의사는 PlatRTC의 reject 메소드를 호출합니다.

다음 예제 소스는 AlertDialog를 통해서 연결 수락 여부를 결정하고 상대방에게 통보 하도록 구현한 예입니다.

```Android
@Override
public void onRing(final PlayRTC obj, final String peerId, final String peerUid){

  AlertDialog.Builder alert = new AlertDialog.Builder(this);
  alert.setTitle("PlayRTC");

  // peerUid : 상대방이 connectChannel 호출 시 uid를 넘긴 값
  alert.setMessage(peerUid + "이 연결을 요청했습니다.");

  // 연결 수락
  alert.setPositiveButton("연결", new DialogInterface.OnClickListener() {
    public void onClick(DialogInterface dialog, int which) {
      dialog.dismiss();
      playRTC.accept(peerId);
    }
  });

  // 연결 거부
  alert.setNegativeButton("거부", new DialogInterface.OnClickListener() {
    public void onClick(DialogInterface dialog, int whichButton) {
      dialog.dismiss();
      playRTC.reject(peerId);
    }
  });
  alert.show();
}
```

### 채널에 나중에 입장한 경우 – USER B

SDK는 먼저 입장해 있는 USER A 사용자에게 연결 수락 여부를 묻는 메시지를 전달하게 되고 상대방 사용자의 수락 여부를 PlayRTCObserver onAccept 또는 onReject를 통해 알려줍니다.

```Android
// 연결 수락
@Override
public void onAccept(final PlayRTC obj, final String peerId, final String peerUid){
  Log.d("PlayRTCObserver", "["+ peerId +"] onAccept....");
}

// 연결 거부
@Override
public void onAccept(final PlayRTC obj, final String peerId, final String peerUid){
  Log.d("PlayRTCObserver", "["+ peerId +"] onReject....");
}
```

## PlayRTC의 미디어 스트림의 영상 출력 처리

SDK는  영상/음성 스트림을 위한 내부 개체를 생성 후  PlayRTCMedia 인터페이스를 onAddLocalStream 또는 onAddRemoteStream 를 통해서 전달합니다. PlayRTCMedia를 전달 받으면 PlayRTCMedia객체에 영상 출력 뷰(PlayRTCVieoView)의 렌더러 인터페이스를 전달하여 영상을 출력하도록 처리해야 합니다.

### 영상 출력을 위한 PlayRTCVideoView 객체 생성

PlayRTC SDK는 영상 스트림을 출력하는 PlayRTCVieoView Class를 제공합니다.
PlayRTCVideoView는 생성자에 Context와 View 크기를 지정한 Point 를 전달해야 합니다. Sample 예에서는 View 인스턴스 생성을 onCreated에서 하지 않고 뷰의 화면 크기를 계산하여 지정할 수 있는 Activity의 onWindowFocusChanged 에서 생성 하였습니다. 생성 한 PlayRTCVideoView는  PARENT_VIEW에 자식 View로 추가합니다.

뷰 배치는 상대방 스트림 View 위에 로컬 스트림을 출력하는 작은 View를 배치하도록 구성하겠습니다.

Sample 소스의 com.playrtc.sample.view.VideoGroupView.createVideoView를 참고하세요.

소스 코드는 다음과 같습니다.

```Android
@Override
public void onWindowFocusChanged(boolean hasFocus) {
  super.onWindowFocusChanged(hasFocus);

  if(hasFocus && videoView == null) {
    this.createVideoView();
  }
}

// 영상 출력용 PlayRTCVideoView객체 동적 생성 및 부모 ViewGroup 설정
private void createVideoView() {
  /* video 스트림 출력을 위한 PlayRTCVideoView의 부모 ViewGroup의 사이즈 재조정
  * 가로-세로 비율 1(가로) 대 0.75 (세로), 폭 기준으로 높이 재지정
  */
  RelativeLayout layout = (RelativeLayout)findViewById(R.id.media_layout);
  float height = layout.getHeight();
  float width = (width * 1.0f) / 0.75f;

  // PlayRTCVideoView의 부모 뷰 크기 조절, Layout XML 에서 정의한 ViewGroup 객체
  RelativeLayout.LayoutParams param =
  RelativeLayout.LayoutParams)videoArea.getLayoutParams();
  param.width = (int)width;
  param.height = (int)height;
  [PARENT_VIEW].setLayoutParams(param);

  // 영상 출력 메인 PlayRTCVideoView 동적 생성을 위한 사이즈 정의
  Point displaySize = new Point();
  displaySize.x = (int)width;
  displaySize.y = (int)height;
  //PlayRTCVideoView(GLSurfaceView를 상속) 동적 생성, 생성자에 화면 사이즈 전달
  // 상대방의 영상은 큰화면으로 출력하도록 한다.
  remoteVideoView = new PlayRTCVideoView(this.getApplicationContext(), displaySize);

  // 부모뷰를 채우도록 사이즈 지정
  RelativeLayout.LayoutParams viewRParam = new RelativeLayout.LayoutParams(LayoutParams.MATCH_PARENT, LayoutParams.MATCH_PARENT);
  remoteVideoView.setLayoutParams(viewRParam);
  // 부모뷰에 추가
  [PARENT_VIEW].addView(remoteVideoView);

  // 자신의 영상은 작은 화면으로 출력하도록 한다
  Point displaySize2 = new Point();
  displaySize2.x = (int)(width*0.26);
  displaySize2.y = (int)(height*0.26);

  localVideoView = new PlayRTCVideoView(this.getApplicationContext(), displaySize2);
  RelativeLayout.LayoutParams viewLParam = new RelativeLayout.LayoutParams(displaySize2.x, displaySize2.y);
  viewLParam.addRule(RelativeLayout.ALIGN_PARENT_RIGHT);
  viewLParam.addRule(RelativeLayout.ALIGN_PARENT_TOP);
  viewLParam.setMargins(0,50,30,0); //left, top, right, bottom

  localVideoView.setLayoutParams(viewLParam);
  localVideoView.setVisibility(View.VISIBLE);
  [PARENT_VIEW].addView(localVideoView);
  localVideoView.setZOrderOnTop(true);
}
```

### Local 영상 미디어 스트림 출력 – PlayRTCObserver.onAddLocalStream

PlayRTCMedia 개체에 영상 출력 처리를 위해 PlayRTCVieoView의 렌더러 인터페이스를 전달합니다.

Sample 소스의 com.playrtc.sample.handler.PlayRTCObserverImpl.onAddLocalStream을 참고하세요.

```Android
private PlayRTCMedia localMedia = null;

@Override
public void onAddLocalStream(final PlayRTC obj, final PlayRTCMedia media) {

  Log.d("MEDIA", "onAddLocalStream PlayRTCMedia=" + rtcMedia);

  localMedia = rtcMedia;

  // PlayRTCVIdeoView의 렌더러를 전달하여 화면 출력
  localMedia.setVideoRenderer(localVideoView.getRenderer());
}=
```



### Remote 영상 미디어 스트림 출력 – PLayRTCObserver.onAddRemoteStream

PlayRTCMedia 에 영상 출력 처리를 위해 PlayRTCVieoView의 렌더러 인터페이스를 전달합니다.

Sample 소스의 com.playrtc.sample.handler.PlayRTCObserverImpl.onAddRemoteStream을 참고하세요.

```Android
private PlayRTCMedia remoteMedia = null;

@Override
public void onAddRemoteStream(final PlayRTC obj, final String peerId, final String peerUid, final PlayRTCMedia media){

  Log.d("MEDIA", "onRemoteMedia["+peerId+"] PlayRTCMedia=" + rtcMedia);

  remoteMedia = rtcMedia;

  // PlayRTCVIdeoView의 렌더러를 전달하여 화면 출력
  remoteMedia.setVideoRenderer(remoteVideoView.getRenderer());

}
```

## PlayRTCData – 데이터 스트림을 이용한 데이터 교환

PlayRTCSetting에 데이터 스트림을 사용하도록 설정 하였다면, PlayRTC SDK는 P2P 연결이 수립되면 데이터 통신을 제공하는 개체를 생성하고 개체의 인터페이스를 전달합니다. 데이터 스트림은 텍스트, 바이너리, 파일을 송/수신하는 기능을 제공합니다.

Sample 소스의 com.playrtc.sample.handler.DataChannelHandler 클래스를 참고하세요.

### PlayRTCData 전달 받기 – PlayRTCObserver.onAddDataStream

PlayRTCObserver의 onAddDataStream를 통해 PlayRTCData 인터페이스를 전달받습니다. PlayRTCData를 전달 받으면 상대방의 데이터를 수신하거나 데이터 스트림 객체의 상테 및 오류등을 처리 하기 위해서 PlayRTCDataObserver를 구현한 개체를 등록해야 합니다.

```Android
/* P2P 데이터 통신을 위한 PlayRTCData객체 */
private PlayRTCData dataChannel = null;

// P2P 연결 수립 후 데이터 통신 객체 생성 후 전달 받아 PlayDataObserver 인터페이스 구현 개체를 등록
@Override
public void onAddDataStream(final PlayRTC obj, final String peerId, final String peerUid, final PlayRTCData data){
  data.setEventObserver(dataObserver);
  dataChannel = data;
}
```

### 데이터 수신 및 PlayRTCData 성태 및 오류 정보 처리를 위한 PlayRTCDataObserver 구현

데이터 수신과  PlayRTCData의 상태 및 오류 정보를 전달 받는 인터페이스를 수현합니다. PlayRTCData는 데이터 전송 시 헤더 정보를 구성하여 데이터를 전송하며 데이터 수신 시 헤더 정보를 읽어 이벤트를 통해 알려 줍니다. 데이터는 고유 아이디가 지정 되어 있으며 헤더에서 데이터 종류(Text, Binary, File)를 확인하여 데이터를 처리해야 합니다. 파일 데이터의 경우 헤더 정보에 파일 이름 및 MimeType이 같이 전달 됩니다

데이터 수신부 구현은 다음과 같습니다. onProgress에서  데이터 수신 진행 정도를 처리하며, 오류 없이 데이터 수신을 완료되면 onMessage에서 데이터 유형에 맞게 처리를 합니다.

```Android
@Override
public void onProgress(final PlayRTCData obj, final String peerId, final String peerUid, final long recvSize, final PlayRTCDataHeader header) {
  // 데이터 유형을 확인할 수 있습니다.
  String dataType = header.isBinary()?"binary":"text";
  Log.d(LOG_TAG, "onProgress "+peerUid+" "+header.getId()+" recv "+dataType+"["+recvSize+"] ");
}

@Override
public void onMessage(final PlayRTCData obj, final String peerId, final String peerUid, final PlayRTCDataHeader header, final byte[] data) {
  // 텍스트 데이터 수신인 경우
  if(header.getType() == PlayRTCDataHeader.DATA_TYPE_TEXT)
  {
    String recvText = new String(data);
    Log.d(LOG_TAG, "Text["+recvText+"]");

  }
  // 바이너리 데이터 수신인 경우
  else {

    // 헤더에서 파일명을 학인한다.
    String filaNmae = header.getFileName();
    // 파일 명이 없다면 바이너리 데이터 수신.
    if(TextUtils.isEmpty(filaNmae))
    {
      Log.d(LOG_TAG, "Binary["+header.getSize()+"]");

    }
    // 파일명이 있다면 파일 데이터 수신 임
    else {
      Log.d(LOG_TAG, "File["+filaNmae+"]");

      // 수신 데이터를 파일로 저장한다.

    }

  }
}
```

PlayRTCData의 상태 정보와 오류 정보를 확인하기 위해서 아래와 같이 로그를 출력하도록 하겠습니다.

```Android
@Override
public void onStateChange(final PlayRTCData obj, final String peerId, final String peerUid, final PlayRTCDataStatus state) {
  Log.d(“Data-Channel”, “[“+ peerUid+ "] "+state+"...");
}

@Override
public void onError(final PlayRTCData obj, final String peerId, final String peerUid, final long id, final PlayRTCDataCode code, final String desc) {
  Log.d(“Data-Channel”, “[“+ peerUid+ "] onError["+code+"] "+desc);
}
```

### 데이터 전송하기

#### 텍스트 전송하기

테스트를 전송하기 위해서는 PlayRTCDAta의 sendText를 이용하며 데이터 전송 결과를 확인하기 위해서 PlayRTCSendDataObserver 구현 개체가 필요합니다.

```Android
String sendData = "DataChannel 한글 Text...";
dataChannel.sendText(sendData, new PlayRTCSendDataObserver() {
  @Override
  public void onSuccess(PlayRTCData obj, String peerId, String peerUid, long id, long size) {
    Log.d(LOG_TAG,  "sendText onSuccess "+peerUid+" "+id+"["+size+"]");
  }

  @Override
  public void onError(PlayRTCData obj, String peerId, String peerUid, long id, PlayRTCDataCode code, String desc) {
    Log.d(LOG_TAG,  "sendText onError "+peerUid+" "+id+"["+code+"] "+desc);
  }
});
```

#### 바이너리 전송하기

바이너리를 전송하기 위해서는 PlayRTCDAta의 sendBinary를 이용하며 데이터 전송 결과를 확인하기 위해서 PlayRTCSendDataObserver 구현 개체가 필요합니다.

```Android
String sendData = "DataChannel Text";
dataChannel.sendByte(sendData.getBytes(), null, new PlayRTCSendDataObserver(){
  @Override
  public void onSuccess(PlayRTCData obj, String peerId, String peerUid, long id, long size) {
    Log.d(LOG_TAG,  "sendBinary onSuccess "+peerUid+" "+id+"["+size+"]");
  }
  @Override
  public void onError(PlayRTCData obj, String peerId, String peerUid, long id, PlayRTCDataCode code, String desc) {
    Log.d(LOG_TAG,  "sendBinary onError "+peerUid+" "+id+"["+code+"] "+desc);
  }
});
```

#### 파일전송하기

파일을 전송하기 위해서는 PlayRTCDAta의 sendFile을 이용하며 데이터 전송 결과를 확인하기 위해서 PlayRTCSendDataObserver 구현 개체가 필요합니다.

```Android
//파일 전송 InputStream
private InputStream dataIs = null;
,,,,
final< String fileName = "main.html";
dataIs = null;
try {
    dataIs = [ACTIVITY].getAssets().open(fileName);
    dataChannel.sendFile(dataIs, fileName, new PlayRTCSendDataObserver(){

        @Override
        public void onSuccess(PlayRTCData obj, String peerId, String peerUid, long id, long size) {
            Log.d(LOG_TAG,  "sendFile onSuccess "+peerUid+" "+id+"["+size+"]");
            // Input-Stream Close 처리
        }
        @Override
        public void onError(PlayRTCData obj, String peerId, String peerUid, long id, PlayRTCDataCode code, String desc) {
           Log.d(LOG_TAG,  "sendFile onError "+peerUid+" "+id+"["+code+"] "+desc);
           // Input-Stream Close 처리
       }

    });
} catch (IOException e) {
    e.printStackTrace();
}
```

## PlayRTC 연결 종료

PlayRTC에서 상대방과 연결을 종료하는 경우는 다음의 경우가 있습니다.

1. 상대방이 채널에서 퇴장 하는 경우 – 채널이 유효한 상태
    - 상대방이 disconnectChannel를 호출하면 SDK는 채널 서비스로부터 채널 퇴장 이벤트를 받아 채널에서 퇴장하며, 채널 서비스는 나에게 상대방의 채널 퇴장을 알리는 이벤트(onOtherDisconnectChannel)를 보내 줍니다.

2. 자신이 채널에서 퇴장하는 경우 – 채널이 유효한 상태
    - disconnectChannel을 호출하면 SDK는 채널 서비스로부터 채널 퇴장 이벤트(onDisconnectChannel)를 받아 채널에서 퇴장하고, 채널 서비스는 상대방에게도 나의 채널 퇴장 이벤트(onOtherDisconnectChannel)를 전달하게 됩니다. 상대방은 이 이벤트를 받아 나의 채널 퇴장을 알게 됩니다.

3. 상대방 또는 자신이 채널을 종료 하는 경우 – 예제에서의 채널 종료 방법
    - deleteChannel을 호출하면 채널 서비스는 채널에 입장한 모든 사용자에게 채널 종료를 통보(onDisconnectChannel)하고 채널에 있는 모든 사용자의 연결을 종료합니다. 이때 각각의 사용자는 종료하는 과정을 진행합니다.

### 채널 종료를 이용한 PlayRTC 연결 종료

Android 단말기의 Back-Key를 이용하여 종료과정을 구현합니다. boolean isCloseAction이라는 멤버 변수를 선언한 후 onBackPressed에서 isCloseActivity 값을 확인하여 false이면 사용자에게 종료 의사를 물어보는 다이얼로그를 화면에 출력하고, 사용자가 확인을 선택하면 deleteChannel을 호출하여 채널 종료를요청합니다.

채널 서비스에서 채널 종료 이벤트(onDeleteChannel)를 받으면  isCloseActivity을 true로 설정하여 onBackPressed를 호출합니다.

onBackPressed에서 isCloseAction값이 true이면 super.onBackPressed를 호출하여 종료 과정을 진행합니다.

소스 코드는 다음과 같습니다.

```Android
private boolean isCloesActivity = false;
@Override
public void onBackPressed() {
    if(isCloesActivity == true) {
        // BackPress 처리 -> onDestroy 호출
        super.onBackPressed();
    }
    else {
        AlertDialog.Builder alert = new AlertDialog.Builder([ACTIVITY]);
        alert.setTitle("PlayRTCSample");
        alert.setMessage("앱을 종료하겠습니까?");

        alert.setPositiveButton("종료", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int which) {
                dialog.dismiss();
                // 채널에 입장힌 상태이면 userPid값이 존재하므로 이값을 기준으로 채널 입장한 상태를
                // 판별하여 채널 퇴장을 호출한다.
                // 채널에서 퇴장을 하면 PlayRTCObserver의 onDisconnectChannel에서
                // isCloesActivity를 true로 설정하고 onBackPressed()를 호출하여 종료 처리를 한다.
                String userPid = playRTC.getPeerId();
                if(TextUtils.isEmpty(userPid) == {
                    isCloesActivity = false;
                    playRTC.deleteChannel();
                }
                // 채널에 입장한 상태가 아니라면 바로 종료 처리를 한다.
                else {
                    isCloesActivity = true;
                    onBackPressed();
                }
            }
        });
      alert.setNegativeButton("취소", new DialogInterface.OnClickListener() {
          public void onClick(DialogInterface dialog, int whichButton) {
              dialog.dismiss();
              isCloesActivity = false;
          }
      });
      alert.show();
   }
}

//PlayRTCObserver 구현 체. 상대방의 채널 퇴장  구현
@Override
public void onDisconnectChannel(final PlayRTC obj, final String reason) {
    if(reson.equals("disconnect")){
       // 자신이 채널 퇴장을 요청한 경우
    }
    else{
       Log.d("DISCONNECT", ">>PlayRTC 채널이 종료되었습니다....");
       // 채널이 종료된 경우

       //  종료 처리한다.
       isCloesActivity = true;
       [ACTIVITY].onBackPressed();
    }
}
```
