# 영상/음성 통화 iOS 앱 만들기
본 튜토리얼을 진행하려면 iOS용 PlayRTC SDK와 샘플을 다운로드받아야 합니다.

## iOS  SDK  사용 환경

PlayRTC SDK를 이용하기 위한 환경은 다음과 같습니다.

- iOS Target: iOS SDK 7.0
- 개발 언어 : Object-C
- SKT PlayRTC Library
  - libsktplayrtc.X.X.X.a
  - header files
- 관련 link binary Libraryes
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

## iOS Application 프로젝트 환경 설정

libsktplayrtc.X.X.X.a파일을 Biuld Phases에 추가, Application을 만들때 사용되는 아카이브 파일.
libsktplayrtc.X.X.X.a 관련 header 파일 추가

## 프로젝트 구성 환경 (Build Settings)

- Architecture
  - Architecture : $(ARCHS_STANDARD),$(ARCHS_STANDARD_INCLUDING_64_BIT)
  - Build Active Architecture Only : NO
  - Valid Architecture : armv7
- Linking
  - Other Linker FLags : -ObjC
- Apple LLVM 6.0 – Language
  - C Language Dialect : C99[-std=c99]
- Apple LLVM 6.0 – Language – C
  - C++ Language Dialect : C++11[-std=c++11]
- Copy Bundle Resource
  - icudtl.dat

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

Sample 소스의MainViewController_PlayRTC의 createPlayRTCHandler를 참고하세요.

```Objective-C
#import "PlayRTC.h"
#import "PlayRTCFactory.h"
.....

String svcSvrUrl = "http://서비스서버호스트:포트/restful";
// 만약 서비스버서 RESTful API의 createChannel URL이
// </strong>http://서비스서버호스트:포트/restful/v1/channels 이라면
// 서비스 서버 RESTful 서비스 URL은 http://서비스서버호스트:포트/restful 이 됨

/* PlayRTC 생성
* - url String, 서비스 서버 URL
* - playRTCObserver, PlayRTCOberver 인터페이스 구현 리스너
*/
NSString* url = @"http://www.playrtc.com:5100";
self.playRTC = [PlayRTCFactory newInstanceWithServiceUrl:url observer:(id<PlayRTCObserver>)self];
```

## PlayRTCSetting 개체를 이용한 서비스 설정

PlayRTC 객체를 생성하고 나면 getSettings() 메소드를 호출하여 PlayRTC에 설정해둔 기본적인 세팅값들을 가져올 수 있습니다. 즉 getSettings 메소드를 호출하면 PlayRTCSettings 객체를 얻을 수 있습니다. createChannel이나 connectChannel등의 메소드를 호출하는 채널작업을 하기 전에 이 객체를 수정하게 되면 수정된 값을 이용하여 채널 작업을 수행할 수 있게 됩니다.

PlayRTCSetting의 주요 설정 항목은 다음과 같습니다.

- PlayRTC 미디어 서비스 관련 설정 – 사용자 재지정
- PlayRTC 로깅 설정 – 사용자 지정

### PlayRTCSetting 개체 구하기

PlayRTC 구현 객체를 생성한 후 PlayRTC의 getSettings() 메소드를 호출하여 PlayRTCSetting 개체를 얻어와 구현할 서비스에 맞게 설정 항목을 설정할 수 있습니다. PlayRTCSetting 개체는 기본값으로 초기화 되어 있는 상태입니다.

```Objective-C
#import "PlayRTCSettings.h"
/* PlayRTC 서비스 설정 구하기*/
PlayRTCSettings* settings = [playRTC getSettings];
```

### 서비스 설정하기

Sample 소스의MainViewController_PlayRTC의 setConfiguration를 참고하세요.

### 영상 및 음성 스트리밍 사용 설정

PlayRTCSettings의 videoEnable과 audioEnable 메소드로  Boolean 값을 지정하며, 각 항목에 FALSE를 설정하면 자신의 미디어 스트림은 상대에게 전송이 되지 않으나 상대방이 사용하도록 설정 한 경우에는 상대방의 미디어 스트림을 수신하게 됩니다.  SDK 서비스 설정에 정의한 값을 다음과 같이 재 지정 할 수 있습니다.

```Objective-C
[settings setAudioEnable:TRUE or FALSE];   /* 음성 전송 사용 여부 */
[settings setVideoEnable:TRUE or FALSE];   /* 영상 전송 사용 여부 */
```

