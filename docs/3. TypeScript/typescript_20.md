---
layout: default
title: 인덱스 접근 타입 /  조건부 타입
parent: TypeScript
nav_order: 19
---

# 인덱스 접근 타입 /  조건부 타입

# 인덱스 접근 타입

특정 프로퍼티의 타입을 가져오기 위해 인덱스 접근 타입을 사용할 수 있습니다

```tsx
type Person = { age: number; name: string; alive: boolean };
type Age = Person["age"]; // type Age = number;

type P1 = Person["age" | "name"]; // string | number
type P2 = Person[keyof Person]; // string | number | boolean
```

# 조건부 타입

삼항 연산자 조건문같은 형태를 통해 조건에 따라 타입을 결정할 수 있고 이를 `조건부 타입`이라 합니다

```tsx
interface Animal {
  live(): void;
}
interface Dog extends Animal {
  woof(): void;
}
 
type Example1 = Dog extends Animal ? number : string; // number
 
type Example2 = RegExp extends Animal ? number : string; // string
```

### 오버로드 단순화

다음은 조건부 타입을 사용해 오버로드를 단일 함수로 단순화하는 예시입니다

```tsx
interface IdLabel {
  id: number /* some fields */;
}
interface NameLabel {
  name: string /* other fields */;
}
 
function createLabel(id: number): IdLabel;
function createLabel(name: string): NameLabel;
function createLabel(nameOrId: string | number): IdLabel | NameLabel;
function createLabel(nameOrId: string | number): IdLabel | NameLabel {
  throw "unimplemented";
}
```

위의 오버로드를 아래와 같이 단일 함수로 단순화할 수 있습니다

```tsx
type NameOrId<T extends number | string> = T extends number ? IdLabel : NameLabel;

function createLabel<T extends number | string>(idOrName: T): NameOrId<T> {
  throw "unimplemented";
}
 
let a = createLabel("typescript");
let b = createLabel(2.8);
let c = createLabel(Math.random() ? "hello" : 42);
```

### 조건부 타입으로 제한하기

배열 타입이면 배열의 개별 요소 타입으로 평탄화 시키지만, 배열 타입이 아니면 그대로 유지하는 Flatten 타입을 만들 수 있습니다

```tsx
type Flatten<T> = T extends any[] ? T[number] : T;

type Str = Flatten<string[]>; // string
type Num = Flatten<number>; // number
```

### 조건부 타입 내에서 추론하기

조건부 타입은 `infer` 키워드를 사용해서 유용한 타입 별칭을 사용할 수 있습니다

```tsx
type GetReturnType<Type> = Type extends (...args: never[]) => infer Return 
  ? Return : never;

type Num = GetReturnType<() => number>; // number
type Str = GetReturnType<(x: string) => string>; // string
```