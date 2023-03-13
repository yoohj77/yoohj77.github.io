---
layout: default
title: 함수
parent: TypeScript
nav_order: 4
---

# 함수

# 함수의 기본적인 타입 선언

함수의 매개변수와 반환값의 타입을 다음과 같이 선언할 수 있습니다

```tsx
function sum(a: number, b: number): number {
  return a + b;
}
```

# 함수의 인자

타입스크립트에서는 함수의 인자를 모두 필수 값으로 간주합니다. 따라서 정의된 매개변수 이외에 추가로 인자를 넘길 수 없습니다

```tsx
function sum(a: number, b: number): number {
  return a + b;
}
sum(10, 20); // 30
sum(10, 20, 30); // error, too many parameters
sum(10); // error, too few parameters
```

만약 인자를 필수가 아닌 옵션으로 설정하고 싶다면 매개변수명에 `?` 를 붙인 optional parameter를 사용하면 됩니다. 이 경우 인자 값을 넘기지 않아도 됩니다

```tsx
function sum(a: number, b?: number): number {
  return a + b;
}
sum(10, 20); // 30
sum(10, 20, 30); // error, too many parameters
sum(10); // 타입 에러 없음
```

매개변수에 기본값을 주고 싶다면 es6 문법과 동일하게 설정할 수 있습니다

```tsx
function sum(a: number, b = '100'): number {
  return a + b;
}
sum(10); // 110
```

# REST 문법이 적용된 매개변수

```tsx
function sum(a: number, ...nums: number[]): number {
  //...
}
```

# this

타입스크립트에서 다음과 같이 `this`의 타입을 명시할 수 있습니다

```tsx
function defer(this: Function, ms: number) {
    setTimeout(this, ms);
}
```

# 오버로드

타입스크립트의 오버로드는 다른 정적 언어의 오버로드 문법과 조금 다른데 `오버로드 시그니처`와 `구현 시그니처`로 구성됩니다. 오버로드 시그니처는 함수의 오버로드 타입을 선언하는 부분이고 구현 시그니처는 실제 함수의 구현 부분입니다

타입스크립트의 오버로드는 오버로드 타입마다 구현을 각각 하는 것이 아니라 하나의 함수로 구현해야 합니다

```tsx
function makeDate(timestamp: number): Date; // 오버로드 시그니처
function makeDate(m: number, d: number, y: number): Date; // 오버로드 시그니처
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date { // 구현 시그니처
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}

const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3);
```

구현 시그니처는 외부에서 보이지 않고 오버로드 시그니처만 보입니다

또한 구현 시그니처는 자신의 모든 오버로드 시그니처와 호환되어야 합니다