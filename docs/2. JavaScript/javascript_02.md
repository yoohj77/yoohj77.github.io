---
layout: default
title: 자바스크립트 기본
parent: JavaScript
nav_order: 2
---

# 자바스크립트 기본

### 엄격모드

잘못 만든 자바스크립트 구문과 규칙을 사용하지 않기 위한 모드입니다. 특별한 경우가 아니면 항상 사용되는 것이 좋으나 일반적으로 번들링을 하거나 lint 도구를 사용하면 알아서 자바스크립트 구문을 만들거나 만들도록 도와주기 때문에 직접 엄격모드를 명시하는 경우는 요새 잘 없습니다

```jsx
"use strict";
```

### 변수와 상수

- `let` - 모던한 변수 선언 키워드입니다
- `var` - 오래된 변수 선언 키워드로서 잘 사용하지 않습니다
- `const` - 상수 선언 키워드입니다. `let`과 비슷하지만 변수의 값을 변경할 수 없습니다

### 자바스크립트의 자료형 8가지

- 원시형 - 변수 메모리에 해당 값이 그대로 저장

  - 숫자형

  - BigInt - 아주 큰 숫자나 아주 높은 정밀도가 필요한 경우 사용

    ```jsx
    // 끝에 'n'이 붙으면 BigInt형 자료입니다.
    const bigInt = 1234567890123456789012345678901234567890n;
    ```

  - 문자형

  - 불린형 (true/false)

  - `null` 값 - 값이 존재하지 않거나 비어 있는 경우

  - `undefined` 값 - 값이 할당되지 않은 경우

    - `undefined` 를 직접 사용하는 것을 권장하지 않음

  - 심볼형 - 객체의 고유한 식별자를 만들 때 사용

    ```jsx
    let user = { // 서드파티 코드에서 가져온 객체
      name: "John"
    };
    let id = Symbol("id");
    user[id] = 1;
    alert( user[id] ); // 심볼을 키로 사용해 데이터에 접근할 수 있습니다.
    ```

- 객체형 (참조형) - 변수 메모리에 객체 값의 참조 주소가 저장

- `typeof` 연산자는 인수의 자료형을 반환합니다

  ```jsx
  typeof undefined // "undefined"
  
  typeof 0 // "number"
  
  typeof 10n // "bigint"
  
  typeof true // "boolean"
  
  typeof "foo" // "string"
  
  typeof null // "object", 언어 자체의 오류. null은 object가 아닙니다
  
  typeof alert // "function", function이라는 자료형은 없으나 하위 호환성을 위해 유지하고 있습니다
  ```

### 상호작용

```
alert` / `prompt` / `confirm
alert("Hello");

let age = prompt('나이를 입력해주세요.', 100);

let isBoss = confirm("당신이 주인인가요?");
```

### 형 변환

```jsx
alert("1234" + 5); //문자형으로 변환 12345

alert(Number("1234") + 5); //숫자형으로 변환 1239
alert(+"1234" + 5); //숫자형으로 변환 1239

"1234" && alert(true); //불린형으로 변환 true
```

### 기본 연산자

```jsx
alert( 5 % 2 ); //나머지 연산자(%)   5를 2로 나눈 후의 나머지인 1을 출력
alert( 2 ** 3 ); //거듭제곱 연산자(**)   8  (2 * 2 * 2)

let c = 3 - (a = 1); //a=1, c=2  할당 연산자(=)는 값을 반환
a = b = c = 2 + 2; //할당 연산자 여러 개 연결 가능

let a = (0, 1 + 2, 3 + 4); //a=7  쉼표 연산자(,)는 코드를 짧게 쓰고자 할 때 사용. 마지막 표현식 반환
```

### 비교 연산자

```jsx
alert( 0 == false ); // true  동등 연산자
alert( 0 === false ); // false, 일치 연산자. 피연산자의 형이 다르기 때문입니다.

alert( null == undefined ); // true
alert( null === undefined ); // false

alert( undefined > 0 ); // false (1)
alert( undefined < 0 ); // false (2)
alert( undefined == 0 ); // false (3) undefined는 비교 연산자시 항상 false
```

### 논리 연산자

```jsx
alert( '' || '' || '바이올렛' || "익명"); // 바이올렛. 첫번째 truthy한 값 반환
alert( 1 && 2 && null && 3 ); // null. 첫번째 falsy한 값 반환
```

### nullish 병합 연산자

```jsx
let name = '';

alert(name || '홍길동'); // 홍길동
alert(name ?? '홍길동'); // ''. null 또는 undefined 일 때만 뒤의 값이 반환
```

### 함수

```jsx
function showMessage(from, text = "no text given") { //매개변수 기본값
  alert( from + ": " + text );
}
function showMessage(from, text) {
  text = text ?? "no text given"; // 또는 || 연산자 사용
  alert( from + ": " + text );
}

// 함수 선언문 - 위치와 상관없이 어디서든지 실행가능
sayHi("John"); // Hello, John
function sayHi(name) {
  alert( `Hello, ${name}` );
}

// 함수 표현식 - let, const 는 해당 위치 이후에만 사용가능
sayHi("John"); // error!
let sayHi = function(name) {  // (*) 마술은 일어나지 않습니다.
  alert( `Hello, ${name}` );
};
```

### 화살표 함수

```jsx
let sum = (a, b) => a + b;

/* 위 화살표 함수는 아래 함수의 축약 버전입니다.
let sum = function(a, b) {
  return a + b;
};
*/
```

화살표 함수는 자체 컨텍스트가 없는 짧은 코드를 담을 용도로 만들어 졌기 때문에 일반 함수와 다음과 같은 다른 점이 있습니다

- `this` 를 가지지 않습니다
- `arguments` 를 지원하지 않습니다
- `new` 와 함께 호출할 수 없습니다
- `super` 를 호출할 수 없습니다

```jsx
var adder = {
  base : 1,

  add : function(a) {
    let f = v => v + this.base;
    return f(a);
  },

	normalAdd: function(a) {
		let f = function(v) { return v + this.base; }
		return f(a);
	}

  addThruCall: function(a) {
    let f = v => v + this.base;
    let b = {
      base : 2
    };

    return f.call(b, a);
  }
};

console.log(adder.add(1));         // 이는 2가 콘솔에 출력될 것임
console.log(adder.normalAdd(1));   // NaN. this.base가 undefined임
console.log(adder.addThruCall(1)); // 이도 2가 콘솔에 출력될 것임
```