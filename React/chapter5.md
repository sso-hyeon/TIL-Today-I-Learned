# ref: DOM에 이름 달기

HTML에서 id를 사용하여 DOM에 이름을 부여하는 것처럼 리액트 프로젝트 내부에서 DOM에 이름을 부여하는 방법이 ref(reference) 개념이다.

☝ 리액트 프로젝트 안에서도 id를 사용해도 되지만 특수한 경우가 아니라면 권장❌

-   id는 유일해야한다! 하지만 컴포넌트를 여러번 사용한다고 가정하면 중복 id를 가진 DOM이 여러 개 생기게 되므로 잘못된 사용
-   ref는 전역적으로 작동하지 않고 컴포넌트 내부에서만 작동

## ref는 어떤 상황에서 사용해야 할까?

-   DOM을 꼭 직접적으로 건드려야 할 때

    ```JSX
    class ValidationSample extends Component {
        state = {
            password:"",
            clicked:false,
            validated: false
        }

        handleChange = (e) => {
            this.setState({
                password: e.target.value
            });
        }

        handleButtonClick = () => {
            this.setState({
                clicked: true,
                validated: this.state.password === "0000"
            })
        }

        render() {
            return (
                <div>
                    <input
                        type="password"
                        value={this.state.password}
                        onChange={this.handleChange}
                        className={this.state.clicked ? (this.state.validated ? "success" : "failure") : ""} />
                    <button
                        onClick={this.handleButtonClick}>
                        검증하기
                    </button>
                </div>
            )
        }

    }
    ```

    🚀 검증 결과에 따라 success, failure class명으로 input 색상 변경

-   state를 사용하여 필요한 기능을 구현할 수 있지만, 가끔 state만으로 해결할 수 없는 기능이 있다.
    -   특정 input에 포커스 주기
    -   스크롤 박스 조작하기
    -   canvas 요소에 그림 그리기 등

---

## ref 사용

### 콜백 함수를 통한 ref 설정

-   ref를 달고자 하는 요소에 ref라는 콜백 함수를 props로 전달
-   함수 내부에서 파라미터로 받은 ref를 컴포넌트의 멤버 변수로 설정

```JSX
<input ref={(ref) => {this.input=ref}}>
// this.input은 input 요소의 DOM을 가리킨다.
// ref의 이름은 원하는대로 자유롭게 지정 가능 this.name = ref
```

### createRef를 통한 ref 설정

-   리액트에 내장되어 있는 createRef 함수 사용
-   v16.3부터 도입
-   ref를 달고자 하는 요소에 ref props로 넣어주면 ref 설정 완료
-   나중에 설정해 준 DOM에 접근하려면 this.input.current를 조회

```JSX
class RefSample extends Component {
    input = React.createRef();

    handleFocus = () => {
        this.input.current.focus();
    }

    render() {
        return (
            <div>
                <input ref={this.input} />
            </div>
        )
    }
}
```

---

## 컴포넌트에 ref 달기

-   컴포넌트에도 ref를 달 수 있다.
-   컴포넌트 내부에 있는 DOM을 컴포넌트 외부에서 사용할 때 쓴다.
-   컴포넌트 내부의 메서드 및 멤버 변수에도 접근할 수 있다. (내부의 ref에도 접근 가능)

```JSX
<Component ref={(ref) => {this.Component=ref}}
```

### 사용 예제

```JSX
// ScrollBox
class ScrollBox extends Component {
    scrollToBottom = () => {
        const { scrollHeight, clientHeight } = this.box;
        this.box.scrollTop = scrollHeight = clientHeight;
    }
    render() {
        const style = {
            border: "1px solid black",
            height: "300px",
            width: "300px",
            overflow: "auto",
            position: "relative"
        };

        const innerStyle = {
            width: "100%",
            height: "650px",
            background: "lineat-gradient(white, black)"
        };

        return (
            <div
                style={style}
                ref={(ref) => {this.box=ref}}>
                <div style={innerStyle}></div>
            </div>
        );
    }
}
```

```JSX
// App
class App extends Component {
    render () {
        <div>
            <ScrollBox ref={(ref) => this.scrollBox=ref} />
            <button onClick={() => this.scrollBox.scrollToBottom()}>
            맨 밑으로
            </button>
        </div>
    }
}
```

---

## 정리

-   컴포넌트 내부에서 DOM에 직접 접근해야 할 때 ref 사용
-   ref를 사용하지 않고도 원하는 기능을 구현할 수 있는지 고려한 후 사용
-   서로 다른 컴포넌트끼리 데이터를 교류할 때 사용하는 것은 잘못된 사용
-   컴포넌트끼리 데이터를 교류할 때는 언제나 부모↔자식 흐름으로 교류
-   함수 컴포넌트에서는 useRef 라는 Hook 함수 사용 (chapter8)
