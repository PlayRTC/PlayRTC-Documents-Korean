# 영상/음성 통화 안드로이드 앱 만들기


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


## PlayRTC Application 주요 개발 단계

com.sktelecom.playrtc.PlayRTC는 PlayRTC SDK에서 가장 중요한 Class입니다. PlayRTC 클래스를 이용하여 P2P 연결에 필요한 각종 작업을 수행하고 명령을 내립니다. 또한 PlayRTCObserver 인터페이스를 구현한 클래스를 등록하여 내부의 CallBack 이벤트를 처리할 수 있습니다. 아래는 PlayRTC 클래스를 이용하여 작업하게 될 대부분의 일을 간단하게 정리하였습니다.

1. PlayRTC 객체 생성
    - 먼저 PlayRTC 객체를 생성하려면 PlayRTCFactory의 newInstance 메소드로 객체를 생성합니다.

2. 서비스 설정
    - PlayRTC 인터페이스를 통해서 기본 값이 설정되어 있는 PlayRTCSettings 개체를 얻어와 구현하는 서비스에 맞게  설정 값을 지정할 수 있으며, PlayRTCSettings 객체에 지정한 값은 내부적으로 PlayRTC 플랫폼 서비스에서 조회한 서비스 설정에 우선하여 적용됩니다.

3. PlayRTC 플랫폼 서비스 채널 입장
    - PlayRTC 플랫폼 채널 서비스에 새로운 채널을 생성하고 입장을 하거나, 기존의 만들어진 채널에 입장하여 P2P연결 수립을 수행합니다.  한 사용자는 채널을 생성하여야 하고, 다은 사용자는 만들어진 채널의 아이디로 채널에 입장을 합니다. 채널의 아이디는 PlayRTCObserver의 onConnectChannel에서 확인 할 수 있습니다.

4. 로컬미디어스트림 출력
    - 채널 서버에 연결 되면 SDK는 로컬 영상/음성 스트림을 제어를 위한 개체를 생성하여 PlayRTCObserver의 onAddLocalStream 을 통해 PlayRTCMedia인터페이스를 전달하며, 전달 받은 PlayRTCMedia에 영상 출력 뷰의 렌더러를 전달하여 영상을 출력하도록 해야 합니다.

5. 연결수립 후 원격미디어스트림 출력
    - P2P연결 수립 후 상대방의 영상/음성 스트림을 제어를 위한 개체를 생성하여 PlayRTCObserver의 onAddRemoteStream을 통해 PlayRTCMedia인터페이스를 전달하며, 전달 받은 PlayRTCMedia에 영상 출력 뷰의 렌더러를 전달하여 영상을 출력하도록 해야 합니다.

6. PlayRTC의 상태 변경 정보와 오류 정보 처리
    - PlayRTC의 주요상태 변화와 오류 정보를 전달 받아 처리합니다.

7. 상대방과의연결종료처리
    - PlayRTC의 disconnectChannel을 호출하여 채널에서 퇴장하거나 deleteChannel을 호출하여 채널을 종료하여 상대방과의 연결을 종료하도록합니다.

이제 좀 더 자세하게 PlayRTC 클래스를 이용하는 방법을 차근차근히 살펴보겠습니다. 아래의 과정은 이해를 돕기위해 하나의 액티비티안에 모든 구현을 담고 있음을 알려둡니다.


## PlayRTC 객체 생성

PlayRTC SDK의 PlayRTCFactory Class의 newInstance 메소드를 이용하여 PlayRTC 객체를 생성할 수 있습니다. newInstance의 파라미터값은 보통 2개입니다.

첫번째 파라미터값은 서비스 서버의 RESTful API 주소입니다.

두번째 파라미터값은 Callback 이벤트 처리를 위한 observer 클래스를 등록합니다. PlayRTCObserver 인터페이스를 구현한 클래스가 필요합니다. 이 클래스를 통해서 통신 상태에 대한 여러가지 이벤트를 수신할 수 있습니다.

다음과 같은 형태로 구현할 수 있습니다.

