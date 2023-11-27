# VS Options api
- 템플릿과 스타일링은 그대로
- 프로퍼티, 사용자 설정 이벤트도 그대로
- JavaScript 코드는 격변
- 컴포지션 API는 어디까지나 옵션

## Options API
```js
// Options API의 예시
{
    data(){
        return { name: 'Max' };
    },
    watch: {},
    computed: {},
    methods: {},
}
```
- 구성 객체에 데이터와 메서드를 설정하고, 이벤트를 발생시키는 구조

## Options API의 한계
- 더 큰 Vue app을 제작하는데, 문제가 있을 수 있다.
1. 한번에 실행되는 로직에서 필요한 여러가지 기능/ 옵션들이, data, methods, computed 등으로 나뉘어져 있다.

2. 컴포넌트 재사용시, 로직이 까다롭거나 복잡하기 쉽다.

## Composition API
- **setup()** 메서드를 이용해, 로직을 번들로 만든다. 
    - 그리고 골라서 컴포넌트 구성 객체에 추가한다.

```js
{
    setup(){
        const name = ref('Max');
        function doSmth(){...}
        return { name, doSmth };
    }
}
```
- data(), methods 등 다음에 setup() 메서드를 추가해도 되나,
    - setup() 메서드는 data()와 methods의 기능을 대체한다.
    - 데이터, 함수 연산, 감시자 모두 setup() 메서드에서 관리하게 된다.
        - setup은 이를 상호작용 가능하게 한다.
        - 템플릿 코드나, v-if, 데이터 바인딩과 같은 [vue 내장 디렉티브](./01_dom_widget.md/#vue-내장-디렉티브) 기능은 Options API와 똑같이 그대로 사용 가능하다.
        - props, emits, components도 그대로이다.
        - 한마디로, setup 메서드는 **vue 객체를 구성시킨다는 것!**
        - 생명 주기는 조금 달라진다.
![setup](./img/setup.jpeg)
- setup 메서드는 Options API 기능들을 하나로 통합시키는데 의의를 둔다.


# setup
- data를 대체
    - setup 안에서 호출
    - DOM 요소를 참조하는 것처럼, 
        - setup 안에서 값을 참조하여, 
        - 템플릿 안에서 사용가능하게 한다.
    - 반응형 객체를 생성하여, vue가 인식하고 감시한다.
        - 값이 바뀌거나 템플릿에 사용시, Vue가 반응하는 것
            - 반응: 값이 변경(setter)되었을 때, DOM에서 템플릿을 업데이트

```html
<template>
    <h2>{{ userName }}</h2>
</template>
<script>
    import { ref } from 'vue';
    
    export default{
        setup() {
            const uName = ref('lshh');

            setTimeout(function(){
                uName.value = 'Iol';
            }, 2000);

            return {
                userName: uName
            };
        }
    };
</script>
```
- setup은 export default 객체 내부에 있어서, this가 참조가 불필요 하다.
    - setup은 vue가 컴포넌트 초기화 프로세스의 초반에 실행되는 메서드이다.
        - 컴포넌트가 제대로 초기화되지 않은 시점이다.
        - 때문에, this를 통해 제대로 액세스할 수 없다.
- setup의 변수를 template이 인식하기 위해선 return되어야 한다.

## setup 축약
- setup을 script 태그에 어트리뷰트로 붙임으로써, 많은 코드를 생략 가능하다.
```html
<script setup>
    import { ref } from 'vue';
    
    const uName = ref('lshh');

    setTimeout(function(){
        uName.value = 'Iol';
    }, 2000);
</script>
```

## ref
- 평범한 순수 변수나 상수를 저장한다.
    - ref는 내부적으로 객체를 생성하고, 값을 그 객체에 저장한다.
        - vue는 이 객체를 감시한다.
        - 값이 변경되면, 변화를 인식하고 DOM을 업데이트한다.
- ref 없이 변수에 값을 할당하고, return 할 수 있지만,
    - 반응형을 갖지 않는다. (리엑트가 감시하지 않는다.)
### value
- ref 내장 프로퍼티로, getter, setter 기능을 지원한다.
    - 템플릿에서는 .value를 쓸 필요없다.
        - vue가 감시하고 있기때문에 알아서, .value 값을 보여준다.

