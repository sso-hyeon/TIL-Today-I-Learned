# Vue.js는 무엇인가?

-   사용자 인터페이스 개발을 위한 Progressive Framework (웹과 네이티브 앱의 이점을 모두 수용하고 표준 패턴을 사용해 개발한 것)
    -   웹 : 경우 별도의 설치 없이 브라우저만 있으면 접속 가능하여 접근성이 매우 뛰어나다.
    -   네이티브 앱 : 일반적인 웹보다 빠르고 더 뛰어난 사용자 경험 제공
-   SPA(단일 페이지 애클리케이션) 개발을 위한 프론트엔드 프레임워크 (Angular, React, Vue)
    -   일반적인 웹사이트의 경우는 페이지 이동할 때마다 페이지 전체 다시 로딩 (이전 파일에서 동일하게 사용된 파일도 서버에서 다시 가져와 로딩)
    -   SPA는 처음 접속한 후 페이지 중 변경이 필요한 부분만 바뀐다. > 페이지 전환 속도가 굉장히 빠르고, 웹 자원 굉장히 효율적으로 관리
    -   당장 필요하지 않은 자원도 로딩 > 로딩 속도가 느리고 많은 웹 자원을 가져와야 하는 단점이 있다.

## 장점

-   직관적이고 배우기 쉽다.
-   재사용을 통한 애플리케이션을 개발 기간 단축 및 양질의 코드를 생산할 수 있다.
-   Angular의 장점(데이터 바인딩)과 React 장점(가상 돔)을 모두 가지고 있다.

## 특징

-   MVVM(Model-View-ViewModel) 패턴 사용
-   컴포넌트를 사용한 높은 재사용성