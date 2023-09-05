> [Home](../README.md)

- [Vue.js 작성 가이드](#vuejs-작성-가이드)
    - [1. 기본 구성](#1-기본-구성)
    - [2. vue.common.js 활용](#2-vuecommonjs-활용)
        - [2.1 공통 컴포넌트 및 함수 등록](#21-공통-컴포넌트-및-함수-등록)
    - [3. 라우터의 활용](#3-라우터의-활용)
        - [3.1 라우터의 구조](#31-라우터의-구조)
        - [3.2 setup](#32-setup)
            - [3.2.1 reactive](#321-reactive)
            - [3.2.2 ref](#322-ref)
            - [3.2.3 route](#323-route)
            - [3.2.4 router](#324-router)
    - [4. axios](#4-axios)
        - [4.1 비동기 API 호출](#41-비동기-api-호출)
        - [4.2 axios 확장 함수](#42-axios-확장-함수)
        - [4.3 axios 요청 전 실행 함수](#43-axios-요청-전-실행-함수)
        - [4.4 axios 응답 후 실행 함수](#44-axios-응답-후-실행-함수)
    - [5. 인증/인가 함수](#5-인증인가-함수)
    - [6. Spinner](#6-spinner)
    - [7. xlsx](#7-xlsx)

# Vue.js 작성 가이드
# 1. 기본 구성
- vue.js 버전은 3.0 이상을 이용한다.
- 2.7에서 사용되던 data, mounted, watch, computed 등은 setup 함수 내에서 구현한다.
- ajax 통신은 axios를 이용한다.

# 2. vue.common.js 활용
- `/Content/Js/vue.common.js` 파일은 vue.js로 개발하면서 발생하는 전역변수(전역함수) 및 전역 컴포넌트 등을 담는다.

- `gfPaging` [컴포넌트]: 게시판 페이징 구현에 사용하는 공통 컴포넌트
- `gfFileUpload` [컴포넌트]: 파일 업로드 처리하는 공통 컴포넌트
- `gfSpinner` [함수]: axios 등에서 처리시, progress bar를 출력하는 공통 함수
- `gfTrySubmit` [함수]: submit form을 시도하는 공통 함수
- `gfSubmitForm` [함수]: 파일 전송을 제공하는 submit form 공통 함수

## 2.1 공통 컴포넌트 및 함수 등록
- vue.js에서 사용하고자 하는 기본 함수는 Vue 객체로 부터, 구조분해 할당을 통해, import 한다.
```javascript
const { createApp, onMounted, onUpdated, ref, reactive, computed, watchEffect } = Vue;
const { createWebHistory, createRouter, useRoute, useRouter } = VueRouter;
```

- 공통 함수 및 변수는 global function의 약자인 `gf`를 접두어로 붙인다.
```javascript
function gfFileUploadAndDataSend(url, arg) {
    const el = document.querySelectorAll('input[type="file"]');
    const formData = new FormData();
    // ~~~
}
```

# 3. 라우터의 활용
- 라우터란 실제 페이지 이동이 없이 `History.State`를 변경하며 동작한다.
- URL 별로 Template가 출력된다.

## 3.1 라우터의 구조
```javascript
const router = createRouter({       // 라우터를 선언한다.
    history: createWebHistory(),    // createWebHistory 메소드는 일반 URL 패턴을 대체하여 동작된다.
    routes: [
        {
            path: "/Home/View",     // 라우터 URL을 설정
            name: "View",           // 라우터 명칭을 설정
            component: {
                template: '#templ_View',    // Template의 ID 값 또는 내용
                setup: function() {          // setup() 메소드 내에서 모든 작업이 이루어진다.
                    const $router = useRouter();
                    const $route = useRoute();
                    const $this = ref({});
                    const idx = $route.query.idx;
                    const pageNo = $route.query.pageno;
                    const key = $route.query.key;
                    const val = $route.query.val;

                    gfSpinner();

                    axios
                        .post("http://localhost:9090/home/jsonView", { "idx": idx })
                        .then(r=>{
                            if (r && r.data) {
                                $this.value = r.data;
                            }
                        }).catch{e=>{
                            alert(e);
                        }});

                    const mfList = function() {
                        $router.push({ "name": "Index", "query": { "pageno": pageNo, "key": key, "val": val } };)
                    }

                    const mfModify = function() {
                        $router.push({ "name": "Index", "query": { "pageno": pageNo, "key": key, "val": val , "idx": idx } };)
                    }

                    return {
                        "data": $this,      // 변수(함수)를 리턴하여, Template에서 사용할 수 있다.
                        mfList,
                        mfModify,
                    };
                }
            }
        },{
            // 다른 route 페이지
        }
    ]
})
```

## 3.2 setup
- 2.7 버전과 다르게 3.0 버전에서는 `setup()`에서 모두 선언 가능하다.
- setup 내부에서 onMounted, onUpdated, watch와 같은 기존 뷰 메서드를 실행한다.
- 변수/메서드를 리턴하여 Template에서 이용할 수 있다.


### 3.2.1 reactive
- reactive는 연관 배열(또는 일반 배열) 형식의 값을 리턴할 때 사용한다.

```javascript
setup: function(props) {
    const $this = reactive({    // 연관 배열을 다음과 같이 reactive 안에 인자로 준다.
        startPage: 0,
        endPage: 0,
        pageCount: 0,
        pageList: [],
    });

    watchEffect(function() {
       // ~~~ 
    });

    return {
        "data": $this,          // 변수(함수)를 리턴하여, Template에서 사용할 수 있다.
    }
}
```

### 3.2.2 ref
- ref는 reactive와 동일한 기능이나, 모든 타입의 속성을 포함할 수 있다.

```javascript
const $this = ref({});
```

- 값을 초기화 하거나, 가져올 때, value property를 사용한다.
```javascript
const id = $this?.value?.UserID ?? "";
const title = $this?.value?.Tittle ?? "";
const content = $this?.value?.Content ?? "";
```

- Template에서 이용시, value property를 생략한다.
```html
<tr>
    <th>아이디</th>
    <td>{{data.UserID}}</td>
</tr>
```

### 3.2.3 route
- `route()`는 라우터에 보낸 파라미터 정보를 받아올 수 있다.

```javascript
// 1. 선언한다
const $route = useRoute();
// ~

// 2. 파라미터 값을 받아, 변수에 담는다
const idx = $route.query.idx ?? ""
const pageNo = $route.query.pageno ?? "1";
const key = $route.query.key ?? "";
const val = $route.query.val ?? "";
```

### 3.2.4 router
- 다른 라우터로 이동시, `router()`를 이용한다.

```javascript
// 1. 선언한다.
const $router = useRouter();
// ~

// 2. 이동하려는 경로와 파라미터를 설정한다.
function mfView() {
    if (idx) {
        $router.push({ "name": "View", "query": { "pageno": pageNo, "key": key, "val": val , "idx": idx } };
    } else {
        $router.push({ "name": "Index", "query": { "pageno": pageNo, "key": key, "val": val } };
    }
}
```

# 4. axios
- API로부터 JSON 데이터를 받을 때 사용한다.

## 4.1 비동기 API 호출

```javascript
axios
    .post("http://localhost:9090/home/jsonView", { "idx": idx })
    .then( r => {   // 결과를 받아 처리
        if (r && r.data) {
            $this.value = r.data;
        }
    }).catch( e => {    // 오류시 처리
        alert(e);
    });
```
## 4.2 axios 확장 함수
- [/Content/Js/vue.common.js](../Web/Content/Js/vue.common.js)
- 로그인 관련 함수들을 추가

- `axios.getAccess(url, username, password)`
    - username, password로 인증 (로그인::Access 토큰 요청)
- `axios.refreshAccess(url)`
    - Access가 만료되었을 때, Refresh 토큰을 이용한 Access 토큰 요청
- `axios.logoutAccess(url)`
    - Refresh 토큰 폐기 요청 (Refresh 토큰에 대해 로그아웃)
- `axios.logoutUser(url)`
    - username을 통한 Refresh 토큰 폐기 요청 (전체 로그아웃)

## 4.3 axios 요청 전 실행 함수
```javascript
    axios.interceptors.request.use(
        // 요청 전, 실행
        function(config){
            gfSetAxiosAccessHeader();
            return config;
        },
        // 요청 전, 오류시 실행
        function(error){
            return Promise.reject(error);
        }
      );
```

## 4.4 axios 응답 후 실행 함수
```javascript
    axios.interceptors.response.use(
        // 응답 후, 실행
        function(response){
            return response;
        },
        // 응답 후, 오류시 실행
        async function(error){
            
            // access 만료 응답을 받았을 때만, refresh 요청 및 재 요청
            if(error.response.status === 401 || error.response.status === 403){
                return await gfTryAgain(error);
            }
            return Promise.reject(error);
        }
    );
```

# 5. 인증/인가 함수
- `gfSetAxiosAccessHeader()`
    - 쿠키의 A_T(Access 토큰)를 Axios의 default header에 등록
    - [axios 요청 전 함수](#543-axios-요청-전-실행-함수)에서 사용

- `gfSetAccessToken(A_T)`
    - 쿠키에 A_T를 쓰기

- `gfSetRefreshToken(R_T)`
    - 쿠키에 R_T(Refresh 토큰)를 쓰기

- `gfTryAgain(beforeError)`
    - Access 만료로 실패시, Refresh를 자동으로 요청하고, 다시 Request
    - [axios 응답 후 함수](#544-axios-응답-후-실행-함수)에서 사용

# 6. Spinner
- axios로 데이터 처리시 등의 처리 딜레이가 발생하는 경우, progress bar를 출력한다.

![img_Progress_Bar](./Docs-Ref/img_Progress_Bar.PNG)

```javascript
// 소스코드에서 axios 동작 직전에 호출한다.
gfSpinner();
axios
    .post("http://localhost:9090/home/jsonView", { "idx": idx })
    .then(
        // ~~
```
## 6.1 v-cloak
- v-cloak 적용시, vue가 데이터가 적용 되기 전까지, 지정한 스타일을 따르게 한다.
```css
[v-clock] {
  display: none;
}
```
```HTML
<div v-if="false" v-cloak>TEST</div>
```
```javascript
function gfSpinner()
{
    const spinner = document.getElementsByClassName("spinner-grow")[0];

    axios.interceptors.request.use(function (config) {
        spinner.style.display = "block";
        return config;
    }, function (error) {
        spinner.style.display = "none";
        return Promise.reject(error);
    });

    axios.interceptors.response.use(function (response) {
        spinner.style.display = "none";
        return response;
    }, function (error) {
        spinner.style.display = "none";
        return Promise.reject(error);
    });
}
```

## 7. xlsx
- `https://cdnjs.com/libraries/xlsx`
