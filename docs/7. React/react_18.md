---
layout: default
title: 16. 리덕스 라이브러리 이해하기
parent: React
nav_order: 18
---

# 16. 리덕스 라이브러리 이해하기

리덕스를 이해하기 위해 React에 바로 적용하기 보다는 먼저 바닐라 자바스크립트로 사용해 보도록 하겠습니다. 리덕스를 활용하여 간단한 카운터 앱을 만들어 보겠습니다

# 1. 프로젝트 생성

프로젝트를 새로 만들겠습니다. 설정없이 빠르게 예제 앱을 만들기 위해서 `parcel` 번들러를 사용하겠습니다. 많은 작업들이 설정없이 자동으로 이루어지는 매우 편한 툴입니다

```tsx
> npm init -y
> npm install parcel-bundler
```

카운터 앱을 만들기 위해 다음과 같이 index.html과 main.ts 파일을 작성합니다. Typescript 역시 설정없이 parcel을 통해 사용 가능합니다

```tsx
// 📁 index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <h1>바닐라 리덕스 카운터</h1>
    <div id="number"></div>
    <div>
      <button id="btnMinus">-1</button>
      <button id="btnPlus">+1</button>
    </div>
    <script src="main.ts"></script>
  </body>
</html>
// 📁 main.ts
const numberEl = document.getElementById("number") as HTMLElement;
const btnMinusEl = document.getElementById("btnMinus") as HTMLElement;
const btnPlusEl = document.getElementById("btnPlus") as HTMLElement;

let count = 0;

btnMinusEl.onclick = () => {
  count--;
  numberEl.textContent = count.toString();
};

btnPlusEl.onclick = () => {
  count++;
  numberEl.textContent = count.toString();
};

numberEl.textContent = count.toString();
```

parcel을 실행하면 자동으로 번들이 이루어 지면서 개발서버가 실행됩니다. 브라우저에서 접속해 보면 카운터가 잘 작동하는 것을 확인할 수 있습니다

매번 실행 명령어를 작성하는 것이 번거로우면 packge.json에 start 스크립트로 등록해 놓아도 좋습니다

```tsx
> npx parcel src/index.html
```

# 2. 카운터 앱 개선하기

먼저 코드 중복을 없애기 위해 render 함수를 만들어 호출합니다

```tsx
...
let count = 0;

btnMinusEl.onclick = () => {
  count--;
  render();
};

btnPlusEl.onclick = () => {
  count++;
  render();
};

const render = () => {
  numberEl.textContent = count.toString();
};

render();
```

값(상태)에 직접 접근하는 것은 좋지 않은 패턴입니다. 상태를 변경하는 함수를 만들고 이 함수를 호출하도록 합니다

```tsx
...
btnMinusEl.onclick = () => {
  dispatch("minus");
  render();
};

btnPlusEl.onclick = () => {
  dispatch("plus");
  render();
};

let count = 0;
const dispatch = (type: "minus" | "plus") => {
  switch (type) {
    case "minus":
      count--;
      break;
    case "plus":
      count++;
      break;
  }
};
```

dispatch를 하면 상태가 바뀌기 때문에 항상 새로 렌더링해야 합니다. 그런데 dispatch 안에서 render를 직접 호출해 주기 보다는 dispatch 후 작업을 주입받는 방식이 더 유연한 방식입니다

```tsx
...
btnMinusEl.onclick = () => {
  dispatch("minus");
};

btnPlusEl.onclick = () => {
  dispatch("plus");
};

const _listeners: { (): void }[] = [];
const subscribe = (listener: { (): void }) => {
  _listeners.push(listener);
};

let count = 0;
const dispatch = (type: "minus" | "plus") => {
  switch (type) {
    case "minus":
      count--;
      break;
    case "plus":
      count++;
      break;
  }
  _listeners.forEach((listener) => listener());
};

const render = () => {
  numberEl.textContent = count.toString();
};

subscribe(render);
render();
```

count 상태와 관련된 코드를 파일을 따로 만들어 분리합니다. 이렇게 하면 로직이 매우 단순해 집니다

```tsx
// 📁 main.ts
import { dispatch, getCount, subscribe } from "./countStore";

const numberEl = document.getElementById("number") as HTMLElement;
const btnMinusEl = document.getElementById("btnMinus") as HTMLElement;
const btnPlusEl = document.getElementById("btnPlus") as HTMLElement;

btnMinusEl.onclick = () => {
  dispatch("minus");
};

btnPlusEl.onclick = () => {
  dispatch("plus");
};

const render = () => {
  const count = getCount();
  numberEl.textContent = count.toString();
};

subscribe(render);
render();
// 📁 countState.ts
let count = 0;
const _listeners: { (): void }[] = [];

export const getCount = () => count;

export const subscribe = (listener: { (): void }) => {
  _listeners.push(listener);
};

export const dispatch = (type: "minus" | "plus") => {
  switch (type) {
    case "minus":
      count--;
      break;
    case "plus":
      count++;
      break;
  }
  _listeners.forEach((listener) => listener());
};
```

### Flux 패턴

이와 같이 개선한 패턴을 `Flux` 패턴이라 합니다. `Action` / `Dispatcher` / `Store` / `View` 의 요소로 나누고 이들간에 데이터가 단방향으로 흐르도록 하면서 데이터와 화면이 자연스럽게 갱신되는 방식입니다

