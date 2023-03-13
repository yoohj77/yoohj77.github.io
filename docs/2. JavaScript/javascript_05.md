---
layout: default
title: 자료구조와 자료형
parent: JavaScript
nav_order: 5
---

# 자료구조와 자료형

# 숫자형

### 기본 활용

```jsx
alert( 7.3e9 ); // 73억 (7,300,000,000)
alert( 1e-6 ); // 1마이크로 (0.000001)

alert( 0xff ); // 255. 16진수
alert( 0b11111111 ); // 255. 2진수
alert( 0o377 ); // 255. 8진수

let num = 255;
alert( num.toString(16) );  // ff. 16진수 문자형으로 변환
alert( num.toString(2) );   // 11111111. 2진수 문자형으로 변환
```

### 어림수 구하기

```jsx
Math.round(3.6) //4
Math.ceil(3.6) //4
Math.floor(3.6) //3
Math.trunc(3.6) //3

Math.floor(-1.6) //-2
Math.trunc(-1.6) //-1
```

### 부정확한 계산

자바스크립트의 숫자는 내부적으로 64비트 형식으로 표현되는데 무한소수(2진법)의 경우 정확하게 나타낼 방법이 없어 부정확한 계산이 발생합니다

이를 해결하려면 toFixed(n) 메서드를 사용하여 어림수를 만들면 됩니다

※ toFixed(n): 해당 숫자의 소수점을 n자리로 고정한 문자열을 반환합니다

```jsx
alert( 0.1 + 0.2 == 0.3 ); // false
alert( 0.1 + 0.2 ); // 0.30000000000000004
alert( +(0.1 + 0.2).toFixed(2) ); // 0.3
```

### parseInt와 parseFloat

문자열의 시작부터 숫자를 읽어 반환합니다. 이렇게 하면 좀 더 느슨한 규칙으로 문자열에서 더 쉽게 숫자를 읽을 수 있습니다

```jsx
alert( parseInt('100px') ); // 100
alert( parseFloat('12.5em') ); // 12.5
```

# 문자열

다른 언어들에 흔히 있는 문자 하나만 저장하는 자료형이 자바스크립트에는 존재하지 않습니다. 자바스크립트에서 문자열은 항상 `UTF-16`형식을 따릅니다

```jsx
alert(`1 + 2 = ${1 + 2}.`); // 1 + 2 = 3. 템플릿 리터럴
alert( "Widget with id".includes("Widget") ); // true
alert( "Widget".startsWith("Wid") ); // true, "Widget"은 "Wid"로 시작합니다.
alert( "Widget".endsWith("get") ); // true, "Widget"은 "get"으로 끝납니다.

let str = "stringify";
alert( str.slice(0, 5) ); // 'strin', 0번째부터 5번째 위치까지(5번째 위치의 글자는 포함하지 않음)
```

# 배열

순서가 있는 컬렉션을 저장할 때 배열 자료구조를 사용할 수 있습니다. 배열은 객체이며 특별하게 동작하는 객체의 한 부류로서 객체이기 때문에 참조형 자료입니다

```jsx
//deque 자료구조 관련 연산
let fruits = ["사과", "오렌지", "배"];
alert( fruits.pop() ); //배. 배열에서 마지막 요소를 꺼내 반환합니다
alert( fruits ); // 사과,오렌지

fruits.push("배"); //배열 마지막에 요소를 추가합니다
alert( fruits ); // 사과,오렌지,배

alert( fruits.shift() ); //사과. 배열 처음 요소를 꺼내 반환합니다
alert( fruits ); // 오렌지,배

fruits.unshift('사과'); //배열 처음에 요소를 추가합니다
alert( fruits ); // 사과,오렌지,배

//숫자가 아닌 값을 프로퍼티 키로 사용할 수 있습니다
//하지만 이런 경우 배열 연산 성능이 떨어지기 때문에 가급적 사용하지 않아야 합니다
fruits.age = 25;

//for..of 반복문
//for..in 반복문은 객체를 위한 구문이기 때문에 배열에 대하여 가급적 사용하지 않아야 합니다
for (let fruit of fruits) {
  alert( fruit );
}

//다차워 배열
let matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];
alert( matrix[1][1] ); // 5
```

### 배열의 메서드

배열에는 다양한 메서드가 제공됩니다. 배열 자료구조를 많이 사용하는 만큼 이 메서드들을 잘 익혀 놓아야 합니다

