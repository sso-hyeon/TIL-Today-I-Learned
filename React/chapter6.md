# 컴포넌트 반복

## 자바스크립트 배열의 map() 함수

### 문법 : arr.map(callback, &lbrack;thisArg&rbrack;)

-   callback : 새로운 배열의 요소를 생성하는 함수
    -   currentValue : 현재 처리하고 있는 요소
    -   index : 현재 처리하고 있는 요소의 index
    -   array: 현재 처리하고 있는 원본 배열
-   thisArg(선택 항목) : callback 함수 내부에서 사용할 this 레퍼런스

---

## 데이터 배열을 컴포넌트 배열로 변환하기

```JSX
const Component = () => {
    const fruits = ["🍎", "🍋", "🍒", "🍇"];
    const fruitsList = fruits.map(fruit => <li>{fruit}<li>);
    return (
        <ul>
            {fruitsList}
        </ul>
    )
}
```

🚀 "key" prop이 없다는 경고 메세지 표시!

---

## key

-   리액트에서 key는 컴포넌트 배열을 렌더링했을 때 어떤 원소에 변동이 있었는지 알아내기 위해 사용
-   key가 없을 때는 Virtual DOM을 비교하는 과정에서 리스트를 순차적으로 비교하며 변화 감지
-   key가 있다면 이 값을 사용하여 변화를 더욱 빠르게 감지!
-   key 설정
    -   map 함수의 인자로 전달되는 함수 내부에서 컴포넌트 props 설정하듯이!
    -   key 값은 언제나 유일해야 한다!

```JSX
const Component = () => {
    const fruits = ["🍎", "🍋", "🍒", "🍇"];
    const fruitsList = fruits.map((fruit, index) => <li key={index}>{fruit}<li>);

    return (
        <ul>
            {fruitsList}
        </ul>
    )
}
```

🚀 고유한 값이 없을 때만 index 값을 key로 사용해야 한다!<br> index를 key로 사용하면 배열이 변경될 때 효율적으로 리렌더링❌

---

## 응용

-   항목 추가하기

```JSX
const Component = () => {
    const [names, setNames] = useState([
        {id:1, text: "눈사람"},
        {id:2, text: "얼음"},
        {id:3, text: "눈"},
        {id:4, text: "바람"},
    ]);
    const [inputText, setInputText] = useState("");
    const [nextId, setNextId] = useState(5); // 새로운 항목을 추가할 때 사용할 id

    const onChange = e => setInputText(e.target.value);
    const onClick = () => {
        const nextNames = names.concat({
            id: nextId, // nextId 값을 id로 설정
            text: inputText
        });
        setNextId(nextId + 1); // nextId 값에 1을 더해준다.
        setNames(nextNames); // names 값을 업데이트한다.
        setInputText(""); // inputText를 비운다.
    };

    const namesList = names.map(name => <li key={name.id}>{name.text}</li>);
    return (
        <>
            <input value={inputText} onChange={onChange} />
            <button onClick={onClick}>추가</button>
            <ul>{namesList}</ul>
        </>
    );
};
```

-   데이터 제거 기능 : filter 함수 사용, 더블클릭시 삭제

```JSX
const Component = () => {
    const [names, setNames] = useState([
        {id:1, text: "눈사람"},
        {id:2, text: "얼음"},
        {id:3, text: "눈"},
        {id:4, text: "바람"},
    ]);
    const [inputText, setInputText] = useState("");
    const [nextId, setNextId] = useState(5); // 새로운 항목을 추가할 때 사용할 id

    const onChange = e => setInputText(e.target.value);
    const onClick = () => {
        const nextNames = names.concat({
            id: nextId, // nextId 값을 id로 설정
            text: inputText
        });
        setNextId(nextId + 1); // nextId 값에 1을 더해준다.
        setNames(nextNames); // names 값을 업데이트한다.
        setInputText(""); // inputText를 비운다.
    };

    const onRemove = id => {
        const nextNames = names.filter(name => name.id !== id);
        setNames(nextNames);
    };

    const namesList = names.map(name => <li key={name.id} onDoubleClick={() => onRemove(name.id)}>{name.text}</li>);
    return (
        <>
            <input value={inputText} onChange={onChange} />
            <button onClick={onClick}>추가</button>
            <ul>{namesList}</ul>
        </>
    );
};
```

---

## 정리

-   컴포넌트 배열을 렌더링할 때 key 값 설정에 항상 주의!
-   key 값은 언제나 유일! 만일 중복될 시 렌더링 과정에서 오류 발생
-   상태 안에서 배열 변형할 때는 배열에 직접 접근❌ concat, filter 등 배열 내장 함수 사용하여 새로운 배열을 만든 후 새로운 상태로 설정해 주어야 한다!

```

```
