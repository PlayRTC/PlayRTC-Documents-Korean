# 1.x 에서 2.x로 마이그레이션 하기
1.x에서 2.x로 API변경사항은 없음으로 코드 자체는 그대로 사용이 가능합니다.

다만, PlayRTC 객체를 생성하는 과정에서 설정이 일부 변경되었습니다. 서비스 서버가 없어짐으로써 서비스 서버에서 설정해 주었던 PlayRTC 프로젝트키를 클라이언트에서 설정해 주어야 합니다.

- `SERVICE_URL` 제거
- `setTDCProjectID` 추가
- `settings.setCamera('front or back')` -> `settings.video.setFrontCameraEnable(true or false)`, `settings.video.setBackCameraEnable(true or false)` 변경

아래와 같이 수정합니다.

```Android
// 1.x
public class MainActivity extends Activity {

	private static final String SERVICE_URL = "http://www.playrtc.com:5100";      // REMOVED

  private PlayRTCHandler createPlayRTCHandler(PlayRTCObserverImpl observer) {
    PlayRTCHandler handler = null;
    handler = new PlayRTCHandler(this, SERVICE_URL, observer);                  // CHANGED (SERVICE_URL REMOVED)
    return handler;
  }
}

public PlayRTCHandler(MainActivity activity, String serviceUrl, PlayRTCObserver oberver) // CHANGED (String serviceUrl REMOVED)
  throws UnsupportedPlatformVersionException, RequiredParameterMissingException {

	this.activity = activity;
	this.playRTC = PlayRTCFactory.newInstance(serviceUrl, oberver);               // CHANGED (serviceUrl REMOVED)
}

public void setConfiguration() {
  PlayRTCSettings settings = playRTC.getSettings();
  settings.android.setContext(activity.getApplicationContext());

  settings.setVideoEnable(true);
  settings.setCamera('front');                                                  // REMOVED

  ... something_else ...
}

// 2.x
public class MainActivity extends Activity {

  private PlayRTCHandler createPlayRTCHandler(PlayRTCObserverImpl observer) {
    PlayRTCHandler handler = null;
    handler = new PlayRTCHandler(this, observer);
    return handler;
  }
}

public PlayRTCHandler(MainActivity activity, PlayRTCObserver oberver)
  throws UnsupportedPlatformVersionException, RequiredParameterMissingException {

	this.activity = activity;
	this.playRTC = PlayRTCFactory.newInstance(oberver);                           // CHANGED (serviceUrl REMOVED)
}

public void setConfiguration() {
  PlayRTCSettings settings = playRTC.getSettings();
  settings.android.setContext(activity.getApplicationContext());
  settings.setTDCProjcetID("60ba608a-e228-4530-8711-fa38004719c1");             // ADDED

  settings.setVideoEnable(true);
  settings.video.setFrontCameraEnable(true);                                    // ADDED
  settings.video.setBackCameraEnable(true);                                     // ADDED

	... something_else ...
}
```
