# Hooks

-   v16.8에 새로 도입된 기능
-   함수 컴포넌트에서도 상태 관리 할 수 있는 useState
-   렌더링 직후 작업 설정하는 useEffect

## useState

-   가장 기본적인 Hooks
-   함수 컴포넌트에서도 가변적인 상태를 지닐 수 있게 해준다.
-   상태 관리
-   함수의 파라미터에는 상태의 기본값
    ```jsx
    const [value, setValue] = useState(0);
    ```

---

## useEffect

-   컴포넌트가 렌더링될 때마다 특정 작업을 수행하도록 설정할 수 있다.
-   클래스형 컴포넌트의 componentDidMount, componentDidUpdate를 합친 형태로 보아도 무방
-   React.strictMode에서는 코드에 문제가 있는지 없는지 감지하기 위해 두 번 실행

### 마운트될 때만 실행

-   함수의 두 번째 파라미터로 비어 있는 배열을 넣어 주면 된다.
    ```jsx
    useEffect(() => {
        console.log("마운트될 때만 실행됩니다.");
    }, []);
    ```

### 특정 값이 업데이트될 때만 실행

-   두 번째 파라미터로 전달되는 배열 안에 검사하고 싶은 값을 넣어주기
    ```jsx
    useEffect(() => {
        console.log(name);
    }, [name]);
    ```
-   useState를 통해 관리하고 있는 상태, props로 전달받은 값을 넣어주어도 된다.

### 뒷정리하기

-   컴포넌트가 언마운트되기 전이나 업데이트되기 직전에 작업 수행하고 싶다면 뒷정리(cleanup) 함수 반환
    ```jsx
    useEffect(() => {
        console.log("effect");
        console.log(name);
        return () => {
            console.log("cleanup");
            console.log(name);
        };
    }, [name]);
    ```
-   렌더링될 때마다 뒷정리 함수가 계속 나타난다. 뒷정리 함수 호출시 업데이트되기 직전의 값을 보여준다. > 오직 언마운트될 때만 뒷정리 함수를 호출하고 싶다면 두 번째 파라미터에 비어있는 배열을 넣으면 된다.
    ```jsx
    useEffect(() => {
        console.log("effect");
        return () => {
            console.log("unmount");
        };
    }, []);
    ```

---

## useReducer

🚀 내용이 어렵다면 chapter17 내용 보고 난 후 다시 보기!

-   useState보다 더 다양한 컴포넌트 상황에 따라 다양한 상태를 다른 값으로 업데이트해 주고 싶을 때 사용
-   현재 상태, 업데이트를 위해 필요한 정보를 담은 액션값을 전달받아 새로운 상태를 반환하는 함수
-   반드시 불변성을 지켜줘야 한다!

```JSX
function reducer(state, action) {
    return {...};
    // 불변성을 지키면서 업데이트한 새로운 상태를 반환한다.
}

// 액션 값의 형태
{
    type: "INCREMENT"
    // 다른 값들이 필요하다면 추가로 들어간다.
}
```

-   리덕스에서 사용하는 액션 객체에는 어떤 액션인지 알려주는 type 필드 필수! > useReducer에서 사용하는 액션 객체는 반드시 type을 지니고 있을 필요❌ 심지어 객체가 아니라 문자열, 숫자여도 상관❌

```jsx
function reducer(state, action) {
    // action.type에 따라 다른 작업 수행
    switch (action.type) {
        case "INCREMENT":
            return { value: state.value + 1 };
        case "DECREMENT":
            return { value: state.value - 1 };
        default:
            // 아무것도 해당되지 않을 때 기존 상태 반환
            return state;
    }
}

const Counter = () => {
    const [state, dispatch] = useReducer(reducer, { value: 0 });

    return (
        <div>
            <p>
                현재 카운터 값은 <b>{state.value}</b>입니다.
            </p>
            <button onClick={() => dispatch({ type: "INCREMENT" })}>+1</button>
            <button onClick={() => dispatch({ type: "DECREMENT" })}>-1</button>
        </div>
    );
};
```

