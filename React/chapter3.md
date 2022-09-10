# 컴포넌트

## 클래스형 컴포넌트

### [ 클래스형 컴포넌트 ]

-   state 기능 및 라이프 사이클 기능을 사용할 수 있다.
-   임의 메서드를 정의할 수 있다.
-   render 함수 필수! 그 안에서 보여 주어야 할 JSX 반환

### [ 함수 컴포넌트 ]

-   클래스형 컴포넌트보다 선언이 훨씬 편하다.
-   메모리 자원도 클래스형 컴포넌트보다 덜 사용한다.
-   결과물의 파일 크기가 더 작다.
-   state와 라이프사이클 API 사용이 불가능<br>
    ▶ v16.8 업데이트 이후 Hooks 기능 도입되면서 해결

> 함수 컴포넌트와 클래스형 컴포넌트는 성능과 파일 크기 면에서 사실상 별 차이가 없다!

### 모듈 내보내기

```jsx
export default MyComponent;
```

### 모듈 불러오기

```jsx
import { MyComponent } from "./MyComponent";

const App = () => {
    return <MyComponent />;
};

export default App;
```

---

## props

-   properties를 줄인 표현으로 컴포넌트 속성을 설정할 때 사용
-   해당 컴포넌트를 불러와 사용하는 부모 컴포넌트에서 설정

### JSX 내부에서 props 렌더링

```jsx
const MyComponent = props => {
    return <div>안녕하세요. 제 이름은 {props.name}입니다.</div>;
};

export default MyComponent;
```

### 컴포넌트를 사용할 때 props 값 지정하기

```jsx
import MyComponent from "./MyComponent";

const App = () => {
    return <MyComponent name="React" />;
};

export default App;
```

### props 기본값 설정: defaultProps

☝ name 값을 지정하지 않았을 때 보여줄 기본값 설정

```jsx
const MyComponent = props => {
    return <div>안녕하세요. 제 이름은 {props.name}입니다.</div>;
};

MyComponent.defaultProps = {
    name: "기본 이름"
};

export default MyComponent;
```

### 태그 사이의 내용을 보여 주는 children

```jsx
import MyComponent from "./MyComponent";

const App = () => {
    return <MyComponent>리액트</MyComponent>;
};

export default App;
```

```jsx
const MyComponent = props => {
    return (
        <div>
            안녕하세요. 제 이름은 {props.name}입니다.
            <br />
            children 값은 {props.children}입니다.
        </div>
    );
};

MyComponent.defaultProps = {
    name: "기본 이름"
};

export default MyComponent;
```

🚀**결과물** : 안녕하세요. 제이름은 기본 이름 입니다.<br>
children 값은 리액트입니다.

### 비구조화 할당 문법을 통해 props 내부 값 추출하기

-   구조 분해 문법이라고도 한다.
-   함수의 파라미터 부분에서도 사용 가능

```jsx
const MyComponent = props => {
    const { name, children } = props;
    return (
        <div>
            안녕하세요. 제 이름은 {name}입니다.
            <br />
            children 값은 {children}입니다.
        </div>
    );
};

MyComponent.defaultProps = {
    name: "기본 이름"
};

export default MyComponent;
```

```jsx
const MyComponent = { name, children } => {
    return (
        <div>
            안녕하세요. 제 이름은 {name}입니다.
            <br />
            children 값은 {children}입니다.
        </div>
    );
};

MyComponent.defaultProps = {
    name: "기본 이름"
};

export default MyComponent;
```

### propTypes를 통한 props 검증

-   컴포넌트의 필수 props 지정 및 타입 지정 시 propTypes 사용

```jsx
import PropTypes from "prop-types";

const MyComponent = ({ name, children }) => {
    return (...);
}

MyComponent.propTypes = {
    name : PropTypes.string,
    number : PropTypes.number.isRequired
};

export default MyComponent;
```

🚀 name 값은 무조건 string 형태로 전달해야 된다는 것을 의미!<br>
🚀 isRequired : 필수 props 지정하기

