---
layout: default
title: 객체
parent: JavaScript
nav_order: 4
---

# 객체: 기본

### 기본 활용

```jsx
// 단축 프로퍼티
function makeUser(name, age) {
  return {
    name, // name: name 과 같음
    age,  // age: age 와 같음
    // ...
  };
}

// in 연산자 - 프로퍼티 존재 여부 확인
let user = { name: "John", age: 30 };
alert( "age" in user ); //true

// for...in 반복문 - 객체의 모든 프로퍼티 순회
let user = {
  name: "John",
  age: 30,
  isAdmin: true
};
for (let key in user) {
  alert( key );  // name, age, isAdmin
  alert( user[key] ); // John, 30, true
}
```

### 참조에 의한 객체 복사

```jsx
// 참조에 의한 비교
let a = {};
let b = a; // 참조에 의한 복사
let c = {};

alert( a == b ); // true, 두 변수는 같은 객체를 참조합니다.
alert( a == c ); // false, 참조하는 메모리 주소가 다름

// Object.assign
let user = { name: 'John', age: 30 };
Object.assign(user, permissions1, permissions2); // permissions1과 permissions2의 프로퍼티를 user로 복사
let clone = Object.assign({}, user); // 객체 얕은 복사

// 객체 깊은 복사
// _.cloneDeep 을 사용하거나 다른 알고리즘을 사용해야 한다
```

### 가비지 컬렉션

자바스크립트 엔진 내에서는 `가비지 컬렉터`가 끊임없이 동작하면서 도달할 수 없는 객체를 삭제합니다. 도달 가능하다는 것은 어떻게든 접근하거나 사용할 수 있는 값을 의미합니다.

C언어와 같은 경우 가비지 컬렉터가 없어 개발자가 메모리에서 값을 직접 해제해야 했으나 최근의 언어들은 그러한 일들을 가비지 컬렉터가 자동으로 해줍니다

### 메서드와 this

객체에 프로퍼티로 할당되어 있는 함수를 객체의 `메서드`라고 합니다

```jsx
let user = {
  name: "John",
  age: 30,

  // say: function() { ... } 의 단축 구문입니다
  sayHi() { 
    // 'this'는 '현재 객체'를 나타냅니다
    alert(this.name);
  }
};

user.sayHi(); // John
```

`this`의 값은 런타임에 결정되며 컨텍스트에 따라 달라집니다

```jsx
let user = { name: "John" };
let admin = { name: "Admin" };

function sayHi() {
  alert( this.name );
}

// 별개의 객체에서 동일한 함수를 사용함
user.f = sayHi;
admin.f = sayHi;

// 'this'는 '점(.) 앞의' 객체를 참조하기 때문에
// this 값이 달라짐
user.f(); // John  (this == user)
admin.f(); // Admin  (this == admin)

sayHi(); // undefined 참조 에러 (this == undefined)
```

이러한 자유로운 `this`는 개발자의 실수를 유발하기 쉽습니다. 그래서 this를 가지는 일반 함수가 아닌 this를 가지지 않는 화살표 함수를 사용하는 경우도 많습니다

### new 연산자와 생성자 함수

유사한 객체를 여러 개 만들고 싶을 때 `new` 연산자로 동일한 형태의 객체를 생성해낼 수 있습니다. 이 때 사용되는 함수를 `생성자 함수`라고 합니다

모던 자바스크립트에서는 생성자 함수 대신 class 문법을 많이 사용합니다

```jsx
// 생성자 함수
function User(name) {
  // this = {};  (빈 객체가 암시적으로 만들어짐)

  this.name = name;
  this.isAdmin = false;
  
  this.sayHi = function() { // 메서드도 할당 가능
    alert( "제 이름은 " + this.name + "입니다." );
  };

  // return this;  (this가 암시적으로 반환됨)
}

let user = new User("보라");

bora.sayHi(); // 제 이름은 보라입니다.
```

### 옵셔널 체이닝 `?.`

옵셔널 체이닝을 사용하면 프로퍼티가 없는 중첩 객체를 에러 없이 안전하게 접근할 수 있습니다

```jsx
let user = { name: '민수', friends: null }; // 주소 정보가 없는 사용자
alert( user?.address?.street ); // undefined, 에러가 발생하지 않습니다.

user.admin?.(); // 메서드가 호출되지 않습니다

alert( user.friends?.[0] ) // undefined
```

### 객체 순회 메서드

- Object.keys(obj) – 객체의 키만 담은 배열을 반환합니다.
- Object.values(obj) – 객체의 값만 담은 배열을 반환합니다.
- Object.entries(obj) – `[키, 값]` 쌍을 담은 배열을 반환합니다.