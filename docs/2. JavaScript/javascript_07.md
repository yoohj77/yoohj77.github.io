---
layout: default
title: 렉시컬 환경
parent: JavaScript
nav_order: 7
---

# 렉시컬 환경

자바스크립트에서는 실행 중인 함수, 코드 블록 {…}, 스크립트 전체는 `렉시컬 환경(Lexical Environment)`이라 불리는 내부 숨김 연관 객체를 갖습니다

렉시컬 환경 객체는 두 부분으로 구성됩니다

1. `환경 레코드` - 모든 지역 변수를 프로퍼티로 저장하고 있는 객체
2. `외부 렉시컬 환경 참조` - 외부 코드와 연관됨

### 예시 1

다음 코드에서 해당 스코프에 대한 렉시컬 환경이 하나 생성이 되고 환경 레코드에 해당 스코프의 지역 변수가 저장됩니다. 외부 참조할 렉시컬 환경이 없기 때문에 null 입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f799f1c4-2192-46b3-a721-2ad5de05ae64/Untitled.png)

### 예시 2

다음 코드가 실행되면서 렉시컬 환경은 다음과 같이 변화합니다.

- 최초 스코프 내의 모든 지역 변수가 `uninitialized` 상태로 올라옵니다. 그렇기 때문에 선언한 것보다 먼저 변수를 사용하면 에러가 발생합니다
- 이후 변수에 값이 할당되면서 렉시컬 환경에 있는 값이 변경됩니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/08a2a180-4ba8-46fe-9026-1d33b417a3df/Untitled.png)

### 예시 3

함수가 호출되었을 때 실행 중인 함수의 렉시컬 환경은 다음과 같습니다

- 함수 스코프에 대한 렉시컬 환경이 생성됩니다. 아래 예시에서는 `name` 매개변수가 생성됩니다
- 이 함수는 외부 환경이 존재합니다. 그렇기 때문에 `외부 렉시컬 환경` 을 참조하게 됩니다. 아래 예시에서 참조하는 외부 렉시컬 환경에는 `phrase` 와 `say` 변수 및 함수가 존재합니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7aaa7abe-149d-4845-9555-9a74d66864a3/Untitled.png)

이 때, 변수 `phrase`와 `name` 값을 다음과 같이 가져옵니다. 자신의 렉시컬 환경에서 가져오거나 참조된 외부 렉시컬 환경에서 가져옵니다. 변수를 찾을 때 까지 참조된 외부 렉시컬 환경을 계속 거슬러 올라가 탐색합니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ad4a4031-f1f3-4446-9c0e-4047b5bfdeae/Untitled.png)

### 예시 4 (클로저)

다음과 같이 함수를 반환하는 경우를 살펴보겠습니다

- 함수의 숨김 프로퍼티로 `[[Environment]]`를 갖습니다. 이 값은 함수가 생성될 때의 렉시컬 환경을 기억합니다
- 아래 코드에서 `[[Environment]]`는 `makeCounter` 함수가 실행되면서 생성된 렉시컬 환경을 참조합니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f4988182-ef19-4ac7-bb30-2a2564876afb/Untitled.png)

이 반환된 함수가 호출되었을 때의 렉시컬 환경은 다음과 같습니다

- 해당 반환된 함수의 렉시컬 환경이 생성됩니다
- `[[Environment]]`에 들어 있던 렉시컬 환경을 외부 렉시컬 환경으로 참조하게 됩니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/24933827-3e56-45a7-93a8-fdae2281dfb1/Untitled.png)

결국 계속 반복적으로 `makeCounter` 함수를 실행하면 서로 다른 독립적인 렉시컬 환경이 생성되게 되고 그렇기 때문에 생성된 `conter` 함수 또한 서로 완전히 독립적으로 동작하게 됩니다.

이러한 함수를 클로저라 하며 이러한 원리로 인해 다음과 같은 코드의 동작이 가능해 집니다