-   첫 번째 파라미터 : 리듀서 함수
-   두 번째 파라미터 : 리듀서의 기본값
-   state, dispatch 함수를 받아 온다
-   state = 현재 가리키고 있는 상태
-   dispatch = 액션을 발생시키는 함수 > dispatch(action) 형태, 파라미터로 액션 값을 넣어 주면 리듀서 함수가 호출
-   가장 큰 장점은 컴포넌트 업데이트 로직을 컴포넌트 밖으로 빼낼 수 있다!

### input 상태 관리하기

-   기존에는 input이 여러 개여서 useState 여러 번사용!
-   useReducer > 기존 클래스형 컴포넌트에서 input 태그에 name 값 할당 후 e.target.name 참조하여 setState 해 준 것과 유사하게 작업 처리 가능

```jsx
function reducer(state, action) {
    return {
        ...state,
        [action.name]: action.value
    };
}

const Info = () => {
    const [state, dispatch] = useReducer(reducer, {
        name: "",
        nickname: ""
    });
    const { name, nickname } = state;
    const onChange = e => {
        dispatch(e.target);
    };

    return (
        <div>
            <div>
                <input name="name" value={name} onChange={onChange} />
                <input name="nickname" value={nickname} onChange={onChange} />
            </div>
            <div>
                <div>이름: {name}</div>
                <div>닉네임: {nickname}</div>
            </div>
        </div>
    );
};
```

-   action : 어떤 값도 사용 가능

---

## useMemo

-   함수 컴포넌트 내부에서 발생하는 연산 최적화
-   렌더링하는 과정에서 특정 값이 바뀌었을 때만 연산을 실행
-   원하는 값이 바뀌지 않았다면 이전에 연산했던 결과 다시 사용

```jsx
const getAverage = numbers => {
    console.log("평균값 계산 중...");
    if (numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b) => a + b);
    return sum / numbers.length;
};

const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState("");

    const onChange = e => {
        setNumber(e.target.value);
    };
    const onInsert = () => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber("");
    };

    const avg = useMemo(() => getAverage(list), [list]);

    return (
        <div>
            <input value={number} onChange={onChange} />
            <button onClick={onInsert}>등록</button>
            <ul>
                {list.map((value, index) => (
                    <li key={index}>{value}</li>
                ))}
            </ul>
            <div>평균값: {avg}</div>
        </div>
    );
};
```

---

## useCallback

-   useMemo와 비슷한 함수
-   렌더링 성능을 최적화해야 하는 상황에서 사용
-   만들어 놨던 함수 재사용 가능
-   위에서 구현한 Average 함수에 onChange, onInsert 함수 선언 방식은 컴포넌트가 리렌더링될 때마다 새로 만들어진 함수 사용 > 컴포넌트의 렌더링이 자주 발생하거나 렌더링해야 할 컴포넌트의 개수가 많아지면 최적화해 주는 것이 좋다.

```jsx
const getAverage = numbers => {
    console.log("평균값 계산 중...");
    if (numbers.length === 0) return 0;
    const sum = numbers.reduce((a, b) => a + b);
    return sum / numbers.length;
};

const Average = () => {
    const [list, setList] = useState([]);
    const [number, setNumber] = useState("");

    const onChange = useCallback(e => {
        setNumber(e.target.value);
    }, []); // 컴포넌트가 처음 렌더링될 때만 함수 생성
    const onInsert = useCallback(() => {
        const nextList = list.concat(parseInt(number));
        setList(nextList);
        setNumber("");
    }, [number, list]); // number 혹은 list가 바뀌었을 때만 함수 생성

    const avg = useMemo(() => getAverage(list), [list]);

    return (
        <div>
            <input value={number} onChange={onChange} />
            <button onClick={onInsert}>등록</button>
            <ul>
                {list.map((value, index) => (
                    <li key={index}>{value}</li>
                ))}
            </ul>
            <div>평균값: {avg}</div>
        </div>
    );
};
```

