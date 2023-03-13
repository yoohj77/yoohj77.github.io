---
layout: default
title: 타입 별칭
parent: TypeScript
nav_order: 8
---

# 타입 별칭

타입 별칭은 특정 타입이나 인터페이스를 참조할 수 있는 타입 변수를 의미합니다. 타입 별칭은 새로운 타입 값을 하나 생성하는 것이 아니라 정의한 타입에 대해 나중에 쉽게 참고할 수 있게 이름을 부여하는 것과 같습니다

```tsx
type MyName = string;
const name: MyName = 'capt';

type Developer = {
  name: string;
  skill: string;
}

type Direction = 'Up' | 'Down' | 'Left' | 'Right';
```

# type vs interface

얼핏보면 타입 별칭과 인터페이스는 차이가 없어 보입니다. 하지만 다음과 같은 엄연한 차이가 존재합니다

1. 인터페이스는 상속할 수 있지만 타입 별칭은 상속할 수 없습니다
2. 인터페이스는 확장이 가능하지만 타입 별칭은 확장이 불가합니다

```jsx
interface Window {
  title: string
}

// 기존 인터페이스에 새 필드를 추가. 타입 별칭은 이러한 확장이 불가하다
interface Window {
  ts: TypeScriptAPI
}
```

그렇기 때문에 완전히 새로운 타입을 선언할 때는 interface로 선언하고 이미 존재하는 타입을 참조하여 타입을 선언하는 경우에만 type을 사용하는 것이 좋습니다