---
layout: default
title: 제너레이터와 비동기 이터레이션
parent: JavaScript
nav_order: 14
---

# 제너레이터와 비동기 이터레이션

`제너레이터`는 이터레이터를 쉽게 구현할 수 있도록 지원하는 문법입니다. 제너레이터를 사용하여 여러 개의 값을 필요에 따라 하나씩 반환할 수 있습니다

제너레이터를 만들려면 `function*` 키워드를 사용하여 함수를 만들고 `yield` 키워드를 사용하여 값을 반환합니다. 요청이 오면 다음 `yield` 키워드까지 코드가 실행되고 멈춥니다

```jsx
function* generateSequence() {
  yield 1;
sadf
asdf
  yield 2;
  return 3;
}

let generator = generateSequence(); //제너레이터가 생성됩니다
let one = generator.next(); // next함수를 통해 다음 값을 얻을 수 있습니다
alert(JSON.stringify(one)); // {value: 1, done: false}

generator.next(); // {value: 2, done: false}
generator.next(); // {value: 3, done: true}
```

제너레이터는 이터레이터를 쉽게 구현할 수 있도록 만든 문법이기 때문에 제너레이터에 `for..of` 반복문을 사용할 수 있습니다. 마찬가지로 전개 문법도 사용가능합니다

```jsx
function* generateSequence() {
  yield 1;
  yield 2;
  yield 3;
}

// for..of
let generator = generateSequence();
for(let value of generator) {
  alert(value); // 1, 2, 3
}

// 전개 문법
let sequence = [0, ...generateSequence()];
alert(sequence); // 0, 1, 2, 3
```

### 제너레이터 컴포지션

제너레이터와 제너레이터를 조합하여 제너레이터 안에서 제너레이터를 사용할 수 있도록 하는 것이 `제너레이터 컴포지션`입니다

`yield*` 키워드를 사용하면 제너레이터 내부에서 제너레이터를 끼워 넣을 수 있습니다