### 데이터 통신 스트림  사용 설정

PlayRTCSettings의 dataEnable 메소드로 TRUE를 설정하면 데이터 통신 채널을 생성 하여 텍스트 및 Binary,파일 데이터를 송수신 할 수 있는 통신 스트림 개체가 생성이 됩니다. 데이터 통신 스트림만  사용하려면 영상/음성 설정을 FALSE로 지정하면 됩니다. SDK 서비스 설정에 정의한 값을 다음과 같이 재 지정 할 수 있습니다.

```Objective-C
[settings setDataEnable:TRUE or FALSE];   /* 데이터 스트림 사용 여부 */
```

### 상호 연결 승인 과정 설정

서비스 이용자 상호간에 통신 연결 여부를 확인하는 과정을 설정할 수 있습니다.
channel 설정에서 setRing 메소드에 TRUE를 설정하면 연결과정에서 채널에 나중에 입장하는 사용자는 먼저 입장한 사용자에게 연결 확인을 받고 연결 과정을 수행하게 됩니다.

```Objective-C
/* ring, 연결 수립 여부를 상대방에게 묻는지 여부를 지정, TRUE면 상대의 수락이 있어야 연결 수립 진행 */
[settings.channel setRing:TRUE or FALSE];
```

### SDK 로깅 설정

PlayRTC SDK는 PlayRTC 로그를 X-Code 로그 Console에 출력하고, 단말기에 파일로 저장 할 수 있으며 주요 실행 로그 및 오류 사항을 PlayRTC 플랫폼 로그 서비스에 전달 합니다.
console, file 로그는 각각의 로그레벨(TRACE, WARN, ERROR)을 설정할 수 있습니다. 로그 설정 예는 다음과 같습니다.

```Objective-C
/* SDK Console 로그 레벨 지정 */
[settings.log.console setLevel:LOG_TRACE];

/* SDK 파일 로깅을 위한 로그 파일 경로, 파일 로깅을 사용하지 않는다면 Pass */
NSString* docPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0];
NSString* logPath = [docPath stringByAppendingPathComponent:@"log"];

/* SDK 파일 로그 레벨 지정 */
[settings.log.file setLevel:LOG_TRACE];

/* 파일 로그를 남기려면 로그파일 폴더 지정 . [PATH]/yyyyMMdd.log */
[settings.log.file setLogPath:logPath];
[settings.log.file setRolling:10]; /* 10일간 보존 */

/* 서버 로그 전송 실패 시 임시 로그 DB 저장 폴더 */
NSString* cachePath = [docPath stringByAppendingPathComponent:@"cache"];
[settings.log setCachePath:cachePath];

/* 서버 로그 전송 실패 시 재 전송 지연 시간, msec */
[settings.log setRetryQueueDelays:1000];

/* 서버 로그 재 전송 실패시 로그 DB 저장 후 재전송 시도 지연 시간, msec */
[settings.log setRetryCacheDelays:(10 * 1000)];
```

## PlayRTC P2P 연결을 위한 채널 서비스 입장 및 이벤트처리

먼저 채널에 입장히는 사용자가 채널을 생성해야 하며 다른 사용자는 생성된 채널의 아이디로 채널에 입장을 하여 P2P연결을 합니다.

### 신규 채널을 생성하고 입장하기 – USER A

PlayRTC 플랫폼 채널 서비스에 채널을 새로 생성하고 입장하는 메소드는 PlayRTC::createChannel 입니다. 이 메소드를 호출하면 USER A를 위한 채팅방이 채널서버에 만들어진다고 보면 됩니다. 이 요청은 서비스서버를 거쳐서 채널서버로 전달됩니다. 채널서버는 채널id와 USER A에게 할당된 토큰값등을 반환해줄 것입니다. 가장 기본적인 사용법은 아무런 입력값 없이 nil을 입력하여 createChannel(nil) 메소드 호출하여 채널을 생성하는 방법이 있습니다.

하지만 PlayRTC는 부가적인 채널이나 peer정보등을 이 createChannel 메소드에 실어서 채널서버에  저장할 수 있도록 하고 있습니다. 즉, createChannel 호출 시 채널 및 채널 사용자의 부가 정보를 같이 전달하여 채널/사용자 정보 조회 시 받아볼 수 있습니다.

