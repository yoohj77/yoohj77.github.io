---
layout: default
title: 타입 호환
parent: TypeScript
nav_order: 15
---

# 타입 호환

타입스크립트의 타입 호환성은 `구조적 타이핑(structual typing)`을 기반으로 합니다. 구조적 타이핑이란 오직 멤버만으로 타입을 관계시키는 방식입니다. 명목적 타이핑(nominal typing) 과는 대조적입니다

아래 코드는 C#이나 Java와 같은 명목적 타입 언어에서는 오류가 발생하지만 타입스크립트에서는 구조적 타이핑으로 인해 오류가 발생하지 않습니다

```tsx
interface Named {
    name: string;
}
class Person {
    name: string;
}
let p: Named;
// 성공, 구조적 타이핑이기 때문입니다.
p = new Person();
```

# 구조적 타입 시스템의 기본

`y`가 최소한 `x`와 동일한 멤버를 가지고 있다면 `y`는 `x`에 호환됩니다

```tsx
interface Named {
    name: string;
}
let x: Named;
let y = { name: "Alice", location: "Seattle" };
x = y; // 호환되기 때문에 할당이 가능합니다

function greet(n: Named) {
    console.log("Hello, " + n.name);
}
greet(y); // Named 인터페이스에 호환되기 때문에 호출 가능합니다
```

# 함수의 호환

다음의 코드에서 x와 y는 반환 타입이 동일하고 x의 모든 매개변수를 y가 모두 가지고 있기 때문에 x는 y에 호환되지만, y의 모든 매개변수를 x가 모두 가지고 있지 않기 때문에 y는 x에 호환되지 않습니다

```tsx
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

y = x; // 성공
x = y; // 오류
```

반환 타입이 다른 경우에도 매개변수와 같은 원칙으로 호환이 됩니다

```tsx
let x = () => ({name: "Alice"});
let y = () => ({name: "Alice", location: "Seattle"});
x = y; // 성공
y = x; // 오류, x()는 location 프로퍼티가 없습니다.
```

# 이넘의 호환

이넘은 숫자와 호환되며 숫자는 이넘과 호환됩니다. 다른 이넘 타입의 이넘 값은 호환되지 않는 것으로 간주됩니다

```tsx
enum Status {
  Ready,
  Waiting,
}
enum Color {
  Red,
  Blue,
  Green,
}
let status = Status.Ready;
let x: number = status; // 성공
status = Color.Green; // 오류
```

# 클래스의 호환

두 클래스 타입을 비교할 때 정적 멤버(static member)와 생성자(constructor)를 제외하고 속성만 비교합니다

```tsx
class Hulk {
  handSize: number;
  constructor(name: string, numHand: number) { }
}

class Captain {
	static x: number;
  handSize: number;
  constructor(numHand: number) { }
}

let a: Hulk;
let s: Captain;

a = s;  // OK
s = a;  // OK
```

# 제네릭의 호환

제네릭은 타입 변수가 속성에 할당되었는지를 기준으로 호환여부가 결정됩니다. 속성에 할당되지 않으면 호환이 가능하고 속성에 할당되었으면 호환이 되지 않습니다

```tsx
interface Empty<T> {
}
let x: Empty<number>;
let y: Empty<string>;

x = y;  // OK, because y matches structure of x
```