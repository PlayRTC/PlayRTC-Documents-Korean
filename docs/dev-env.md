# 환경 준비하기
PlayRTC로 1:1 영상/음성 통화 웹앱을 만들기 위해서는, 간단한 텍스트 에디터와 간단한 웹서버가 필요합니다. 이미 이러한 환경에 익숙 하다면, 다음장으로 곧장 넘어가도 좋습니다. 아니라면 각각 다음과 같이 준비해 보도록 합니다.

## 텍스트 에디터
평상시 사용하는 텍스트 에디터나 [IDE](http://ko.wikipedia.org/wiki/%ED%86%B5%ED%95%A9_%EA%B0%9C%EB%B0%9C_%ED%99%98%EA%B2%BD) 환경을 준비합니다.

간편 하면서도 편리한 모던 에디터를 찾고 있다면, 다음을 추천합니다.
 - [Sublime Text](http://www.sublimetext.com/)
	- 윈도우즈, OSX, Linux등 모든 환경에서 잘 작동하는 모던 [마크업](http://ko.wikipedia.org/wiki/%EB%A7%88%ED%81%AC%EC%97%85_%EC%96%B8%EC%96%B4)/텍스트 에디터. 다양한 플러그인을 통해 확장이 가능하며, 무료로 모든 기능을 사용가능.
	- 참고 : [Sublime Text 수업 - 생활코딩](http://opentutorials.org/course/671/3595)
- [Atom](https://atom.io/)
	- 소셜 코딩으로 유명한 [Github](https://github.com/)에서 만든 [V8 자바스크립트 엔진](http://ko.wikipedia.org/wiki/V8_(%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EC%97%94%EC%A7%84))기반 모던 마크업/텍스트 에디터. 다양한 플러그인을 통해 확장이 가능하며, 오픈소스이자 무료. `Sublime Text`에 비해 첫 시작 속도가 약간 느리고, 아직은 4M이상 파일을 편집할 수 없는점이 단점.

## 웹 서버
튜토리얼을 진행하기 위해서는 간단한 웹 서버가 필요합니다. 여기서 웹서버는 PlayRTC 웹앱을 실행하는데 필요하며 실제로는 `ruby`, `php`, `node.js`와 같은 서버쪽 코드는 전혀 없이 단순히 `html`파일을 전달하는 정적 웹서버 역할 입니다.

만약 웹서버에 대해 자세히 모른 다면 다음과 같이 `node`기반의 정적 웹 서버 `Harp`를 준비합니다.

### Harp - node기반 정적 웹 서버

이제 `node.js`를 설치하고 `Harp`를 설치하여 웹서버를 준비해 봅시다.

[Harp](http://harpjs.com/)는 [nodejs](https://nodejs.org/)기반 정적 웹 서버 입니다. [node](https://nodejs.org/)는 브라우저에서 작동하는 [자바스크립트](http://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8)를 서버, 시스템 프로그래밍등의 범용으로 사용하기 위해 만든 체계 입니다.

- `node.js`설치
	- Windows :
		- [node.js 홈페이지](https://nodejs.org/)에서 다운로드 받아 설치하면 됩니다.
		- 패키지 관리자 [Chocolatey](https://chocolatey.org/)를 통해 설치하려면 [명령 프롬프트(cmd)](http://ko.wikipedia.org/wiki/%EB%AA%85%EB%A0%B9_%ED%94%84%EB%A1%AC%ED%94%84%ED%8A%B8) 혹은 윈도우 파워쉘에서 다음과 같이 실행합니다.
			c:\> choco install nodejs
	- OSX :
		- [node.js 홈페이지](https://nodejs.org/)에서 다운로드 받아 설치하면 됩니다.
		- 패키지 관리자 [Home Brew](http://brew.sh/index_ko.html)를 통해 설치하려면 터미널에서 다음과 같이 실행합니다.
			`$ brew install node`
	- unix-like :
		- 아래의 페이지를 참고하여 운영체제, 배포본에 적합한 방법으로 설치 합니다.
		- [Installing Node.js via package manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)
- `Harp` 설치
	- Windows :
			`c:\> npm install -g harp`
	- OSX :
			`$ npm install -g harp`
	- unix-like :
			`$ sudo npm install -g harp`

이제 명령 프롬프트/파워쉘/터미널에서 다음과 같이 실행하면 간단하게 웹서버가 작동합니다.
- Windows :
	 - 사용자의 홈 폴더로 이동합니다.
		`c:\> cd %UserProfile%`
	- 웹 서버의 루트가 될 www 폴더를 만듭니다.
		`c:\> mkdir www`
	- www란 폴더로 이동합니다.
		`c:\> cd www`
	- harp 웹 서버를 실행시킵니다.
		`c:\> harp server -p 8000`
- OSX, unix-like :
	- 사용자의 홈 폴더로 이동합니다.
		`$ cd ~`
	- 웹 서버의 루트가 될 www폴더를 만든다음 이동합니다.
		`$ mkdir www && cd www`
	- harp 웹 서버를 실행시킵니다.
		`$ harp server -p 8000`

이제 브라우저를 열고 주소 입력난에 `http://localhost:8000/`을 입력하면 `사용자의 홈폴더/www/`폴더에 있는 파일을 `http`의 웹 통신을 통해 제공(serve)하게 됩니다. 위와 같은 harp 사용법을 익혀두면 앞으로 웹앱을 만들때 여러모로 요긴하게 사용하게 됩니다.

`javascript`, `node.js`, `Harp`에 대해 보다 더 자세한 내용을 알고 싶으면, 아래의 링크를 참고 하면 좋습니다.

- [자바스크립트 수업 - 생활코딩](http://opentutorials.org/course/743)
- [node.js 수업 - 생활코딩](http://opentutorials.org/course/86)
- [Harp 전처리 기능을 가진 정적 웹서버 - Outsider’s Dev Story](http://blog.outsider.ne.kr/999)

### 기타 웹서버 준비

만약 튜토리얼을 진행하는 컴퓨터에 `python`, `php`이 깔려있다면 별도의 준비가 필요 없이 원하는 폴더에서 다음과 같이 입력하기만 하면 웹서버가 가동됩니다.

- php
	`$ php -S localhost:8080`
- python
	`$ python -m SimpleHTTPServer 8000`

[Apache httpd](http://httpd.apache.org/), [nginx](http://nginx.org/), [IIS](http://www.iis.net/)와 같은 웹서버를 다룰줄 안다면 해당 웹서버를 사용해도 좋습니다.

## 예제 앱 다운로드
이제 예제앱을 다운로드 받고 준비 해 봅시다.

예제앱은 아래를 통해 압축파일 형태로 다운로드 받을 수 있습니다.
- http://www.playrtc.com/download/
- https://developers.sktelecom.com/develop/doc/sdk/

혹은 PlayRTC GitHub 저장소에서 Git을 통해 클론받을 수 있습니다.
	`$ git clone https://github.com/playrtc/example-simple-chat-webapp`

이로써 모든 튜토리얼을 개발하기 위한 모든 환경이 갖추어졌습니다. 다음장을 통해 본격적으로 영상/음성 웹앱을 만들어 보도록 하겠습니다.

Git에 대한 설치와 자세한 내용을 알고 싶다면 아래의 링크를 참고하세요.
- [Git 간편 안내서 - Roger Dudler, Juntai Park, Ardie Hwang](http://rogerdudler.github.io/git-guide/index.ko.html)
- [Git - 생활코딩](http://opentutorials.org/course/1492)
- [Git 브랜치 배우기 - Peter Cottle, 우리깃](http://learnbranch.urigit.com/)
- [Pro Git - schacon, dogfeet](http://dogfeet.github.io/articles/2012/progit.html)
