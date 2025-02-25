---
layout: single
title: "바닐라 자바스크립트로 리액트 만들기 - 렌더링 최적화"
category: VanillaJS
tags:
  - JavaScript
  - React
toc: true
---

> 부스트캠프에서 마스터로 활동하고 계신 황준일님이 쓰신 [Vanilla Javascript로 가상돔(VirtualDOM) 만들기](https://junilhwang.github.io/TIL/Javascript/Design/Vanilla-JS-Virtual-DOM/){:target="_blank"}를 먼저 읽고 읽으면 이해하기 수월합니다.

## 1. 개요

지난 [포스팅](https://gyeongsu1997.github.io/vanillajs/usestate/){:target="_blank"}에서 상태 관리를 위한 useState 훅을 만들어 보았습니다. setState 함수를 호출하면 상태가 바뀌고 컴포넌트가 재렌더링 되는 것을 확인했습니다. 하지만 아직 아래와 같은 두 가지 문제가 남아있습니다.

1. setState가 연속해서 여러 번 호출되면 매번 다시 렌더링을 합니다.
2. 재렌더링을 할 때 상태가 변경되지 않은 컴포넌트까지 다시 렌더링이 됩니다.

오늘의 포스팅에서는 위 두 가지 문제를 해결하고 라이브러리를 개선해 보겠습니다.

## 2. Debounce

### (1) debounce란?

debounce는 함수를 마지막으로 호출한 후 일정 시간이 경과한 후에만 함수가 실행되도록 하는 방법입니다. Web API 중 [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame){:target="_blank"}과 [cancelAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/Window/cancelAnimationFrame){:target="_blank"} 메소드를 이용해 debounce를 구현할 수 있습니다.

requestAnimationFrame 메소드는 인자로 전달받은 콜백 함수를 다음 리페인트 전에 수행하도록 브라우저에 요청합니다. requestAnimationFrame은 number 타입의 requestID를 반환하는데 이 requestID를 cancelAnimationFrame의 인자로 전달하면 이전의 요청을 취소할 수 있습니다.

아래의 debounceFrame 함수에서는 콜백 함수를 인자로 받아 requestAnimtaionFrame과 cancelAnimationFrame을 사용하는 익명 함수를 반환하고 있습니다. greet 함수가 호출되면 클로저를 이용해 requestAnimationFrame에 콜백 함수의 실행을 요청합니다. greet 함수가 다음 리페인트 전에 다시 호출되면 nextFrameCallback 변수에 담긴 requestID를 이용해 기존의 요청을 취소하고 콜백 함수의 실행을 새롭게 요청합니다. 결과적으로 아래 예제에서는 가장 마지막으로 호출한 greet 함수에서만 출력이 실행됩니다.

<script src="https://gist.github.com/Gyeongsu1997/98c2e4def5927fa1b0290cf415eecad9.js?file=debounce.js"></script>

### (2) 렌더링에 debounce 적용

렌더링을 담당하는 함수를 위에서 본 debounceFrame으로 감싸면 짧은 간격으로 여러 번 호출되더라도 마지막으로 호출된 함수만 실행됩니다. 따라서 불필요한 렌더링을 줄일 수 있습니다. 

<script src="https://gist.github.com/Gyeongsu1997/98c2e4def5927fa1b0290cf415eecad9.js?file=render-in-debounce.js"></script>

## 3. Diffing Algorithm

### (1) diffing algorithm이란?

현재는 렌더링을 할 때마다 전체 DOM을 새롭게 만들고 있습니다. 이는 전체 DOM의 리플로우를 유발하므로 브라우저의 렌더링 성능에 치명적입니다. 따라서 변경이 있는 부분만 찾아 다시 렌더링 되도록 할 필요가 있습니다. 리액트에서는 이를 diffing algorithm이라 부릅니다.

diffing algorithm을 만들 때 고려해야할 경우를 아래와 같이 다섯 가지로 나누어 볼 수 있습니다.

1. 자식 노드가 삭제된 경우
2. 자식 노드가 새롭게 추가된 경우
3. 자식 노드가 텍스트 노드인 경우에는 값을 비교해서 달라진 경우에만 값을 덮어씌웁니다.
4. 기존 자식 노드와 새 자식 노드의 태그 이름이 다른 경우에는 속성을 비교할 필요 없이 대체합니다.
5. 기존 자식 노드와 새 자식 노드의 태그 이름이 같은 경우에는 속성을 비교해 달라진 속성만을 반영합니다.

위 내용을 루트 노드부터 시작하여 자식 노드까지 재귀적으로 반복하면 변경이 있는 부분만 루트 노드에 반영됩니다.

### (2) 렌더링에 diffing algorithm 적용

_render 함수에서는 새로 만들어진 루트 노드를 더이상 DOM에 바로 반영하지 않고 기존에 있던 루트 노드와 달라진 점을 비교하기 위해 _diff 함수를 호출합니다.

<script src="https://gist.github.com/Gyeongsu1997/98c2e4def5927fa1b0290cf415eecad9.js?file=render-with-diff.js"></script>

_diff 함수에서는 기존 노드와 새 노드의 자식 노드를 비교하기 위해 _updateElement 함수를 반복해서 호출합니다. _diff 함수의 실행이 끝나면 변경 사항이 기존 노드에 반영됩니다.

<script src="https://gist.github.com/Gyeongsu1997/98c2e4def5927fa1b0290cf415eecad9.js?file=diff.js"></script>

_updateAttributes 함수는 새 자식 노드에서 달라진 속성과 없어진 속성을 기존 자식 노드에 반영하는 역할을 합니다.

<script src="https://gist.github.com/Gyeongsu1997/98c2e4def5927fa1b0290cf415eecad9.js?file=updateAttributes.js"></script>

우리가 구현한 diffing algorithm에서 핵심이 되는 _updateElement 함수에서는 상황을 위에서 본 다섯 가지 경우로 나누어 처리합니다.

<script src="https://gist.github.com/Gyeongsu1997/98c2e4def5927fa1b0290cf415eecad9.js?file=updateElement.js"></script>

두 자식 노드의 태그 이름이 같은 경우에는 달라진 속성을 반영하고 _diff 함수를 재귀적으로 호출하기 때문에 루트 노드부터 리프 노드까지의 변경된 부분이 반영됩니다. 이로써 재렌더링을 할 때 변경이 있는 부분만 DOM에 반영할 수 있습니다.

### (3) 결과

![result]({{site.url}}/images/2024-10-29-diffing-algorithm/result.gif)

위의 실행 화면에서 변경이 있는 부분만 다시 렌더링되는 것을 볼 수 있습니다.

## Summary

오늘의 포스팅에는 debounce와 diffing algorithm을 적용해 렌더링 성능을 개선해보는 과정을 담았습니다. debounce를 통해 setState가 연속해서 여러 번 호출될 때 마지막에 한 번만 렌더링이 일어나도록 했고 diffing algorithm을 통해 재렌더링을 할 때 변경이 있는 부분만 DOM에 반영되도록 했습니다.

이것으로 된 걸까요? 다음 [포스팅](https://gyeongsu1997.github.io/vanillajs/event-delegation/){:target="_blank"}에서 이어집니다.

## Repository

본 게시글에서 사용된 코드는 [이곳](https://github.com/Gyeongsu1997/create-react-with-vanilla-js/tree/03-diffing-algorithm){:target="_blank"}에서 확인하실 수 있습니다.

## Reference

- [Vanilla Javascript로 가상돔(VirtualDOM) 만들기](https://junilhwang.github.io/TIL/Javascript/Design/Vanilla-JS-Virtual-DOM/){:target="_blank"}
