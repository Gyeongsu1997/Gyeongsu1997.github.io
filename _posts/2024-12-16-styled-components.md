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

스타일드 컴포넌트는 대표적인 CSS-in-JS 라이브러리입니다. 스타일드 컴포넌트를 사용하면 CSS 셀렉터가 필요없이 재사용 가능한 컴포넌트를 스타일링 할 수 있습니다.

## 2. Tagged Templates

본격적으로 스타일드 컴포넌트를 구현하기 전에 태그드 템플릿이라는 문법을 알아보겠습니다. ES6에서 추가된 템플릿 리터럴에 대해서는 대부분 익숙하실 것입니다. 태그드 템플릿은 일반적인 템플릿 리터럴 앞에 tag function이라 불리는 함수가 위치하고 있는 형태입니다. 태그드 템플릿에서는 tag function 뒤의 템플릿 리터럴이 분해되어 tag function의 인자로 전달되고 이 함수 안에서 원하는 작업을 수행할 수 있습니다. 사실 tag function을 명시하지 않은 템플릿 리터럴에서는 기본적으로 default function이 사용되고 있는 것입니다. 예시를 들어보겠습니다.

```js
const name = '짐 레이너';
const job = '마사라의 보안관';

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
const name = '짐 레이너';
const job = '마사라의 보안관';

const introduce = (strs, ...exprs) => {
  return strs.reduce((result, str, i) => `${result}${str}${exprs[i] ? exprs[i] : ''}`, '');
};

const str = introduce`제 이름은 ${name}, ${job}입니다.`;
console.log(str);
```

```
제 이름은 짐 레이너, 마사라의 보안관입니다.
```

여기까지만 보면 일반적인 템플릿 리터럴을 사용하는 것과 별로 차이가 없어보입니다. 하지만 표현식으로 전달하는 값이 문자열이나 숫자가 아니라 객체나 함수일 때 태그드 템플릿의 진가는 드러납니다.

```js
const props = {
  name: '짐 레이너',
  job: '마사라의 보안관',
};

const introduce = (strs, ...fns) => {
  return strs.reduce((result, str, i) => `${result}${str}${fns[i] ? fns[i](props) : ''}`, '');
};

const str = introduce`제 이름은 ${props => props.name}, ${props => props.job}입니다.`;
console.log(str);
```

```
제 이름은 짐 레이너, 마사라의 보안관입니다.
```

위처럼 태그드 템플릿을 사용하면 표현식으로 문자열이나 숫자가 아닌 객체나 함수를 넣어도 값을 조회할 수 있습니다.

## 3. Styled Components

### (1) styled

styled-components의 styled 함수는 HTML 요소의 이름을 인자로 받아 tag function으로 사용되는 함수를 반환합니다.

```js
const styled = (tag) => (strs, ...exprs) => {
    const style = strs.reduce((result, str, i) => {
        const isFunc = typeof exprs[i] === 'function';
        const value = isFunc ? exprs[i](props) : exprs[i];
  
        return `${result}${str}${value ? value : ''}`;
      }, '');

    return style;
};

const style = styled('div')`
	width: 200px;
	height: 200px;
	border: 5px solid;
	border-radius: 50%;
	color: white;
	background-color: black;
	text-align: center;
	line-height: 200px;
`;

console.log(style);
```

```

        width: 200px;
        height: 200px;
        border: 5px solid;
        border-radius: 50%;
        color: white;
        background-color: black;
        text-align: center;
        line-height: 200px;

```

그런데 이 함수가 스타일을 문자열로 반환하지는 않습니다. 그 대신 앞서 전달한 HTML 요소를 렌더링하는 함수 컴포넌트를 반환하고, 반환된 함수 컴포넌트가 렌더링되면서 스타일이 적용됩니다.

```js
const styled = (tag) => (strs, ...exprs) => ({ children, ...props }) => {
    const style = strs.reduce((result, str, i) => {
      const isFunc = typeof exprs[i] === 'function';
      const value = isFunc ? exprs[i](props) : exprs[i];

      return `${result}${str}${value ? value : ''}`;
    }, '');

    return React.createElement(tag, { ...props, style }, children);
};
```

아래처럼 사용할 수 있습니다.

<script src="https://gist.github.com/Gyeongsu1997/988944758866b595b9168e728366f359.js?file=App.js"></script>

<iframe src="https://codesandbox.io/embed/nvnky2?view=preview&module=%2Findex.html&hidenavigation=1"
     style="width:100%; height: 500px; border:0; border-radius: 4px; overflow:hidden;"
     title="05-styled-components"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

### (2) styled.div

그런데 위에서 본 styled 함수는 일반적으로 styled-components의 styled 함수를 사용하는 방법과 조금 다릅니다. 일반적으로 사용되는 styed.div와 같은 형식으로 styled 함수를 사용하고 싶으면 어떻게 해야할까요?



## Repository

본 게시글에서 사용된 코드는 [이곳](https://github.com/Gyeongsu1997/create-react-with-vanilla-js/tree/main/04-event-delegation){:target="\_blank"}에서 확인하실 수 있습니다.

## Reference

- [styled-components는 어떻게 동작할까?](https://john015.netlify.app/styled-components%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%8F%99%EC%9E%91%ED%95%A0%EA%B9%8C){:target="_blank"}
