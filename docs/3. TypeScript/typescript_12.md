---
layout: default
title: 제네릭
parent: TypeScript
nav_order: 12
---

# 제네릭(Generics)

`제네릭`은 하나의 타입에 의존하지 않고 타입을 일반화하여 여러 타입에서 동작할 수 있도록 하는 방식을 의미합니다. 일반화를 통해 해당 컴포넌트의 재사용성을 높입니다

마치 타입을 매개변수로 받는다고 이해해도 좋습니다

# 제네릭 예시

다음의 코드에서 getText()를 호출하여 반환된 값이 특정 타입으로 한정되지 않고 `string | number` 로 반환되기 때문에 값을 사용하기가 불편합니다

```tsx
function getText(text: string | number): string | number {
    return text;
}

const str = getText("hi");
const num = getText(10);
```

이를 제네릭을 사용하면 반환 값의 타입이 하나로 특정되어 사용하기 편해 집니다. 타입 변수 `T`는 하나의 예시이며 어떠한 변수명도 사용 가능합니다

```tsx
function getText<T>(text: T): T {
    return text;
}

const str = getText<string>("hi");
const num = getText<number>(10);
```

# 제네릭 타입 변수 생략시 추론

위의 예시에서 만약 제네릭 타입 변수를 지정하지 않고 호출해도 오류가 발생하지 않습니다. 왜냐하면 text 매개변수가 넘어오기 때문에 `T` 를 추론할 수 있기 때문입니다

```tsx
function getText<T>(text: T): T {
    return text;
}

const str = getText("hi"); // T는 "hi" 로 추론됩니다
const num = getText(10); // T는 10으로 추론됩니다
```

# 제네릭 타입 변수

다음의 코드에서 `T` 가 어떤 타입인지 알 수 없기 때문에 `length` 프로퍼티를 사용하려 할 때 에러가 발생합니다

```tsx
function logText<T>(text: T): T {
  console.log(text.length); // Error: T doesn't have .length
  return text;
}
```

반면에  다음과 같이 배열이라는 것을 확실히 알 수 있다면 `length` 프로퍼티를 사용할 수 있습니다

```tsx
function logText<T>(text: T[]): T[] {
  console.log(text.length); // 제네릭 타입이 배열이기 때문에 `length`를 허용합니다.
  return text;
}
```

# 제네릭 인터페이스

다음의 함수에 대하여 A부터 D까지의 타입은 모두 동일한 타입입니다

```tsx
function logText<T>(text: T): T {
    return text;
}

type A = <T>(text: T) => T;
type B = { <T>(text: T): T };

interface C {
    <T>(text: T): T;
}

interface D<T> {
    (text: T): T;
}
```

# 제네릭 클래스

클래스를 선언할 때 제네릭을 사용할 수 있습니다. 단, 제네릭 타입 변수는 인스턴스가 생성될 때 결정되기 때문에 정적 프로퍼티나 정적 메소드는 제네릭 타입 변수를 사용할 수 없습니다.

```jsx
class GenericMath<T> {
  pi: T;
  sum: (x: T, y: T) => T;
}

let math = new GenericMath<number>();
```

# 제네릭 제약 조건

특별히 명시하지 않으면 제네릭 타입에 어떠한 타입도 넣을 수가 있는데 이를 `extends` 키워드를 사용해 특정한 타입으로 제약할 수 있습니다