---
layout: default
title: 기본 타입
parent: TypeScript
nav_order: 3
---

# 기본 타입

# 타입 선언

타입스크립트는 다음과 같이 변수명 뒤에 타입을 명시하는 것으로 타입을 선언할 수 있습니다

```jsx
let foo: string = 'hello';

// 타입에 맞지 않는 값을 할당하면 컴파일 시점에 에러가 발생합니다
let bar: number = true; // error TS2322: Type 'true' is not assignable to type 'number'.
```

# 타입 종류

타입스크립트의 기본 타입에는 다음의 타입들이 있습니다.

- Boolean
- Number
- String
- Object
- Symbol
- Array
- Tuple
- Enum
- Any
- Void
- Null
- Undefined
- Unknown
- Never

### String

```tsx
let str: string = 'hi';
```

### Number

```tsx
let num: number = 10;
```

### Boolean

```tsx
let bool: boolean = false;
```

### Object

```tsx
let obj: object = {};
```

### Symbol

```tsx
let sym: symbol = Symbol("id");
```

### Array

```tsx
let arr: number[] = [1,2,3];
let arr2: Array<number> = [1,2,3];
```

### Tuple

튜플은 배열의 길이가 고정되고 각 요소의 타입이 지정되어 있는 배열 형식을 의미합니다

```tsx
let arr: [string, number] = ['hi', 10];
```

### Enum

이넘은 특정 값(상수)들의 집합을 의미합니다. 값은 기본으로 0부터 1씩 증가하여 할당됩니다

```tsx
enum Fruit {
	Apple = 2, //값을 변경할 수 있습니다
	Pear,
	Melon
}
```

### Void

변수에서는 `undefined`와 `null`만 할당 가능하고, 함수에서는 반환 값이 없는 경우 사용합니다

```tsx
let value: void = undefined;

function func(): void {
  console.log('void func');
}
```

### Any

어떠한 자료도 가능한 타입입니다

```tsx
let str: any = 'hi';
let num: any = 10;
let arr: any = ['a', 2, true];
```

### Unknown

`unknown`은 타입스크립트의 Top Type 입니다. 타입스크립트에 존재하는 모든 타입들을 포함하여 어떤 값이든 가질 수 있지만, 그로 인해 모든 타입이 공통적으로 할 수 있는 연산 외에는 할 수 있는 것이 아무것도 없습니다. 그래서 이름 그대로 어떤 타입인지 알 수 없는 unknown 타입입니다

```tsx
let myVar: unknown = 42;
myVar = 'hello'; // 어떤 타입이든 재할당이 가능
myVar.slice(); // Error
```

### Never

어떠한 타입도 불가능한 경우에 사용됩니다