---
layout: single
title: "바닐라 자바스크립트로 리액트 만들기 - Styled Components"
category: VanillaJS
tags:
  - JavaScript
  - React
toc: true
---

## 1. 개요

아마 지금까지 구현한 라이브러리만으로도 웬만한 싱글 페이지 애플리케이션을 만드는 데는 무리가 없을 겁니다. 하지만 뭔가 아쉽습니다. 스타일드 컴포넌트는 대표적인 CSS-in-JS 라이브러리입니다. 스타일드 컴포넌트를 사용하면 CSS 셀렉터가 필요없이 재사용 가능한 컴포넌트를 스타일링 할 수 있습니다.

## 2. Tagged templates

본격적으로 스타일드 컴포넌트를 구현하기 전에 태그드 템플릿이라는 문법을 알아보겠습니다. ES6에서 추가된 템플릿 리터럴에 대해서는 대부분 익숙하실 것입니다. 태그드 템플릿은 일반적인 템플릿 리터럴 앞에 tag function이라 불리는 함수가 위치하고 있는 형태입니다. 태그드 템플릿에서는 tag function 뒤의 템플릿 리터럴이 분해되어 tag function의 인자로 전달되고 이 함수 안에서 원하는 작업을 수행할 수 있습니다. 사실 tag function을 명시하지 않은 템플릿 리터럴에서는 기본적으로 default function이 사용되고 있는 것입니다. 예시를 들어보겠습니다.

```js
const name = "짐 레이너";
const job = "마사라의 보안관";

const introduce = (strs, ...exprs) => {
  console.log(strs);
  console.log(exprs);
};

introduce`제 이름은 ${name}, ${job}입니다.`;
```

```
[ '제 이름은 ', ', ', '입니다.' ]
[ '짐 레이너', '마사라의 보안관' ]
```

위와 같이 템플릿 리터럴에 포함된 문자열들의 배열이 tag function의 첫 번째 인자로 전달되며 표현식들이 나머지 인자로 전달됩니다. 아래와 같이 이 값들을 조합해 하나의 문자열로 만들어 반환할 수 있습니다.

```js
const name = "짐 레이너";
const job = "마사라의 보안관";

const introduce = (strs, ...exprs) => {
  return strs.reduce(
    (result, str, i) => `${result}${str}${exprs[i] ? exprs[i] : ""}`,
    ""
  );
};

const str = introduce`제 이름은 ${name}, ${job}입니다.`;
console.log(str);
```

```
제 이름은 짐 레이너, 마사라의 보안관입니다.
```

여기까지만 보면 일반적인 템플릿 리터럴을 사용하는 것과 별로 차이가 없어보입니다. 태그드 템플릿의 진가는 드러납니다. 태그드 템플릿을 사용하면 표현식으로 문자열이나 숫자가 아닌 객체나 함수를 넣어도 값을 조회할 수 있습니다.

```js
const props = {
  name: "짐 레이너",
  job: "마사라의 보안관",
};

const introduce = (strs, ...fns) => {
  return strs.reduce(
    (result, str, i) => `${result}${str}${fns[i] ? fns[i](props) : ""}`,
    ""
  );
};

const str = introduce`제 이름은 ${(props) => props.name}, ${(props) =>
  props.job}입니다.`;
console.log(str);
```

```
제 이름은 짐 레이너, 마사라의 보안관입니다.
```

## Repository

본 게시글에서 사용된 코드는 [이곳](https://github.com/Gyeongsu1997/create-react-with-vanilla-js/tree/main/04-event-delegation){:target="\_blank"}에서 확인하실 수 있습니다.

## Reference
