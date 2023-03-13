---
layout: default
title: 리터럴 타입
parent: TypeScript
nav_order: 10
---

# 리터럴 타입

기본 타입 이외에도 구체적인 문자열과 숫자 값을 타입으로 지정할 수 있으며 이를 `리터럴 타입`이라 합니다

```tsx
// const는 값이 절대 변하지 않기 때문에 타입을 문자열 그대로 지정합니다
const helloWorld = "Hello World"; // "Hello World"

// 반면, let은 변경될 수 있으므로 타입을 string 으로 지정합니다
let hiWorld = "Hi World";
```

union 타입과 함께 사용하면 유용한 상황들이 많습니다. 예를 들어, 함수의 인자로 특정한 값만 받을 수 있습니다

```tsx
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
printText("Hello, world", "left");
printText("G'day, mate", "center");
```