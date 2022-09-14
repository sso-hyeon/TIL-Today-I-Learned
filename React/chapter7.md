# 컴포넌트의 라이프사이클 메서드

모든 리액트 컴포넌트에는 라이프사이클(수명 주기)이 존재한다. 컴포넌트의 수명은 페이지 준비 과정에서 시작 → 사라질 때 끝난다.

프로젝트를 진행하다 보면 컴포넌트를 처음으로 렌더링할 때, 컴포넌트 업데이트하기 전후로 작업을 처리하거나 불필요한 업데이트를 방지해야 할 수도 있다.

이럴 때 사용하는 것이 라이프사이클 메서드!<br>
🚀 클래스형 컴포넌트에서만 사용 가능 > 함수 컴포넌트에서는 Hooks 기능을 사용하여 비슷한 작업 처리

## 라이프사이클 메서드의 이해

### 마운트(mount) : DOM이 생성되고 웹 브라우저상에 나타나는 것

🚀 마운트할 때 호출하는 메서드

> 컴포넌트 만들기 → constructor → getDerivedStateFormProps → render → componentDidMount

-   constructor : 컴포넌트를 새로 만들 때마다 호출되는 클래스 생성자 메서드
-   getDerivedStateFormProps : props에 있는 값을 state에 넣을 때 사용하는 메서드
-   render : 우리가 준비한 UI를 렌더링하는 메서드
-   componentDidMount : 컴포넌트가 웹 브라우저상에 나타난 후 호출하는 메서드

### 업데이트

🚀 컴포넌트가 업데이트하는 경우

-   props가 바뀔 때
-   state가 바뀔 때
-   부모 컴포넌트가 리렌더링될 때
-   this.forceUpdate로 강제로 렌더링을 트리거할 때

🚀 업데이트할 때 호출하는 메서드

> 업데이트를 발생시키는 요인 → getDerivedStateFormProps → shouldComponentUpdate → (true 반환 시 render 호출, false반환 시 작업 취소) → render(forceUpdate) → getSnapshotBeforeUpdate → (웹 브라우저상의 실제 DOM 변화) → componentDidUpdate

-   getDerivedStateFormProps : 마운트 과정에서도 호출, 업데이트가 시작하기 전에 호출, props의 변화에 따라 state 값에도 변화를 주고 싶을 때 사용
-   shouldComponentUpdate : 컴포넌트가 리렌더링을 해야 할지 말아야 할지를 결정하는 메서드, true 반환 시 계속 실행 / false 반환 시 작업 중지(컴포넌트 리렌더링❌), 만약 특정 함수에서 this.forceUpdate() 함수를 호출한다면 이 과정 생략하고 바로 render 함수 호출
-   render : 컴포넌트 리렌더링
-   getSnapshotBeforeUpdate : 컴포넌트 변화를 DOM에 반영하기 바로 직전에 호출하는 메서드
-   componentDidUpdate : 컴포넌트의 업데이트 작업이 끝난 후 호출하는 메서드

### 언마운트(unmount) : 마운트의 반대 과정, 컴포넌트를 DOM에서 제거하는 것

🚀 언마운트할 때 호출하는 메서드

> 언마운트하기 → componentWillUnmount

-   componentWillUnmount : 컴포넌트가 웹 브라우저상에서 사라지기 전에 호출하는 메서드

---

## 라이프사이클 메서드 살펴보기

#### 1. render() 함수

-   컴포넌트 모양새 정의
-   라이프사이클 메서드 중 유일한 필수 메서드
-   this.props, this.state 접근 가능
-   리액트 요소 반환
-   이벤트 설정이 아닌 곳에서 setState 사용❌
-   브라우저의 DOM에 접근❌
-   DOM 정보를 가져오거나 state에 변화를 줄 때는 componentDidMount에서 처리

#### 2. constructor 메서드

```JSX
constructor(props) {...}
```

-   컴포넌트의 생성자 메서드
-   컴포넌트를 만들 때 처음으로 실행
-   초기 state를 정할 수 있다.