createChannel 메소드를 호출하면 PlayRTC는 새로 생성한 채널의 id를 PlayRTCObserver의 onConnectChannel 메소드를 통해 전달해 줍니다. 이때 전달 받은 채널id는 USER B가 connectChannel로 채널에 입장할 때 사용해야 하는  채널 아이디입니다.

createChannel과 관련된 예제는 다음과 같습니다.

Sample 소스의  MainViewController_PlayRTC의 createChannel을 참고하세요.

```Objective-C
if(self.playRTC == nil) {
  return;
}

NSMutableDictionary* parameters = [NSMutableDictionary dictionary];
if(channelName != nil) {
  NSDictionary * channel = [NSDictionary dictionaryWithObject:channelName forKey:@"channelName"];
  [parameters setObject:channel forKey:@"channel"];
}

if(userId != nil) {
  self.userUid = userId;
  NSDictionary * peer = [NSDictionary dictionaryWithObject:userId forKey:@"uid"];
  [parameters setObject:peer forKey:@"peer"];
}
NSLog(@"[PlayRTCViewController] createChannel channelName[%@] userId[%@]", channelName, userId);
[self.playRTC createChannel:parameters];=
```

### 만들어진 채널에 입장하기 – USER B

이미 생성된 채널에 입장히는 메소드는 connectChannel 입니다.

connectChannel  호출 시  해당 채널 아이디가 필요합니다.  채널 사용자의 부가 정보를 같이 전달하여 채널/사용자  정보 조회 시 받아볼 수 있습니다. connectChannel가 잘 수행되면 onConnectChannel 메소드를 통해 보안등을 위한 채널 사용자 토큰정보등을 받을 수 있으므로 잘 보관해둬야 합니다.

connectChannel에 대한 소스코드는 다음과 같습니다.

Sample 소스의  MainViewController_PlayRTC의connectChannel을 참고하세요.

```Objective-C
if(self.playRTC == nil) {
   return;
}
if(chId != nil) {
   self.channelId = chId;
}
NSMutableDictionary* parameters = [NSMutableDictionary dictionary];
if(userId != nil) {
    self.userUid = userId;
    NSDictionary * peer = [NSDictionary dictionaryWithObject:userId forKey:@"uid"];
    [parameters setObject:peer forKey:@"peer"];
}
NSLog(@"[PlayRTCViewController] connectChannel channelId[%@] userId[%@]", chId, userId);
[self.playRTC connectChannel:self.channelId parameters:parameters];
```

### 채널 입장 이벤트 받기

createChannel, connectChannel을 호출하여 채널에 입장하면  PlayRTCObserver의 onConnectChannel 이 호출이되며, 입장한 채널의 아이디를 전달하게 됩니다.  onConnectChannel이 호출 될때 reason의 문자열 값으로 채널 입장 시 사용한 createChannel, connectChannel을 구분할 수 있습니다.

Sample 소스의  MainViewController_PlayRTC의 onConnectChannel 메소드를 참고하세요.

```Objective-C
//PlayRTCObserver.h
-(void)onConnectChannel:(PlayRTC*)obj channelId:(NSString*)chId reason:(NSString*)reason
{
 self.channelId = chId;
 NSLog(@"[PlayRTCViewController] onConnectChannel[%@] reason[%@]", channelId, reason);
 [self appendLogView:@"onConnectChannel"];
 [channelPopup setChannelId:self.channelId];
 [channelPopup hide];
}
```

## P2P 연결 승인과정 적용하기

PlayRTCSettings 객체의 channel 의 ring 을 TRUE로 설정 하면 PlayRTC SDK는 사용자간에 연결 여부를 확인하는 과정을 수행 하게 됩니다. ring 옵션을 FALSE로 설정하면 이 과정을 수행하지 않습니다.

채널에 나중에 입장한 사용자는 먼저 입장한 연결 대상자에게 연결 승인을 물러본다.
채널에 먼저 입장한 사용자는 연결 상대방에게 연결 승인을 통보한다.
Sample 소스의  MainViewController_PlayRTC의 onRing, onAccept, onReject를 참고하세요.

### 채널에 먼저 입장한 경우 – USER A

채널에 먼저 입장한 사용자는 연결 과정 시작 전에 상대방으로부터 연결 의사를 묻는  onRing을 받게 됩니다.

