# 1.x 에서 2.x로 마이그레이션 하기
1.x에서 2.x로 API변경사항은 없음으로 코드 자체는 그대로 사용이 가능합니다.

다만, PlayRTC 객체를 생성하는 과정에서 설정이 일부 변경되었습니다. 서비스 서버가 없어짐으로써 서비스 서버에서 설정해 주었던 PlayRTC 프로젝트키를 클라이언트에서 설정해 주어야 합니다.

아래와 같이 수정합니다.
```Javasciprt

// 기존 1.x 코드
app = new PlayRTC({
  url: 'http://myservice.com/api/'      //제거 사항
  localVideoTarget: 'localVideo',
  remoteVideoTarget: 'remoteVideo'
});


// 2.x 코드
app = new PlayRTC({
  projectKey: '나의 프로젝트 키',           //추가 사항
  localVideoTarget: 'localVideo',
  remoteVideoTarget: 'remoteVideo'
});

```
