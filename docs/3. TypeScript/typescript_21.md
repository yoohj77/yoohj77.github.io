---
layout: default
title: 유틸리티 타입
parent: TypeScript
nav_order: 20
---
# 유틸리티 타입

타입스크립트는 일반적인 타입 변환을 쉽게 하기 위해서 몇 가지 유틸리티 타입을 제공합니다. 이러한 유틸리티는 전역으로 사용 가능합니다

# 자주 사용되는 유틸리티 타입

### Partial

특정 타입의 모든 부분 집합을 만족하는 타입을 정의할 수 있습니다. 모든 프로퍼티가 옵셔널로 변경되는 것과 같습니다

```tsx
interface Address {
  email: string;
  address: string;
}

type MayHaveEmail = Partial<Address>;
const me: MayHaveEmail = {}; // 가능
const you: MayHaveEmail = { email: 'test@abc.com' }; // 가능
const all: MayHaveEmail = { email: 'capt@hero.com', address: 'Pangyo' }; // 가능
```

### Pick

특정 타입에서 몇 개의 속성을 선택하여 타입을 정의할 수 있습니다

```tsx
interface Hero {
  name: string;
  skill: string;
}
const human: Pick<Hero, 'name' | 'skill' | 'dsf'> = {
  name: '스킬이 없는 사람',
};
```

### Omit

특정 타입에서 지정된 속성만 제거한 타입을 정의해 줍니다

```tsx
interface AddressBook {
  name: string;
  phone: number;
  address: string;
  company: string;
}
const phoneBook: Omit<AddressBook, 'address'> = {
  name: '재택근무',
  phone: 12342223333,
  company: '내 방'
}
const chingtao: Omit<AddressBook, 'address'|'company'> = {
  name: '중국집',
  phone: 44455557777
}
```