연결 수락 의사는 PlayRTC의 accept를 호출하며, 거부 의사는 PlatRTC의 reject 메소드를 호출합니다.

다음 예제 소스는 Alert Dialog를 통해서 연결 수락 여부를 결정하고 상대방에게 통보 하도록 구현한 예입니다.

```Objective-C
@interface RingAlertViewDelegate : NSObject<UIAlertViewDelegate>
{
  __weak PlayRTC* playRTC;
  NSString* tagetId;
}
@property (weak, nonatomic) PlayRTC* playRTC;
@property (strong, nonatomic) NSString* tagetId;
- (void)alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex;
@end

//PlayRTCObserver.h
-(void)onRing:(PlayRTC*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid
{
  self.ringPid = peerId;
  alertDelegate = [[RingAlertViewDelegate alloc] init];
  alertDelegate.playRTC = self.playRTC;
  alertDelegate.tagetId = peerId;
  UIAlertView *alert = [[UIAlertView alloc] initWithTitle:nil
  message:[NSString stringWithFormat:@"%@님이 연결을 요청하였습니다.", peerUid]
  delegate:alertDelegate
  cancelButtonTitle:nil
  otherButtonTitles:@"연결",@"거절", nil];
  [alert show];
}
```

### 채널에 나중에 입장한 경우 – USER B

SDK는 먼저 입장해 있는 USER A 사용자에게 연결 수락 여부를 묻는 메시지를 전달하게 되고 상대방 사용자의 수락 여부를 PlayRTCObserver onAccept 또는 onReject를 통해 알려줍니다.

```Objective-C
// 연결 수락
-(void)onAccept:(PlayRTC*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid
{
  NSLog(@"[PlayRTCViewController] onAccept peerId[%@] peerUid[%@]", peerId, peerUid);
  [self appendLogView:[NSString stringWithFormat:@"onAccept peerId[%@] peerUid[%@]", peerId, peerUid]];
}

// 연결 거부
-(void)onReject:(PlayRTC*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid
{
  NSLog(@"[PlayRTCViewController] onReject peerId[%@] peerUid[%@]", peerId, peerUid);
  [self appendLogView:[NSString stringWithFormat:@"onReject peerId[%@] peerUid[%@]", peerId, peerUid]];
}
```

## PlayRTC의 미디어 스트림의 영상 출력 처리

SDK는  영상/음성 스트림을 위한 내부 개체를 생성 후  PlayRTCMedia 인터페이스를 onAddLocalStream 또는 onAddRemoteStream 를 통해서 전달합니다. PlayRTCMedia를 전달 받으면 PlayRTCMedia객체에 영상 출력 뷰(PlayRTCVieoView)의 렌더러 인터페이스를 전달하여 영상을 출력하도록 처리해야 합니다.

### 영상 출력을 위한 PlayRTCVideoView 객체 생성

PlayRTC SDK는 영상 스트림을 출력하는 PlayRTCVieoView Class를 제공합니다.
Sample 예에서는 ViewController의 viewWillAppear에서 MainRTCViewController_UILayout의 initScreenLayoutView를 호출하여 생성하도록 하였습니다.

뷰 배치는 상대방 스트림 View 위에 로컬 스트림을 출력하는 작은 View를 배치하도록 구성하겠습니다.

Sample 소스의 MainRTCViewController_UILayout의 initScreenLayoutView를 참고하세요.

소스 코드는 다음과 같습니다.

