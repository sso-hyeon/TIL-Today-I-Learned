# Vue CLI로 Vue 프로젝트 생성하기

## Vue CLI 설치하기

```bash
npm install -g @vue/cli
```

-   Vue 프로젝트를 빠르게 구성, 빌드, 디플로이 할 수 있게 도와주는 도구
-   Command Line Interface의 약자로 터미널에 명령어를 입력하여 컴퓨터와 상호 작용하는 방식을 의미
-   -g (global)
    -   설치하는 패키지가 현재 디렉토리뿐만 아니라 앞으로 생성하게 되는 모든 프로젝트에서 사용할 수 있는 global 패키지로 등록

```bash
npm install -g 패키지명
```

-   —save
    -   현재 작업중인 디렉토리 내에 있는 ./node_modules에 패키지 설치 > package.json 파일 dependencies 객체에 지금 설치한 패키지 정보 추가
    -   설치되는 모든 패키지는 node_modules 디렉토리에 설치
    -   실무에서 프로젝트 개발시, 설치된 패키지가 많아진다. 공동 작업을 하고 있다면 매번 패키지 파일 전체를 공유하는 일은 자원 낭비가 될 수 있기 때문에 —save 옵션 사용
    -   package.json 파일에 설치한 패키지 정보가 추가되므로 파일 공유시 npm install을 통해 없는 패키지 전체 한번에 설치 가능

```bash
npm install 패키지명 --save
```

## Default 옵션으로 프로젝트 설치하기

```
vue create vue-basic > vue 3 버전 으로 설치

// 📌 서버 시작하기 - 기본 포트인 8080으로 실행
yarn serve
npm run serve
// 다른 포트를 사용하려면 npm run serve -- --port 포트번호
```

## Vue 프로젝트 파일 구조

-   node_modules: npm으로 설치된 패키지 파일들이 모여 있는 디렉토리
-   public: 웹팩을 통해 관리되지 않는 정적 리소스가 모여 있는 디렉토리
-   src/assets: 이미지, css, 폰트 등 관리하는 디렉토리
-   src/components: Vue 컴포넌트 파일이 모여 있는 디렉토리
-   App.vue: 최상위(Root) 컴포넌트
-   main.js: 가장 먼저 실행되는 자바스크립트 파일, Vue 인스턴스를 생성
-   .gitignore: 깃허브에 업로드 할 때 제외할 파일 설정
-   babel.config.js: 바벨 설정 파일
-   package-lock.json: 설치된 package의 dependency 정보 관리하는 파일
-   package.json: 프로젝트에 필요한 package를 정의하고 관리하는 파일
-   README.md: 프로젝트 정보 기록

## Package.json

-   name: 프로젝트 이름
-   version: 프로젝트의 버전 정보
-   private: true로 설정시 해당 프로젝트 npm으로 올리기❌
    개발자가 실수로 해당 프로젝트를 npm에 올려도 true면 배포를 막을 수 있다.
-   scripts: 프로젝트 실행과 관련된 명령어 등록
    프로젝트 실행, 빌드 등 프로젝트 개발, 운영 시 사용되는 다양한 명령어를 등록 및 사용 가능
    직접 정의한 명령어는 npm run 명령어로 사용, npm에서 제공되는 명령어는 npm 명령어
-   dependencies: 사용 중인 패키지 정보 입력
-   devDependencies: 개발 시에만 필요한 패키지 정보 등록
-   eslintConfig: ESLint는 일관성 있게 코드를 작성하고 버그를 식별하고 회피할 목적으로
    ECMAScript/Javascript 코드에서 발견된 패턴을 개발자에게 알려주는 플러그인
    구문 분석을 위해 babel-eslint를 파서로 사용
-   browserlist: 전 세계 사용 통계 속에서 상위 1% 이상 사용된 브라우저, 각 브라우저의 최신 버전 2개 지원

## Vue 프로젝트 매니저로 프로젝트 설치

```bash
vue ui
```

-   생성부터 실행 관리까지 GUI를 이용해서 손쉽게 처리 가능
-   화면을 통해 직관적으로 사용할 수 있다
