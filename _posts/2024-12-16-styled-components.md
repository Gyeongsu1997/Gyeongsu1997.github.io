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

스타일드 컴포넌트는 대표적인 CSS-in-JS 라이브러리입니다. CSS-in-JS란 자바스크립트 파일 안에서 CSS 스타일을 작성할 수 있게 해주는 기술입니다. 스타일드 컴포넌트를 사용하면 손쉽게 재사용 가능한 UI 컴포넌트를 만들고 동적으로 스타일을 적용할 수 있습니다. 오늘의 포스팅에서는 스타일드 컴포넌트에서 사용되는 태그드 템플릿이라는 문법에 대해 알아본 뒤 스타일드 컴포넌트를 직접 구현해보겠습니다.

## 2. Tagged Templates

ES6에서 추가된 템플릿 리터럴에 대해서는 대부분 익숙하실 것입니다. 태그드 템플릿은 일반적인 템플릿 리터럴 앞에 tag function이라 불리는 함수가 위치하고 있는 형태입니다. 태그드 템플릿에서는 tag function 뒤의 템플릿 리터럴이 분해되어 tag function의 인자로 전달되고 이 함수 안에서 원하는 작업을 할 수 있습니다. 사실 tag function을 명시하지 않은 템플릿 리터럴에서는 기본적으로 default function이 사용되고 있는 것입니다. 예시를 들어보겠습니다.

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

위와 같이 템플릿 리터럴에 포함된 문자열들의 배열이 tag function의 첫 번째 인자로 전달되며 표현식들이 나머지 인자로 전달됩니다. 아래처럼 이 값들을 조합해 하나의 문자열로 만들어 반환할 수 있습니다.

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

여기까지만 보면 일반적인 템플릿 리터럴을 사용하는 것과 별로 차이가 없어보입니다. 하지만 표현식으로 전달하는 값이 문자열이나 숫자가 아니라 객체나 함수일 때 태그드 템플릿의 진가가 드러납니다.

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

위 예제에서 보시다시피 태그드 템플릿을 사용하면 tag function 안에서 표현식으로 전달한 객체나 함수를 사용해 원하는 작업을 할 수 있습니다.

## 3. Styled Components

### (1) 고차 함수 styled

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

사실 이 함수는 스타일을 문자열로 반환하는 대신 앞서 전달한 HTML 요소를 렌더링하는 함수 컴포넌트를 반환합니다. 그리고 반환된 함수 컴포넌트가 렌더링될 때 스타일이 적용됩니다.

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

<script src="https://gist.github.com/Gyeongsu1997/988944758866b595b9168e728366f359.js?file=styled.js"></script>

<iframe src="https://codesandbox.io/embed/nvnky2?view=preview&module=%2Findex.html&hidenavigation=1"
     style="width:100%; height: 500px; border:0; border-radius: 4px; overflow:hidden;"
     title="05-styled-components"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

### (2) styled 함수의 비밀

그런데 위에서 본 styled 함수는 일반적으로 styled-components의 styled 함수를 사용하는 방법과 조금 다릅니다. 일반적으로 사용되는 ```styled.div```와 같은 형식으로 styled 함수를 사용하고 싶으면 어떻게 해야할까요? 여기서 자바스크립트의 함수형 프로그래밍 언어로서의 특성이 잘 드러납니다. 바로 styled 함수에 div 등 HTML 요소의 이름으로 메서드를 만들어주는 것입니다.

```js
const elements = [
  'article',
  'button',
  'div',
  'form',
  'h1',
  'header',
  'img',
  'input',
  'li',
  'p',
  'span',
  'ul',
  ...
];
  
const domElements = new Set(elements);

domElements.forEach(domElement => {
	styled[domElement] = styled(domElement);
});
```

위와 같이 styled 함수에 DOM 요소의 이름으로 메서드를 만들어주면 아래처럼 사용할 수 있습니다.

<script src="https://gist.github.com/Gyeongsu1997/988944758866b595b9168e728366f359.js?file=styled.div.js"></script>

결국 styled-components를 ```styled.div```와 같은 형식으로 사용하는 것은 styled 함수에 메서드로 할당해 놓은 ```styled('div')``` 함수를 사용하게 되는 것입니다.

### (3) 동적 스타일링

앞서 태그드 템플릿을 사용하면 tag function 안에서 표현식으로 전달한 함수를 사용할 수 있다고 했습니다. 이를 이용하면 아래와 같이 표현식으로 함수를 전달하여 props에 따라 스타일이 동적으로 달라지게 만들 수 있습니다.

<script src="https://gist.github.com/Gyeongsu1997/988944758866b595b9168e728366f359.js?file=dynamic-styling.js"></script>

<iframe src="https://codesandbox.io/embed/jwg5pw?view=preview&hidenavigation=1"
     style="width:100%; height: 500px; border:0; border-radius: 4px; overflow:hidden;"
     title="05-styled-components/02-styled"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

## Summary

오늘의 포스팅에서는 스타일드 컴포넌트에서 사용되는 태그드 템플릿이라는 문법에 대해 알아본 뒤 스타일드 컴포넌트를 간단하게 구현해 보았습니다. 하지만 이 게시글에서 구현한 스타일드 컴포넌트는 실제 스타일드 컴포넌트의 동작과는 다릅니다. 실제 스타일드 컴포넌트에서는 인라인 스타일을 사용하지 않고 컴포넌트에 동적으로 생성된 클래스 이름을 부여하며, nesting selector 등 추가적인 기능을 제공합니다. 실제 스타일드 컴포넌트의 동작을 보다 자세히 알고 싶으면 다음 [게시글](https://john015.netlify.app/styled-components%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%8F%99%EC%9E%91%ED%95%A0%EA%B9%8C){:target="_blank"}을 참고하시기 바랍니다.

## Repository

본 게시글에서 사용된 코드는 [이곳](https://github.com/Gyeongsu1997/create-react-with-vanilla-js/tree/05-styled-components){:target="\_blank"}에서 확인하실 수 있습니다.

## Reference

- [styled-components는 어떻게 동작할까?](https://john015.netlify.app/styled-components%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%8F%99%EC%9E%91%ED%95%A0%EA%B9%8C){:target="_blank"}
