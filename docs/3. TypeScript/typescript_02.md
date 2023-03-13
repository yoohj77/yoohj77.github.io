---
layout: default
title: 타입스크립트 시작하기
parent: TypeScript
nav_order: 2
---

# 타입스크립트 시작하기

### 타입스크립트 파일 작성

타입스크립트를 작성하기 위해서는 `.ts` 확장자를 갖는 타입스크립트 파일을 생성합니다

```jsx
// 📁 app.ts
function sum(a: number, b: number): number {
    return a + b;
}

const result = sum(10, 20);
console.log(result);
```

### 타입스크립트 설치

타입스크립트 파일은 그대로 실행이 불가하며 꼭 자바스크립트 파일 `.js` 로 변환하여 실행해야 합니다. 이를 `컴파일(compile)` 또는 트랜스파일이라고 합니다

타입스크립트를 설치하면 컴파일러인 `tsc`를 사용할 수 있습니다.

```bash
// 타입스크립트를 전역으로 설치합니다
> npm install -g typescript

// 버전을 확인합니다
> tsc -v
```

### 타입스크립트 컴파일

tsc를 사용하여 ts파일을 js파일로 컴파일합니다

```bash
// 기본 옵션으로 특정 파일을 컴파일 합니다
> tsc app.ts

// 파일이 수정되면 자동으로 컴파일이 실행됩니다
> tsc app.ts -w
```

같은 경로에 같은 이름의 js파일이 생성됩니다. 생성된 js파일을 확인해 보면 다음과 같이 타입이 사라진 것을 확인할 수 있습니다

```jsx
// 📁 app.js
function sum(a, b) {
    return a + b;
}
var result = sum(10, 20);
console.log(result);
```

생성된 파일을 브라우저 또는 Node.js 등의 환경에서 실행합니다