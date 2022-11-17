# Vuex (v4.x)

## Vuex란?

-   Vue.js 애플리케이션을 위한 상태관리 패턴 + 라이브러리
-   모든 컴포넌트에 대한 중앙집중식 저장소 역할을 하며 예측 가능한 방식으로 상태를 변경할 수 있다.
-   Vuex를 이용하지 않고 컴포넌트 간 데이터를 주고받는 것은 방법이 있지만, 대규모 프로젝트가 될수록 매우 복잡해진다.
-   데이터를 store에 저장하고, 프로젝트 전체에서 사용할 수 있도록 해준다.

## Vuex 설치

```
npm install vuex@next --save
```

## 시작하기

-   모든 Vuex 애플리케이션의 중심에는 store가 있다.
-   저장소(store)는 애플리케이션 상태를 저장하고 있는 컨테이너
-   Vuex 저장소가 일반 전역 개체와 다른점

    1. Vuex store는 반응형이다.

    -   저장소의 상태(state)를 검색할 때 저장소의 상태에 정의된 변수 값의 변경 여부를 바로 알 수 있다.

    2.  저장소의 상태를 직접 변경할 수 없다.

    -   저장소의 상태를 변경하는 유일한 방법은 명시적인 커밋을 이용한 변이 > 모든 상태에 대한 추적이 가능한 기록이 남을 수 있으며 틀을 사용하여 앱을 더 잘 이해할 수 있다.

-   단순한 형태의 store 예시

```js
import { createStore } from "vuex";

const store = createStore({
    state() {
        return {
            count: 0
        };
    },
    mutations: {
        increment(state) {
            state.count++;
        }
    }
});

export default store;
```

-   Vue 컴포넌트에서는 this.$store로 접근 가능

```vue
<template>
    <p>Count : {{ count }}</p>
    <button type="button" @click="increment">Increment</button>
</template>
<script>
export default {
    computed: {
        count() {
            return this.$store.state.count;
        }
    },
    methods: {
        increment() {
            this.$store.commit("increment");
        }
    }
};
</script>
```

-   commit을 통해서만 변경할 수 있다.

## State

-   state는 프로젝트 전체에서 공통으로 사용할 변수를 정의하는 곳
-   state에 변수를 정의함으로써, 모든 컴포넌트에서 사용 가능
-   state 관리를 통해 모든 컴포넌트에서 동일한 값을 사용할 수 있다.

```
computed: {
    count() {
        return this.$store.state.count;
    }
}
```

## Getters

-   쇼핑몰에서 장바구니 처럼 어떤 화면에서든지 제품을 장바구니에 추가하면 바로 장바구니 아이콘의 제품 수가 증가한다
-   장바구니에 담긴 제품 데이터를 저장소의 state에 변수로 관리하고 있다면 장바구니에 담긴 제품 수, 특정 카테고리 제품 리스트 등을 getters를 정의하여 쉽게 가져올 수 있다.

```js
import { createStore } from "vuex";

const store = createStore({
    state() {
        return {
            count: 0,
            cart: [
                {
                    product_id: 1,
                    product_name: "아이폰 거치대",
                    category: "A"
                }
            ]
        };
    },
    getters: {
        cartCount: state => {
            return state.cart.length;
        }
    },
    mutations: {
        increment(state) {
            state.count++;
        }
    }
});

export default store;
```

-   Vue 컴포넌트 getters에 정의된 값에 접근할 수 있다.

```
computed: {
    cartCount() {
        return this.$store.getters.cartCount;
    }
}
```

## Mutations

-   state에 정의된 변수를 직접 변경하는 것을 허용❌
-   반드시 mutations를 이용해서 변경
-   state를 변경시키는 역할
-   비동기(Async) 처리가 아니라 동기(Sync) 처리를 통해 state에 정의된 변수의 변경사항을 추적할 수 있게 해준다
-   commit을 통해서 호출하는 것으로 저장소의 state에 정의된 변수의 값을 변경할 수 있다.

```
methods: {
    increment() {
        this.$store.commit("increment");
    }
}
```

## Actions

-   mutations와 매우 유사한 역할
-   action을 통해 mutations에 정의된 함수를 실행시킬 수 있다.
-   굳이 action을 통해 mutations를 실행하는 이유 : actions에 정의된 함수 안에서는 여러 개의 mutations를 실행시킬 수 있을 뿐만 아니라, mutations와 달리 비동기 작업 가능
-   actions에 등록된 함수는 비동기 처리 후 mutations를 커밋할 수 있어서 저장소(store)에서 비동기 처리 로직을 관리할 수 있게 해준다.

```
actions: {
    increment(context) {
        // 비동기 처리 로직 수행 가능
        context.commit("increment")
    }
}
```

## Vuex 실무 예제

-   vuex 실무에서 가장 많이 사용되는 예 : 사용자가 로그인을 하면 사용자 정보를 Vuex의 store에 저장해서 사용(모든 컴포넌트에서는 사용자가 로그인 했는지 정보를 알 필요가 있다.)
-   사용자 계정(account) 정보를 프로젝트 전체에 걸쳐서 변경사항을 관리해야 하는 데이터를 처리할 때 매우 유용

```js
import { createStore } from "vuex";
import persistedstate from "vuex-persistedstae";

const store = createStore({
    state() {
        return {
            user: {}
        };
    },
    mutations: {
        user(state, data) {
            state.user = data;
        }
    },
    plugins: [
        persistedstate({
            paths: ["user"]
        })
    ]
});

export default store;
```
