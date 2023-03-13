---
layout: default
title: Redux Toolkit
parent: React
nav_order: 20
---

# 추가: Redux Toolkit

리덕스팀에서는 리덕스를 사용할 때 이전 방법이 아닌 Redux Toolkit 을 사용하는 것을 권장하고 있습니다

# 1. 목적

`Redux Toolkit` 은 리덕스 로직을 작성하는 표준적인 방법을 제공합니다. 리덕스에 대한 다음의 문제를 해결하기 위해 만들어 졌습니다

- 리덕스 store를 설정하는 것이 너무 복잡하다
- 리덕스를 유용하게 사용하려면 너무 많은 라이브러리를 추가해야 한다
- 리덕스는 너무 많은 보일러플레이트 코드를 필요로 한다

# 2. 설치

### Create React App

```tsx
> npx create-react-app my-app --template redux
> npx create-react-app my-app --template redux-typescript
```

### 기존 프로젝트

```tsx
> npm install @reduxjs/toolkit
```

# 3. Redux Toolkit APIs

### configureStore()

단순한 설정과 좋은 기본값을 제공하는 store 생성 함수입니다. 자동으로 Redux DevTools 과 연결도 설정됩니다

### createReducer()

switch/case 문에서 벗어나 더욱 간편하고 보기 쉽게 리듀서를 작성할 수 있습니다. 또한, 내부적으로  `immer` 라이브러리를 사용하기 때문에 쉽게 불변성을 유지할 수 있습니다

### createAction

action 생성 함수를 만들어 줍니다

### createSlice

리듀서 함수와 slice 명, 상태 초기값을 사용하여 쉽게 action과 리듀서를 생성할 수 있습니다

`slice` 는 state를 작게 쪼갠 조각이라는 뜻으로 state의 일부에 대한 리듀서와 action을 따로 정의하는 것이 아니라 한번에 정의하고 생성하는 방식입니다

### createSelector

`Reselect` 라이브러리의 함수로써 캐싱 등의 기능이 있는 selector를 생성할 수 있습니다

# 4. Redux Toolkit 빠르게 시작하기

### 리덕스 Store 만들기

`configureStore` 로 store를 만들 수 있습니다. `reducer` 를 필수로 설정해야 하지만 일단 빈 객체로 설정하여 생성합니다

state와 dispatch의 타입도 제공합니다

```tsx
// 📁 app/store.ts
import { configureStore } from "@reduxjs/toolkit";

export const store = configureStore({
  reducer: {},
});

export type RootState = ReturnType<typeof store.getState>;

export type AppDispatch = typeof store.dispatch;
```

### 리액트에 리덕스 Store 제공하기

```tsx
// 📁 index.tsx
...
import { store } from "./app/store";

const root = ReactDOM.createRoot(
  document.getElementById("root") as HTMLElement
);
root.render(
  <React.StrictMode>
    <BrowserRouter>
      **<Provider store={store}>**
        <App />
      **</Provider>**
    </BrowserRouter>
  </React.StrictMode>
);
```

### 리덕스 상태 slice 만들기

```tsx
// 📁 features/counter/counterSlice.ts
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

export interface CounterState {
  value: number;
}

const initialState: CounterState = {
  value: 0,
};

export const counterSlice = createSlice({
  name: "counter",
  initialState,
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action: PayloadAction<number>) => {
      state.value += action.payload;
    },
  },
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;

export default counterSlice.reducer;
```

### store에 slice 리듀서 추가하기

```tsx
// 📁 app/store.ts
...
import counterReducer from "../features/counter/counterSlice";

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});
```

### 타입 가이드를 위한 Hook 생성하기

바로 `useSelector` 와 `useDispatch` Hook을 사용해도 되지만 그러면 현재 어플리케이션의 리덕스 store에 대한 타입을 매번 명시해 줘야 합니다. 그렇기 때문에 타입을 명시하는 Hook을 어플리케이션 전용으로 만들어 사용하는 것이 좋습니다

```tsx
// 📁 app/hooks.ts
import { TypedUseSelectorHook, useDispatch, useSelector } from "react-redux";
import { AppDispatch, RootState } from "./store";

export const useAppDispatch: () => AppDispatch = useDispatch;
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

### 컴포넌트에서 리덕스 state와 action 사용하기

미리 어플리케이션 전용으로 만들어 놓은 `useAppSelector` 와 `useAppDispatch` 로 리덕스 store를 사용할 수 있습니다

```tsx
// 📁 features/counter/Counter.tsx
import { useAppDispatch, useAppSelector } from "../../app/hooks";
import { decrement, increment } from "./counterSlice";

const Counter = () => {
  const count = useAppSelector((state) => state.counter.value);
  const dispatch = useAppDispatch();

  return (
    <div>
      <button onClick={() => dispatch(increment())}>Increment</button>
      <span>{count}</span>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
    </div>
  );
};

export default Counter;
```