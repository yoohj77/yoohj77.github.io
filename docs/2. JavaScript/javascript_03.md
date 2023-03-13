---
layout: default
title: 코드품질
parent: JavaScript
nav_order: 3
---

# 코드품질

### 좋은 주석 쓰기

설명이 담긴 주석은 코드 수정시 주석을 항상 함께 수정해 주어야 하기 때문에 좋지 않습니다. 그래서 함수명과 코드만으로 설명이 가능한 것이 좋습니다.

단, 실무적으로 코드가 너무 복잡한 경우에는 불가피하게 주석으로 설명할 수 있습니다

좋은 주석은 설명보다는 다음의 내용을 담습니다

- 아키텍처를 설명하는 주석

  이는 코드만으로 보이지 않는 전체 조감도의 역할을 합니다

  ```jsx
  /* workflow는 task를 호출하고 
  * task에서는 addin을 호출할 수 있다
  */
  ```

- 함수 용례와 매개변수 정보를 담고 있는 주석

  JSDoc 을 사용하여 함수에 관한 주석을 작성하면 좋습니다. 대부분의 에디터가 JSDoc에 대한 편리한 기능들을 제공합니다

  ```jsx
  /**
   * x를 n번 곱한 수를 반환함
   *
   * @param {number} x 거듭제곱할 숫자
   * @param {number} n 곱할 횟수, 반드시 자연수여야 함
   * @return {number} x의 n 거듭제곱을 반환함
   */
  function pow(x, n) {
    ...
  }
  ```

- 왜 이런 방법으로 문제를 해결했는지를 설명하는 주석

  ```jsx
  // 반복호출되는 경우가 많아 캐싱을 사용하였음
  ```

### 테스트 자동화

코드를 수정할 때마다 발생할 수 있는 side effect를 테스트 자동화로 손쉽게 확인하여 기존 기능에 영향이 미치지 않도록 할 수 있습니다

테스트를 자동화하지 않으면 다음의 문제가 발생합니다

1. 아무 대책 없이 코드를 변경합니다. 버그가 발생하기 쉽습니다.
2. 수정이나 개선을 기피하게 됩니다. 코드가 구식이 되어도 누구도 코드를 건드리려 하지 않습니다

테스트 자동화는 이러한 문제를 해결해 줍니다. 자세한 테스트 자동화 방법은 향후에 다룹니다

### 폴리필

자바스크립트 표준에 새로 추가된 기능을 구현한 스크립트를 `폴리필(polyfill)`이라 합니다. 폴리필은 최신 표준을 따르지 못하는 브라우저에 대하여 구현이 누락된 새로운 기능을 매꿔주는 역할을 합니다.

예를 들어, ie와 같은 구식 브라우저에서는 promise가 지원되지 않습니다. 이때 promise를 동일하게 사용할 수 있도록 구현해 줍니다.

최근에는 ie가 서비스 종료되면서 대부분의 브라우저들이 최신 표준을 잘 따르고 빌드 도구에서 자동으로 폴리필을 생성해 주기 때문에 개발자가 직접 폴리필을 구현하는 경우는 잘 없습니다