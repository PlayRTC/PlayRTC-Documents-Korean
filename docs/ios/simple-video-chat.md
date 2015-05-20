# 영상/음성 통화 iOS 앱 만들기

## PlayRTC Application  주요 개발 단계

PlayRTC SDK에서 가장 중요한 Class입니다. PlayRTC 클래스를 이용하여 P2P 연결에 필요한 각종 작업을 수행하고 명령을 내립니다. 또한 PlayRTCObserver 인터페이스를 구현한 클래스를 등록하여 내부의 CallBack 이벤트를 처리할 수 있습니다. 아래는 PlayRTC 클래스를 이용하여 작업하게 될 대부분의 일을 간단하게 정리하였습니다.

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

이제 좀 더 자세하게 PlayRTC 클래스를 이용하는 방법을 차근차근히 살펴보겠습니다.


## PlayRTC 객체 생성

PlayRTC SDK의 PlayRTCFactory Class의 newInstance 메소드를 이용하여 PlayRTC 객체를 생성할 수 있습니다.

두번째 파라미터값은 Callback 이벤트 처리를 위한 observer 클래스를 등록합니다. PlayRTCObserver 인터페이스를 구현한 클래스가 필요합니다. 이 클래스를 통해서 통신 상태에 대한 여러가지 이벤트를 수신할 수 있습니다.

다음과 같은 형태로 구현될 수 있습니다.

```Objective-C
self.playRTC = [PlayRTCFactory newInstanceWithServiceUrl:url observer:(id<PlayRTCObserver>)self];
```

```Swift
self.playRTC = PlayRTCFactory.newInstance(self as PlayRTCObserver)
```


## PlayRTCSetting 개체를 이용한 서비스 설정

PlayRTC 객체를 생성하고 나면 getSettings() 메소드를 호출하여 PlayRTC에 설정해둔 기본적인 세팅값들을 가져올 수 있습니다. 즉 getSettings 메소드를 호출하면 PlayRTCSettings 객체를 얻을 수 있습니다. createChannel이나 connectChannel등의 메소드를 호출하는 채널작업을 하기 전에 이 객체를 수정하게 되면 수정된 값을 이용하여 채널 작업을 수행할 수 있게 됩니다.

PlayRTCSetting의 주요 설정 항목은 다음과 같습니다.

- PlayRTC 미디어 서비스 관련 설정 – 사용자 재지정
- PlayRTC 로깅 설정 – 사용자 지정


### PlayRTCSetting 개체 구하기

PlayRTC 구현 객체를 생성한 후 PlayRTC의 getSettings() 메소드를 호출하여 PlayRTCSetting 개체를 얻어와 구현할 서비스에 맞게 설정 항목을 설정할 수 있습니다. PlayRTCSetting 개체는 기본값으로 초기화 되어 있는 상태입니다.

다음과 같은 형태로 구현할 수 있습니다.

```Objective-C
- (void)setConfiguration
{
    isClose = FALSE;

    PlayRTCSettings* settings = [self.playRTC getSettings];

    [settings setTDCProjectId:TDCPROJECTID];
    [settings setTDCHttpReferer:nil];
    [settings setVideoEnable:TRUE];
    [settings.video setFrontCameraEnable:TRUE];
    [settings.video setBackCameraEnable:FALSE];
    [settings setAudioEnable:TRUE];
    [settings setDataEnable:TRUE];
    [settings.channel setRing:FALSE];
    [settings.log.console setLevel:LOG_LEVEL];
}
```

```Swift
func setPlayRTCConfiguration() {
    var settings: PlayRTCSettings
    isClose = false

    settings = self.playRTC.getSettings()

    // T Developers Project Key.
    settings.setTDCProjectId("60ba608a-e228-4530-8711-fa38004719c1")
    settings.setTDCHttpReferer(nil)
    settings.setVideoEnable(true)
    settings.video.setFrontCameraEnable(true)
    settings.video.setBackCameraEnable(false)
    settings.setAudioEnable(true)
    settings.setDataEnable(true)
    settings.channel.setRing(false)
    settings.log.console.setLevel(LOG_LEVEL)
}
```


