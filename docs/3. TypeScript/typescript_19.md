---
layout: default
title: keyof / typeof 타입 연산자
parent: TypeScript
nav_order: 18
---

# keyof / typeof 타입 연산자

# keyof

`keyof` 연산자는 객체 타입에서 객체의 키 값들을 숫자나 문자열 리터럴 유니언을 생성합니다

```tsx
type Point = { x: number; y: number };

// 타입 P와 P2는 동일한 타입입니다
type P = keyof Point;
type P2 = "x" | "y"; 
```

# typeof

자바스크립트는 이미 typeof 연산자를 가지고 있습니다. 거기에 더해 타입스크립트에서는 `typeof` 연산자를 타입을 선언할 때도 사용할 수 있습니다

```tsx
let s = "hello";
let n: typeof s; // let n: string
```

인터페이스가 선언되어 있지 않은 객체에 대하여 타입을 가져와 사용할 수 있습니다