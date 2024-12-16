---
layout: single
title:  "바닐라 자바스크립트로 리액트 만들기 - Styled Components"
category: VanillaJS
tags:
  - JavaScript
  - React
toc: true
---

## 1. 개요

아마 지금까지 구현한 라이브러리만으로도 웬만한 싱글 페이지 애플리케이션을 만드는 데는 무리가 없을 겁니다. 하지만 뭔가 아쉽습니다. 스타일드 컴포넌트는 대표적인 CSS-in-JS 라이브러리입니다. 스타일드 컴포넌트를 사용하면 CSS 셀렉터가 필요없이 재사용 가능한 컴포넌트를 스타일링 할 수 있습니다.

## 2. Tagged templates

본격적으로 스타일드 컴포넌트를 구현하기 전에 [tagged templates](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates){:target="_blank"}라는 문법을 알아보겠습니다. ES6에서 추가된 template literals에 대해서는 대부분 익숙하실 것입니다. tagged templates은 발전된 형태의 template literals입니다.

<!-- tagged templates은 tag function -->

```js
const name = '짐 레이너';
const job = '마사라의 보안관';

const introduce = (texts, ...values) => {
  console.log(texts);
  console.log(values);
}

introduce`제 이름은 ${name}, ${job}입니다.`
```

```
[ '제 이름은 ', ', ', '입니다.' ]
[ '짐 레이너', '마사라의 보안관' ]
```

```js
const name = '짐 레이너';
const job = '마사라의 보안관';

const introduce = (texts, ...values) => {
   return texts.reduce((result, text, i) => `${result}${text}${values[i] ? values[i] : ''}`, '');
}
const str = introduce`제 이름은 ${name}, ${job}입니다.`
console.log(str);
```

```
제 이름은 짐 레이너, 마사라의 보안관입니다.
```

Tagged Template Literal의 진가는 드러납니다. Tagged Template Literal을 사용하면 만약 ${} 안에 문자열이나 숫자가 아닌 객체나 함수를 넣어도 값을 조회할 수 있습니다.

## Repository

본 게시글에서 사용된 코드는 [이곳](https://github.com/Gyeongsu1997/create-react-with-vanilla-js/tree/main/04-event-delegation){:target="_blank"}에서 확인하실 수 있습니다.

## Reference