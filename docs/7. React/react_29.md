---
layout: default
title: 참고사항
parent: React
nav_order: 28
---

# 참고사항

### React.PureComponent

클래스형 컴포넌트를 구현할 때 `React.Component` 대신에 `React.PureComponent` 를 상속받아 구현할 수 있습니다

React.Component는 항상 리렌더링이 이루어지는 반면에 React.PureComponent는 props와 state가 동일하면 리렌더링이 이루어지지 않으며 동일한지 비교할 때는 얕은 비교를 수행합니다

### Create React App 설정 꺼내기

`npm run eject` 로 eject 스크립트를 실행하면 숨겨져 있던 모든 설정이 드러나게 되며 한번 실행한 후에는 다시는 이전으로 돌아갈 수 없습니다

설정을 한번 확인해 보거나 수정하고 싶을 때 사용하면 됩니다