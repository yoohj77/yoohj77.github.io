---
layout: default
title: 맵드 타입
parent: TypeScript
nav_order: 21
---
# 맵드 타입 (Mapped Types)

`맵드 타입` 이란 이미 존재하는 타입을 사용해 마치 자바스크립트의 `map()` 함수를 사용하는 것과 같이 동일한 프로퍼티에 대해 다른 타입을 가지는 새로운 타입을 생성하는 것입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/88649fcb-385b-4170-8d0a-756188566e30/Untitled.png)

다음과 같이 인덱스 시그니처에 `in` 키워드를 사용하여 맵드 타입을 생성할 수 있습니다



```tsx
type FeatureFlags = {
  darkMode: () => void;
  newUserProfile: () => void;
};

type FeatureOptions = { [Property in keyof FeatureFlags]: boolean };

const options: FeatureOptions = {
    darkMode: true,
    newUserProfile: false,
};
```