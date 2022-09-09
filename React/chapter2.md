# JSX
## 코드 이해하기
* import : 특정 파일을 불러오는 것을 의미<br>
☝ 원래 브라우저에 없던 기능! Node.js에서 지원하는 기능이다<br>
  (Node.js 안에서는 import가 아닌 require)
* 브라우저에서도 사용하기 위해 번들러(bundler) 사용<br>
  bundle : 묶는다 > 파일을 묶듯이 연결한다.
  > 대표적인 번들러 : 웹팩, parcel, browserify (각 도구마다 특성 다름)
* 번들러 도구를 사용하면 import(or require)로 불러온 모듈을 모두 함쳐서 하나의 파일 생성. 또 최적화 과정에서 여러 개의 파일로 분리될 수 있다.
* 2017년 부터 브라우저에서도 import 사용할 수 있게 되었지만, 단순히 다른 경로 자바스크립트를 불러오는 용도로만 사용 > 프로젝트 번들링과는 다르다!
* 파일들을 불러오는 것은 웹팩의 로더(loader)라는 기능이 담당, 여러 종류가 있다.
  - css-loader : css 파일 불러옴
  - file-loader : 웹 폰트 미디어 파일 등 불러옴
  - babel-loader : 자바스크립트 파일 불러오면서 최신 자바스크립트 문법으로 작성된 코드를 ES5 문법으로 변환(바벨)
    > 이전 문법으로 변환하는 이유 : 구버전 웹 브라우저와 호환하기 위함. JSX 문법도 정식 자바스크립트 문법이 아니어서 ES5 형태의 코드로 변환해야 함.
* 웹팩의 로더는 원래 직접 설치, 설정해야 함.<br>
But, 리액트 프로젝트 생성시 **create-react-app** 이 번거로운 작업을 모두 대신 해주어서 별도의 설정이 필요없다.

* 함수형 컴포넌트, 컴포넌트 렌더링하면 함수에서 반환하고 있는 내용을 나타낸다. HTML 처럼 보이지만 HTML도 문자열 템플릿도 아닌 JSX 라고 부른다.

```jsx
 function App() {
    return(
        <div className="App">
            <header></header>
        </div>
    )
} 
```

---
## JSX란?
* 자바스크립트의 확장 문법, XML과 매우 비슷하게 생겼다.
* 브라우저에서 실행되기 전 번들링되는 과정에서 바벨 사용하여 일반 자바스크립트 형태로 변환.

```javascript
function App() {
    return (
        <div>
            Hello <b>react</b>
        </div>
    )
}

// 📌 자바스크립트로 변환
function App() {
    return React.createElement("div", null, "Hello ", React.createElement("b", null, "react"))
}
```

*  JSX는 리액트로 프로젝트 개발시에만 사용되므로 공식적인 자바스크립트 문법❌
* 바벨에서 여러 문법 지원할 수 있도록 preset, plugin 설정 > 개발자들이 임의로 만든 문법, 차기 자바스크립트 문법 사용할 수 있다.

--- 

## JSX의 장점
* HTML 코드를 작성하는 것과 비슷해 보기 쉽고 익숙하다
* 더욱 높은 활용도

> **React.StrictMode** : 리액트 프로젝트에서 앞으로 사라질 레거시 기능 사용시 경고, 앞으로 미래의 리액트 버전에 도입될 기능들이 정상적으로 호환될 수 있도록 유도하는 디버깅용 컴포넌트

> **reportWebVitals** : 웹 성능 측정 도구

---

## JSX 문법
### 반드시 부모 요소 하나로 감싸기!
  ☝ Virtual DOM에서 컴포넌트 변화를 감지해 낼 때 효율적으로 비교할 수 있도록 컴포넌트 내부는 하나의 DOM 트리 구조로 이루어져야 한다!
  > v16 이상부터 < div > 대신 < Fragment > 사용 가능(<>)
```jsx
function App() {
    return (
        <div>
            <h1>리액트 안녕!</h1>
            <h2>잘 작동하니?</h2>
        </div>
    )
}
```
### 자바스크립트 표현
* 자바스크립트 표현식 작성하려면 JSX 내부에서 { }로 감싸면 된다!

### If문 대신 조건부 연산자
* JSX 내부 if문 사용❌ ▶ JSX 밖에서 사용하여 사전 값 설정하거나, { } 안에 조건부 연산자(삼항 연산자) 사용

### AND 연산자(&&)를 사용한 조건부 렌더링
```jsx
{name === "리액트" ? <h1>리액트입니다.</h1> : null}
// 📌 AND 연산자 사용
{name === "리액트" && <h1>리액트입니다.</h1>} 
```
☝ falsy한 값인 0은 예외적으로 화면에 나타남!
```jsx
const number = 0;
return number && <div>내용</div>
```

### undefined를 렌더링하지 않기
```jsx
// 📌 오류 발생
function App() {
    const name = undefined;
    return name;
}
export default App;
```
* 어떤 값이 undefined일 수도 있다면, OR( || ) 연산자 사용
* JSX 내부에서 undefined를 렌더링하는 것은⭕

  ``return <div>{name}</div>``

### 인라인 스타일링
* 카멜 표기법(camelCase)로 작성 

``background-color ▶ backgroundColor``

### class 대신 className
### 꼭 닫아야 하는 태그
### 주석 ▶ {/* ~ */}

---

## ESLint와 Prettier 적용하기
### [ ESLint ]
* 문법 검사 도구
* 코드 작성시 실수하면 에러, 경고 메세지 VS Code 에디터에서 바로 확인 가능
### [ Prettier ]
* 코드 스타일 자동 정리 도구
* 코드의 가독성을 위해 들여쓰기 사용
* Beautify 설치시 충돌 발생할 수도 있다
* 자동 코드 정리, 세미콜론 자동 추가, 작은따옴표는 큰따옴표로 변경 등 스타일 커스터마이징 가능
* .prettierrc 파일 생성
  ```json
  {
    "singleQuote": true,
    "semi": true,
    "useTabs": false,
    "tabWidth": 2
  }
  ```
    - 들여쓰기 할 때 탭 대신 공백 2칸
    - 큰 따옴표 대신 작은 따옴표
    - 세미콜론 항상 붙임
    
    [▶ Prettier Options 보러가기](https://prettier.io/docs/en/options.html)
* 저장시 코드 자동 정리
  - 파일(Code) > 기본 설정 > 설정 > format on save ✔

---

## 정리
* JSX는 HTML과 비슷하지만 완전히 동일하지 않다!
* XML 형식이지만 실제로는 자바스크립트 객체이며, 용도도 다르고 문법도 조금씩 차이가 있다.