#### 3. getDerivedStateFormProps 메서드

```JSX
static getDerivedStateFormProps(nextProps, prevState) {
    // 조건에 따라 특정 값 동기화
    if(nextProps.value !== prevState.value) {
        return { value: nextProps.value };
    }
    return null; // state를 변경할 필요가 없다면 null 반환
}
```

-   v16.3 이후에 새로 만든 라이프사이클 메서드
-   props로 받아 온 값을 state에 동기화시키는 용도
-   컴포넌트가 마운트, 업데이트될 떼 호출

#### 4. componentDidMount 메서드

```jsx
componentDidMount() {...}
```

-   컴포넌트를 만들고 첫 렌더링을 다 마친 후 실행
-   자바스크립트 라이브러리, 프레임워크 함수 호출, 이벤트 등록, setTimeout, setInterval, 네트워크 요청 같은 비동기 작업 처리

#### 5. shouldComponentUpdate 메서드

```jsx
shouldComponentUpdate(nextProps, nextState) {...}
```

-   props 또는 state를 변경했을 때, 리렌더링 시작 여부 지정
-   반드시 true / false 값 반환
-   메서드 따로 생성하지 않으면 기본값으로 true 반환
-   현재 props, state는 this.props, this.state로 접근 / 새로 설정될 porps, state는 nextProps, nextState로 접근
-   프로젝트 성능 최적화할 때, 상황에 맞는 알고리즘 작성하여 리렌더링 방지할 때 false값 반환

#### 6. getSnapshotBeforeUpdate 메서드

```jsx
getSnapshotBeforeUpdate(prevProps, prevState) {
    if(prevState.array !== this.state.array) {
        const { scrollTop, scrollHeight } = this.list;
        return { scrollTop, scrollHeight };
    }
}
```

-   v16.3 이후 만들어진 메서드
-   render에서 만들어진 결과물이 웹브라우저에 실제로 반영되기 직전에 호출
-   반환 값은 componentDidUpdate에서 세 번째 파라미터 snapshot 값으로 전달받을 수 있다.
-   주로 업데이트 직전의 값을 참고할 일이 있을 때 활용(ex : 스크롤바 위치 유지)

#### 7. componentDidUpdate 메서드

```jsx
componentDidUpdate(prevProps, prevState, snapshot) {...}
```

-   리렌더링 완료 후 실행
-   업데이트 끝난 직후 → DOM 관련 처리⭕
-   prevProps, prevState 사용하여 컴포넌트가 이전에 가졌던 데이터 접근 가능
-   getSnapshotBeforeUpdate에서 반환한 값이 있다면 snapshot 값을 전달받을 수 있다.

#### 8. componentWillUnmount 메서드

```jsx
componentWillUnmount() {...}
```

-   컴포넌트를 DOM에서 제거할 때 실행
-   componentDidMount에서 등록한 이벤트, 타이머, 직접 생성 DOM 여기서 제거 작업

#### 9. componentDidCatch 메서드

```jsx
componentDidCatch(error, info) {
    this.setState({
        error: true
    });
    console.log({ error, info });
}
```

-   v16에서 새롭게 도입
-   컴포넌트 렌더링 도중에 에러 발생 시 애플리케이션이 먹통이 되지 않고 오류 UI를 보여 줄 수 있게 해준다.
-   error : 파라미터에 어떤 에러가 발생했는지 알려줌
-   info : 어디에 있는 코드에서 오류가 발생했는지에 대한 정보
-   console.log가 아닌 서버 API 호출하여 따로 수집 가능
-   컴포넌트 자신에게 발생하는 에러는 잡을 수 없고 자신의 this.props.children으로 전달되는 컴포넌트에서 발생하는 에러만 잡을 수 있다.

---

## 정리

-   라이프사이클 메서드는 컴포넌트 상태에 변화가 있을 때마다 실행
-   서드파티 라이브러리를 사용하거나 DOM을 직접 건드려야 하는 상황에서 유용
-   컴포넌트 업데이트의 성능 개선할 때는 shouldComponentUpdate 중요하게 사용
