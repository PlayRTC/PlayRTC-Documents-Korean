Deprecated Documents.
이 저장소의 문서는 더 이상 사용되지 않습니다.

Plase, Visit <http://www.playrtc.com/> and it's documents.
<http://www.playrtc.com/>의 문서를 참고해 주세요.

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

## 작성
- 삽입되는 모든 다이어그램은 구글 드라이브 PlayRTC 프로젝트의 [12. 문서용 다이어그램/이미지](PlayRTC Google Drive for Diagram)에 관리하여 필요에 따라 수정 및 재사용
- ***주의: 구글 드라이브는 PlayRTC 프로젝트는 권한이 있는 사용자만 접근 가능합니다.***

## 체계
<!-- Read the Docs가 최신 mkdocs를 지원하지 않아 사용치 아니함. -->
<!-- - Read the Docs
  - `master` 갈래에서 문서작성
  - [PlayRTC Github][PlayRTC Github]의 문서 저장소로 `push`
  - 연동된 [Read the Docs][Read the Docs]를 통해 빌드
  - http://playrtc.readthedocs.org/ko/latest/ 로 배포 -->
- Github pages
  - `master` 갈래에서 문서작성
  - `mkdocs gh-pages` 실행
  - http://playrtc.github.io/PlayRTC-Documents-Korean/ 로 배포

[MkDocs]: http://www.mkdocs.org/
[PlayRTC Github]: https://github.com/playrtc
[Read the Docs]: https://readthedocs.org/
[PlayRTC Google Drive for Diagram]:https://drive.google.com/drive/u/1/folders/0B4KbhsuI_9DIdWlMc0hhckpvcGc/0B4qm2JzFaK41fkxXbFZXcmM1dm1wNlpzZzkxMWd6VlNaRVYxVnZ6bDNyQlhHMU9JVFRaSDg
