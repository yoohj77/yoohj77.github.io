---
layout: default
title: 타입 추론
parent: TypeScript
nav_order: 14
---

# 타입 추론

타입 추론이란 타입이 생략되었을 때 이를 추론해 내는 것을 의미합니다

# 타입 추론의 기본

```jsx
let x = 3; // x는 number로 추론됩니다
```

# 최적 공통 타입

여러 표현 식에서 타입을 추론할 때, 표현식들의 타입을 이용해 `최적 공통 타입`을 계산합니다

```jsx
let x = [0, 1, null]; // (number | null)[] 로 추론
```

# 문맥상 타이핑

`문맥상 타이핑`은 표현식의 타입이 위치에 의해 암시될 때 발생합니다

다음과 같이 동일한 함수더라도 문맥에 따라 다르게 타입을 추론합니다. 아래 코드에서 타입 추론이 발생한 이유는 매개변수의 타입을 명시하지 않았기 때문입니다

```jsx
// window.onmousedown 의 타입으로 매개변수 event 타입을 추론하여 에러가 발생하지 않습니다
window.onmousedown = function (event) {
  console.log(event.button); //<- OK
};

// window.onscroll 의 타입으로 매개변수 event 타입을 추론하여 에러가 발생합니다
window.onscroll = function (event) {
  console.log(event.button); //<- Error!
};
```