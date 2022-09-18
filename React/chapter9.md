# 컴포넌트 스타일링

## 가장 흔한 방식, 일반 CSS

-   컴포넌트를 스타일링하는 가장 기본적인 방식
-   CSS 클래스 중복되지 않게 만들어야 한다!
    1. 이름을 지을 때 특별한 규칙을 사용하기
    2. CSS Selector 활용

### 이름 짓는 규칙

-   컴포넌트 이름-클래스 : 클래스 이름에 컴포넌트 이름 포함하여 다른 컴포넌트에서 중복되는 실수 방지
-   BEM 네이밍 : 이름을 지을 때 일종의 규칙을 준수하여 해당 클래스가 어디에서 어떤 용도로 사용되는지 명확하게 작성하는 방식
    > .card_title-primary

### CSS Selector

-   CSS 클래스가 특정 클래스 내부에 있는 경우에만 스타일 적용

---

## Sass 사용하기

-   자주 사용되는 CSS 전처리기 중 하나로 확장된 CSS 문법을 사용하여 CSS 코드를 더욱 쉽게 작성할 수 있게 해준다.
-   스타일 코드의 재활용성을 높여 줄 뿐 아니라 코드의 가독성을 높여 유지 보수를 쉽게 해준다.
-   .scss, .sass 지원

### 📌 .sass

```sass
$font-stack: Helvetica, sans-serif
$primary-color: #333

body
    font: 100% $font-stack
    color: $primary-color
```

### 📌 .scss

```scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
    font: 100% $font-stack;
    color: $primary-color;
}
```

-   .sass 확장자는 중괄호({})와 세미콜론(;) 사용❌
-   sass 라이브러리(Sass를 CSS로 변환해준다) 설치  
    `$ yarn add sass`

### Sass 작성하기 .scss

```scss
// 변수 사용하기
$red: #fa5252;
$orange: #fd7e14;

// 믹스인 만들기(재사용되는 스타일 블록 함수처럼 사용 가능)
@mixin square($size) {
    $calculated: 32px * $size;
    width: $calculated;
    height: $calculated;
}

.SassComponent {
    display: flex;
    .box {
        // 일반 CSS에서는 .SassComponent .box와 마찬가지
        background: red;
        &.red {
            // .red 클래스가 .box와 함께 사용되었을 때
            background: $red;
            @include square(1);
        }
        &:hover {
            // .box에 마우스를 올렸을 때
            background: black;
        }
    }
}
```

### utils 함수 분리하기

-   여러 파일에서 사용될 수 있는 Sass 변수 및 믹스인은 다른 파일로 따로 분리하여 작성한 뒤 필요한 곳에서 쉽게 불러와 사용
-   src/styles/utils.scss 파일 생성 > 작성한 변수와 믹스인 잘라내서 이동

### sass-loader 설정 커스터마이징하기

-   프로젝트에 디렉터리를 많이 만들어서 구조가 깊어졌다면 상위 폴더로 한참 거슬러 올라가야 한다는 단점이 있다. (@import "../../../styles/utils")
-   yarn eject 명령어로 세부 설정 밖으로 꺼낼 수 있다.<br>☝️ git에 커밋되지 않은 변화가 있다면 진행❌

```bash
$ yarn eject
$ react-scripts eject
```

-   config 디렉터리 생성 > webpack.config.js
-   "sassRegex" 찾기 > use: 에 있는 "sass-loader" 삭제 후 뒷부분에 concat으로 커스터마이징된 sass-loader 설정 추가 > 저장 후 서버 재시작
-   상대 경로 입력 없이 styles 디렉터리 기준 절대 경로로 사용 가능  
    `@import "utils.scss"`
    ```js
    {
        test: sassRegex,
        exclude: sassModuleRegex,
        use: getStyleLoaders({
            importLoaders: 3,
            sourceMap: isEnvProduction ? shouldUseSourceMap : isEnvDevelopment
        }).concat({
            loader: require.resolve("sass-loader"),
            options: {
                sassOptions: {
                    includePaths: [paths.appSrc + "/styles"]
                }
            }
        }),
        slideEffects: true
    }
    ```
-   새 파일 생성할 때마다 매번 포함시켜야 하는데 그럴 때는 sass-loader의 additionalData 옵션을 설정 > Sass 파일을 불러올 때마다 코드의 맨 윗부분에 특정 코드 포함시켜 준다.
    ```js
    {
        test: sassRegex,
        exclude: sassModuleRegex,
        use: getStyleLoaders({
            importLoaders: 3,
            sourceMap: isEnvProduction ? shouldUseSourceMap : isEnvDevelopment
        }).concat({
            loader: require.resolve("sass-loader"),
            options: {
                sassOptions: {
                    includePaths: [paths.appSrc + "/styles"]
                },
                additionalData: "@import 'utils';"
            }
        }),
        slideEffects: true
    }
    ```
    🚀 재시작하면 scss 파일에서 utils.scss를 자동으로 불러오므로, Sass 맨 윗줄에 있는 @improt 구문을 지워도 정상적으로 작동