```Objective-C
// MainViewController.h
- (void)viewWillAppear:(BOOL)animated
{
  NSLog(@"[%@] viewWillAppear", LOG_TAG);
  [super viewWillAppear:animated];
  CGRect bounds = self.view.bounds;
  [self initScreenLayoutView:bounds];
}

#import "PlayRTCVideoView.h"
// MainRTCViewController_UILayout
// 영상 출력용 PlayRTCVideoView객체 동적 생성 및 부모 View설정
- (void) initScreenLayoutView:(CGRect)frame
{
  NSLog(@"[%@] initScreenLayoutView...", LOG_TAG);
  CGRect bounds = frame;
  CGRect mainFrame = bounds;
  mainFrame.origin.y = 0;
  mainFrame.size.height = bounds.size.height;

  if([[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPad)
  {
    mainFrame.origin.y += 30.0f;
    mainFrame.size.height -= 60.0f;
  }

  mainAreaView = [[UIView alloc] initWithFrame:mainFrame];
  mainAreaView.backgroundColor = [UIColor whiteColor];

  /* video 스트림 출력을 위한 PlayRTCVideoView의 부모 뷰 사이즈
  * 가로-세로 비율 1(가로):0.75(세로), 높리 기준으로 폭 재지정
  */
  CGRect videoFrame = mainFrame;
  videoFrame.size.height = mainFrame.size.height;
  videoFrame.size.width = videoFrame.size.height / 0.75f;
  videoFrame.origin.y=0;

  videoAreaView = [[UIView alloc] initWithFrame:videoFrame];
  videoAreaView.center = mainAreaView.center;
  videoAreaView.backgroundColor = [UIColor brownColor];

  /* PlayRTCVideoView를 생성하여 부모뷰에 추가 */
  [self initVideoLayoutView:videoAreaView videoFrame:videoAreaView.bounds];
  [mainAreaView addSubview:videoAreaView];

  .......

}

/* PlayRTCVideoView를 생성하여 부모뷰에 추가 */
- (void) initVideoLayoutView:(UIView*)parent videoFrame:(CGRect)videoFrame
{
  NSLog(@"[%@] initVideoLayoutView...", LOG_TAG);

  CGRect bounds = videoFrame;

  self.remoteVideoView = [[PlayRTCVideoView alloc] initWithFrame:bounds];

  // 부모 View의 30% 비율로 크기를 지정한다
  CGRect localVideoFrame = videoFrame;
  localVideoFrame.size.width = localVideoFrame.size.width * 0.35;
  localVideoFrame.size.height = localVideoFrame.size.height * 0.35;
  // 부모 뷰 좌측 상단 위치
  localVideoFrame.origin.x = bounds.size.width - localVideoFrame.size.width - 10.0f;
  localVideoFrame.origin.y = 10.0f;

  self.localVideoView = [[PlayRTCVideoView alloc] initWithFrame:localVideoFrame];

  [parent addSubview:self.remoteVideoView];
  [parent addSubview:self.localVideoView];
}
```

### Local 영상 미디어 스트림 출력 – PlayRTCObserver.onAddLocalStream

PlayRTCMedia 개체에 영상 출력 처리를 위해 PlayRTCVieoView의 렌더러 인터페이스를 전달합니다.

Sample 소스의 MainRTCViewController_PlayRTC의 onAddLocalStream을 참고하세요.

```Objective-C
#import "PlayRTCMedia.h"
....
-(void)onAddLocalStream:(PlayRTC*)obj media:(PlayRTCMedia*)media
{
  NSLog(@"[PlayRTCViewController] onAddLocalStream media[%@]", media);
  [self appendLogView:@"onAddLocalStream..."];

  self.localMedia = media;
  //PlayRTCVieoView의 렌더러 인터페이스를 전달
  [media setVideoRenderer:[self.localVideoView getVideoRenderer]];

}
```


### Remote 영상 미디어 스트림 출력 – PLayRTCObserver.onAddRemoteStream

PlayRTCMedia 에 영상 출력 처리를 위해 PlayRTCVieoView의 렌더러 인터페이스를 전달합니다.

Sample 소스의 MainRTCViewController_PlayRTC의 onAddRemoteStream을 참고하세요.

```
#import "PlayRTCMedia.h"
....
-(void)onAddRemoteStream:(PlayRTC*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid media:(PlayRTCMedia*)media
{
  NSLog(@"[PlayRTCViewController] onAddRemoteStream peerId[%@] peerUid[%@] media[%@]", peerId, peerUid, media);
  [self appendLogView:[NSString stringWithFormat:@"onAddRemoteStream peerId[%@] peerUid[%@]", peerId, peerUid]];
  self.remoteMedia = media;
  //PlayRTCVieoView의 렌더러 인터페이스를 전달
  [media setVideoRenderer:[self.remoteVideoView getVideoRenderer]];
}
```

## PlayRTCData – 데이터 스트림을 이용한 데이터 교환

PlayRTCSetting에 데이터 스트림을 사용하도록 설정 하였다면, PlayRTC SDK는 P2P 연결이 수립되면 데이터 통신을 제공하는 개체를 생성하고 개체의 인터페이스를 전달합니다. 데이터 스트림은 텍스트, 바이너리, 파일을 송/수신하는 기능을 제공합니다.