-   첫 번째 파라미터 : 생성하고 싶은 함수
-   두 번째 파라미터 : 어떤 값이 바뀌었을 때 함수를 새로 생성해야 하는지 명시된 배열
-   두 번째 파라미터 빈 배열 = 컴포넌트가 렌더링될 때 만들었던 함수를 계속해서 재사용
-   두 번째 파라미터 값이 들어있는 배열 = 값이 변경되거나 새로운 항목이 추가되면 새로 만들어진 함수 사용
-   함수 내부에서 상태 값에 의존해야 할 때 그 값을 반드시 두 번째 파라미터 안에 포함시켜주어야 한다!
-   onChange의 경우 기존의 값 조회하지 않고 바로 설정만 하기 때문에 배열이 비어 있어도 상관❌, onInsert는 기존의 number, list를 조회하여 nextList를 생성하기 때문에 배열 안에 number, list 필수!

---

## useRef

-   함수 컴포넌트에서 ref 쉽게 사용할 수 있게 해준다.
-   컴포넌트 로컬 변수(렌더링과 상관없이 바뀔 수 잇는 값)를 사용해야 할 때도 useRef 활용

```jsx
// 📌 클래스형 컴포넌트
class MyComponent extends Component {
    id = 1;
    setId = n => {
        this.id = n;
    };
    printId = () => {
        console.log(this.id);
    };
    render() {
        return <div>MyComponent</div>;
    }
}
// 📌 함수 컴포넌트
const RefSample = () => {
    const id = useRef(1);
    const setId = n => {
        id.current = n;
    };
    const printId = () => {
        console.log(id.current);
    };

    return <div>refSample</div>;
};
```

🚀 ref 안의 값이 바뀌어도 컴포넌트 렌더링❌ > 렌더링과 관련되지 않은 값을 관리할 떄만 이러한 방식 사용

---

## 커스텀 Hooks 만들기

-   여러 컴포넌트에서 비슷한 기능 공유할 경우, Hook 커스텀하여 재사용 가능
-   useReducer로 작성했던 로직을 따로 분리해보기

```jsx
function reducer(state, action) {
    return {
        ...state,
        [action.name]: action.value
    };
}
export default function useInputs(initialForm) {
    const [state, dispatch] = useReducer(reducer, initialForm);
    const onChange = e => {
        dispatch(e.target);
    };
    return [state, onChange];
}

// 📌 컴포넌트에서 사용하기
const Info = () => {
    const [state, onChange] = useInputs({
        name: "",
        nickname: ""
    });
    const { name, nickname } = state;

    return (
        <div>
            <div>
                <input name="name" value={name} onChange={onChange} />
                <input name="nickname" value={nickname} onChange={onChange} />
            </div>
            <div>
                <div>이름: {name}</div>
                <div>닉네임: {nickname}</div>
            </div>
        </div>
    );
};
```

---

## 다른 Hooks

-   다른 개발자가 만든 Hooks도 라이브러리로 설치하여 사용 가능!
    <https://nikgraf.github.io/react-hooks>  
    <https://github.com/rehooks/awesome-react-hooks>

---

## 정리

-   클래스형 컴포넌트를 작성하지 않고도 대부분의 기능 구현 가능 > 기존의 setState를 사용하는 방식이 잘못된 것은 아니다.
-   기존의 클래스형 컴포넌트는 앞으로도 계속해서 지원될 예정 > 유지 보수하고 있는 프로젝트에서 클래스형 컴포넌트를 사용하고 있다면, 굳이 함수 컴포넌트와 Hooks 형태로 전환할 필요❌
-   하지만 매뉴얼에서는 새로 작성하는 컴포넌트의 경우 함수 컴포넌트, Hooks 사용 권장
-   함수 컴포넌트의 사용을 첫 번째, 꼭 필요한 상황에서만 클래스형 컴포넌트로 구현!
