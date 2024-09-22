---
layout: single
title:  "바닐라 자바스크립트로 리액트 함수형 컴포넌트 만들기"
category: VanillaJS
tags:
  - JavaScript
toc: true
---

> 부스트캠프에서 마스터로 활동하고 계신 황준일님이 쓰신 [Vanilla Javascript로 가상돔(VirtualDOM) 만들기](https://junilhwang.github.io/TIL/Javascript/Design/Vanilla-JS-Virtual-DOM/){:target="_blank"}를 먼저 읽고 읽으면 이해하기 수월합니다.

## 1. 개발 배경

바닐라 자바스크립트로 프로젝트를 수행하게 되어 Web Components에 styled-components를 모방한 방식으로 스타일을 적용하여 아래와 같이 사용하고 있었습니다.

- index.html

<script src="https://gist.github.com/Gyeongsu1997/514e5909c3bb1e8b7116b2d39868b346.js?file=index.html"></script>

- MyElement.js

<script src="https://gist.github.com/Gyeongsu1997/514e5909c3bb1e8b7116b2d39868b346.js?file=MyElement.js"></script>

- StyledComponents.js

<script src="https://gist.github.com/Gyeongsu1997/514e5909c3bb1e8b7116b2d39868b346.js?file=StyledComponents.js"></script>

순수한 자바스크립트 파일에서는 JSX 문법을 사용할 수 없어 템플릿 리터럴을 중첩해 사용하는 것에 불편을 느끼던 와중에 JSX 문법을 컴파일 해주는 babel이라는 도구를 발견했습니다.

## 2. babel을 이용한 컴파일

babel을 사용해 JSX 문법을 컴파일하기 위해서는 @babel/plugin-transform-react-jsx라는 플러그인을 사용합니다. 자세한 내용은 [여기](https://babeljs.io/docs/babel-plugin-transform-react-jsx#with-a-configuration-file-recommended){:target="_blank"}에서 보실 수 있지만 간략하게 정리하고 넘어가겠습니다. 우선, 아래와 같은 명령어로 npm 패키지를 설치해야 합니다.

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

```json
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

<script src="https://gist.github.com/Gyeongsu1997/514e5909c3bb1e8b7116b2d39868b346.js?file=MyComponent.jsx"></script>

위 파일을 컴파일한 결과는 아래와 같습니다.

<script src="https://gist.github.com/Gyeongsu1997/514e5909c3bb1e8b7116b2d39868b346.js?file=MyComponent.js"></script>

여기서 주목할만한 점이 있습니다. 바로 Wrapper, Title 같은 변수가 h 함수에 첫 번째 인자로 전달된다는 것입니다. 만약 이 변수가 함수라면, 이 함수에 props와 children을 인자로 넣어 호출해 준다면 React의 함수형 컴포넌트를 만들 수 있지 않을까라고 생각했습니다.

## 3. 함수형 컴포넌트 만들기

### (1) 폴더 구조

다음과 같은 구조로 폴더를 구성해보겠습니다.

```
.
├── babel.config.json
├── index.html
├── package.json
├── src
│   ├── App.js
│   ├── components
│   │   ├── Article.js
│   │   ├── ArticleList.js
│   │   └── Header.js
│   ├── core
│   │   ├── internal
│   │   │   ├── dom.js
│   │   │   └── root.js
│   │   ├── react-dom.js
│   │   └── react.js
│   └── index.js
└── static
    └── css
        └── index.css
```

### (2) 설정 파일 및 정적 파일

- package.json

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=package.json"></script>

- babel.config.json

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=babel.config.json"></script>

- index.html

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=index.html"></script>

- static/css/index.css

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=index.css"></script>

### (3) React와 ReactDOM

VirtualDOM을 만들어주는 함수를 h라는 이름 대신 React.createElement라는 이름으로 만들어주겠습니다.

- src/core/react.js

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=react.js"></script>

ReactDOM.render 함수는 루트 엘리먼트와 루트 컴포넌트를 설정하고 처음으로 렌더링을 합니다.

- src/core/react-dom.js

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=react-dom.js"></script>

### (4) Virtual DOM을 실제 DOM으로

root.js는 루트 엘리먼트와 루트 컴포넌트를 변수로 가지고 있습니다.

- src/core/internal/root.js

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=root.js"></script>

여기서 $root은 실제 DOM의 element이고, rootComponent는 Virtual DOM입니다. 따라서 Virtual DOM을 실제 DOM의 element로 만들어주는 과정이 필요합니다. 이를 위해 dom.js에 _createElement라는 함수를 만들었습니다.

- src/core/internal/dom.js

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=dom.js"></script>

핵심적인 부분은 node의 type이 함수라면 props와 children을 담은 객체를 인자로 하여 해당 함수를 호출하고, 그 반환값을 다시 _createElement에 재귀적으로 전달하는 것입니다.

### (5) 컴포넌트 개발

코어 로직은 완성되었습니다. 이제 함수형 컴포넌트를 사용할 수 있습니다. 우리가 만든 함수형 컴포넌트는 위에서 전달한 props를 인자로 받습니다. children prop을 이용하면 컴포넌트의 composition이 가능합니다.

- src/components/Header.js

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=Header.js"></script>

Article 컴포넌트는 title, author, content를 props로 받아 렌더링합니다.

- src/components/Article.js

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=Article.js"></script>

ArticleList 컴포넌트는 props로 전달받은 배열을 순회하며 Article 컴포넌트를 렌더링합니다.

- src/components/ArticleList.js

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=ArticleList.js"></script>

### (6) Entry Point

App 컴포넌트는 위에서 만든 Header 컴포넌트와 ArticleList 컴포넌트를 사용합니다.

- src/App.js

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=App.js"></script>

index.js는 위에서 만든 ReactDOM.render 함수에 루트 엘리먼트와 루트 컴포넌트를 전달합니다.

- src/index.js

<script src="https://gist.github.com/Gyeongsu1997/0195a230616068d47df448b2beb1eec2.js?file=index.js"></script>

### (7) 빌드 및 실행

터미널에서 ```npm run build``` 명령어를 입력하면 아래와 같이 static 폴더 아래에 컴파일된 자바스크립트 파일들이 생성됩니다.

```
.
├── babel.config.json
├── index.html
├── package.json
├── src
│   ├── App.js
│   ├── components
│   │   ├── Article.js
│   │   ├── ArticleList.js
│   │   └── Header.js
│   ├── core
│   │   ├── internal
│   │   │   ├── dom.js
│   │   │   └── root.js
│   │   ├── react-dom.js
│   │   └── react.js
│   └── index.js
└── static
    ├── css
    │   └── index.css
    └── js
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

실행 화면입니다.

<iframe src="https://codesandbox.io/embed/8skfrc?view=preview&hidenavigation=1"
     style="width:100%; height: 500px; border:0; border-radius: 4px; overflow:hidden;"
     title="function-components"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

## Reference

- [Vanilla Javascript로 가상돔(VirtualDOM) 만들기](https://junilhwang.github.io/TIL/Javascript/Design/Vanilla-JS-Virtual-DOM/){:target="_blank"}