Flux 패턴을 사용하면 각 요소의 책임이 명확하고 단방향의 관계를 갖기 때문에 복잡한 대규모 어플리케이션을 단순한 구조로 관리할 수 있는 장점이 있습니다

- Action - 데이터를 변경하고자 하는 특정 행위
- Dispatcher - 특정 행위에 따른 데이터 변경 수행
- Store - 데이터를 보관하는 장소. dispatcher에 의해 데이터가 변경되면 View에 데이터가 변경되었음을 알립니다
- View - 화면. 데이터가 변경되면 화면을 갱신합니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/89b9501e-2ab7-4c26-aa66-c481ff61c654/Untitled.png)

# 3. 리덕스 적용

Flux 패턴을 사용하여 쉽게 상태를 관리할 수 있도록 제공하는 것이 리덕스 라이브러리 입니다. 리덕스를 사용하도록 변경해 보겠습니다

![reduxdataflowdiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2daa0a55-62cf-4109-b527-e1d365906289/reduxdataflowdiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif)

리덕스를 설치합니다

```tsx
> npm install redux
```

파일을 새로 생성하여 먼저 Action을 정의 합니다

```tsx
// 📁 reduxStore.ts
// 액션 정의
interface MinusAction {
  type: "minus";
}
interface PlusAction {
  type: "plus";
}
type CountAction = MinusAction | PlusAction;
```

다음으로 상태를 정의하고 초기 상태값을 정의합니다

```tsx
...
// 초기값
interface CountState {
  count: number;
}
const initialState: CountState = {
  count: 0,
};
```

상태를 변경하는 리듀서를 작성합니다. state가 없는 경우 초기 상태값을 넣습니다

```tsx
...
// 리듀서
const reducer = (state = initialState, action: CountAction) => {
  switch (action.type) {
    case "minus":
      return { ...state, count: state.count - 1 };
    case "plus":
      return { ...state, count: state.count + 1 };
    default:
      return state;
  }
};
```

최종적으로 리덕스의 `createStore` 함수로 store를 생성하고 외부에서 사용하도록 export합니다

createStore 함수는 *deprecated* 되어 있어 현재는 사용이 권장되지 않으며 `Redux Toolkit` 을 사용해야 합니다. 여기서는 일단 리덕스를 이해하기 위해 createStore 함수를 사용합니다

```tsx
import { createStore } from "redux";
...
// 스토어 생성
export const store = createStore(reducer);
```

이제 생성된 리덕스 store를 사용하도록 main.ts 파일을 수정합니다

- `store.dispatch` 로 action을 실행할 수 있습니다
- `store.getState` 로 현재 상태값을 가져올 수 있습니다
- `store.subscribe` 로 상태가 변경되는 이벤트를 구독할 수 있습니다

```tsx
// 📁 main.ts
import { store } from "./reduxStore";

const numberEl = document.getElementById("number") as HTMLElement;
const btnMinusEl = document.getElementById("btnMinus") as HTMLElement;
const btnPlusEl = document.getElementById("btnPlus") as HTMLElement;

btnMinusEl.onclick = () => {
  store.dispatch({ type: "minus" });
};

btnPlusEl.onclick = () => {
  store.dispatch({ type: "plus" });
};

const render = () => {
  const { count } = store.getState();
  numberEl.textContent = count.toString();
};

store.subscribe(render);
render();
```

# 4. 리덕스의 세 가지 규칙

다음의 규칙을 지키지 않아도 에러가 발생하는 것은 아니지만 리덕스가 올바른 상태 관리 동작을 보장하지 못합니다

### 단일 스토어

하나의 어플리케이션에는 하나의 스토어만 사용해야 합니다. 상태 관리가 복잡해질 수 있으므로 여러 개의 스토어를 생성하는 것을 권장하지 않습니다

### 읽기 전용 상태

리덕스의 상태는 읽기 전용이며 불변성을 유지해야 합니다. 내부적으로 상태가 변경되는 것을 감지하기 위해 얕은 비교 검사를 하기 때문에 이전 상태와 새로운 상태는 서로 다른 참조 주소를 가져야 합니다

### 리듀서는 순수 함수

사실 리듀서가 `순수 함수`여야 한다는 것은 상태가 읽기 전용이라는 것과 같은 규칙입니다. 순수 함수는 외부의 값을 변경하지 않아야 하는데 이는 이전 상태를 변경하지 않아야 한다는 의미입니다

side effect (부수 효과) 가 없어야 한다

순수 함수는 또한 동일한 입력에 대해 항상 동일한 값을 출력해야 합니다. 좀 더 살펴보아야 하겠지만 책이나 구글링에서는 이 또한 만족시켜야 한다고 하며 그렇기 때문에 다음의 행위가 동일한 출력을 보장할 수 없어 리듀서에서는 금지라고 합니다

- 현재시간을 가져오면 안됩니다
- 랜덤한 값을 생성하면 안됩니다
- 네트워크 요청을 해서는 안됩니다

(하지만 이에 대해 저는 동의할 수 없습니다. 만약 리듀서가 입력값에 대해 결과가 캐싱이 된다고 한다면 맞는 말이지만 캐싱하지 않고 매번 실행되기 때문에 출력값이 어떻게 나오던 사실 상관이 없습니다. 이 규칙에 대해서는 더 살펴볼 필요가 있습니다)