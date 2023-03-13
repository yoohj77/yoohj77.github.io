---
layout: default
title: 클래스
parent: JavaScript
nav_order: 11
---

# 클래스

# 기본 문법

모던 자바스크립트에서는 객체 지향 프로그래밍을 지원하기 위해 `class`문법을 사용할 수 있습니다

```jsx
class User {

	age = 20; // 클래스 필드

  constructor(name) {
    this.name = name;
  }

  sayHi() {
    alert(this.name);
  }

	get name() {
    return this._name;
  }

  set name(value) {
    if (value.length < 4) {
      alert("이름이 너무 짧습니다.");
      return;
    }
    this._name = value;
  }
}

let user = new User("John");
user.sayHi();
alert(user.name); // 보라

user = new User(""); // 이름이 너무 짧습니다.
```

# 클래스 상속

`extends` 문법으로 다른 클래스를 상속할 수 있습니다

```jsx
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  run(speed) {
    this.speed = speed;
    alert(`${this.name} 은/는 속도 ${this.speed}로 달립니다.`);
  }
  stop() {
    this.speed = 0;
    alert(`${this.name} 이/가 멈췄습니다.`);
  }
}

class Rabbit extends Animal {
  hide() {
    alert(`${this.name} 이/가 숨었습니다!`);
  }
}

let rabbit = new Rabbit("흰 토끼");

rabbit.run(5); // 흰 토끼 은/는 속도 5로 달립니다.
rabbit.hide(); // 흰 토끼 이/가 숨었습니다!
```

### 메서드 오버라이딩

부모 객체와 동일한 이름의 메서드를 생성하면 오버라이딩이 됩니다. `super` 키워드를 사용해서 부모 객체에 직접 접근할 수 있습니다

```jsx
class Rabbit extends Animal {
  hide() {
    alert(`${this.name}가 숨었습니다!`);
  }

  stop() {
    super.stop(); // 부모 클래스의 stop을 호출해 멈추고,
    this.hide(); // 숨습니다.
  }
}

let rabbit = new Rabbit("흰 토끼");

rabbit.run(5); // 흰 토끼가 속도 5로 달립니다.
rabbit.stop(); // 흰 토끼가 멈췄습니다. 흰 토끼가 숨었습니다!
```

### 생성자 오버라이딩

생성자를 직접 구현하면 오버라이딩이 되며 부모 객체의 생성자를 호출하기 위해서는 `super` 키워드를 사용하면 됩니다

```jsx
class Rabbit extends Animal {

  constructor(name, earLength) {
    super(name);
    this.earLength = earLength;
  }

  // ...
}
```

# 정적 메서드와 정적 프로퍼티

클래스를 통해 생성된 객체가 아닌 클래스 자체에서 제공하는 메서드와 프로퍼티를 `정적 메소드`, `정적 프로퍼티` 라 합니다. `static` 키워드를 붙여서 만들 수 있습니다

```jsx
class Article {
	title = "title";

  constructor(title, date) {
    this.title = title;
    this.date = date;
  }

	static publisher = "Ilya Kantor";

  static createTodays() {
    // this는 Article입니다.
    return new this("Today's digest", new Date());
  }
}

let article = Article.createTodays();

alert( article.title ); // Today's digest
alert( Article.publisher ); // Ilya Kantor
```

# private, protected

객체 지향 프로그래밍에서는 객체의 요소에 대한 외부 접근을 제어하는 것이 중요합니다. 일반적으로 다음으로 나눌 수 있으며 자바스크립트에서 아래 코드와 같이 구현할 수 있습니다

- `public` - 외부에서 접근이 가능함. 일반적인 모든 메소드, 프로퍼티
- `private` - 외부에서 접근이 불가능하며 내부에서만 접근이 가능함. `#` 키워드 사용
- `protected` - 외부에서 접근이 불가능하지만 상속받은 객체에서는 접근이 가능함. 자바스크립트에서 지원하지 않으며 변수에 `_` 를 붙이는 관습(컨벤션)으로 한정할 수 있음

```jsx
class CoffeeMachine {

	// private
  #waterAmount = 0; 

	// protected
	_power = 100;

	// public
  get waterAmount() { 
    return this.#waterAmount;
  }

	// public
  set waterAmount(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    this.#waterAmount = value;
  }
}

let machine = new CoffeeMachine();

machine.waterAmount = 100;
alert(machine.#waterAmount); // Error
alert(machine._power); // 접근이 가능하지만 관습적으로 _변수를 직접 호출하지 않음
```

# instanceof 로 클래스 확인하기

`instaceof` 연산자를 사용하면 객체가 특정 클래스의 instance인지 아닌지를 확인할 수 있습니다. 이는 상속 관계도 확인해 줍니다

```jsx
class Rabbit {}
let rabbit = new Rabbit();

alert( rabbit instanceof Rabbit ); // true
```

# 믹스인

자바스크립트는 프로토타입 상속을 사용하며 다중상속을 지원하지 않습니다. 그렇기 때문에 다중상속을 원하는 경우 `믹스인`을 사용해야 합니다.

`믹스인`이란 여러 클래스의 기능을 혼합하는 것입니다