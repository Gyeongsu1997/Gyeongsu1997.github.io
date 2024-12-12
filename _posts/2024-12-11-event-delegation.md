---
layout: single
title:  "바닐라 자바스크립트로 리액트 만들기 - 이벤트 위임"
category: VanillaJS
tags:
  - JavaScript
  - React
toc: true
---

## 1. 문제 발생

지난 [포스팅](https://gyeongsu1997.github.io/vanillajs/diffing-algorithm/){:target="_blank"}에서는 debounce와 diffing algorithm을 적용해 렌더링 성능을 개선했습니다. 그런데 문제가 생겼습니다. 상태 변화가 제대로 적용이 안되는 것 같습니다.

<iframe src="https://codesandbox.io/embed/7tdldc?view=preview&hidenavigation=1"
     style="width:100%; height: 500px; border:0; border-radius: 4px; overflow:hidden;"
     title="03-diffing-algorithm"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
></iframe>

왜 이런 문제가 발생하는 걸까요? 오늘의 포스팅에서는 문제가 발생한 원인을 분석하고 해결해보는 과정을 담았습니다.

## 2. 문제 분석

버튼을 클릭했을 때 숫자가 바뀌는 걸 보면 이벤트 등록은 잘 되는 것 같습니다. 하지만 1과 -1 사이에서만 왔다갔다 할 뿐입니다. 한번 이벤트 리스너 안에서 count 값을 출력해보겠습니다.

<script src="https://gist.github.com/Gyeongsu1997/d6f4e4b88ae7231ef8b4cf55bb54b668.js?file=Counter.js"></script>

![counter]({{site.url}}/images/2024-12-11-event-delegation/counter.gif)

이럴 수가. count 값이 계속 0으로 나옵니다. 도대체 왜 이런 일이 발생하는 걸까요? 문제는 새로 도입한 diffing algorithm과 기존의 이벤트 등록 방식 사이의 간극 때문에 발생합니다.

button 컴포넌트에 onClick prop으로 전달한 이벤트 리스너는 아래의 _setAttributes 함수에서 addEventListener API를 이용해 button 엘리먼트에 등록됩니다.

<script src="https://gist.github.com/Gyeongsu1997/d6f4e4b88ae7231ef8b4cf55bb54b668.js?file=setAttributes.js"></script>

이후 버튼을 클릭하면 상태 변화에 의해 재렌더링되는데 이때 변경이 있는 부분만 DOM에 반영됩니다. 아래의 _updateAttributes 함수에서 달라진 속성만 적용하게 되는데 이벤트 리스너는 속성이 아니므로 이전에 등록된 리스너를 그대로 사용하고 있습니다. 

<script src="https://gist.github.com/Gyeongsu1997/d6f4e4b88ae7231ef8b4cf55bb54b668.js?file=updateAttributes.js"></script>

결국 버튼 엘리먼트에 걸려있는 이벤트 리스너는 새로 만들어진 함수가 아니라 처음 렌더링될 때 만들어진 함수이며 그 안에서 참조하는 count 값도 클로저로 인해 계속 0으로 유지되는 것입니다.

## 3. 첫 번째 시도: window 전역 객체에 이벤트 리스너 등록

문제의 원인은 알아냈습니다. 그렇다면 이제 문제를 해결해야합니다. addEventListener로 이벤트 리스너를 등록하지 말고 window 전역 객체에 등록한 다음 이를 사용하는 방법은 어떨까요? 그러면 재렌더링될 때마다 함수가 새로 정의되지 않을까요? 밑져야 본전이니 한번 시도해 보겠습니다.

<script src="https://gist.github.com/Gyeongsu1997/d6f4e4b88ae7231ef8b4cf55bb54b668.js?file=event-listener-in-window.js"></script>

언뜻 보기에는 잘 되는 것처럼 보입니다.

## 4. 두 번째 시도: 

## Repository

본 게시글에서 사용된 코드는 [이곳](https://github.com/Gyeongsu1997/create-react-with-vanilla-js/tree/main/04-usestate){:target="_blank"}에서 확인하실 수 있습니다.