## PlayRTC P2P 연결을 위한 채널 서비스 입장 및 이벤트처리

먼저 채널에 입장히는 사용자가 채널을 생성해야 하며 다른 사용자는 생성된 채널의 아이디로 채널에 입장을 하여 P2P연결을 합니다.


### 신규 채널을 생성하고 입장하기 – USER A

PlayRTC 플랫폼 채널 서비스에 채널을 새로 생성하고 입장하는 메소드는 PlayRTC::createChannel 입니다. 이 메소드를 호출하면 USER A를 위한 채팅방이 채널서버에 만들어진다고 보면 됩니다. 이 요청은 서비스서버를 거쳐서 채널서버로 전달됩니다. 채널서버는 채널id와 USER A에게 할당된 토큰값등을 반환해줄 것입니다. 가장 기본적인 사용법은 아무런 입력값 없이 nil을 입력하여 createChannel(nil) 메소드 호출하여 채널을 생성하는 방법이 있습니다.

하지만 PlayRTC는 부가적인 채널이나 peer정보등을 이 createChannel 메소드에 실어서 채널서버에  저장할 수 있도록 하고 있습니다. 즉, createChannel 호출 시 채널 및 채널 사용자의 부가 정보를 같이 전달하여 채널/사용자 정보 조회 시 받아볼 수 있습니다.

createChannel 메소드를 호출하면 PlayRTC는 새로 생성한 채널의 id를 PlayRTCObserver의 onConnectChannel 메소드를 통해 전달해 줍니다. 이때 전달 받은 채널id는 USER B가 connectChannel로 채널에 입장할 때 사용해야 하는  채널 아이디입니다.

다음과 같은 형태로 구현할 수 있습니다.

```Objective-C
- (void)createChannel:(NSString*)chName userId:(NSString*)userId {
    if(self.playRTC == nil) {
        return;
    }

    self.channelName = chName;

    if(self.channelName == nil) self.channelName = @"";

    NSMutableDictionary* parameters = [NSMutableDictionary dictionary];

    if(channelName != nil) {
        NSDictionary * channel = [NSDictionary dictionaryWithObject:chName forKey:@"channelName"];
        [parameters setObject:channel forKey:@"channel"];
    }

    if(userId != nil) {
        self.userUid = userId;
        NSDictionary * peer = [NSDictionary dictionaryWithObject:userId forKey:@"uid"];
        [parameters setObject:peer forKey:@"peer"];
    } else {
        self.userUid = @"";
    }

    [self.playRTC createChannel:parameters];

}
```

```Swift
func createChannel(channelId: String?, userId: String?) {
    var parameters = [String: String]()

    if self.playRTC == nil {
        return
    }

    if channelId != nil {
        parameters.updateValue(channelId!, forKey: "channelId")
    }

    if userId != nil {
        self.userId = userId
        parameters.updateValue(userId!, forKey: "userId")
    }

    self.playRTC.createChannel(parameters)
}
```


### 만들어진 채널에 입장하기 – USER B

이미 생성된 채널에 입장히는 메소드는 connectChannel 입니다.

connectChannel  호출 시  해당 채널 아이디가 필요합니다.  채널 사용자의 부가 정보를 같이 전달하여 채널/사용자  정보 조회 시 받아볼 수 있습니다. connectChannel가 잘 수행되면 onConnectChannel 메소드를 통해 보안등을 위한 채널 사용자 토큰정보등을 받을 수 있으므로 잘 보관해둬야 합니다.

다음과 같은 형태로 구현할 수 있습니다.