Sample 소스의 MainRTCViewController_PlayRTC 클래스를 참고하세요.

### PlayRTCData 전달 받기 – PlayRTCObserver.onAddDataStream

PlayRTCObserver의 onAddDataStream를 통해 PlayRTCData 인터페이스를 전달받습니다. PlayRTCData를 전달 받으면 상대방의 데이터를 수신하거나 데이터 스트림 객체의 상테 및 오류등을 처리 하기 위해서 PlayRTCDataObserver를 구현한 개체를 등록해야 합니다.

Sample 소스의 MainRTCViewController_PlayRTC의 onAddDataStream을 참고하세요.

```
#import "PlayRTCData.h"
#import "PlayRTCDataObserver.h"

/* P2P 데이터 통신을 위한 PlayRTCData객체 */
//PlayRTCData* dataChannel;

// P2P 연결 수립 후 데이터 통신 객체 생성 후 전달 받아 PlayDataObserver 인터페이스 구현 개체를 등록
-(void)onAddDataStream:(PlayRTC*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid data:(PlayRTCData*)data
{
  NSLog(@"[PlayRTCViewController] onAddDataStream peerId[%@] peerUid[%@] data[%@]", peerId, peerUid, data);
  [self appendLogView:[NSString stringWithFormat:@"onAddDataStream peerId[%@] peerUid[%@]", peerId, peerUid]];

  self.dataChannel = data;
  //P2P 연결 수립 후 데이터 통신 객체 생성 후 전달 받아 PlayDataObserver 인터페이스 구현 개체를 등록
  [self.dataChannel setEventObserver:(id<PlayRTCDataObserver>)self];
}
```

### 데이터 수신 및 PlayRTCData 성태 및 오류 정보 처리를 위한 PlayRTCDataObserver 구현

데이터 수신과  PlayRTCData의 상태 및 오류 정보를 전달 받는 인터페이스를 수현합니다. PlayRTCData는 데이터 전송 시 헤더 정보를 구성하여 데이터를 전송하며 데이터 수신 시 헤더 정보를 읽어 이벤트를 통해 알려 줍니다. 데이터는 고유 아이디가 지정 되어 있으며 헤더에서 데이터 종류(Text, Binary, File)를 확인하여 데이터를 처리해야 합니다. 파일 데이터의 경우 헤더 정보에 파일 이름 및 MimeType이 같이 전달 됩니다

데이터 수신부 구현은 다음과 같습니다. onProgress에서  데이터 수신 진행 정도를 처리하며, 오류 없이 데이터 수신을 완료되면 onMessage에서 데이터 유형에 맞게 처리를 합니다.

Sample 소스의 MainRTCViewController_PlayRTC의 onProgress, onMessage를 참고하세요.

```Objective-C
#import "PlayRTCDataObserver.h"
....

-(void)onProgress:(PlayRTCData*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid recvSize:(long)recvSize header:(PlayRTCDataHeader*)header
{
  // 데이터 유형을 확인할 수 있습니다.</pre>
  NSString* dataType = [header isBinary]?@"binary":@"text";
  NSLog(@"[PlayRTCViewController] DataChannel onProgress peerId[%@] peerUid[%@] dataType[%@] recvSize[%ld]", peerId, peerUid, dataType, recvSize);
  [self appendLogView:[NSString stringWithFormat:@"DataChannel onProgress peerId[%@] peerUid[%@]", peerId, peerUid]];
}

-(void)onMessage:(PlayRTCData*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid header:(PlayRTCDataHeader*)header data:(NSData*)data
{
  NSLog(@"[PlayRTCViewController] DataChannel onMessage peerId[%@] peerUid[%@] ", peerId, peerUid);
  // 텍스트 데이터 수신인 경우
  if([header getType] == DC_DATA_TYPE_TEXT)
  {
    // 수신 문자열을 로그창에 출력
    NSString* dataStr = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
    NSLog(@"[PlayRTCViewController] DataChannel Text[%@]", dataStr);
    [self appendLogView:[NSString stringWithFormat:@">>Data-Channel[%@]", dataStr]];
  }
  else {

    NSString* fileName = [header getFileName];
    if(fileName == nil || fileName.length ==0) {
      // 수신 데이터가 바이너리 데이터임
      NSLog(@"[PlayRTCViewController] DataChannel Binary[%ld]", [header getSize]);
      [self appendLogView:[NSString stringWithFormat:@">>Data-Channel[%@] onMessage Binary[%ld]",peerUid, [header getSize]]];
    }
    else {
      // 파일명이 있는 경우는 파일 전송인 경우임
      // 수신 데이터를 파일 저장 처리가 필요.
    }
  }
}
```

