---
layout: single
title:  "바닐라 자바스크립트로 리액트 함수형 컴포넌트 만들기"
---

## 개발 배경

바닐라 자바스크립트로 프로젝트를 수행하게 되어 Web Components에 styled-components를 모방한 방식으로 스타일을 적용하여 아래와 같이 사용하고 있었습니다.

- index.html

<script src="https://gist.github.com/Gyeongsu1997/4b224f6f158c792ff199d6e52d256c7b.js?file=index.html"></script>

- src/MyElement.js

<script src="https://gist.github.com/Gyeongsu1997/4b224f6f158c792ff199d6e52d256c7b.js?file=MyElement.js"></script>

- src/StyledComponents.js

<script src="https://gist.github.com/Gyeongsu1997/4b224f6f158c792ff199d6e52d256c7b.js?file=StyledComponents.js"></script>

순수한 자바스크립트 파일에서는 JSX 문법을 사용할 수 없어 템플릿 리터럴을 중첩해 사용하는 것에 불편을 느끼던 와중에 JSX 문법을 컴파일 해주는 babel이라는 도구를 발견했습니다.

## babel을 이용한 컴파일

babel을 사용해 JSX 문법을 컴파일하기 위해서는 @babel/plugin-transform-react-jsx라는 플러그인을 사용합니다. 자세한 내용은 [여기](https://babeljs.io/docs/babel-plugin-transform-react-jsx#with-a-configuration-file-recommended)에서 보실 수 있지만 간략하게 정리하고 넘어가겠습니다. 우선, 아래와 같은 명령어로 npm 패키지를 설치해야 합니다.

```
npm install --save-dev @babel/core @babel/cli @babel/plugin-transform-react-jsx
```

다음으로 babel.config.json파일을 만들어 플러그인 설정을 합니다.

- babel.config.json

<script src="https://gist.github.com/Gyeongsu1997/4b224f6f158c792ff199d6e52d256c7b.js?file=babel.config.json"></script>




