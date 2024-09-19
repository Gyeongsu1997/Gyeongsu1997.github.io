---
layout: single
title:  "바닐라 자바스크립트로 리액트 함수형 컴포넌트 만들기"
---

부스트캠프에서 마스터로 활동하고 계신 황준일님이 쓰신 [게시글](https://junilhwang.github.io/TIL/Javascript/Design/Vanilla-JS-Virtual-DOM/)을 먼저 읽고 읽으면 이해하기 수월합니다.

## 개발 배경

바닐라 자바스크립트로 프로젝트를 수행하게 되어 Web Components에 styled-components를 모방한 방식으로 스타일을 적용하여 아래와 같이 사용하고 있었습니다.

- index.html

<script src="https://gist.github.com/Gyeongsu1997/4b224f6f158c792ff199d6e52d256c7b.js?file=index.html"></script>

- MyElement.js

<script src="https://gist.github.com/Gyeongsu1997/4b224f6f158c792ff199d6e52d256c7b.js?file=MyElement.js"></script>

- StyledComponents.js

<script src="https://gist.github.com/Gyeongsu1997/4b224f6f158c792ff199d6e52d256c7b.js?file=StyledComponents.js"></script>

순수한 자바스크립트 파일에서는 JSX 문법을 사용할 수 없어 템플릿 리터럴을 중첩해 사용하는 것에 불편을 느끼던 와중에 JSX 문법을 컴파일 해주는 babel이라는 도구를 발견했습니다.

## babel을 이용한 컴파일

babel을 사용해 JSX 문법을 컴파일하기 위해서는 @babel/plugin-transform-react-jsx라는 플러그인을 사용합니다. 자세한 내용은 [여기](https://babeljs.io/docs/babel-plugin-transform-react-jsx#with-a-configuration-file-recommended)에서 보실 수 있지만 간략하게 정리하고 넘어가겠습니다. 우선, 아래와 같은 명령어로 npm 패키지를 설치해야 합니다.

```
npm install --save-dev @babel/core @babel/cli @babel/plugin-transform-react-jsx
```

다음으로 babel.config.json파일을 만들어 플러그인 설정을 합니다.

```json
{
	"plugins": ["@babel/plugin-transform-react-jsx"]
}
```

src 디렉토리의 파일을 컴파일한 결과가 lib 디렉토리에 생성되게 하려면 CLI에서 아래 명령어를 입력하면 됩니다.

```
npx babel src --out-dir lib
```

package.json 파일에 scripts로 등록해 놓고 사용하면 편리합니다.

```
{
  "scripts": {
    "build": "npx babel src --out-dir lib"
  }
}
```

이것으로 환경 설정은 모두 끝났습니다. 하지만 JSX 문법으로 작성된 파일을 컴파일하기 위해서는 아래와 같은 시그니처를 갖는 함수가 필요합니다. 이 함수의 역할은 Virtual DOM을 만들어주는 것입니다.

```js
function h(type, props, ...children) {
	return { type, props, children: children.flat() };
}
```

또한, 컴파일할 파일 상단에 다음과 같은 주석을 달아 주어야 합니다.

```js
/** @jsx h */
```

아래처럼 사용할 수 있습니다.

<script src="https://gist.github.com/Gyeongsu1997/4b224f6f158c792ff199d6e52d256c7b.js?file=MyComponent.jsx"></script>

위 파일을 컴파일한 결과는 아래와 같습니다.

<script src="https://gist.github.com/Gyeongsu1997/4b224f6f158c792ff199d6e52d256c7b.js?file=MyComponent.js"></script>

여기서 주목할만한 점이 있습니다. 바로 Wrapper, Title 같은 변수가 h 함수에 첫 번째 인자로 전달된다는 것입니다. 만약 이 변수가 함수라면, 이 함수에 props와 children을 인자로 넣어 호출해준다면 React의 함수형 컴포넌트를 만들 수 있지 않을까라고 생각했습니다.

## 함수형 컴포넌트 만들기

1. 폴더 구조

다음과 같은 구조로 폴더를 구성해보겠습니다.

```
.
├── babel.config.json
├── index.css
├── index.html
├── package.json
└── src
    ├── App.js
    ├── components
    │   ├── Article.js
    │   ├── ArticleList.js
    │   └── Header.js
    ├── core
    │   ├── internal
    │   │   ├── dom.js
    │   │   └── root.js
    │   ├── react-dom.js
    │   └── react.js
    └── index.js
```

- babel.config.json

<script src="https://gist.github.com/Gyeongsu1997/405c8ae383bda9bcf5a36ec256682574.js?file=babel.config.json"></script>

- index.css

<script src="https://gist.github.com/Gyeongsu1997/405c8ae383bda9bcf5a36ec256682574.js?file=index.css"></script>

- index.html

<script src="https://gist.github.com/Gyeongsu1997/405c8ae383bda9bcf5a36ec256682574.js?file=index.html"></script>

- package.json

<script src="https://gist.github.com/Gyeongsu1997/405c8ae383bda9bcf5a36ec256682574.js?file=package.json"></script>

2. React와 ReactDOM

VirtualDOM을 만들어주는 함수를 h라는 이름 대신 React.createElement라는 이름으로 만들어주겠습니다.

- src/core/react.js

<script src="https://gist.github.com/Gyeongsu1997/405c8ae383bda9bcf5a36ec256682574.js?file=react.js"></script>

react-dom.js는 루트 엘리먼트와 루트 컴포넌트를 설정하고 처음으로 렌더링하는 역할을 합니다.

- src/core/react-dom.js

<script src="https://gist.github.com/Gyeongsu1997/405c8ae383bda9bcf5a36ec256682574.js?file=react-dom.js"></script>

가장 먼저 필요한 것은 Virtual DOM을 실제 DOM으로 만들어주는 것입니다.

- src/core/internal/dom.js

<script src="https://gist.github.com/Gyeongsu1997/405c8ae383bda9bcf5a36ec256682574.js?file=dom.js"></script>

_createElement 함수는 아래와 같은 형태의 Virtual DOM을 인자로 받아 실제 DOM의 element를 만들어 반환합니다.

```js
{
  type: [Function (anonymous)],
  props: null,
  children: [
    { type: [Function (anonymous)], props: null, children: [Array] },
    { type: 'p', props: null, children: [Array] }
  ]
}
```