```Objective-C
- (void)connectChannel:(NSString*)chId userId:(NSString*)userId {
    if(self.playRTC == nil) {
        return;
    }

    if(chId != nil) {
        self.channelId = chId;
    } else {
        self.channelId = @"";
    }

    NSMutableDictionary* parameters = [NSMutableDictionary dictionary];

    if(userId != nil) {
        self.userUid = userId;
        NSDictionary * peer = [NSDictionary dictionaryWithObject:userId forKey:@"uid"];
        [parameters setObject:peer forKey:@"peer"];
    }

    [self.playRTC connectChannel:self.channelId parameters:parameters];
}
```

```Swift
func connectChannel(channelId: String?, userId: String?) {
    var parameters = [String: String]()

    if self.playRTC == nil {
        return
    }

    if channelId != nil {
        self.channelId = channelId
    }

    if channelId != nil {
        parameters.updateValue(channelId!, forKey: "channelId")
    }

    if userId != nil {
        parameters.updateValue(userId!, forKey: "userId")
    }

    self.playRTC.connectChannel(self.channelId, parameters: parameters)
}
```


### 채널 입장 이벤트 받기

createChannel, connectChannel을 호출하여 채널에 입장하면  PlayRTCObserver의 onConnectChannel 이 호출이되며, 입장한 채널의 아이디를 전달하게 됩니다.  onConnectChannel이 호출 될때 reason의 문자열 값으로 채널 입장 시 사용한 createChannel, connectChannel을 구분할 수 있습니다.

다음과 같은 형태로 구현할 수 있습니다.

```Objective-C
-(void)onConnectChannel:(PlayRTC*)obj channelId:(NSString*)chId reason:(NSString*)reason
{
    self.channelId = chId;
    [(Sample1ViewController*)self.controller onConnectChannel:chId reason:reason];
}
```

```Swift
func onConnectChannel(playRTC: PlayRTC, channelId: String?, resion: String?) {
    self.channelId = channelId
}
```

## PlayRTC의 미디어 스트림의 영상 출력 처리

SDK는  영상/음성 스트림을 위한 내부 개체를 생성 후  PlayRTCMedia 인터페이스를 onAddLocalStream 또는 onAddRemoteStream 를 통해서 전달합니다. PlayRTCMedia를 전달 받으면 PlayRTCMedia객체에 영상 출력 뷰(PlayRTCVieoView)의 렌더러 인터페이스를 전달하여 영상을 출력하도록 처리해야 합니다.

### 영상 출력을 위한 PlayRTCVideoView 객체 생성

PlayRTC SDK는 영상 스트림을 출력하는 PlayRTCVieoView Class를 제공합니다.
Sample 예에서는 ViewController의 viewWillAppear에서 MainRTCViewController_UILayout의 initScreenLayoutView를 호출하여 생성하도록 하였습니다.

뷰 배치는 상대방 스트림 View 위에 로컬 스트림을 출력하는 작은 View를 배치하도록 구성하겠습니다.

다음과 같은 형태로 구현할 수 있습니다..