PlayRTCData의 상태 정보와 오류 정보를 확인하기 위해서 아래와 같이 로그를 출력하도록 하겠습니다.

```Objective-C
-(void)onError:(PlayRTCData*)obj
peerId:(NSString*)peerId
peerUid:(NSString*)peerUid
did:(uint64_t)did
code:(PlayRTCDataCode)code
desc:(NSString*)desc
{
  NSString* sCode = nil;
  if(code == PlayRTCDataCodeNone) sCode = @"PlayRTCDataCodeNone";
  else if(code == PlayRTCDataCodeNotOpend) sCode = @"PlayRTCDataCodeNotOpend";
  else if(code == PlayRTCDataCodeSendBusy) sCode = @"PlayRTCDataCodeSendBusy";
  else if(code == PlayRTCDataCodeSendFail) sCode = @"PlayRTCDataCodeSendFail";
  else if(code == PlayRTCDataCodeFileIO) sCode = @"PlayRTCDataCodeFileIO";
  else if(code == PlayRTCDataCodeParseFail)sCode = @"PlayRTCDataCodeParseFail";

  NSLog(@"[PlayRTCViewController] DataChannel onError peerId[%@] peerUid[%@] code[%@] desc[%@]", peerId, peerUid, sCode, desc);
  [self appendLogView:[NSString stringWithFormat:@"DataChannel onError peerId[%@] peerUid[%@] code[%@] desc[%@]", peerId, peerUid, sCode, desc]];
}

-(void)onStateChange:(PlayRTCData*)obj
peerId:(NSString*)peerId
peerUid:(NSString*)peerUid
state:(PlayRTCDataStatus)state
{
  NSString* sState = nil;
  if(state == PlayRTCDataStatusNone) sState = @"PlayRTCDataStatusNone";
  else if(state == PlayRTCDataStatusConnecting) sState = @"PlayRTCDataStatusConnecting";
  else if(state == PlayRTCDataStatusOpen) sState = @"PlayRTCDataStatusOpen";
  else if(state == PlayRTCDataStatusClosing) sState = @"PlayRTCDataStatusClosing";
  else if(state == PlayRTCDataStatusClosed) sState = @"PlayRTCDataStatusClosed";
  NSLog(@"[PlayRTCViewController] DataChannel onStateChange peerId[%@] peerUid[%@] state[%@]", peerId, peerUid, sState);
  [self appendLogView:[NSString stringWithFormat:@"DataChannel onStateChange peerId[%@] peerUid[%@] state[%@]", peerId, peerUid, sState]];  
}
```

### 데이터 전송하기

전송 결과를 확인하기 위해서 PlayRTCDataSendObserver 구현 개체 정의

MainRTCViewController_PlayRTC.m 참고.

```Objective-C
#import "PlayRTCDataSendObserver.h"
....
@interface PlayRTCDataChannelSendObserver : NSObject<PlayRTCDataSendObserver>
{
  __weak PlayRTC* playRTC;
  __weak MainViewController* viewcontroller;
}
@property (weak, nonatomic) PlayRTC* playRTC;
@property (weak, nonatomic) MainViewController* viewcontroller;
-(void)onSuccess:(PlayRTCData*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid did:(uint64_t)did size:(uint64_t)size;
-(void)onError:(PlayRTCData*)obj peerId:(NSString*)peerId peerUid:(NSString*)peerUid did:(uint64_t)did code:(PlayRTCDataCode)code desc:(NSString*)desc;
@end
```

#### 텍스트 전송하기

테스트를 전송하기 위해서는 PlayRTCData의 sendText를 이용하며 데이터 전송 결과를 확인하기 위해서 PlayRTCDataSendObserver 구현 개체가 필요합니다.

Sample 소스의 MainRTCViewController_PlayRTC의 sendDataChannelText를 참고하세요.

