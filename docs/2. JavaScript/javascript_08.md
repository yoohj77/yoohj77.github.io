---
layout: default
title: 객체 프로퍼티 설정
parent: JavaScript
nav_order: 8
---

# 객체 프로퍼티 설정

# 프로퍼티 getter와 setter

객체의 프로퍼티는 두 종류로 나뉩니다

1. `데이터 프로퍼티` - 일반적인 객체의 프로퍼티
2. `접근자 프로퍼티` - getter와 setter함수로 이루어진 프로퍼티입니다. 외부에서는 일반적인 프로퍼티처럼 보입니다

### 예시 1

```jsx
let user = {
  name: "John",
  surname: "Smith",

  get fullName() {
    return `${this.name} ${this.surname}`;
  },

  set fullName(value) {
    [this.name, this.surname] = value.split(" ");
  }
};

alert(user.fullName); // John Smith
user.fullName = "Alice Cooper"; // 주어진 값을 사용해 set fullName이 실행됩니다.

alert(user.name); // Alice
alert(user.surname); // Cooper
```

### 예시 2