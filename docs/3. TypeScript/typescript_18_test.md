---
layout: default
title: 타입스크립트 연습2
parent: TypeScript
nav_order: 17
---

# 타입스크립트 연습2

`any` 타입을 사용하지 마세요. 필요한 경우 코드를 수정해도 됩니다

# 문제1

주어진 배열에 대해 중복된 항목을 제거하고 유일한 항목의 집합을 반환하는 `uniq` 함수를 타입스크립트로 작성하세요

다음의 예제 코드가 실행되어야 합니다

```tsx
// compare가 없는 경우 === 연산자로 비교합니다
function uniq(arr, compare) { ... }

uniq([2, 1, 2]);
uniq(["사과", "귤", "사과"]);

const objArr = [
    { key: 1, name: '길동' },
    { key: 2, name: '민수' },
    { key: 1, name: '민지' },
]
uniq(objArr, (a, b) => a.key === b.key);
```

# 문제2

다음의 코드가 실행되도록 타입스크립트로 작성하세요

const tuttle = {    name: "거북이",    speed: 1,    walk() {        console.log("엉금엉금");    },     shellThickness: 10, }; const rabbit = {    name: "토끼",    speed: 10,    walk() {        console.log("깡총깡총");    },     earLength: 20, }; function getProperty(animal) {    if (/* 거북이이면 */) {        return animal.shellThickness;    }    if (/* 토끼이면 */) {        return animal.earLength;    }    return animal.name; } getProperty(tuttle); getProperty(rabbit);