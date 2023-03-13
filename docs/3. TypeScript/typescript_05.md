---
layout: default
title: 인터페이스
parent: TypeScript
nav_order: 5
---


# 인터페이스

타입스크립트에서는 인터페이스를 통해 객체(함수 포함) 또는 클래스의 형태를 정의할 수 있습니다

# 인터페이스 선언

다음과 같이 객체의 인터페이스를 선언할 수 있습니다

```tsx
interface Todo {
  id: number;
  content: string;
  completed: boolean;
}

// Todo 인터페이스를 준수해야 합니다
const todo: Todo = { 
	id: 1,
	content: 'typescript',
	completed: false,
};

// 인터페이스에 정의되어 있지 않은 프로퍼티는 사용할 수 없습니다
todo.name;
```

# 옵셔널 프로퍼티

필수로 구현하는 것이 아닌 선택적으로 구현해도 되는 프로퍼티를 옵셔널 프로퍼티라고 하며 속성명에 `?` 를 붙입니다

```tsx
interface CraftBeer {
  name: string;
  hop?: number;  
}

let myBeer = {
  name: 'Saporo'
};
function brewBeer(beer: CraftBeer) {
  console.log(beer.name); // Saporo
}
brewBeer(myBeer);
```

# 읽기 전용 속성

`readonly` 키워드로 읽기 전용 속성을 만들 수 있습니다

```tsx
interface CraftBeer {
  readonly brand: string;
}

const myBeer: CraftBeer = {
  brand: 'Belgian Monk'
};
myBeer.brand = 'Korean Carpenter'; // error!
```

# 읽기 전용 배열

`ReadonlyArray<T>` 타입을 사용하면 읽기 전용 배열을 생성할 수 있습니다

```tsx
let arr: ReadonlyArray<number> = [1,2,3];
arr.splice(0,1); // error
arr.push(4); // error
arr[0] = 100; // error
```

# 함수 타입

인터페이스는 함수의 타입을 정의할 때도 사용할 수 있습니다

```tsx
interface login {
  (username: string, password: string): boolean;
}

let loginUser: login = function(id: string, pw: string) {
  console.log('로그인 했습니다');
  return true;
}
```

# 클래스 타입

클래스에서 인터페이스를 구현할 수 있습니다

```tsx
interface CraftBeer {
  beerName: string;
  nameBeer(beer: string): void;
}

class myBeer implements CraftBeer {
  beerName: string = 'Baby Guinness';
  nameBeer(b: string) {
    this.beerName = b;
  }
  constructor() {}
}
```

# 인터페이스 상속

```tsx
interface Person {
    name: string;
}
interface Drinker {
    drink: string;
}
interface Developer extends Person, Drinker {
    skill: string;
}
const fe: Developer = {
    name: "josh",
    skill: "TypeScript",
    drink: "Beer",
};
```

# 하이브리드 타입

인터페이스를 여러 가지 타입을 조합하여 만들 수 있습니다

```tsx
interface CraftBeer {
    (beer: string): string;
    brand: string;
    brew(): void;
}

function myBeer(): CraftBeer {
    const hybrid = (beer: string) => beer;
    hybrid.brand = "Beer Kitchen";
    hybrid.brew = () => {};
    return hybrid;
}

const brewedBeer = myBeer();
brewedBeer("My First Beer");
brewedBeer.brand = "Pangyo Craft";
brewedBeer.brew();
```

# 인덱스 시그니처

타입의 프로퍼티명을 미리 알지 못하지만, 프로퍼티와 값의 타입은 아는 경우들이 있습니다. 이런 경우에 `인덱스 시그니처`를 통해 키와 값의 타입을 선언할 수 있습니다

```tsx
interface FruitDictionary {
    [key: string]: number;
}

const dic: FruitDictionary = {};
dic.apple = 100;
dic.orange = "good"; // Error
```

인덱스 시그니처를 사용하는 경우 모든 프로퍼티는 선언된 인덱스 시그니처를 만족시켜야 합니다