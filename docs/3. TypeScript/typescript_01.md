---
layout: default
title: 타입스크립트 소개
parent: TypeScript
nav_order: 1
---

# 타입스크립트 소개

`타입스크립트`는 자바스크립트에 타입을 부여한 언어로서 2012년에 Microsoft에서 오픈소스로 발표한 자바스크립트의 확장된 언어(Superset) 입니다

자바스크립트는 동적 타입 언어로서 값이 할당되는 시점에 변수에 타입이 결정되며, 변수의 타입을 언제든지 자유롭게 변경할 수 있습니다. 하지만 이러한 자유도와 유연성이 오히려 신뢰성을 떨어뜨리고 많은 단점을 야기합니다.

이를 해결하기 위해 자바스크립트를 정적 타입 언어로 바꾼 것이 타입스크립트입니다. 그렇기 때문에 다음과 같이 정적 타입의 장점을 모두 가지고 있습니다. 또한, 자바스크립트의 상위확장이기 때문에 자바스크립트 표준의 모든 기능을 사용할 수 있으며 ECMAScript 표준 업그레이드에 따라 기능을 지속적으로 추가하고 있습니다

특히, 과거와 달리 자바스크립트를 통해 더욱 큰 어플리케이션을 개발하게 되고 더 많은 협업을 하게 되면서 타입스크립트를 채택하는 경우가 많아지고 있는 추세입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cc981b4f-6a13-442f-b798-d4511b6d4e73/Untitled.png)

### 에러의 사전 방지

타입체크를 통해 에러를 사전에 방지할 수 있습니다

```jsx
function sum(a, b) {
  return a + b;
}
sum('10', '20'); //1020
```

위의 코드에서 sum은 숫자를 더하는 의도로 만든 함수이기 때문에 잘못된 코드이지만 실제 실행하기 전까지는 오류가 발생하지 않습니다

```jsx
function sum(a: number, b: number) {
  return a + b;
}
sum('10', '20'); // Error: '10'은 number에 할당될 수 없습니다.
```

위와 같이 typescript를 사용하면 바로 에러가 발생하여 의도치 않은 동작을 사전에 방지할 수 있어 안정성을 향상시킵니다

### 코드 자동 완성과 가이드

타입이 지정되어 있지 않으면 사용할 수 있는 프로퍼티, 메서드를 알 수가 없기 때문에 코드 자동 완성이나 가이드가 동작할 수 없습니다

```jsx
function sum(a, b) {
  return a + b;
}
let total = sum(10, 20);
total.toLocaleString();
```

위의 코드에서 `toLocalString()` 메서드는 가이드되지 않기 때문에 직접 대소문자를 맞추어 정확히 작성해야만 합니다

```jsx
function sum(a: number, b: number): number {
  return a + b;
}
let total = sum(10, 20);
total.toLocaleString();
```

위의 코드에서는 `total`의 타입이 숫자라는 것을 알기 때문에 `toLocalString()`이 vscode에서 가이드되고 이외에도 사용할 수 있는 프로퍼티, 메서드를 쉽게 확인할 수 있습니다. 이러한 특징을 통해 개발 생산성이 향상됩니다