---
layout: default
title: 타입 가드
parent: TypeScript
nav_order: 16
---

# 타입 가드

타입 가드를 사용하면 조건문에서 객체의 타입을 좁혀나갈 수 있습니다

# typeof

```tsx
function doSomething(x: number | string) {
  if (typeof x === 'string') {
	  console.log(x.substr(1)); // string이기 때문에 에러가 발생하지 않습니다
  }
  x.substr(1); // Error: `x`가 `string`이라는 보장이 없습니다
}
```

# instanceof

```tsx
class Foo {
  foo = 123;
}

class Bar {
  bar = 123;
}

function doStuff(arg: Foo | Bar) {
  if (arg instanceof Foo) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error!
  }
  else {  // Bar
    console.log(arg.foo); // Error!
    console.log(arg.bar); // ok
  }
}

doStuff(new Foo());
doStuff(new Bar());
```

# in

```tsx
interface A {
  x: number;
}
interface B {
  y: string;
}

function doStuff(q: A | B) {
  if ('x' in q) {
	  // q: A
  }
  else {
	  // q: B
  }
}
```

# 리터럴 타입 가드

```tsx
type TriState = 'yes' | 'no' | 'unknown';

function logOutState(state:TriState) {
  if (state == 'yes') {
	  console.log('사용자가 yes를 골랐습니다');
  } else if (state == 'no') {
	  console.log('사용자가 no를 골랐습니다');
  } else {
	  console.log('사용자가 아직 결정을 내리지 않았습니다.');
  }
}
```

# 사용자 정의 타입 가드

`is` 키워드를 사용하여 타입 추론에 도움을 줄 수 있습니다

```tsx
interface Foo {
  foo: number;
  common: string;
}

interface Bar {
  bar: number;
  common: string;
}

/**
 * 사용자 정의 Type Guard!
 */
function isFoo(arg: any): arg is Foo {
  return arg.foo !== undefined;
}

/**
 * 사용자 정의 Type Guard 사용 예시
 */
function doStuff(arg: Foo | Bar) {
  if (isFoo(arg)) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error!
  }
  else {
    console.log(arg.foo); // Error!
    console.log(arg.bar); // ok
  }
}

doStuff({ foo: 123, common: '123' });
doStuff({ bar: 123, common: '123' });
```