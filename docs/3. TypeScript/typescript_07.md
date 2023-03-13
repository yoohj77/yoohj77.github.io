---
layout: default
title: 연산자를 이용한 타입 정의
parent: TypeScript
nav_order: 7
---


# 연산자를 이용한 타입 정의

타입스크립트에서는 기존의 타입을 기반으로 다양한 연산자를 사용하여 새로운 타입을 만들 수 있습니다

# Union Type

union 타입은 서로 다른 두 개 이상의 타입들을 사용하여 만드는 것으로, 사용된 타입 중 무엇이든 하나를 타입으로 가질 수 있습니다.

```tsx
function logText(text: string | number) {
    console.log(text);
}

// 숫자와 문자 모두 사용 가능합니다
logText("text");
logText(100);
```

예를 들어, 다음과 같이 문자열 또는 문자열의 배열을 인자로 받는 경우를 처리할 수 있습니다

```tsx
function welcomePeople(x: string[] | string) {
  if (Array.isArray(x)) {
    // 여기에서 'x'는 'string[]' 타입입니다
    console.log("Hello, " + x.join(" and "));
  } else {
    // 여기에서 'x'는 'string' 타입입니다
    console.log("Welcome lone traveler " + x);
  }
}
```

# Intersection Type

intersection 타입은 사용된 타입 모두를 만족하는 타입으로써 여러 타입을 하나로 합칩니다