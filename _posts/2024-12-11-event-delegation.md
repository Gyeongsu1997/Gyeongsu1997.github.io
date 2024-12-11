---
layout: single
title:  "바닐라 자바스크립트로 리액트 만들기 - 이벤트 위임"
category: VanillaJS
tags:
  - JavaScript
  - React
toc: true
---

## 1. 문제 상황

지난 [포스팅](https://gyeongsu1997.github.io/vanillajs/usestate/){:target="_blank"}에서는 debounce와 diffing algorithm을 적용해 렌더링 성능을 개선했습니다. 그런데 문제가 있습니다. 상태 변화가 제대로 적용이 안되는 것 같습니다.

<iframe src="https://codesandbox.io/embed/7tdldc?view=preview&hidenavigation=1"
     style="width:100%; height: 500px; border:0; border-radius: 4px; overflow:hidden;"
     title="03-diffing-algorithm"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

왜 이런 문제가 발생하는 걸까요? 오늘의 포스팅에서는 문제가 발생한 원인을 분석하고 해결해보는 과정을 담았습니다.

## 2. 문제 분석

버튼을 클릭했을 때 숫자가 바뀌는 걸 보면 이벤트 등록은 제대로 되는 것 같습니다. 하지만 1과 -1 사이에서만 왔다갔다 할 뿐입니다. 이벤트 리스너 안에서 count 값을 한번 출력해보겠습니다.

이럴 수가. count 값이 0으로 나옵니다. 도대체 왜 이런 일이 발생하는 걸까요? diffing algorithm

onClick prop으로 전달한 이벤트 리스너는 아래의 _setAttributes 함수에서 addEventListener를 이용해 button 엘리먼트에 등록됩니다.

<script src="https://gist.github.com/Gyeongsu1997/d6f4e4b88ae7231ef8b4cf55bb54b668.js?setAttributes.js"></script>

button 엘리먼트는 속성이 달라지지 않았기 때문에 재렌더링 과정에서 새롭게 만들어지지 않는 것입니다. 결국 클로저가 발생해 이벤트 리스너 안에서 참조하는 계속 0으로 유지되는 것입니다.

## 3. 첫 번째 시도: window 전역 객체에 함수로 등록

문제의 원인은 알아냈습니다. 그렇다면 이제 문제를 해결해야합니다.

## 4. 두 번째 시도: 

## Repository

본 게시글에서 사용된 코드는 [이곳](https://github.com/Gyeongsu1997/create-react-with-vanilla-js/tree/main/04-usestate){:target="_blank"}에서 확인하실 수 있습니다.