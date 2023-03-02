
> [Home](/README.md)

- [3. 변수 및 함수 명명 규칙](#3-변수-및-함수-명명-규칙)
  - [3.1. 변수 명명 규칙](#31-변수-명명-규칙)
  - [3.2. 함수 명명 규칙](#32-함수-명명-규칙)
  - [3.3 HTML Element id명 규칙](#33-html-element-id명-규칙)
  - [3.4 JavaScript 문법](#34-javascript-문법)
    - [3.4.1 변수](#341-변수)
      - [3.4.1.1 const와 let](#3411-const와-let)
        - [3.4.1.1.1 const](#34111-const)
        - [3.4.1.1.2 let](#34112-let)
      - [3.4.1.2 호이스팅](#3412-호이스팅)
    - [3.4.2 불변성](#342-불변성)
      - [3.4.2.1 배열](#3421-배열)
        - [3.4.2.1.1 filter](#34211-filter)
        - [3.4.2.1.2 map](#34212-map)
        - [3.4.2.1.3 reduce](#34213-reduce)
        - [3.4.2.1.4 전개연산자 (...)](#34214-전개연산자)
    - [3.4.3 Null 유효성 관리](#343-null-유효성-관리)
      - [3.4.3.1 논리OR(||) VS 널병합연산자(??)](#3431-논리or-vs-널병합연산자)
      - [3.4.3.2 옵셔널 체이닝(.?)](#3432-옵셔널-체이닝)
    - [3.4.4 객체](#344-객체)
    - [3.4.5 함수](#345-함수)
      - [3.4.5.1 this](#3451-this)
        - [3.4.5.1.1 일반 function](#34511-일반-function)
        - [3.4.5.1.2 화살표 함수](#34512-화살표-함수)
        - [3.4.5.1.3 생성자 함수(new)에 의한 인스턴스의 function](#34513-생성자-함수new에-의한-인스턴스의-function)
      - [3.4.5.2 함수 호이스팅 방지](#3452-함수-호이스팅-방지)
      - [3.4.5.3 책임 분리](#3453-책임-분리)
        - [3.4.5.3.1 데이터 호출 함수](#34531-데이터-호출-함수)
        - [3.4.5.3.2 비즈니스 로직 함수](#34532-비즈니스-로직-함수)
        - [3.4.5.3.3 프레젠테이션 로직 함수](#34533-프레젠테이션-로직-함수)
      - [3.4.5.4 매개변수](#3454-매개변수)
        - [3.4.5.4.1 default 매개변수](#34541-default-매개변수)
        - [3.4.5.4.2 구조분해 할당](#34542-구조분해-할당)
        - [3.4.5.4.3 나머지 매개변수 (...)](#34543-나머지-매개변수)
      - [3.4.6 비동기](#346-비동기)
        - [3.4.6.1 then](#3461-then)
        - [3.4.6.2 async await](#3462-async-await)
        - [3.4.6.3 쓰로틀링과 디바운스](#3463-쓰로틀링과-디바운스)
          - [3.4.6.3.1 디바운스 처리(Debouncing)](#34631-디바운스-처리debouncing)
          - [3.4.6.3.2 쓰로틀링 처리(Throttling)](#34632-쓰로틀링-처리throttling)



# 3. 변수 및 함수 명명 규칙
## 3.1. 변수 명명 규칙
- 변수명은 카멜 표기법을 사용한다.

```javascript
const nowDay = new Date();
const userName = 'ooo';
```
- 첫 문자를 대문자로 시작하여, 의미 구분을 위해 대문자를 사용한다.

## 3.2 함수 명명 규칙
- 카멜 표기법
- 동사 + 명사 [+ By 명사]
- 전역 함수 전치사 gf
- 지역 함수 전치사 mf

## 3.3 HTML Element id명 규칙

## 3.4 JavaScript 문법
### 3.4.1 변수
- 자바스크립트는 동적 타입
- Call By Sharing

#### 3.4.1.1 const와 let
##### 3.4.1.1.1 const
- 변수는 기본적으로 `const`로 정의한다.
```javascript
const foo;
```
##### 3.4.1.1.2 let
- 반복의 인자로써 변수는 `let`로 정의한다.
```javascript
const fooArr = [1, 2, 3, 4];

for(let idx in fooArr){
    console.log(fooArr[idx])
}
```

#### 3.4.1.2 호이스팅
- 변수(함수 포함)의 호이스팅에 주의하여 작성한다.
- 변수들의 선언을, 사용되는 로직과 가까이하여, 쉐도잉을 방지한다.
- `var` 사용을 지양한다.
- 함수 선언시, `const`로 정의한 변수에 할당시켜 사용한다.  

### 3.4.2 불변성
- 모든 객체는 불변성 유지를 지향한다.
- 원본 데이터는, 다시 새 데이터를 입력받아서만 수정한다.
  - [4.5 axios 사용하기 참조]()

#### 3.4.2.1 배열
- 속성 배열의 값이 바뀌었을때, deep copy를 통한 재할당을 지향한다.
- filter, map, reduce 함수 사용을 지향한다.
- 전개연산자 `...` 사용을 지향한다.

##### 3.4.2.1.1 filter
```javascript
const fooArr = [1,2,3,4];
const evenArr = fooArr.filter(e => e % 2 === 0);
```

##### 3.4.2.1.2 map
```javascript
const fooArr = [1,2,3,4];
const doubleArr = fooArr.map(e => e * 2);
```

##### 3.4.2.1.3 reduce
```javascript
const fooArr = [1,2,3,4];
const sum = fooArr.reduce((acc, e) => acc + e, 0);
```

##### 3.4.2.1.4 전개연산자 (`...`)
```javascript
const foo = {
    barArr: [1,2,3,4],
}
// put 5
foo.barArr = [...foo.barArr, 5];
// pop 5
foo.barArr = [...foo.barArr.pop()];
```

### 3.4.3 Null 유효성 관리
- null과 undefined 관리를 지향한다.
- 논리OR(`||`) 와 널병합연산자(`??`)를 적절한 곳에 사용한다.
- 옵셔널 체이닝(`.?`) 활용을 지향한다.

#### 3.4.3.1 논리OR(`||`) VS 널병합연산자(`??`)
```javascript
// 빈 문자열 할당 (논리 평가시 false)
let myText = '';

// # 논리OR
let notFalsyText = myText || 'Hello world';
console.log(notFalsyText); // Hello world

// # 널병합연산자
let preservingFalsy = myText ?? 'Hi neighborhood';
console.log(preservingFalsy); // '' -> undefined나 null이 아니므로!
```

#### 3.4.3.2 옵셔널 체이닝(`.?`)
```javascript
const adventurer = {
  name: 'Alice',
  cat: {
    name: 'Dinah'
  }
};

console.log(adventurer.cat?.name); // Dinah
console.log(adventurer.dog?.name); // undefined
console.log(adventurer.someNonExistentMethod?.()); // undefined
```
[참조: MDN Optional chaining](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Optional_chaining)


### 3.4.4 객체


### 3.4.5 함수
- JavaScript 함수는 다음과 같다.
  - 일반 function
    - 익명 함수
  - 화살표 함수
  - 생성자
  - 메서드

#### 3.4.5.1 this
- this는 함수 선언 방식에 따라, 위치가 다르므로, 사용을 최소화할 것을 추천한다.

##### 3.4.5.1.1 일반 function
- this는 function을 선언한 위치가 된다.
- call by sharing 이므로 this는 공유된다.
- 객체로서 객체 내부의 값을 연산할 필요성이 있을때 추천한다.

```javascript
const name = 'bar';

const foo = {
  name: 'foo',
  getName: function(){
    console.log(this.name);
  },
}

foo.getName(); //foo
```

##### 3.4.5.1.2 화살표 함수
- this는 화살표 함수를 호출한 위치가 된다.
- 모듈의 기능 제공에 추천한다.

```javascript
const name = 'bar';

const foo = {
  name: 'foo',
  getName: () => {
    console.log(this.name);
  },
}

foo.getName(); // bar
```

##### 3.4.5.1.3 생성자 함수(new)에 의한 인스턴스의 function
- this는 생성된 인스턴스를 의미한다.
- 객체 생성 방식을 추천하지 않는다.
  - 하나의 로직 안에서 동일한 구조의 객체를 여러번 생성할 때, 추천한다.
  - 생성자 함수로 사용시, return을 지양한다.

```javascript
const Circle = function (radius){
  this.radius = radius;
  this.getDiameter = function(){
    return 2 * this.radius;
  };
}
const circle1 = new Circle(5);
const circle2 = new Circle(10);

// this는 각각의 인스턴스를 가리킨다.
console.log(circle1.getDiameter()); //10
console.log(circle2.getDiameter()); //20
```


#### 3.4.5.2 함수 호이스팅 방지
- const 로 변수 선언과 할당을 함으로써, 함수의 호이스팅을 방지한다.
```javascript
/* 함수 선언시 호이스팅이 적용되므로, const로 할당함으로써, 혼동을 방지한다.
 (컴파일러의 '=' 기준 우측 표현식 우선 파싱 특징을 이용한, 호이스팅 방지) */
const foo = function(){
  // ~
};
const bar = () => {
  // ~
}
```

#### 3.4.5.3 책임 분리
- 책임에 따라 적확하게 작명한다.
- 콜스택이 쌓이더라도, 하나의 함수는 하나의 동작만 하는 것을 추천한다.
- '데이터 호출' / '비즈니스' / '프레젠테이션'으로 로직을 분리할 것을 추천한다.
- parameter/return을 명확히하고 독립적으로 작성함으로써, 사이드이펙트를 줄이는 방향을 지향한다.

##### 3.4.5.3.1 데이터 호출 함수
- API로 부터 데이터 호출
- 사이드이펙트를 지양한다.
  - input과 output을 명확히 하고, 외부와 독립한다.
- [4.5 axios 사용하기 참조]()

##### 3.4.5.3.2 비즈니스 로직 함수
- 유효성 연산
- 기타 연산
- 사이드이펙트를 지양한다.
  - input과 output을 명확히 하고, 외부와 독립한다.

##### 3.4.5.3.3 프레젠테이션 로직 함수
- DOM 객체 어트리뷰트 / 프로퍼티에 영향
- event 처리


#### 3.4.5.4 매개변수
##### 3.4.5.4.1 default 매개변수
- 작성을 추천한다.
- 타입과 기본값 명시 용도
```javascript
const foo = function(bar = 1){
  console.log(bar);
};
foo(); // 1
```
##### 3.4.5.4.2 구조분해 할당
- 인자를 facade하게 하는 것을 추천한다.
  - 매개변수 갯수는 3개 초과 되지 않는 것을 추천한다.
```javascript
const foo = function({name, age, depart}, kind = '참가자'){
  console.log(kind + '는 ' + age +'살 '+ name + '이며, '+ depart + '입니다.');
}

const bar = {
  name: '철수',
  age: 19,
  depart: '1반',
};
foo(bar, '도전자'); // 도전자는 19살 철수이며, 1반입니다.
```
##### 3.4.5.4.3 나머지 매개변수 (`...`)
- 나머지 매개변수 활용을 추천한다.
```javascript
const foo = function(...members){ // 나머지 매개변수
  const outputString = members.reduce((acc, cur, idx)=> acc + idx + '.' + cur + ' ', '');
  console.log(outputString);  // 0.철수 1.영희 2.짱구 3.훈이 
}

const members = ['철수', '영희', '짱구', '훈이']
foo(...members); // 전개 연산자
```


### 3.4.6 비동기
- 콜백 방식 부정적
  - 콜백 지옥 우려
- 비동기를 위한, 프로미스 처리 기능 추천 (다음 중 택 1)
  1. 프로미스 처리 함수 then
  2. async await
- 쿼리 요청(트래픽)을 줄이고자, 쓰로틀링과 디바운스 처리를 지향한다.

#### 3.4.6.1 then
- 세부적인 프로미스 처리가 가능하다.
#### 3.4.6.2 async await
- try catch와 같이 사용이 용이하다.

#### 3.4.6.3 쓰로틀링과 디바운스 처리
##### 3.4.6.3.1 디바운스(Debouncing)
  - 일정 시간동안 대기, 대기 중 함수가 호출된다면, 대기 시간 초기화
  - Ex) 단어 검색 자동 조회
```javascript
let timer;
document.querySelector('#input').addEventListener('input', function(e) {
    if(timer) {
        clearTimeout(timer);
    }
    timer = setTimeout(function() {
      // ~~~내용~~~
    }, 200);
});
```

##### 3.4.6.3.2 쓰로틀링(Throttling)
  - 마지막 실행후, 일정 시간까지 대기 
  - Ex) 스크롤 다운 추가 조회
```javascript
let timer;
document.querySelector('.body').addEventListener('scroll', function (e) {
  if (!timer) {
    timer = setTimeout(function() {
      timer = null;
      // ~~~내용~~~
    }, 200);
  }
});
```