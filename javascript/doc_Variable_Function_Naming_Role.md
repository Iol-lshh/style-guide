
> [Home](../README.md)

- [3. 변수 및 함수 명명 규칙](#3-변수-및-함수-명명-규칙)
  - [3.1. 변수 명명 규칙](#31-변수-명명-규칙)
  - [3.2. 함수 명명 규칙](#32-함수-명명-규칙)
  - [3.3 HTML Element id명 규칙](#33-html-element-id명-규칙)


# 3. 변수 및 함수 명명 규칙
## 3.1. 변수 명명 규칙
- 변수명은 카멜 표기법을 사용한다.

```javascript
const nowDay = new Date();
const userName = 'ㅇㅇㅇ';
```
- 첫 문자를 대문자로 시작하여, 의미 구분을 위해 대문자를 사용한다.

## 3.2 함수 명명 규칙
- 카멜 표기법
- 동사 + 명사 [+ By 명사]
- 전역 함수 전치사 gf
- 지역 함수 전치사 mf

## 3.3 HTML Element id명 규칙
- HTML 객체의 id는 언더바 표기법을 사용한다.
- 언어바 이후 명칭의 첫자리는 대문자로 시작한다.

```html
<input type=”text” id=”txt_Title” />
<div id=”div_Name”>이름</div>
<span id=”spn_Content”>내용</span>
```

|Element Type|ID 규칙|
|:---:|:---:|
|DIV|div_Name|
|SPAN|spn_Name|
|SELECT|sel_Name|
|LABEL|lbl_Name|
|RADIO|rdo_Name|
|CHECKBOX|chk_Name|