```Android
private PlayRTC playrtc;

try {
    playrtc = PlayRTCFactory.newInstance(playrtcObserver);
} catch (UnsupportedPlatformVersionException e) {
    e.printStackTrace();
} catch (RequiredParameterMissingException e) {
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

다음과 같은 형태로 구현할 수 있습니다.

```Android
private void setPlayRTCConfiguration() {
    PlayRTCSettings settings = playrtc.getSettings();

    // PlayRTC instance have to get the application context.
    settings.android.setContext(getApplicationContext());

    // T Developers Project Key.
    settings.setTDCProjectId("60ba608a-e228-4530-8711-fa38004719c1");

    settings.setAudioEnable(true);
    settings.setVideoEnable(true);
    settings.video.setFrontCameraEnable(true);
    settings.video.setBackCameraEnable(true);
    settings.setDataEnable(false);
}
```

## PlayRTC P2P 연결을 위한 채널 서비스 입장 및 이벤트처리

먼저 채널에 입장히는 사용자가 채널을 생성해야 하며 다른 사용자는 생성된 채널의 아이디로 채널에 입장을 하여 P2P연결을 합니다.


### 신규 채널을 생성하고 입장하기 – USER A

PlayRTC 플랫폼 채널 서비스에 채널을 새로 생성하고 입장하는 메소드는 PlayRTC.createChannel 입니다. 이 메소드를 호출하면 USER A를 위한 채팅방이 채널서버에 만들어진다고 보면 됩니다. 이 요청은 서비스서버를 거쳐서 채널서버로 전달됩니다. 채널서버는 채널id와 USER A에게 할당된 토큰값등을 반환해줄 것입니다. 가장 기본적인 사용법은 아무런 입력값 없이 null을 입력하여 createChannel(null) 메소드 호출하여 채널을 생성하는 방법이 있습니다.

하지만 PlayRTC는 부가적인 채널이나 peer정보등을 이 createChannel 메소드에 실어서 채널서버에  저장할 수 있도록 하고 있습니다. 즉, createChannel 호출 시 채널 및 채널 사용자의 부가 정보를 같이 전달하여 채널/사용자 정보 조회 시 받아볼 수 있습니다.

createChannel 메소드를 호출하면 PlayRTC는 새로 생성한 채널의 id를 PlayRTCObserver의 onConnectChannel 메소드를 통해 전달해 줍니다. 이때 전달 받은 채널id는 USER B가 connectChannel로 채널에 입장할 때 사용해야 하는  채널 아이디입니다.

다음과 같은 형태로 구현할 수 있습니다.

```Android
private void createChannel() {
    try {
        // createChannel must have a JSON Object
        playrtc.createChannel(new JSONObject());
    } catch (RequiredConfigMissingException e) {
        e.printStackTrace();
    }
}
```


### 만들어진 채널에 입장하기 – USER B

이미 생성된 채널에 입장히는 메소드는 connectChannel 입니다.

connectChannel  호출 시  해당 채널 아이디가 필요합니다.  채널 사용자의 부가 정보를 같이 전달하여 채널/사용자  정보 조회 시 받아볼 수 있습니다. connectChannel가 잘 수행되면 onConnectChannel 메소드를 통해 보안등을 위한 채널 사용자 토큰정보등을 받을 수 있으므로 잘 보관해둬야 합니다.

다음과 같은 형태로 구현할 수 있습니다.

```Android
private void connectChannel() {
    try {
        channelId = "CHS1234567890";
        // connectChannel must have a JSON Object
        playrtc.connectChannel(channelId, new JSONObject());
    } catch (RequiredConfigMissingException e) {
        e.printStackTrace();
    }
}
```


## PlayRTC의 미디어 스트림의 영상 출력 처리

SDK는  영상/음성 스트림을 위한 내부 개체를 생성 후  PlayRTCMedia 인터페이스를 onAddLocalStream 또는 onAddRemoteStream 를 통해서 전달합니다. PlayRTCMedia를 전달 받으면 PlayRTCMedia객체에 영상 출력 뷰(PlayRTCVieoView)의 렌더러 인터페이스를 전달하여 영상을 출력하도록 처리해야 합니다.

### 영상 출력을 위한 PlayRTCVideoView 객체 생성

PlayRTC SDK는 영상 스트림을 출력하는 PlayRTCVieoView Class를 제공합니다.
PlayRTCVideoView는 생성자에 Context와 View 크기를 지정한 Point 를 전달해야 합니다. Sample 예에서는 View 인스턴스 생성을 onCreated에서 하지 않고 뷰의 화면 크기를 계산하여 지정할 수 있는 Activity의 onWindowFocusChanged 에서 생성 하였습니다. 생성 한 PlayRTCVideoView는  PARENT_VIEW에 자식 View로 추가합니다.

뷰 배치는 상대방 스트림 View 위에 로컬 스트림을 출력하는 작은 View를 배치하도록 구성하겠습니다.

다음과 같은 형태로 구현할 수 있습니다.

```Android
private void createVideoView() {
    // Set the videoViewGroup which is contained local and remote video views.
    videoViewGroup = (RelativeLayout) findViewById(R.id.video_view_group);

    if (localView != null) {
        return;
    }

    // Set the size.
    Point screenDimensions = new Point();
    screenDimensions.x = videoViewGroup.getWidth();
    screenDimensions.y = videoViewGroup.getHeight();

    if (remoteView == null) {
        createRemoteVideoView(screenDimensions, videoViewGroup);
    }

    if (localView == null) {
        createLocalVideoView(screenDimensions, videoViewGroup);
    }
}
```

### Local 영상 미디어 스트림 출력 – PlayRTCObserver.onAddLocalStream

PlayRTCMedia 개체에 영상 출력 처리를 위해 PlayRTCVieoView의 렌더러 인터페이스를 전달합니다.

다음과 같은 형태로 구현할 수 있습니다.

```Android
private void createLocalVideoView(final Point screenDimensions, RelativeLayout videoViewGroup) {
    if (localView == null) {
        // Set the size.
        Point displaySize = new Point();
        displaySize.x = (int) (screenDimensions.x * 0.3);
        displaySize.y = (int) (screenDimensions.y * 0.3);

        // Set the position.
        RelativeLayout.LayoutParams param = new RelativeLayout.LayoutParams(displaySize.x, displaySize.y);
        param.addRule(RelativeLayout.ALIGN_PARENT_RIGHT);
        param.addRule(RelativeLayout.ALIGN_PARENT_TOP);
        param.setMargins(30, 30, 30, 30);

        // Create the localView.
        localView = new PlayRTCVideoView(videoViewGroup.getContext(), displaySize);

        // Set the layout parameters and add the view to the videoViewGrop.
        localView.setLayoutParams(param);
        videoViewGroup.addView(localView);

        // Set the z-order.
        localView.setZOrderMediaOverlay(true);
    }
}
```


### Remote 영상 미디어 스트림 출력 – PLayRTCObserver.onAddRemoteStream

PlayRTCMedia 에 영상 출력 처리를 위해 PlayRTCVieoView의 렌더러 인터페이스를 전달합니다.

다음과 같은 형태로 구현할 수 있습니다.

```Android
private void createRemoteVideoView(final Point screenDimensions, RelativeLayout viewGroup) {
    if (remoteView == null) {
        // Set the size.
        Point displaySize = new Point();
        displaySize.x = (int) (screenDimensions.x);
        displaySize.y = (int) (screenDimensions.y);

        // Set the position.
        RelativeLayout.LayoutParams param = new RelativeLayout.LayoutParams(RelativeLayout.LayoutParams.MATCH_PARENT, RelativeLayout.LayoutParams.MATCH_PARENT);

        // Create the remoteView.
        remoteView = new PlayRTCVideoView(viewGroup.getContext(), displaySize);

        // Set the layout parameters and add the view to the videoViewGroup.
        remoteView.setLayoutParams(param);
        viewGroup.addView(remoteView);
    }
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

다음과 같은 형태로 구현할 수 있습니다.

```Android
@Override
public void onBackPressed() {
    if (isCloseActivity) {
        super.onBackPressed();
    } else {
        this.createCloseAlertDialog();
        closeAlertDialog.show();
    }
}

private void createCloseAlertDialog() {
    // Create the Alert Builder.
    AlertDialog.Builder alertDialogBuilder = new AlertDialog.Builder(this);

    // Set a Alert.
    alertDialogBuilder.setTitle(R.string.alert_title);
    alertDialogBuilder.setMessage(R.string.alert_message);
    alertDialogBuilder.setPositiveButton(R.string.alert_positive, new DialogInterface.OnClickListener() {
        public void onClick(DialogInterface dialogInterface, int id) {
            dialogInterface.dismiss();
            if (isChannelConnected == true) {
                isCloseActivity = false;

                // null means my user id.
                playrtc.disconnectChannel(null);
            } else {
                isCloseActivity = true;
                onBackPressed();
            }
        }
    });
    alertDialogBuilder.setNegativeButton(R.string.alert_negative, new DialogInterface.OnClickListener() {
        public void onClick(DialogInterface dialogInterface, int id) {
            dialogInterface.dismiss();
            isCloseActivity = false;
        }
    });

    // Create the Alert.
    closeAlertDialog = alertDialogBuilder.create();
}
```
