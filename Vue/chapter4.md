# Vue Router 설정

## 라우팅이란?

-   단일 페이지 애플리케이션의 경우 페이지를 이동할 때마다 서버에 요청해서 페이지를 새로 갱신❌ 클라이언트에서 미리 가지고 있던 페이지를 라우팅을 이용해서 화면을 갱신
-   클라이언트에서 url 주소에 따라 페이지가 전환되는 것
-   vue 프로젝트 내부에서 미리 url 주소 정의하고 각 주소마다 vue 페이지를 연결
-   사용자가 메뉴를 클릭, 주소를 직접 갱신했을 때 미리 정의된 url 주소에 해당한느 Vue 페이지로 화면 전환

## Vue-Router 설치

```
vue add router
```

🚀 화면 상단에 Home, About 두 개의 router가 동작되는 샘플 파일 자동 생성

```vue
<!-- 📌 App.vue -->
<template>
    <nav>
        <router-link to="/">Home</router-link> |
        <router-link to="/about">About</router-link>
    </nav>
    <router-view />
</template>
```

-   Home 클릭 : localhost:8080,
-   About 클릭 : localhost:8080/About
-   src/router/index.js > routes 배열

```js
{path: "/", name: "Home", component: Home}
```

-   path: 브라우저에서 접속하는 url 주소 정의
-   component: 지정된 path로 들어왔을 때 보여줄 vue 컴포넌트, 앞으로 구현할 vue 파일 연결 및 실행

```js
import HomeView from "../views/HomeView.vue";
```

-   개발할 화면에 해당하는 vue 파일 import
-   routes 안에 특정 path와 맵핑을 해주면 사용자가 접속하는 브라우저 url 주소에 따라 원하는 vue 파일을 보여줄 수 있다.

```js
// route level code-splitting
// 라우트 레벨에서 코드를 분할하는 방법

// this generates a separate chunk (about.[hash].js) for this route
// 이 라우트에 대한 chunk 파일이 분리되어 생성

component: () => import(/* webpackChunkName: "about" */ "../views/About.vue");
// 라우트 레벨에서 코드를 분할한 후 별도의 chunk 파일을 생성 실제 이 라우트를 방문했을 때 리소스를 로드
// chunk 파일은 about이라는 이름으로 생성된다.
// 컴포넌트 import시 /* webpackChunkName: "about" */ 라는 주석으로 chunk 파일 이름을 정의했기 때문이다.
```

## Lazy Load 적용하기(비동기 컴포넌트)

-   Vue CLI를 통해 빌드하면 소스 코드가 하나의 파일로 합쳐지면서 큰 프로젝트에서는 파일 용량이 매우 커진다.
-   사용자가 웹사이트에 처음 접속시 한 번에 큰 파일을 다운로드 받느라 초기 렌더링 시간이 오래 걸리며 렌더링 속도가 너무 늦어져서 좋지 않은 경험을 가지게 된다.
-   페이지가 한번 로드되고 나서 페이지 전환이 매우 빠르기 때문에 굉장한 이점
-   Lazy Load는 리소스를 컴포넌트 단위로 분리하여 컴포넌트 혹은 라우터 단위로 필요한 것들만 그때 다운받을 수 있게 하는 방법
-   Vue CLI3부터 prefetch 기능 추가 미래에 사용될 수 있는 리소스를 캐시에 저장 > 사용자가 접속했을 때 굉장히 빠르게 리소스를 내려줄 수 있다.
-   비동기 컴포넌트로 정의된 모든 리소스를 당장 사용하지 않더라도 캐시에 담는 비용이 발생 > chunk 파일 각각에 대한 request가 일어나고, 파일 다운로드 받아서 캐시에 저장 > prefetch 기능은 렌더링 시간을 줄이기 위해서 사용하는데 잘못 사용하면 오히려 렌더링 시간이 늘어난다.
-   prefetch 기능을 사용하면 아직 사용하지 않고 있는 화면에 대한 리소스를 모두 내려받고 나서야 첫 화면에서 사용되는 리소스를 내려받기 때문에 첫 화면 접속 시 렌더링 속도가 느려질 수 있다.
-   prefetch 기능은 다른 화면에서 사용될 리소스를 미리 내려받아 애플리케이션 화면 전환 시 빠른 성능을 가져온다는 장점을 이용하기 위해 사용! > 정말 필요한 컴포넌트에만 기능 적용!
-   이동하는 컴포넌트에서 사용되는 리소스가 매우 크면 라우터 이동 시 화면전환이 늦게 진행된다
-   prefetch 기본적으로 true로 설정되어 있다.

```js
//📌 prefetch 끄기 vue.config.js
module.exports = {
    chainWebpack: config => {
        config.plugins.delete("prefetch");
    }
};

//📌 import로 가져올 수 있다.
import(/* webpackPrefetch: true */ "./views/About.vue");
```

### router 사용하기

-   src/main.js

```js
import { createApp } from "vue";
import App from "./App.vue";
import router from "./router";

createApp(App).use(router).mount("#app");
```

🚀 createApp(App) 최상위 컴포넌트 App.vue로 app
생성

🚀 use(route) 코드 추가 App.vue에서 router 사용될 수 있도록 추가

🚀 App.vue를 public 폴더에 index.html에 정의되어 있는 id="app"인 html element에 마운트