- splice / slice
- concat
- forEach
- indexOf / lastIndexOf
- includes
- find / findIndex
- filter
- map
- sort
- reverse
- split / join
- reduce / reduceRight
- Array.isArray

# iterable 객체

iterable(반복 가능한) 객체는 배열을 일반화한 객체입니다. iterable 객체는 `for..of` 구문을 사용할 수 있습니다

```jsx
let range = {
  from: 1,
  to: 5
};

//오류발생! range is not iterable
for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
}

// 1. for..of 최초 호출 시, Symbol.iterator가 호출됩니다.
range[Symbol.iterator] = function() {

  // Symbol.iterator는 이터레이터 객체를 반환합니다.
  // 2. 이후 for..of는 반환된 이터레이터 객체만을 대상으로 동작하는데, 이때 다음 값도 정해집니다.
  return {
    current: this.from,
    last: this.to,

    // 3. for..of 반복문에 의해 반복마다 next()가 호출됩니다.
    next() {
      // 4. next()는 값을 객체 {done:.., value :...}형태로 반환해야 합니다.
      if (this.current <= this.last) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    }
  };
};

// 이제 의도한 대로 동작합니다!
for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
}
```

### 유사배열 (array-like)

유사배열은 인덱스와 `length` 프로퍼티가 있어서 배열처럼 보이는 객체입니다. 대표적으로 함수의 arguments 객체가 유사배열입니다.

이 유사배열은 말 그대로 유사배열이기 때문에 배열과 관련된 메서드를 아무것도 사용할 수 없습니다. 만약 유사배열 또는 iterable을 진짜 배열로 만들고 싶을 때는 `Array.from` 함수를 사용하면 됩니다

```jsx
let arrayLike = {
  0: "Hello",
  1: "World",
  length: 2
};

let arr = Array.from(arrayLike); // 유사배열을 배열로 만들어 줍니다
alert(arr.pop()); // World (메서드가 제대로 동작합니다.)
```

# 맵과 셋

### 맵 (Map)

맵은 키와 값으로 데이터를 저장하는 자료구조이기 때문에 객체와 매우 유사합니다. 하지만 객체는 키로 문자열만 사용가능하지만 맵은 다양한 자료형, 특히 객체를 사용할 수 있습니다

그렇기 때문에 키를 객체로 사용하고 싶다면 객체 대신 맵을 사용하면 됩니다

```jsx
let john = { name: "John" };

// 고객의 가게 방문 횟수를 세본다고 가정해 봅시다.
let visitsCountMap = new Map();

// john을 맵의 키로 사용하겠습니다.
visitsCountMap.set(john, 123);

alert( visitsCountMap.get(john) ); // 123
```

### 셋 (Set)

셋은 중복을 허용하지 않는 값을 모아놓은 특별한 자료구조입니다. 셋에 키가 없는 값이 저장됩니다. 중복이 없는 유일한 값들의 자료를 다룰 때는 셋을 사용하면 됩니다

```jsx
let set = new Set();

let john = { name: "John" };
let pete = { name: "Pete" };
let mary = { name: "Mary" };

// 어떤 고객(john, mary)은 여러 번 방문할 수 있습니다.
set.add(john);
set.add(pete);
set.add(mary);
set.add(john);
set.add(mary);

// 셋에는 유일무이한 값만 저장됩니다.
alert( set.size ); // 3

for (let user of set) {
  alert(user.name); // // John, Pete, Mary 순으로 출력됩니다.
}
```

### 위크맵과 위크셋 (WeakMap / WeakSet)

위크맵/위크셋을 사용하면 맵/셋과 달리 키로 쓰인 객체가 더 이상 사용되지 않으면 가비지 컬렉션의 대상이 되어 삭제됩니다

```jsx
//WeakMap
let john = { name: "John" };

let weakMap = new WeakMap();
weakMap.set(john, "...");

john = null; // john 객체는 메모리에서 삭제됩니다. weakMap에서도 john 요소가 삭제됩니다

//WeakSet
let john = { name: "John" };

let visitedSet = new WeakSet();
visitedSet.add(john);

john = null; // john 객체는 메모리에서 삭제됩니다. WeakSet에서도 john 요소가 삭제됩니다
```

# 구조 분해 할당

### 배열 분해

