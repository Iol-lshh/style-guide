> [Home](../README.md)

- [1. IDE](#1-ide-세팅)
    - [1.1. Extensions](#11-extensions)
        - [1.1.1. C#](#111-c)
        - [1.1.2. Vue Volar extension Pack](#112-vue-volar-extension-pack) 
- [2. 소스 가져오기](#2-소스-가져오기)
    - [2.1. Git 설치](#21-git-설치)
    - [2.2. SSL 설정](#22-ssl-설정)
    - [2.3. Git 이메일, 이름 등록](#23-git-이메일-이름-등록)
    - [2.4. Git으로 소스 복제](#24-git으로-소스-복제)

# 1. IDE 세팅
- 명칭: Visual Studio Code

## 1.1. Extensions

### 1.1.1. C#
    - Name: C#
    - Id: ms-dotnettools.csharp
    - Description: C# for Visual Studio Code (powered by OmniSharp).
    - Version: 1.25.4
    - Publisher: Microsoft
[VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

![vue_C#_Extensions](./Docs-Ref/img_Vue_Csharp_Extensions.png)
1. marketplace 탭 선택
2. C# 검색
3. C# 플러그인 설치


### 1.1.2. Vue Volar extension Pack
    - Name: Vue Language Features (Volar)
    - Id: Vue.volar
    - Description: Language support for Vue 3
    - Version: 1.2.0
    - Publisher: Vue

    1. JavaScript (ES6) code snippets
    2. Path Intellisense
    3. Auto Close Tag
    4. Auto Rename Tag
    5. Auto Import
    6. Sass (.sass only) 
        - 미사용
    7. DotENV
    8. SCSS Formatter 
        - 미사용
    9. Prettier - Code formatter
    10. Vue 3 Support - All In One
    11. Vue VSCode Snippets
    12. ESLint
    13. Vue Language Features (Volar)
    14. TypeScript Vue Plugin (Volar) 
        - 미사용
[VS Marketplace Link](https://marketplace.visualstudio.com/items?itemName=Vue.volar)

![vue_Volar_Extensions](./Docs-Ref/img_Volar_Extensions.PNG)
1. marketplace 탭 선택
2. vue 검색
3. Vue Volar extension Pack 플러그인 설치
   
# 2. 소스 가져오기
## 2.1. Git 설치
[Git Download Link](https://git-scm.com/download/win)

## 2.2. SSL 설정
```bash
# SSL 설정
git config --global http.sslbackend schannel
```

## 2.3. Git 이메일, 이름 등록
```bash
# 이름 확인
git config user.name
# 이메일 확인
git config user.email
# 이메일 변경
git config --global user.email <이메일>
```

## 2.4. Git으로 소스 복제
```bash
# 클론
git clone https://git-pms.milkt.co.kr/milkt-core/vue-style-guide.git
# GitLab(원격 레포지토리) 경로의 자격 증명이 필요하다.
```