```Objective-C
// MainViewController.h
- (void)viewWillAppear:(BOOL)animated {
  NSLog(@"[%@] viewWillAppear", LOG_TAG);

  [super viewWillAppear:animated];
  CGRect bounds = self.view.bounds;
  [self initScreenLayoutView:bounds];
}

#import "PlayRTCVideoView.h"

- (void) initScreenLayoutView:(CGRect)frame {
  NSLog(@"[%@] initScreenLayoutView...", LOG_TAG);
  CGRect bounds = frame;
  CGRect mainFrame = bounds;
  mainFrame.origin.y = 0;
  mainFrame.size.height = bounds.size.height;

  if([[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPad) {
    mainFrame.origin.y += 30.0f;
    mainFrame.size.height -= 60.0f;
  }

  mainAreaView = [[UIView alloc] initWithFrame:mainFrame];
  mainAreaView.backgroundColor = [UIColor whiteColor];

  CGRect videoFrame = mainFrame;
  videoFrame.size.height = mainFrame.size.height;
  videoFrame.size.width = videoFrame.size.height / 0.75f;
  videoFrame.origin.y=0;

  videoAreaView = [[UIView alloc] initWithFrame:videoFrame];
  videoAreaView.center = mainAreaView.center;
  videoAreaView.backgroundColor = [UIColor brownColor];

  [self initVideoLayoutView:videoAreaView videoFrame:videoAreaView.bounds];
  [mainAreaView addSubview:videoAreaView];

  ... Something Else ...
}

- (void) initVideoLayoutView:(UIView*)parent videoFrame:(CGRect)videoFrame
{
  NSLog(@"[%@] initVideoLayoutView...", LOG_TAG);

  CGRect bounds = videoFrame;

  self.remoteVideoView = [[PlayRTCVideoView alloc] initWithFrame:bounds];

  CGRect localVideoFrame = videoFrame;
  localVideoFrame.size.width = localVideoFrame.size.width * 0.35;
  localVideoFrame.size.height = localVideoFrame.size.height * 0.35;
  localVideoFrame.origin.x = bounds.size.width - localVideoFrame.size.width - 10.0f;
  localVideoFrame.origin.y = 10.0f;

  self.localVideoView = [[PlayRTCVideoView alloc] initWithFrame:localVideoFrame];

  [parent addSubview:self.remoteVideoView];
  [parent addSubview:self.localVideoView];
}
```

```Swift
override func viewWillAppear(animated: Bool) {
    var bounds: CGRect?

    super.viewWillAppear(animated)

    bounds = self.view.bounds

    // Make the videoView at the viewWillAppear time.
    self.initScreenLayoutView(bounds!)
}

func initScreenLayoutView(frame: CGRect) {
    var mainAreaView: UIView!
    var videoFrame: CGRect!
    var bounds: CGRect = frame
    var mainFrame: CGRect = bounds

    mainFrame.origin.y = 0
    mainFrame.size.height = bounds.size.height

    mainFrame.origin.y += 20.0
    mainFrame.size.height -= 20.0

    mainAreaView = UIView(frame: mainFrame)

    videoFrame = mainFrame

    videoAreaView = UIView(frame: videoFrame)
    videoAreaView!.backgroundColor = UIColor.brownColor()

    self.initVideoLayoutView(videoAreaView!, videoFrame: videoAreaView!.bounds)

    mainAreaView.addSubview(videoAreaView!)
}

func initVideoLayoutView(parent: UIView, videoFrame: CGRect) {
    var localVideoFrame: CGRect
    var bounds: CGRect = videoFrame

    self.remoteVideoView = PlayRTCVideoView.init(frame: bounds)

    localVideoFrame = videoFrame
    localVideoFrame.size.width = localVideoFrame.size.width * 0.35
    localVideoFrame.size.height = localVideoFrame.size.height * 0.35
    localVideoFrame.origin.x = bounds.size.width - localVideoFrame.size.width - 10.0
    localVideoFrame.origin.y = 10.0

    self.localVideoView = PlayRTCVideoView.init(frame: localVideoFrame)

    parent.addSubview(self.remoteVideoView!)
    parent.addSubview(self.localVideoView!)
}
```

### Local 영상 미디어 스트림 출력 – PlayRTCObserver.onAddLocalStream

PlayRTCMedia 개체에 영상 출력 처리를 위해 PlayRTCVieoView의 렌더러 인터페이스를 전달합니다.

다음과 같은 형태로 구현할 수 있습니다.

```Objective-C
#import "PlayRTCMedia.h"

-(void)onAddLocalStream:(PlayRTC*)obj media:(PlayRTCMedia*)media {
  self.localMedia = media;
  [media setVideoRenderer:[self.localVideoView getVideoRenderer]];
}
```

