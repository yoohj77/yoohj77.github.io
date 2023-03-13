---
layout: default
title: 함수 심화학습
parent: JavaScript
nav_order: 6
---

# 함수 심화학습

# 나머지 매개변수와 스프레드 문법

### 나머지 매개변수 `...`

```jsx
function showName(firstName, lastName, ...titles) {
  alert( titles[0] ); // Software Engineer
  alert( titles[1] ); // Researcher
  alert( titles.length ); // 2
}

showName("Bora", "Lee", "Software Engineer", "Researcher");
```

`arguments` 객체 대신에 나머지 매개변수를 사용하는 것이 좋습니다. arguments 객체는 유사배열로서 배열 메서드를 사용할 수 없어 불편합니다

```jsx
function sumAll(...args) {
  let sum = 0;
  for (let arg of args) sum += arg;
  return sum;
}

alert( sumAll(1, 2, 3) ); // 6
```

### 스프레드 문법

```jsx
let arr = [3, 5, 1];

alert( Math.max(3, 5, 1) ); // 5
alert( Math.max(...arr) ); // 5
```

### 배열과 객체의 복사본 만들기

스프레드 문법을 사용하면 Object.assign 함수보다 더 짧은 코드로 배열과 객체를 복사할 수 있습니다

```jsx
let arr = [1, 2, 3];
let arrCopy = [...arr];

let obj = { a: 1, b: 2, c: 3 };
let objCopy = { ...obj };
```

# 변수의 유효범위와 클로저

### 코드 블록

코드 블록 안에서 선언한 변수는 블록 안에서만 사용할 수 있습니다

```jsx
let message = "바깥입니다";

{
  let message = "안녕하세요."; // 블록 내에서만 변숫값을 얻을 수 있습니다.
  alert(message); // 안녕하세요.
}

alert(message); // 바깥입니다
```

### 클로저

`클로저`는 외부 변수를 기억하고 이 외부 변수에 접근할 수 있는 함수를 의미합니다. 자바스크립트에서는 함수가 생성될 당시의 유효 범위 내에 있는 모든 지역 변수를 환경으로 가집니다. 그러므로 같은 함수더라도 다른 시점에 생성되면 생성 시점이 다르기 때문에 서로 다른 독립적인 환경을 갖게 됩니다

이러한 클로저의 성질을 활용하여 원하는 값을 함수 내에 보존시킬 수 있습니다. 또한, 클로저의 환경은 외부에서 접근할 수 없기 때문에 클로저를 통해 특정 값이나 메서드를 숨길 수 있습니다

```jsx
//예제1
function makeCounter() {
  let count = 0;

  return function() {
    return count++;
  };
}

let counter = makeCounter();
alert( counter() ); // 0
alert( counter() ); // 1

let counter2 = makeCounter(); // 새로운 환경을 갖고 있는 클로저 함수가 반환됩니다
alert( counter2() ); // 0
alert( counter2() ); // 1

// 외부에서 count 변수의 존재를 알지 못합니다
// 이를 통해 클로저로 원하는 값 또는 메서드를 숨길 수 있습니다
//예제2
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

let add5 = makeAdder(5); // 클로저의 환경에 x: 5 의 값을 보존합니다
let add10 = makeAdder(10); // 클로저의 환경에 x: 10 의 값을 보존합니다

console.log(add5(2));  // 7
console.log(add10(2)); // 12
```

# 전역 객체

브라우저 환경에서는 `window`가 전역 객체이며 Node.js 환경에서는 `global`이 전역 객체입니다

전역 변수는 외부 종속성을 갖게 되기 때문에 되도록 사용하지 않는 것이 좋습니다. 전역으로 쓰기 보다는 매 함수마다 필요한 값을 매개변수로 넘겨주는 것이 좋습니다

```jsx
alert("Hello");
window.alert("Hello"); // 위와 동일하게 동작합니다.

window.currentUser = {
  name: "John"
};
alert(currentUser.name);  // John
```

# 객체로서의 함수

자바스크립트의 함수는 객체입니다. 그렇기 때문에 객체처럼 함수도 프로퍼티를 갖습니다