```jsx
let arr = ["Bora", "Lee"]
let [firstName, surname] = arr;
alert(firstName); // Bora
alert(surname);  // Lee

let user = {
  name: "John",
  age: 30
};
Object.entries(user).forEach(([key, value]) => {
	alert(key + value);
});

let [name1, name2, ...rest] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];
alert(rest[0]); // Consul
alert(rest[1]); // of the Roman Republic
alert(rest.length); // 2
```

### 객체 분해

```jsx
let options = {
  title: "Menu",
  width: 100,
  height: 200,

  size: 300,
	depth: 5,
};
let {title, width: w, height: h, ...rest} = options;
alert(title);  // Menu
alert(w);  // 100
alert(h); // 200
alert(rest.depth); //5
```

### 함수 매개변수 응용

아래와 같이 함수 매개변수를 받으면 함수 호출시 불편함이 생깁니다

- 매개변수의 위치를 정확하게 맞추어야 합니다
- 호출하는 코드를 보았을 때 어떠한 값인지 알 수 없습니다

```jsx
function showMenu(title, width, height, items) {
  alert( `${title} ${width} ${height}` ); // My Menu 200 100
  alert( items ); // Item1, Item2
}

showMenu("My menu", 200, 100, ["Item1", "Item2"]);
```

구조 분해를 사용하면 위의 문제를 해결할 수 있어 더 좋은 패턴이 됩니다

```jsx
// 함수에 전달할 객체
let options = {
  title: "My menu",
  items: ["Item1", "Item2"],
	width: 200,
	height: 100,
};

// 똑똑한 함수는 전달받은 객체를 분해해 변수에 즉시 할당함
function showMenu({ title, width, height, items }) {
  alert( `${title} ${width} ${height}` ); // My Menu 200 100
  alert( items ); // Item1, Item2
}

showMenu(options);
```

# Date 객체와 날짜

```jsx
new Date(); // 현재 날짜 및 시간
new Date("2017-01-26"); //문자열 날짜 및 시간. 자동으로 구문분석됨
new Date(2011, 0 , 1); //2011년 1월 1일, 월은 0(1월) ~ 11(12월)

let date = new Date();
date.getFullYear(); // 네 자릿수 연도
date.getMonth(); // 월, 0~11
date.getDate(); // 일
date.getDay(); // 요일, 0(일) ~ 6(토)
date.getHours(); // 시
date.getMinutes(); // 분
date.getSeconds(); // 초

//자동 고침
let date = new Date(2016, 1, 30);
date.setDate(date.getDate() + 2);
alert( date ); // 2016년 2월 1일

date.setDate(0); // 일의 최솟값은 1이므로 0을 입력하면 전 달의 마지막 날을 설정한 것과 같은 효과를 봅니다.
alert( date ); // 2016년 1월 31일
```

# JSON과 메서드

`JSON`이란 Javascript 객체 문법을 따르는 문자 기반의 데이터 포맷입니다. 쉽게 말해 객체를 문자열로 바꿔 주기 때문에 네트워크 통신 시에 유용합니다

하지만 모든 객체가 JSON으로 변환될 수 있는 것은 아닙니다. 다음의 프로퍼티는 변환시 무시됩니다

- 함수 프로퍼티 (메서드)
- 심볼형 프로퍼티
- 값이 undefined인 프로퍼티

또한, 객체 내에 순환참조가 있는 경우 변환이 불가합니다

JSON은 주석을 지원하지 않습니다. 설정 파일 중에 JSON 파일이 많은데 이 경우 주석 작성이 기본적으로 불가능합니다. (예외적으로 주석 작성이 가능한 JSON5 같은 포맷도 있습니다)

### 기본 활용

```jsx
// stringify
JSON.stringify({ ... }); // JSON으로 변환된 문자열 반환

let user = {
  name: "John",
  age: 25,
  roles: {
    isAdmin: false,
    isEditor: true
  }
};

alert(JSON.stringify(user)); // 공백없이 변환
alert(JSON.stringify(user, null, 2));
/* 공백 문자 두 개를 사용하여 들여쓰기함: ex. 로그를 찍는 경우
{
  "name": "John",
  "age": 25,
  "roles": {
    "isAdmin": false,
    "isEditor": true
  }
}
*/

// parse
JSON.parse("..."); // JSON 문자열을 javascript 객체로 변환
```