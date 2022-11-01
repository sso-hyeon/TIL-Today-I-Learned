# Reusability & Composition

## Composition API

-   컴포넌트 내에서 사용하는 특정 기능을 갖는 코드를 유연하게 구성하여 사용할 수 있도록 Vue 3 버전에 추가된 함수 기반의 API
-   프로젝트 규모가 커질수록, 컴포넌트의 계층구조가 복잡할수록 코드에 대한 추적 및 관리가 어려운 점을 컴포지션 API 이용하면 Setup이라는 메소드 안에서 한 덩어리로 코드를 구현할 수 있다. > 코드에 대한 관리가 훨씬 쉬워지게 된다.
-   코드에 대한 재활용성을 높이고, 코드의 가독성을 높이기 위해 추가된 기능
-   Vue 2 에서는 믹스인(mixin)을 통해 코드 재사용 > 오버라이딩 문제, 다중 믹스인을 사용하는 경우 코드에 대한 관리가 어려웠다.
-   기존 개발 방식으로 개발했을 때보다 연관성 있는 로직을 같이 구현할 수 있어서 훨씬 코드가 간결해지고 코드 추적 및 유지 관리가 쉬워진다.
-   동일한 프로그램 로직과 관련된 코드를 함께 배치 할 수 있다면 훨씬 더 좋을 것이다. 컴포지션 API가 추가된 이유

### Setup

-   컴포지션 API를 구현하는 곳

✏️ 컴포지션 API가 어떻게 구현되는지 비교하기!

> 사용자로부터 숫자 2개 입력받고, 입력받은 숫자를 더한 값을 출력하기

```vue
<template>
    <div>
        <h2>Calculator</h2>
        <div>
            <input type="text" v-model="num1" @keyup="plusNumbers" />
            <span> + </span>
            <input type="text" v-model="num2" @keyup="plusNumbers" />
            <span> = </span>
            <span>{{ result }}</span>
        </div>
    </div>
</template>
<script>
export default {
    name: "calculator",
    data() {
        return {
            num1: 0,
            num2: 0,
            result: 0
        };
    },
    methods: {
        plusNumbers() {
            this.result = parseInt(this.num1) + parseInt(this.num2);
        }
    }
};
</script>
```

🚀 사용자로부터 숫자가 입력(keyup) 이벤트 발생할 때마다 plusNumbers 함수 호출해서 입력한 값 더하여 result로 반환

```vue
<!-- 컴포지션 API -->
<template>
    <div>
        <h2>Calculator</h2>
        <div>
            <input type="text" v-model="state.num1" @keyup="plusNumbers" />
            <span> + </span>
            <input type="text" v-model="state.num2" @keyup="plusNumbers" />
            <span> = </span>
            <span>{{ state.result }}</span>
        </div>
    </div>
</template>
<script>
import { reactive } from "vue"; // reactive 추가
export default {
    name: "calculator",
    setup() {
        let state = reactive({
            // reactive를 이용해서 num1, num2, result를 실시간 변경사항에 대한 반응형 적용
            num1: 0,
            num2: 0,
            result: 0
        });
        function plusNumbers() {
            state.result = parseInt(state.num1) + parseInt(state.num2);
        }
        return {
            // reactive로 선언된 state와 plusNumbers 함수를 반환함으로써 기존 data, methods 옵션처럼 사용 가능해짐
            state,
            plusNumbers
        };
    }
};
</script>

<!-- 코드 간결화 -->
<template>
    <div>
        <h2>Calculator</h2>
        <div>
            <input type="text" v-model="state.num1" />
            <span> + </span>
            <input type="text" v-model="state.num2" />
            <span> = </span>
            <span>{{ state.result }}</span>
        </div>
    </div>
</template>
<script>
import { reactive, computed } from "vue"; // computed 추가
export default {
    name: "calculator",
    setup() {
        let state = reactive({
            num1: 0,
            num2: 0,
            result: computed(() => parseInt(state.num1) + parseInt(state.num2)) // computed를 이용해서 num1, num2가 변경이 일어나면 즉시 result로 더한 값 반환
        });
        return {
            state
        };
    }
};
</script>
```

🚀reactive, computed를 이용하니까 input type=text에 바인딩했던 keyup 이벤트를 없앨 수 있어 코드가 훨씬 간결! But, 현재 컴포넌트 내에서만 사용 가능

```vue
<!-- 함수 분리 -->
<template>
    <div>
        <h2>Calculator</h2>
        <div>
            <input type="text" v-model="state.num1" />
            <span> + </span>
            <input type="text" v-model="state.num2" />
            <span> = </span>
            <span>{{ result }}</span>
        </div>
    </div>
</template>
<script>
import { reactive, computed, toRefs } from "vue"; // toRefs 추가
function plusCalculator() {
    let state = reactive({
        num1: 0,
        num2: 0,
        result: computed(() => parseInt(state.num1) + parseInt(state.num2))
    });
    return toRefs(state); // 반응형으로 선언된 num1, num2, result가 외부 function에서 정상적으로 동작하기 위해서는 toRefs를 사용해야 한다.
}
export default {
    name: "calculator",
    setup() {
        let { num1, num2, result } = plusCalculater(); // 외부 function
        return {
            num1,
            num2,
            result
        };
    }
};
</script>
```

-   외부 function에서 반응형 변수 사용하기 위해 toRefs 추가
-   컴포넌트 안에서는 v-model 디렉티브를 통해 바인딩된 변수가 사용자의 입력값이 바뀔 때마다 반응형으로 처리가 되었지만, 함수를 컴포넌트 밖으로 빼면 사용자가 입력한 값에 대한 반응형 처리 불가능 > toRefs 사용하여 반응형 처리 가능하도록 할 수 있다.

🚀컴포넌트 내에서 정의된 코드를 다른 컴포넌트에서도 사용할 수 있도록 컴포넌트 밖으로 분리하기

```js
// common.js
import { reactive, computed, toRefs } from "vue";
const plusCalculator = () => {
    let state = reactive({
        num1: 0,
        num2: 0,
        result: computed(() => parseInt(state.num1) + parseInt(state.num2))
    });
    return toRefs(state);
};
export { plusCalculator };
```

🚀Vue 컴포넌트에서 사용하기

```vue
<template>
    <div>
        <h2>Calculator</h2>
        <div>
            <input type="text" v-model="num1" />
            <span> + </span>
            <input type="text" v-model="num2" />
            <span> = </span>
            <span>{{ result }}</span>
        </div>
    </div>
</template>
<script>
import { plusCalculator } from "../common.js";
export default {
    name: "calculator",
    setup() {
        let { num1, num2, result } = plusCalculator();
        return {
            num1,
            num2,
            result
        };
    }
};
</script>
```

🚀 특정 기능을 갖는 함수를 컴포지션 API를 이용하고 개발해서 공통 스크립트로 제공하면 뷰 컴포넌트 내에서 반응형으로 처리를 할 수 있어서 매우 활용도가 높아진다.
