# PlayRTC 문서 저장소
PlayRTC의 문서 저장소.
- [MkDocs][MkDocs] 기반
- [Docker Documentation 저장소](https://github.com/docker/docker/tree/master/docs) 참고
- [Read the Docs Theme](https://github.com/mkdocs/mkdocs/tree/master/mkdocs/themes/readthedocs) 테마 응용

## 개요
다음과 같은 문제를 해결하기 위해 문서 저장소를 사용함
- 버전대별 문서 관리
- 문서의 다국어 지원
- 다양한 문서에서 지정된 URL과 페이지내의 닻(Anchor)으로 링크 지원
- 빌드, 배포의 자동화

## 체계
- `master` 갈래에서 문서작성
- [Read the Docs][Read the Docs]를 통해 빌드
- http://playrtc.readthedocs.org/ko/latest/ 와 http://docs.playrtc.com/ 으로 배포

[Read the Docs]: https://readthedocs.org/
[MkDocs]: http://www.mkdocs.org/
