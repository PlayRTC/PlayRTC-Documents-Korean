# PlayRTC 문서 저장소
PlayRTC의 문서 저장소.
- [MkDocs](http://www.mkdocs.org/) 기반
- [Docker Documentation 저장소](https://github.com/docker/docker/tree/master/docs) 참고

## 개요
다음과 같은 문제를 해결하기 위해 문서 저장소를 사용함
- 버전대별 문서 관리
- 문서의 다국어 지원
- 다양한 문서에서 지정된 URL과 페이지내의 닻(Anchor)으로 링크 지원
- 빌드, 배포의 자동화

## 체계
- `master` 갈래에서 문서작성
- Build/Depoly를 통해서 `gh-pages` 갈래로 발행
- http://playrtc.github.io/PlayRTC-Docs/ 와 http://docs.playrtc.com/ 으로 배포
