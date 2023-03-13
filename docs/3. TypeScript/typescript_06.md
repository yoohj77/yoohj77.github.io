---
layout: default
title: 이넘
parent: TypeScript
nav_order: 6
---


# 이넘 (enum)

# 숫자형 이넘

숫자형 이넘은 초기 값을 주면 초기 값부터 차례로 1씩 증가합니다. 초기 값이 없으면 0부터 증가합니다

```tsx
enum Direction {
    Up, // 0
    Down, // 1
}

function go(direction: Direction) {
    switch (direction) {
        case Direction.Up:
            console.log("go up");
            break;
        case Direction.Down:
            console.log("go down");
            break;
    }
}

go(Direction.Down);
```

# 문자형 이넘

문자형 이넘은 모든 값을 전부 문자형으로 정의해야 합니다

```tsx
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```

# 런타임 시점에서의 이넘 특징

이넘은 런타임시에 실제 객체 형태로 존재합니다. 그렇기 때문에 다음의 코드가 정상적으로 실행됩니다

```tsx
enum E {
  X, Y, Z
}

function getX(obj: { X: number }) {
  return obj.X;
}
getX(E); // 이넘 E의 X는 숫자이기 때문에 정상 동작
```

위의 코드를 컴파일하면 다음과 같이 이넘 객체가 생성되는 것을 볼 수 있습니다

```tsx
var E;
(function (E) {
    E[E["X"] = 0] = "X";
    E[E["Y"] = 1] = "Y";
    E[E["Z"] = 2] = "Z";
})(E || (E = {}));
function getX(obj) {
    return obj.X;
}
getX(E); // 이넘 E의 X는 숫자이기 때문에 정상 동작
```

# 리버스 매핑

리버스 매핑은 숫자형 이넘에만 존재하는 특징입니다. 이넘의 키로 값을 얻거나 값으로 키를 얻을 수 있습니다

```tsx
enum Enum {
  A
}
let a = Enum.A; // 키로 값을 획득 하기
let keyName = Enum[a]; // 값으로 키를 획득 하기
```

# const enum

const enum은 일반 enum과 달리 컴파일시 완전히 제거됩니다. 대신 enum의 값이 사용되는 곳에 inline으로 들어가는 방식으로 컴파일 됩니다.

```tsx
const enum Direction {
  Up,
  Down,
  Left,
  Right,
}
 
let directions = [
  Direction.Up,
  Direction.Down,
  Direction.Left,
  Direction.Right,
];
```

위 코드는 다음과 같이 컴파일 됩니다

```tsx
let directions = [
    0 /* Direction.Up */,
    1 /* Direction.Down */,
    2 /* Direction.Left */,
    3 /* Direction.Right */,
];
```