```jsx
// name 프로퍼티. 함수명을 반환합니다
function sayHi() {
  alert("Hi");
}
alert(sayHi.name); // sayHi

// length 프로퍼티. 함수 매개변수의 개수를 반환합니다
function f2(a, b) {}
alert(f2.length); // 2

// 커스텀 프로퍼티
function sayHi() {
  alert("Hi");

  // 함수를 몇 번 호출했는지 세봅시다.
  sayHi.counter++;
}
sayHi.counter = 0; // 초깃값

sayHi(); // Hi
sayHi(); // Hi

alert( `호출 횟수: ${sayHi.counter}회` ); // 호출 횟수: 2회
```

# setTimeout과 setInterval

`setTimeout` - 일정 시간이 지난 후 함수 실행

`setInterval` - 일정 시간 간격을 두고 함수를 반복 실행

```jsx
// setTimeout
let timerId = setTimeout(() => alert('안녕하세요.'), 1000); // 1초 후 실행
clearTimeout(timerId); // 스케줄링 취소

// setInterval
let timerId = setInterval(() => alert('째깍'), 2000); // 2초 간격으로 메시지를 보여줌
setTimeout(() => clearInterval(timerId), 5000); // 5초 후에 정지

// 시간이 없거나 0인 setTimeout
// 현재 스크립트의 실행이 종료된 직후에 실행됩니다. 이는 이벤트 루프와 관련있는 것으로 나중에 자세히 설명하겠습니다
setTimeout(() => alert("2"));
alert("1"); // 1이 먼저 나오고 2가 출력
```

# call/apply와 데코레이터, 포워딩

- 다음의 코드에서 `cachingDecorator` 함수는 넘겨받은 함수에 캐싱 기능을 추가해 주고 있습니다. 이와 같이 인수로 받은 함수의 행동을 변경시켜 주는 함수를 `데코레이터(decorator)`라 합니다
- `func.call(this, ...arguments)` 코드는 this와 매개변수를 넘겨줌으로써 원래 함수 호출을 그대로 유지해 줍니다. 이렇게 컨텍스트와 함께 인수 전체를 다른 함수에 전달하는 것을 `콜 포워딩(call forwarding)`이라고 합니다
- `[].join.call(args)` 코드는 배열의 join 메서드를 `args`변수가 빌려 사용한 경우입니다. 이렇게 `call` 을 사용해 컨텍스트를 변경함으로써 네이티브 메서드를 빌려 사용할 수 있습니다

```jsx
let worker = {
  add(a, b) {
		return a + b;
  }

  slow(min, max) {
    alert(`slow(${min},${max})을/를 호출함`);
    return this.add(min, max);
  }
};

function cachingDecorator(func) {
  let cache = new Map();
  return function() {
    let key = hash(arguments); 
    if (cache.has(key)) {
      return cache.get(key);
    }

    let result = func.call(this, ...arguments);

    cache.set(key, result);
    return result;
  };
}

function hash(...args) {
  return [].join.call(args);
}

worker.slow = cachingDecorator(worker.slow);

alert( worker.slow(3, 5) ); // 제대로 동작합니다.
alert( "다시 호출: " + worker.slow(3, 5) ); // 동일한 결과 출력(캐시된 결과)
```

# 함수 바인딩

함수를 전달 할 때 컨텍스트와 인수를 담아 전달하려면 어떻게 해야 할까요?

### 래퍼

함수로 감싸서 `user` 컨텍스트를 유지하였고 `name`을 전달했습니다

```jsx
let user = {
  firstName: "John",
  sayHi(name) {
    alert(`Hello, ${this.firstName}! I'm ${name}`);
  }
};

let name = 'sam';
setTimeout(function() {
  user.sayHi(name); // Hello, John! I'm sam
}, 1000);
```

### bind

`bind` 함수를 사용하면 컨텍스트와 인수를 쉽게 전달할 수 있습니다

```jsx
let user = {
  firstName: "John",
  sayHi(name) {
    alert(`Hello, ${this.firstName}! I'm ${name}`);
  }
};

let name = 'sam';
setTimeout(user.sayHi.bind(user, name), 1000);
```

# 즉시 실행 함수 표현식 (IIFE)

정의하자 마자 바로 실행하는 함수를 말합니다. 모던 자바스크립트에서는 사실상 용도가 없어 거의 사용되지 않는 패턴입니다. 과거 `var`를 사용할 때 스코프를 제한하고 전역 스코프를 보호하기 위해 자주 사용되었습니다. 요즘에도 번들링 등의 빌드시에 `IIFE` 패턴으로 스크립트가 생성되는 경우가 많습니다