
# 동작
- vue는 data 프로퍼티 객체를, **JS 프록시 객체로 래핑**한다.
  - 내장 반응성 기능
    - JS의 Proxy를 통해, 기존 객체 기본 동작 기능에, 반응성 기능을 추가한다.

## JS Proxy
- 원본 객체의 기본 내장 슬롯 함수들의 동작을 확장시켜주는 객체

|Internal Method|Handler|MethodTriggers when...|
|---|---|---|
| [[Get]]               | get                       | reading a property        |
| [[Set]]               | set                       | writing to a property     |
| [[HasProperty]]       | has                       | in operator               |
| [[Delete]]            | deleteProperty            | delete operator           |
| [[Call]]              | apply                     | function call             |
| [[Construct]]         | construct                 | new operator              |
| [[GetPrototypeOf]]    | getPrototypeOf            | Object.getPrototypeOf     |
| [[SetPrototypeOf]]    | setPrototypeOf            | Object.setPrototypeOf     |
| [[IsExtensible]]      | isExtensible              | Object.isExtensible       |
| [[PreventExtensions]] | preventExtensions         | Object.preventExtensions  |
| [[DefineOwnProperty]] | defineProperty            | Object.defineProperty, Object.defineProperties    |
| [[GetOwnProperty]]    | getOwnPropertyDescriptor  | Object.getOwnPropertyDescriptor, for..in, Object.keys/values/entries
| [[OwnPropertyKeys]]   | ownKeys                   | Object.getOwnPropertyNames, Object.getOwnPropertySymbols, for..in, Object.keys/values/entries |

- JS 표준 내장 객체이다. 
- 참조: 
    - [mdn](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
    - [mozilla](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
    - [튜토리얼](https://web.archive.org/web/20171007221059/https://soft.vub.ac.be/~tvcutsem/proxies/)
- 프록시 객체는 다음 두 가지 인자를 받는다.
    - `new Proxy(target, handler)`
    - `target`: 프록시할 원본 객체
    - `handler`: 가로채는 작업과 가로채는 작업을 재정의하는 방법을 정의하는 객체
    
```js
// 핸들러가 비어 있기 때문에, 원본과 동일한 동작
const target = {
  message1: "hello",
  message2: "everyone",
};

const handler1 = {};

const proxy1 = new Proxy(target, handler1);

console.log(proxy1.message1); // hello
console.log(proxy1.message2); // everyone
```

```js
// getter 트랩 적용
// Hanlder function
const target = {
  message1: "hello",
  message2: "everyone",
};

const handler2 = {
  get(target, prop, receiver) {
    return "world";
  },
};

const proxy2 = new Proxy(target, handler2);

console.log(proxy2.message1); // world
console.log(proxy2.message2); // world
```

- 트랩(trap)
- **처리기 함수는 대상 객체에 대한 호출을 잡는다.** 
- 대상 객체의 속성 액세스를 가로채는 **get() 처리기**
- handler2의 트랩은 모든 속성 접근자를 재정의

```js
// Reflect 클래스를 통해, 원래 동작을 제공
const target = {
  message1: "hello",
  message2: "everyone",
};

const handler3 = {
  get(target, prop, receiver) {
    if (prop === "message2") {
      return "world";
    }
    return Reflect.get(...arguments);
  },
};

const proxy3 = new Proxy(target, handler3);

console.log(proxy3.message1); // hello
console.log(proxy3.message2); // world
```




# 가상 돔