### node_modules에서 라이브러리 불러오기

-   상대 경로 사용하여 node_modules까지 들어가서 불러오기
    > @import "../../../node_module/library/styles";
-   물결 문자 사용하기
    > @import "~library/styles";
-   반응형 디자인을 쉽게 만들어주는 라이브러리 include-media  
     <https://include-media.com>
-   매우 편리한 색상 팔레트 open-color <https://www.npmjs.com/package/open-color>

```bash
$ yarn add open-color include-media
```

-   Sass 라이브러리를 불러올 때는 node_modules 내부 라이브러리 경로 안에 들어 있는 scss 파일을 불러와야 한다.

```scss
// 📌 utils.scss
@import "~include-media/dist/include-media";
@import "~open-color/open-color";
(...)

// 📌 style.scss
.SassComponent {
    background: $oc-gray-2;
    @include media("<768px") {
        // 화면 가로 크기가 768px 미만이 되면 배경색 어둡게 변경
        background: $oc-gray-9;
    }
}
```

---

## CSS Module

-   CSS 클래스가 다른 CSS 클래스의 이름과 절대 충돌하지 않도록 파일마다 고유한 이름을 자동으로 생성해 주는 옵션
-   `파일 이름_클래스 이름_해시값` 형태
-   v2 버전 이상부터는 따로 설정❌ .module.css 확장자로 파일 저장

```css
.wrapper {
    ...;
}
/* 글로벌 CSS 작성 */
:global .something {
    color: black;
}
```

-   클래스 이름을 지을 때 그 고유성에 대해 고민❌
-   스타일을 직접 불러온 컴포넌트 내부에서만 작동

### classnames

-   CSS 클래스를 조건부로 설정할 때 매우 유용한 라이브러리
-   CSS Module 사용할 때 여러 클래스에 적용할 때 편리

```
$ yarn add classnames
```

-   간략 사용법

    ```js
    import classNames from "classnames";

    classNames("one", "two"); // = "one two"
    classNames("one", { two: true }); // = "one two"
    classNames("one", { two: false }); // = "one"
    classNames("one", ["two", "three"]); // = "one two three"

    const myClass = "hello";
    classNames("one", myClass, { myCondition: true }); // = "one hello myCondition"
    ```

-   props 값에 따라 다른 스타일을 주기 쉬워진다.
    ```jsx
    const MyComponent = ({ highlighted, theme }) => (
        <div className={classNames("MyComponent", { highlighted }, theme)}>Hello</div>
    );
    ```

### CSS Module이 아닌 파일에서 CSS Module 사용하기

```scss
:local .wrapper {
    /* 스타일 */
}

:local {
    .wrapper {
        /* 스타일 */
    }
}
```

---

## styled-components

-   스타일을 자바스크립트 파일에 내장시키는 방식으로 스타일을 작성함과 동시에 해당 스타일이 적용된 컴포넌트를 만들 수 있게 해준다.
-   CSS-in-JS
-   관련된 라이브러리가 많다.<br>[▶ 라이브러리 종류 보러가기](https://github.com/MicheleBertoli/css-in-js)

```
$ yarn add styled-components
```

-   .css, .scss 확장자를 가진 스타일 파일 따로 만들지 않아도 된다
-   props 값으로 전달해주는 값을 쉽게 스타일에 적용 가능

### props에 따른 조건부 스타일링

-   조건부 스타일링을 간단하게 props로도 처리 가능

```jsx
import styled, { css } from "styled-components";
// 단순 변수의 형태가 아니라 여러 줄의 스타일 구문을 조건부로 설정해야 하는 경우에 css 불러와야 한다.

const Button = styled.button`
    background: white;

    &:hover {
        background: rgba(255, 255, 255, 0.9);
    }

    // inverted 값이 true일 때 특정 스타일 부여
    // css 없이 바로 `...` 작성 가능하지만 Tagged 템플릿 리터럴이 아니어서 함수를 받아 사용하지 못해 해당 부분에서 props 사용❌
    ${props =>
        props.inverted &&
        css`
            background: none;
            color: white;
            &:hover {
                background: white;
                color: black;
            }
        `}
`;
```

### 반응형 디자인

-   CSS와 동일하게 media 쿼리 사용
-   styled-components 매뉴얼에서 제공하는 유틸 함수 사용

[▶ 참고하기](https://www.styled-components.com/docs/advanced#media-templates)

```jsx
const sizes = {
    desktop: 1024,
    tablet: 768
};

// 위에 sizes 객체에 따라 자동으로 media 쿼리 함수 만들어 준다
const media = Object.keys(sizes).reduce((acc, label) => {
    acc[label] = (...args) =>
        css`
            @media (max-width: ${sizes[label] / 16}em) {
                ${css(...args)};
            }
        `;

    return acc;
}, {});

const Box = styled.div`
    ${media.desktop`width: 768px;`}
    ${media.tablet`width: 100%`};
`;
```
