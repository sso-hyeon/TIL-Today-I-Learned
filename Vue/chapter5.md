# 컴포넌트 Basic

## 컴포넌트란?

-   View, Data, Code의 세트
-   재사용이 가능하다
-   화면에서 보는 페이지 자체일 수도 있고 페이지 내의 특정 기능 요소일 수 있다.

## 컴포넌트 구조 이해하기

### 컴포넌트 기본 구조

```vue
<template>
    <div></div>
</template>
<script>
export default {
    name: "", // 컴포넌트 이름
    components: {}, // 다른 컴포넌트 사용 시 컴포넌트를 import하고, 배열로 저장
    data() {
        // html과 자바스크립트 코드에서 사용할 데이터 변수 선언
        return {
            sampleData: ""
        };
    },
    setup() {}, // 컴포지션 API
    created() {}, // 컴포넌트가 생성되면 실행
    mounted() {}, // template에 정의된 html 코드가 렌더링된 후 실행
    unmounted() {}, // unmount가 완료된 후 실행
    methods: {} // 컴포넌트 내에서 사용할 메소드 정의
};
</script>
```

-   &lt;template&gt; : view에 해당하는 html 코드 작성
-   name : 컴포넌트 이름(등록하지 않아도 사용하는 것에 지장❌)
-   components : 외부 컴포넌트를 사용하게 되면 해당 컴포넌트 import 후 배열로 등록!
-   data : html 코드와 자바스크립트 코드에서 전역 변수로 사용하기 위해 선언하는 데이터, 데이터 바인딩을 통해 화면에 해당하는 html과 코드에 해당하는 자바스크립트 간의 양방향 통신이 가능, 데이터 프로퍼티에 정의된 변수는 this를 통해 접근
-   setup : 컴포지션 API 구현하는 메소드
-   created : 컴포넌트가 생성되면 실행
-   mounted : 템플릿에 작성한 HTML 렌더링 후 실행
-   unmounted : 컴포넌트 빠져나갈때 실행
-   methods : 컴포넌트 내에서 사용할 메소드 정의, this를 통해서 접근

### Snippet 설정

> Code(File) > Preference > User Snippet > vue 입력

### Lifecycle Hooks

<img src="imgs/lifecycle.png"></img>

## 데이터 바인딩

-   양방향 데이터 바인딩 지원 (React는 단방향 데이터 바인딩) : 모델(Model)에서 데이터를 정의한 후 뷰(View)와 연결하면 모델과 뷰 중 어느 한쪽에 변경이 일어났을 때 다른 한쪽에 자동으로 반영되는 것을 의미
-   실제 프로젝트 내에서 데이터 바인딩 하는 경우
    -   데이터가 html tag 안에 텍스트로 바인딩
    -   데이터가 html tat 속성(attribute)으로 바인딩
    -   데이터가 html Form element의 value에 바인딩
    -   다중 데이터가 html 다중 element를 생성하기 위해서 바인딩

### 문자열 바인딩

-   이중 중괄호 이용해서 데이터 바인딩

```vue
<template>
    <h1>Hello, {{ title }}</h1>
</template>
<script>
export default {
    name: "DataBinding",
    components: {},
    data() {
        return {
            title: "World"
        };
    }
};
</script>
```

### raw(원시) HTML 데이터 바인딩

-   이중 중괄호를 이용하면 html 태그가 아닌 문자열로 인식
-   v-html 사용

```vue
<template>
    <div>
        <div>{{ htmlString }}</div>
        <div v-html="htmlString"></div>
    </div>
</template>
<script>
export default {
    name: "DataBinding",
    components: {},
    data() {
        return {
            htmlString: '<p style="color:red;">This is a red string</p>'
        };
    }
};
</script>
```

### Form 입력 데이터 바인딩

-   v-model 디렉티브 사용하여 양방향 데이터 바인딩 생성
-   v-model은 내부적으로 서로 다른 속성을 사용하고 서로 다른 입력 요소에 대해 서로 다른 이벤트를 전송한다

### Input type=text

-   단방향으로 데이터가 바인딩 된 것처럼 보이지만, 실제로는 텍스트 직접 입력하여 변경하면 데이터를 가져오는 별도의 코드 작성 없이 valueModel에 그대로 저장

### Input type=number

-   사용자의 입력 값이 문자가 아닌 숫자로 바로 처리할 수 있도록 v-model.number 디렉티브를 사용할 수 있다.

### Textarea

-   &lt;textarea v-model="message"&gt;&lt;/textarea&gt;

### Select

```vue
<template>
    <select v-model="city">
        <option value="02">서울</option>
        <option value="21">부산</option>
        <option value="064">제주</option>
    </select>
</template>
<script>
export default {
    name: "DataBinding",
    components: {},
    data() {
        return {
            city: "064"
        };
    }
};
</script>
```

### 체크박스(input type=checkbox)

-   v-model은 내부적으로 체크박스 checked 속성을 사용
-   v-model이 아닌 v-bind:value 사용

```vue
<template>
    <div>
        <label><input type="checkbox" v-model="checked" /> {{ checked }}</label>
    </div>
</template>
<script>
export default {
    name: "DataBinding",
    components: {},
    data() {
        return {
            checked: true
        };
    }
};
</script>
```

-   체크 되었을 때 기본 값 true, 해제되었을 때 기본 값 false
-   체크/해제 되었을 때의 기본 값 변경

```vue
<label><input type="checkbox" v-model="checked" true-value="yes" false-value="no" /> {{ checked }}</label>
```

-   여러 개의 체크박스를 사용할 때는 배열을 이용해서 데이터 바인딩해서 한 번에 처리 가능

```vue
<template>
    <div>
        <label><input type="checkbox" value="서울" v-model="checked" /> 서울</label>
        <label><input type="checkbox" value="부산" v-model="checked" /> 부산</label>
        <label><input type="checkbox" value="제주" v-model="checked" /> 제주</label>
        <br />
        <span>체크한 지역 : {{ checked }}</span>
    </div>
</template>
<script>
export default {
    name: "DataBinding",
    components: {},
    data() {
        return {
            checked: []
        };
    }
};
</script>
```