```Swift
func onAddLocalStream(obj: PlayRTC!, media: PlayRTCMedia!) {
    self.localMedia = media
    media.setVideoRenderer(self.localVideoView?.getVideoRenderer())
}
```


### Remote 영상 미디어 스트림 출력 – PLayRTCObserver.onAddRemoteStream

PlayRTCMedia 에 영상 출력 처리를 위해 PlayRTCVieoView의 렌더러 인터페이스를 전달합니다.

다음과 같은 형태로 구현할 수 있습니다.

```Objective-C
#import "PlayRTCMedia.h"

-(void)onAddRemoteStream:(PlayRTC*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid media:(PlayRTCMedia*)media {
  self.remoteMedia = media;
  [media setVideoRenderer:[self.remoteVideoView getVideoRenderer]];
}
```

```Swift
func onAddRemoteStream(obj: PlayRTC!, peerId: String!, peerUid: String!, media: PlayRTCMedia!) {
    self.remoteMedia = media
    media.setVideoRenderer(self.remoteVideoView?.getVideoRenderer())
}
```

## PlayRTC 연결 종료

PlayRTC에서 상대방과 연결을 종료하는 경우는 다음의 경우가 있습니다.


### 상대방이 채널에서 퇴장 하는 경우 – 채널이 유효한 상태

상대방이 disconnectChannel를 호출하면 SDK는 채널 서비스로부터 채널 퇴장 이벤트를 받아 채널에서 퇴장하며, 채널 서비스는 나에게 상대방의 채널 퇴장을 알리는 이벤트(onOtherDisconnectChannel)를 보내 줍니다.


### 자신이 채널에서 퇴장하는 경우 – 채널이 유효한 상태

disconnectChannel을 호출하면 SDK는 채널 서비스로부터 채널 퇴장 이벤트(onDisconnectChannel)를 받아 채널에서 퇴장하고, 채널 서비스는 상대방에게도 나의 채널 퇴장 이벤트(onOtherDisconnectChannel)를 전달하게 됩니다. 상대방은 이 이벤트를 받아 나의 채널 퇴장을 알게 됩니다.


### 상대방 또는 자신이 채널을 종료 하는 경우 – 예제에서의 채널 종료 방법

deleteChannel을 호출하면 채널 서비스는 채널에 입장한 모든 사용자에게 채널 종료를 통보(onDisconnectChannel)하고 채널에 있는 모든 사용자의 연결을 종료합니다. 이때 각각의 사용자는 종료하는 과정을 진행합니다.


### 채널 종료를 이용한 PlayRTC 연결 종료

화면의 Close 버튼을 이용하여 종료과정을 구현합니다. 우선 화면에 PlayRTC를 종료하는 버튼을 생성(MainViewController_UILayout)하고 클릭 이벤트 시 PlayRTC의  deleteChannel을 호출하여 채널 종료를요청합니다.

채널 서비스에서 채널 종료 이벤트(MainViewController_PlayRTC의 onDeleteChannel,)를 받으면  ViewController를 종료 하도록 MainViewController의 closeApp을 호출합니다.

다음과 같은 형태로 구현할 수 있습니다.

```Objective-C
[self performSelector:@selector(deleteChannel) withObject:nil afterDelay:0.1];

-(void)onDisconnectChannel:(PlayRTC*)obj reason:(NSString*)reason {
  [self closeApp];
}

-(void)closeApp {
  UIViewController* viewController = [self.navigationController popViewControllerAnimated:TRUE];
  viewController = nil;

  // This needs a approval of the Apple App Store
  exit(0);
}

- (void)dealloc {
  self.localMedia = nil;
  self.remoteMedia = nil;
  self.channelId = nil;
  self.token = nil;
  self.userUid = nil;

  [self.remoteVideoView removeFromSuperview];
  self.remoteVideoView = nil;
  [self.localVideoView removeFromSuperview];
  self.localVideoView = nil;

  self.playRTC = nil;
}
```