-   이 외에도 여러 가지 종류를 설정할 수 있다!<br>
    [▶ prop-types 보러가기](https://github.com/facebook/prop-types)

### 클래스형 컴포넌트에서 props 사용하기

```jsx
class MyComponent extends Component {
    render() {
        const { name, number, children } = this.props; // 비구조화 할당
        return (
            <div>
                안녕하세요. 제 이름은 {name}입니다.
                <br />
                children 값은 {children}입니다.
                <br />
                제가 좋아하는 숫자는 {number}입니다.
            </div>
        );
    }
}

MyComponent.defaultProps = {
    name: "기본 이름"
};

MyComponent.propTypes = {
    name: PropTypes.string,
    number: PropTypes.number.isRequired
};

export default MyComponent;
```

```jsx
// defaultProps, propTypes 설정할 때 class 내부에서 지정하기

class MyComponent extends Component {
    static defaultProps = {
        name: "기본 이름"
    };
    static propTypes = {
        name: PropTypes.string,
        number: PropTypes.number.isRequired
    };
    render() {
        ...
    }
}
```

---

## state

-   컴포넌트 내부에서 바뀔 수 있는 값을 의미
-   props는 컴포넌트가 사용되는 과정에서 부모 컴포넌트가 설정하는 값 > props를 바꾸려면 부모 컴포넌트에서 바꿔줘야 한다.
-   클래스형 컴포넌트가 지니고 있는 **state** / 함수 컴포넌트에서 **useState**라는 함수를 통해 사용하는 **state**

### 클래스형 컴포넌트의 state

-   state를 설정할 때 constructor 메서드를 작성하여 설정
-   constructor 작성시 반드시 super(props) 호출! > 현재 클래스형 컴포넌트가 상속받고 있는 리액트의 Component 클래스가 지닌 생성자 함수 호출
-   컴포넌트의 state는 객체 형식!

```jsx
class Counter extends Component {
    constructor(props) {
        super(props);
        // state의 초기값 설정하기
        this.state = {
            number: 0
        };
    }
}
```

-   render 함수에서 현재 state 조회시 this.state
-   constructor 메서드 선언하지 않고도 state 초기값 설정할 수 있다.

```jsx
class Counter extends Component {
    state = {
        number: 0,
        fixedNumber: 0
    };
    render() {
        const {number, fixedNumber} = this.state;
        return (...);
    }
}
```

-   this.setState를 사용하여 state 값 업데이트할 때 상태가 비동기적으로 업데이트된다.

```jsx
onClick={() => {
    // this.setState를 사용하여 state에 새로운 값을 넣을 수 있다.
    this.setState({number: number + 1});
    this.setState({number: this.state.number + 1});
}}
```

-   setState를 두 번 사용하는 것임에도 불구하고 onClick 함수 호출시, 숫자 1씩 더해진다 > this.setState를 사용한다고 해서 state 값이 바로 바뀌지 않기 때문!
-   해결 방법 : this.setState를 사용할 때 객체 대신에 함수를 인자로 넣어준다!

```jsx
<button
    // onClick을 통해 버튼이 클릭되었을 때 호출할 함수 지정
    onClick={() => {
        this.setState(prevState => {
            return {
                number: prevState.number + 1
            };
        });
        // 위 코드와 아래 코드는 완전히 똑같은 기능을 합니다.
        // 아래 코드는 함수에서 바로 객체를 반환한다는 의미
        this.setState(prevState => ({
            number: prevState.number + 1
        }));
    }}
>
    +1
</button>
```

-   setState를 사용하여 값을 업데이트한 후 특정 작업 실행하고 싶을 때 setState의 두번째 파라미터로 콜백 함수 등록하여 작업

```jsx
<button
    onClick={() => {
        this.setState(
            {
                number: number + 1
            },
            () => {
                console.log("방금 setState가 호출되었습니다.");
            }
        );
    }}
>
    +1
</button>
```

### 함수 컴포넌트에서 useState 사용하기

-   리액트 v16.8 이후 사용
-   배열 비구조화 할당 : 배열 안에 들어 있는 값을 쉽게 추출할 수 있도록 해 주는 문법

```js
const array = [1, 2];
const one = array[0];
const two = array[1];
// 배열 비구조화 할당
const [one, two] = array;
```

-   useState 함수의 인자에는 상태의 초기값 넣어 준다.
-   useState에서는 반드시 객체가 아니어도 된다. 값의 형태 자유
-   함수 호출하면 배열 반환
    -   첫 번째 원소 : 현재 상태
    -   두 번째 원소 : 상태를 바꾸어 주는 함수(세터(Setter) 함수)
-   이름 자유롭게 정의 가능(message, setMessage)
-   한 컴포넌트 내에서 여러 번 사용⭕

---

## state를 사용할 때 주의 사항

-   state 값을 바꾸어야 할 때 setState, useState를 통해 전달받은 세터 함수 사용해야 한다.
-   배열이나 객체 업데이트시, 객체 사본을 만들고 사본에 값 업데이트 후, 그 사본의 상태를 setState 혹은 세터 함수를 통해 업데이트

```jsx
// ❌잘못된 코드
// 클래스형
this.state.number = this.state.number + 1;
this.state.array = this.array.push(1);
this.state.object.value = 5;

// 함수형
const [object, setObject] = useState({ a: 1, b: 1 });
object.b = 2;
```

```jsx
// 객체 다루기
const object = { a: 1, b: 2, c: 3 };
const nextObject = { ...object, b: 2 }; // 사본을 만들어서 b 값만 덮어 쓰기

// 배열 다루기
const array = [
    { id: 1, value: true },
    { id: 2, value: true },
    { id: 3, value: false }
];
let nextArray = array.concat({ id: 4 }); // 새 항목 추가
nextArray.filter(item => item.id !== 2); // id가 2인 항목 제거
nextArray.map(item => (item.id === 1 ? { ...item, value: false } : item)); // id가 1인 항목의 value를 false로 설정
```

---

## 정리

-   porps는 부모 컴포넌트가 설정
-   state는 컴포넌트 자체적으로 지닌 값으로 컴포넌트 내부에서 값을 업데이트
-   prop를 사용한다고 해서 값이 무조건 고정적❌<br>부모 컴포넌트의 state를 자식 컴포넌트 props로 전달 > 자식 컴포넌트에서 특정 이벤트가 발생할 때 부모 컴포넌트 메서드 호출하면 props도 유동적으로 사용 가능
