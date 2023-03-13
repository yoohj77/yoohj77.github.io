---
layout: default
title: 프로토타입과 프로토타입 상속
parent: JavaScript
nav_order: 10
---

# 프로토타입과 프로토타입 상속

# 프로토타입 상속

자바스크립트는 `프로토타입 상속`을 통해 상속 기능을 구현하고 있습니다. 자바스크립트의 객체는 `[[Prototype]]`이라는 숨김 프로퍼티를 갖습니다. 이 값은 null이거나 다른 객체를 참조하는데 참조하는 대상을 `프로토타입`이라 합니다

객체의 프로퍼티를 읽는 경우 먼저 자기 자신 객체에서 찾아보고 없으면 프로토타입에서 찾아봅니다. 프로토타입에 다시 프로토타입이 있으면 이 동작을 계속 반복합니다. 이런 방식으로 프로토타입 상속이 동작합니다

```jsx
let animal = {
  eats: true,
  walk() {
    alert("동물이 걷습니다.");
  }
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

// 메서드 walk는 rabbit의 프로토타입인 animal에서 상속받았습니다.
rabbit.walk(); // 동물이 걷습니다.
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bc37bb7c-fcc5-4f75-b5e8-a02e03d68d45/Untitled.png)

프로토타입은 읽기 전용이기 때문에 프로토타입에 값을 쓸 수 없습니다. 이러한 성질에 의해 객체의 상태는 프로토타입과 공유되지 않고 독립적입니다

```jsx
let animal = {
  eats: true,
  walk() {
    /* rabbit은 이제 이 메서드를 사용하지 않습니다. */
  }
};

let rabbit = {
  __proto__: animal
	walk() {}
};

rabbit.walk = function() {
  alert("토끼가 깡충깡충 뜁니다.");
};

rabbit.walk(); // 토끼가 깡충깡충 뜁니다.
animal.walk();
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/616051c8-5d6d-4345-b070-3b58f5ddaaec/Untitled.png)

### for…in 반복문

for…in 반복문은 상속 프로퍼티도 순회대상에 포함시키는데 `obj.hasOwnProperty(key)`를 이용하면 순회대상에서 제외할 수 있습니다

```jsx
let animal = {
  eats: true
};

let rabbit = {
  jumps: true,
  __proto__: animal
};

for(let prop in rabbit) {
  let isOwn = rabbit.hasOwnProperty(prop);

  if (isOwn) {
    alert(`객체 자신의 프로퍼티: ${prop}`); // 객체 자신의 프로퍼티: jumps
  } else {
    alert(`상속 프로퍼티: ${prop}`); // 상속 프로퍼티: eats
  }
}
```

# 함수의 prototype 프로퍼티

생성자 함수에 `prototype` 프로퍼티를 통해서 생성자 함수로 만들어 지는 객체의 프로토타입을 지정할 수 있습니다. 이는 class의 extends 와 동일합니다

```jsx
let animal = {
  eats: true
};

function Rabbit(name) {
  this.name = name;
}

Rabbit.prototype = animal;

let rabbit = new Rabbit("흰 토끼"); //  rabbit.__proto__ == animal

alert( rabbit.eats ); // true
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2721ad1f-1993-475d-bb0a-7d108ffe5744/Untitled.png)

생성자 함수에 의해 생성된 객체의 프로토타입에는 기본적으로 모두 `constructor` 프로퍼티를 갖습니다

```jsx
function Rabbit() {}
// 디폴트 prototype:
// Rabbit.prototype = { constructor: Rabbit }

let rabbit = new Rabbit(); // {constructor: Rabbit}을 상속받음

alert(rabbit.constructor == Rabbit); // true ([[Prototype]]을 거쳐 접근함)
```

# 내장 객체의 프로토타입

자바스크립트의 내장 객체에는 특별한 프로토타입이 존재하며 그 상속 꼭대기에는 `Object.prototype`이 존재합니다. 바로 이 `Object.prototype`에 toString 등의 함수가 존재하여 우리가 생성하는 모든 객체에 toString 메서드가 보이는 것입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/338a5de9-2a70-4590-a565-08f8175cbc11/Untitled.png)

# 프로토타입 메서드와 __proto__가 없는 객체

위에서 사용한 `__proto__` 는 구식이기 때문에 더는 사용하지 않는 것이 좋습니다. 대신 다음의 모던한 메서드를 사용하는 것이 좋습니다

- Object.create(proto, [descriptors]) – `[[Prototype]]`이 `proto`를 참조하는 빈 객체를 만듭니다. 이때 프로퍼티 설명자를 추가로 넘길 수 있습니다.
- Object.getPrototypeOf(obj) – `obj`의 `[[Prototype]]`을 반환합니다.
- Object.setPrototypeOf(obj, proto) – `obj`의 `[[Prototype]]`이 `proto`가 되도록 설정합니다.

```jsx
let animal = {
  eats: true
};

// 프로토타입이 animal인 새로운 객체를 생성합니다.
let rabbit = Object.create(animal);

alert(rabbit.eats); // true

alert(Object.getPrototypeOf(rabbit) === animal); // true

Object.setPrototypeOf(rabbit, {}); // rabbit의 프로토타입을 {}으로 바꿉니다.
```

이 메서드를 사요하면 객체를 매우 효과적으로 복사할 수 있습니다. 일반적인 복사의 경우 보통 열거 가능한 프로퍼티만 복사되지만 다음의 방식은 객체의 모든 내용이 복사됩니다

```jsx
let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
```

프로토타입이 존재하지 않는 매우 단순한 객체가 필요한 경우가 있습니다. 이런 경우 프로토타입을 null로 하여 객체를 생성할 수 있습니다. 단, 이 경우 toString 같은 함수가 존재하지 않습니다

```jsx
let obj = Object.create(null);
alert(obj); // Error: Cannot convert object to primitive value (toString이 없음)
```