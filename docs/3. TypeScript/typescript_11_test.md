---
layout: default
title: 타입스크립트 연습1
parent: TypeScript
nav_order: 11
---

# 타입스크립트 연습1

다음의 자바스크립트 코드를 타입스크립트로 작성해 주세요

```tsx
function getLength(value) {
    if (typeof value === "number") {
        return value.toString().split(".")[0].length;
    } else {
        return value.length;
    }
}

function addLength(value1, value2, fnGetLength, type) {
    if (!type) {
        type = "value";
    }
    switch (type) {
        case "value":
            return fnGetLength(value1 + value2);
        case "length":
            return fnGetLength(value1) + fnGetLength(value2);
    }
}

console.log(addLength(5, 10, getLength, "length"));
console.log(addLength("Hello", "World", getLength));

// 다음과 같이 숫자와 문자열을 함께 넘기면 타입 에러가 발생해야 합니다
console.log(addLength(100, "World", getLength));
```