```Objective-C
- (void)sendDataChannelText</pre>
{
  //PlayRTCData 생성 여뷰와 채널연결 상탸를 검사
  if(self.dataChannel != nil && [self.dataChannel getStatus] == PlayRTCDataStatusOpen)
  {
    dataChannelDelegate = [[PlayRTCDataChannelSendObserver alloc] init];
    dataChannelDelegate.playRTC = self.playRTC;
    dataChannelDelegate.viewcontroller = self;

    NSString* sendData = @"DataChannel 한글 Text...";
    [self.dataChannel sendText:sendData observer:dataChannelDelegate];
  }
}
```

#### 바이너리 전송하기

바이너리를 전송하기 위해서는 PlayRTCDAta의 sendBinary를 이용하며 데이터 전송 결과를 확인하기 위해서 PlayRTCSendDataObserver 구현 개체가 필요합니다.

Sample 소스의 MainRTCViewController_PlayRTC의 sendDataChannelBinary를 참고하세요.

```Objective-C
- (void)sendDataChannelBinary
{
  // PlayRTCData 생성 연부와 채널 연결 상태를 검사
  if(self.dataChannel != nil && [self.dataChannel getStatus] == PlayRTCDataStatusOpen)
  {
    dataChannelDelegate = [[PlayRTCDataChannelSendObserver alloc] init];
    dataChannelDelegate.playRTC = self.playRTC;
    dataChannelDelegate.viewcontroller = self;

    NSString* sendText = @"DataChannel Text";
    NSData* sendData = [sendText dataUsingEncoding:NSUTF8StringEncoding];
    [self.dataChannel sendByte:sendData mimeType:nil observer:dataChannelDelegate];
  }
}
```

#### 파일전송하기

파일을 전송하기 위해서는 PlayRTCDAta의 sendFile을 이용하며 데이터 전송 결과를 확인하기 위해서 PlayRTCSendDataObserver 구현 개체가 필요합니다.

Sample 소스의 MainRTCViewController_PlayRTC의 sendDataChannelFile를 참고하세요.

```Objective-C
- (void)sendDataChannelFile
{
  // PlayRTCData 생성 여부와 채널연결 상태를 검사
  if(self.dataChannel != nil && [self.dataChannel getStatus] == PlayRTCDataStatusOpen)
  {
    dataChannelDelegate = [[PlayRTCDataChannelSendObserver alloc] init];
    dataChannelDelegate.playRTC = self.playRTC;
    dataChannelDelegate.viewcontroller = self;
    // sample에서는 리소스 폴더의 웹 페이지를 읽어 전송하도록 구현하였습니다.
    NSString *filePath = [[[NSBundle mainBundle] resourcePath] stringByAppendingPathComponent:@"www/main.html"];
    [self.dataChannel sendFileData:filePath observer:dataChannelDelegate];
  }

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

closeApp에서ViewController 종료 과정을 진행합니다.

소스 코드는 다음과 같습니다.

```Objective-C
//Close Button Click, MainViewController_UILayout bottomBtnClick 참고
[self performSelector:@selector(deleteChannel) withObject:nil afterDelay:0.1];

//MainViewController_PlayRTC, 채널 서버에서 채널이 종료되어 호출되는 이벤트.
-(void)onDisconnectChannel:(PlayRTC*)obj reason:(NSString*)reason
{
  NSLog(@"[PlayRTCViewController] onDisconnectChannel reason[%@]", reason);
  // ViewController를 종료한다.
  [self closeApp];
}

//MainViewController
-(void)closeApp
{
  UIViewController* viewController = [self.navigationController popViewControllerAnimated:TRUE];
  viewController = nil;

  NSLog(@"[%@] exit(0)...", LOG_TAG);
  // Sample App을 종료 시키기 위한 코드.
  exit(0);
}
// MainViewController가 해지 될 때 멤버 객체를 해지한다.
- (void)dealloc
{
  NSLog(@"[%@] dealloc...", LOG_TAG);
  self.localMedia = nil;
  self.remoteMedia = nil;
  self.channelId = nil;
  self.token = nil;
  self.userUid = nil;

  NSLog(@"remoteVideoView removeFromSuperview");
  [self.remoteVideoView removeFromSuperview];
  NSLog(@"remoteVideoView relaese");
  self.remoteVideoView = nil;
  NSLog(@"localVideoView removeFromSuperview");
  [self.localVideoView removeFromSuperview];
  NSLog(@"localVideoView relaese");
  self.localVideoView = nil;

  self.playRTC = nil;

}
```
