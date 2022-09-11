# 이벤트 핸들링

-   사용자가 웹 브라우저에서 DOM 요소들과 상호 작용하는 것을 이벤트라고 한다.

## 리액트의 이벤트 시스템

### 이벤트를 사용할 떄 주의 사항

-   이벤트 이름은 카멜 표기법으로 작성
    -   onClick, onKeyUp ...
-   이벤트에 실행할 자바스크립트 코드 전달이 아닌 함수 형태의 값 전달
    -   HTML에서는 큰 따옴표 안에 실행할 코드 작성했지만 리액트에서는 함수 형태의 객체 전달
    -   외부에 미리 만들어서 전달해도 된다.
-   DOM 요소에만 이벤트 설정
    -   Component에 onClick 값 설정하면 클릭할 때마다 함수 실행하는 것이 아니라 그냥 이름이 onClick인 props를 전달하는 것!

| **이벤트 종류** |            |             |
| :-------------: | :--------: | :---------: |
|    Clipboard    |   Touch    | Composition |
|       UI        |  Keyboard  |    Wheel    |
|      Focus      |   Media    |    Form     |
|      Image      |   Mouse    |  Animation  |
|    Selection    | Transition |             |

[▶ 리액트 이벤트 메뉴얼](https://facebook.github.io/react/docs/events.html)

---

## 이벤트 핸들링 예제

-   state에 input 값 담기
-   임의 메서드 만들기 : onChange, onClick에 전달한 함수를 따로 빼내서 컴포넌트 임의 메서드 만들기

```js
class EventPractice extends Component {
    state = {
        message: ""
    };

    constructor(props) {
        super(props);
        this.handleChange = this.handleChange.bind(this);
        this.handleClick = this.handleClick.bind(this);
    }

    handleChange(e) {
        this.setState({
            message: e.target.value
        });
    }

    handleClick() {
        alert(this.state.message);
        this.setState({
            message: ""
        });
    }

    render() {
        return (
            <input
                type="text"
                name="message"
                value={this.state.message}
                onChange={this.handleChange}
                {/* onChange={e => {this.setState({ message: e.target.value }); }} */}
            />
            <button onClick={this.handleClick}>확인</button>
        );
    }
}
```

-   Property Initializer Syntax를 사용한 메서드 작성
-   새 메서드를 만들 때마다 constructor도 수정해야 하는 점을 더 간단하게 할 수 있다. > 바벨의 transform-class-properties 문법 사용

```js
class EventPractice extends Component {
    state = {
        message: ""
    };

    handleChange = (e) => {
        this.setState({
            message: e.target.value
        });
    }

    handleClick = () => {
        alert(this.state.message);
        this.setState({
            message: ""
        });
    }

    render() {
        return (
            <input
                type="text"
                name="message"
                value={this.state.message}
                onChange={this.handleChange}
            />
            <button onClick={this.handleClick}>확인</button>
        );
    }
}
```

-   input 여러개 다루기
-   onKeyPress 이벤트 추가

```jsx
class EventPractice extends Component {
    state = {
        username: "",
        message: ""
    };

    handleChange = e => {
        this.setState({
            [e.target.name]: e.target.value
        });
    };

    handleClick = () => {
        alert(this.state.username + ":" + this.state.message);
        this.setState({
            username: "",
            message: ""
        });
    };

    handleKeyPress = e => {
        if (e.key === "Enter") {
            this.handleClick();
        }
    };

    render() {
        return (
            <div>
                <input
                    type="text"
                    name="username"
                    placeholder="사용자명"
                    value={this.state.username}
                    onChange={this.handleChange}
                />
                <input
                    type="text"
                    name="message"
                    value={this.state.message}
                    onChange={this.handleChange}
                    onKeyPress={this.handleKeyPress}
                />
            </div>
        );
    }
}
```

---

## 함수 컴포넌트로 구현해 보기

```jsx
const EventPractice = () => {
    const [username, setUsername] = useState("");
    const [message, setMessate] = useState("");
    const onChangeUsername = e => setUsername(e.target.value);
    const onChangeMessage = e => setMessage(e.target.value);
    const onClick = () => {
        alert(username + ":" + message);
        setUsername("");
        setMessage("");
    };
    const onKeyPress = e => {
        if (e.key === "Enter") {
            onClick();
        }
    };
    return (
        <div>
            <input type="text" name="username" placeholder="사용자명" value={username} onChange={onChangeUsername} />
            <input type="text" name="message" value={message} onChange={onChangeMessage} onKeyPress={onKeyPress} />
            <button onClick={onClick}>확인</button>
        </div>
    );
};
```

-   input의 개수가 많아질 것 같다면 e.target.name을 활용해도 좋다.

```jsx
const EventPractice = () => {
    const [form, setForm] = useState({
        username: "",
        message: ""
    });
    const [username, message] = form;
    const onChange = e => {
        const nextForm = {
            ...form, // 기존의 form 내용을 복사
            [e.target.name]: e.target.value // 원하는 값 덮어 씌우기
        };
        setForm(nextForm);
    };
    const onClick = () => {
        alert(username + ":" + message);
        setUsername("");
        setMessage("");
    };
    const onKeyPress = e => {
        if (e.key === "Enter") {
            onClick();
        }
    };
    return (
        <div>
            <input type="text" name="username" placeholder="사용자명" value={username} onChange={onChange} />
            <input type="text" name="message" value={message} onChange={onChange} onKeyPress={onKeyPress} />
            <button onClick={onClick}>확인</button>
        </div>
    );
};
```

---

## 정리

-   리액트에서 이벤트를 다루는 것은 순수 자바스크립트 또는 jQuery를 사용한 웹 애플리케이션에서 이벤트를 다루는 것과 비슷하다.
-   자바스크립트에 익숙하다면 쉽게 활용할 수 있다.
-   클래스형 컴포넌트로 할 수 있는 대부분의 작업은 함수 컴포넌트로도 구현⭕
