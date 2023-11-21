# vue-router 설치
- `npm install --save vue-router`

## createRouter
- 라우팅 기능

## createWebHistory
- 브라우저 내장 기능
- 히스토리 기능 (뒤로가기 등)


- main.js
```js
import { createApp } from 'vue';
import './style.css';
import { createRouter, createWebHistory } from 'vue-router';
import App from './App.vue';
import TeamsList from './components/teams/TeamsList.vue';
import UsersList from './components/users/UsersList.vue';

const router = createRouter({
    history: createWebHistory(), 
    routes: [
        { path: '/teams', component: TeamsList },
        { path: '/users', component: UsersList },
    ],
});


const app = createApp(App);
app.use(router);    // 서드파티를 연결
app.mount('#app');
```

- App.vue
```js
<template>
    <the-navigation @set-page="setActivePage"></the-navigation>
    <main>
        <router-view></router-view>
    </main>
</template>
```

- `<router-view>`
    - 해당 위치로 컴포넌트가 로드되어야 함을, vue 앱에게 알리는 플레이스 홀더의 역할


```js
<nav>
    <ul>
        <li>
            <router-link to="/teams">Teams</router-link>
        </li>
        <li>
            <router-link to="/users">Users</router-link>
        </li>
    </ul>
</nav>
```
- `<router-link></router-link>`
    - 특별한 앵커 태그 - 브라우저 기본값으로 로드되는걸 막고, vue 라우터를 이용하여, 알맞은 컴포넌트를 로드하고, url을 변경해준다.
    - to 프로퍼티 중요!

## $router
- `$router.push('/teams')`
    - 해당 라우팅으로 옮긴다.

### :이름 - 동적 경로 세그먼트
```js
...
import TeamMembers from './components/teams/TeamMember.vue';

const router = createRouter({
    history: createWebHistory(), 
    routes: [
        { path: '/teams', component: TeamsList },
        { path: '/users', component: UsersList },
        { path: '/teams:teamId', component: TeamMembers },
    ],
});
```
```js
const teamId = this.$route.params.teamId
```

### watcher 를 이용하여, $route를 감시할 것
```
watch: {
    $route(newRoute){
        ...
    }
}
```
- 라우터는 create 시에만 동작하기 때문에.
    - 뷰가 동작을 감지 못한다.
    - 때문에 와쳐를 붙여서 동작을 감지하도록 한다.


## 라우팅 옵션

### props
```js
{path, component, props: true}
```
- vue 라우터에 동적 매개변수가 $route 프로퍼티에만 전달되는 게 아니라, 프로퍼티로서 컴포넌트에 전달되도록 한다.
    - 동적매개변수. 경로에 달린 동적 경로 세그먼트

### 리다이렉트 redirect
```js
{path: '/', redirect: '/teams'}
```
- path 를 리다이렉션 한다.
- url이 바뀐다.

### 별칭 alias
```js
{path: '/teams', component: TeamsList, alias: '/'}
```
- alias도 라우팅한다.
- url이 바뀌지 않는다.

### not found 처리
```js
{path: '/:notFound(.*)', redirect: 'teams'}

//or
{path: '/:notFound(.*)', component: 'NotFound'}
```
- 정규식으로 가져온다.

### 자식 라우팅
```js
{
    path: '/teams',
    component: TeamsList,
    children: [
        {path: '/:teamId', component: TeamMembers, props: true}
    ]
}
```
- TeamsList.vue
```js
<template>
    <router-view></router-view>
</template>
```


### 객체로 라우팅
```js
<template>
  <li>
    <h3>{{ name }}</h3>
    <div class="team-members">{{ memberCount }} Members</div>
    <router-link :to="teamMembersLink">View Members</router-link>
  </li>
</template>

<script>
export default {
  props: ['id', 'name', 'memberCount'],
  computed: {
    teamMembersLink() {
      // return '/teams/' + this.id + '?sort=asc';
      return {
        name: 'team-members',
        params: { teamId: this.id },
        query: { sort: 'asc' },
      };
      // this.$router.push({ name: 'team-members', params: { teamId: this.id } });
    },
  },
};
</script>
```

## scrollBehavior(to, from, savedPosition)
- to: this.$route에서 얻을 수 있는 라우트 객체. 이동했던 페이지
- from: this.$route에서 얻을 수 있는 라우트 객체. 이전 페이지
- savedPosition: 뒤로가기 버튼시에만 사용. 이동하기 전의 페이지에서 사용자 스크롤의 위치가 어디였는지 left, top으로 갖고 있다.

```js
scrollBehavior(to, from, savedPosition){
    return { left: 0, top: 0 };
}
```
- 이렇게 함으로써, 페이지 이동시, 맨 위로 올릴 수 있다.

```js
scrollBehavior(to, from, savedPosition){
    if(savedPosition){
        return savedPosition;
    }
    return { left: 0, top: 0 };
}
```
- 이전 위치가 있을때, 이전 페이지 이동시, 해당 위치로 가게 할 수 있다.

## 네비게이션 가드
- 인증과 관련
- 페이지 변화 감지
- 사용자가 양식을 저장하지 않고 실수로 나가는 것을 방지해주기도 한다

- to: 이동할 페이지의 라우트 객체
- from: 이전 페이지의 라우트 객체
- next: 네비게이션 동작을 승인하거나 취소할때, 호출하는 함수

- main.js
```js
router.beforeEach(function(to, from, next){
    next();
    // next(false); // 이동 취소
    // next('등록된 라우트'); // 등록된 라우트로 고정 이동
})
```
- 인증 등의 사용에 유리

 