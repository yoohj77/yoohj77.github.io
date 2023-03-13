---
layout: default
title: 18. 리덕스 미들웨어를 통한 비동기 작업 관리
parent: React
nav_order: 22
---

# 18. 리덕스 미들웨어를 통한 비동기 작업 관리

API 요청을 할때는 요청에 대한 상태도 잘 관리해야 합니다. 로딩 중인지 완료했는지 실패했는지를 관리해야 하고 성공하면 응답에 대한 상태 실패하면 에러에 대한 상태를 관리해야 합니다

이러한 비동기 작업에 대한 관리는 `리덕스 미들웨어` 를 사용하면 효율적으로 편하게 상태를 관리할 수 있습니다

# 1. 작업 환경 준비

리덕스 미들웨어를 적용하기 위해 이전에 만들어 놓은 `containers/CounterContainer.tsx` 를 사용하겠습니다. `modules` 폴더에도 리듀서를 작성했었습니다

App.tsx와 index.tsx를 다음과 같이 리덕스 store를 제공하고 CounterContainer를 출력하도록 수정하고 화면을 확인합니다

```tsx
// 📁 App.tsx
import CounterContainer from "./containers/CounterContainer";

function App() {
  **return <CounterContainer />;**
}

export default App;
// 📁 index.tsx
...
import rootReducer from "./modules";
import { Provider } from "react-redux";
import { composeWithDevTools } from "redux-devtools-extension";

**const store = createStore(rootReducer, composeWithDevTools());**

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

# 2. 미들웨어란?

`리덕스 미들웨어`는 액션을 디스패치했을 때 리듀서에서 이를 처리하기에 앞서 사전에 지정된 작업들을 실행합니다. 미들웨어는 액션과 리듀서 사이의 중간자라고 볼 수 있습니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/83f9df8e-6387-4526-8059-accffdbbb7bc/Untitled.png)

### 미들웨어 만들기

미들웨어는 다음과 같이 작성합니다. `store` , `next` , `action` 이 인자로 넘어오며 이를 활용해 로직을 구현할 수 있습니다

```tsx
const middleware = (store) => (next) => (action) => {
  //
};

// 위의 화살표 함수와 같은 형태의 함수
function middleware2(store) {
  return function (next) {
    return function (action) {
      //
    };
  };
}
```

`store`는 리덕스 store 객체입니다. `next` 는 다음 미들웨어를 실행하기 위한 함수입니다. `action` 은 실행된 action 객체입니다

다음과 같이 미들웨어가 실행되고 최종적으로 리듀서가 실행됩니다. 미들웨어 실행 중간에 `store.dispatch` 를 실행하면 첫번째 미들웨어부터 다시 처리합니다

만약 미들웨어에서 `next` 를 실행하지 않으면 액션이 리듀서에 전달되지 않고 해당 액션은 무시됩니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0ab8ac46-aaaa-4d28-8801-36f247a3aea2/Untitled.png)

액션이 디스패치될 때마다 액션의 정보와 액션이 디스패치되기 전후의 상태를 콘솔에 보여 주는 로깅 미들웨어를 작성해 보겠습니다

```tsx
// 📁 lib/loggerMiddleware.ts
import { Middleware } from "redux";
import { AppState } from "../modules";

const loggerMiddleware: Middleware<{}, AppState> =
  (store) => (next) => (action) => {
    console.group(action && action.type);
    console.log("이전상태", store.getState());
    console.log("액션", action);
    next(action);
    console.log("다음 상태", store.getState());
    console.groupEnd();
  };

export default loggerMiddleware;
```

미들웨어를 store에 적용합니다

```tsx
// 📁 index.tsx
...
import { applyMiddleware, createStore } from "redux";
...
const store = createStore(rootReducer, applyMiddleware(loggerMiddleware));
```

화면에서 확인해 보면 카운터를 동작시킬 때마다 로그가 찍히는 것을 확인할 수 있습니다

### redux-logger 사용하기

직접 로거를 구현하기 보다 이미 만들어져 있는 `redux-logger` 미들웨어 라이브러리를 사용해 보겠습니다

redux-logger를 설치합니다

```tsx
> npm install redux-logger
```

store에 미들웨어를 적용합니다

```tsx
// 📁 index.tsx
...
import { createLogger } from "redux-logger";

const logger = createLogger();
const store = createStore(rootReducer, applyMiddleware(logger));
```

화면에서 확인해 보면 로거가 잘 동작되는 것을 확인할 수 있습니다

# 3. 비동기 작업을 처리하는 미들웨어 사용

리덕스의 리듀서는 동기적으로 작동하기 때문에 비동기 작업을 처리할 수가 없습니다. 대신 비동기 작업을 처리하기 위해서 리덕스 미들웨어를 사용할 수 있습니다. 미들웨어에서는 직접 `next` 함수를 호출하여 다음 미들웨어를 처리하기 때문에 중간에 비동기 작업을 처리할 수 있게 됩니다

비동기 작업 처리를 위한 미들웨어 라이브러리는 다양하며 여기서는 `redux-thunk` 를 사용해 보겠습니다

## redux-thunk

Redux Toolkit에도 기본 내장되어 있는 미들웨어로 많이 사용되고 있는 라이브러리입니다

### Thunk란?

`Thunk` 는 특정 작업을 나중에 할 수 있도록 미루기 위해 함수 형태로 감싼 것을 의미합니다. 이러한 thunk 함수를 action객체로 넘겨 dispatch하면 redux-thunk가 미들웨어로 이를 실행해 줍니다

redux-thunk의 thunk 함수는 인자로 `dispatch` 와 `getState` 함수가 넘어옵니다

```tsx
// thunk 함수를 생성하는 함수입니다
const someThunkCreator = () => (dispatch, getState) => {
	//
}
...
// thunk 함수를 생성하여 이를 action으로 dispatch 합니다
dispatch(someThunkCreator());
```

redux-thunk 미들웨어는 다음과 같이 구현되어 있습니다

action이 함수인 경우 실행하고 next를 호출하지 않아 action이 중단됩니다

나머지는 바로 next를 호출하여 아무런 작업없이 다음 미들웨어가 실행됩니다

```tsx
const thunkMiddleware =
  ({ dispatch, getState }) =>
  next =>
  action => {
		// action이 함수인 경우 실행
    if (typeof action === 'function') {
      return action(dispatch, getState)
    }

    // 나머지는 작업없이 그대로 넘김
    return next(action)
  }
```

### 미들웨어 적용하기

redux-thunk를 설치합니다

```tsx
> npm install redux-thunk
```

redux-thunk 미들웨어를 store에 적용합니다

```tsx
// 📁 index.tsx
...
import ReduxThunk from "redux-thunk";

const logger = createLogger();
const store = createStore(rootReducer, applyMiddleware(logger, ReduxThunk));
```

### Thunk 생성 함수 만들기

카운터 값을 비동기적으로 변경시키는 thunk 함수를 만들겠습니다

먼저 어플리케이션의 thunk 타입과 dispatch 타입을 정의합니다

```tsx
// 📁 modules/index.ts
import { applyMiddleware, combineReducers, createStore } from "redux";
import { createLogger } from "redux-logger";
import ReduxThunk, { ThunkAction, ThunkDispatch } from "redux-thunk";
import counter from "./counter";
import todos from "./todos";

const rootReducer = combineReducers({
  counter,
  todos,
});

const logger = createLogger();
export const store = createStore(
  rootReducer,
  applyMiddleware(logger, ReduxThunk)
);

export type AppState = ReturnType<typeof store.getState>;

type AppAction = Parameters<typeof store.dispatch>[0];
export type AppThunk<ReturnType = void> = ThunkAction<
  ReturnType,
  AppState,
  unknown,
  AppAction
>;

export type AppDispatch = ThunkDispatch<AppState, unknown, AppAction>;
```

비동기적으로 동작하는 thunk 함수를 작성합니다. 1초 늦게 카운터가 동작하도록 합니다

```tsx
// 📁 modules/counter.ts
import { AppThunk } from ".";

...

export const increaseAsync = (): AppThunk => (dispatch) => {
  setTimeout(() => {
    dispatch({ type: "counter/INCREASE" });
  }, 1000);
};

export const decreaseAsync = (): AppThunk => (dispatch) => {
  setTimeout(() => {
    dispatch({ type: "counter/DECREASE" });
  }, 1000);
};
```

thunk를 dispatch 하여 사용합니다

```tsx
// 📁 container/CounterContainer.tsx
import { useCallback } from "react";
import { useSelector, useDispatch } from "react-redux";
import Counter from "../components/Counter";
import { AppDispatch, AppState } from "../modules";
import { decreaseAsync, increaseAsync } from "../modules/thunks/counterThunks";

const CounterContainer = () => {
  const number = useSelector<AppState, number>(({ counter }) => counter.number);
  const dispatch = useDispatch<AppDispatch>();

  const increase = useCallback(() => {
    **dispatch(increaseAsync());**
  }, [dispatch]);

  const decrease = useCallback(() => {
    **dispatch(decreaseAsync());**
  }, [dispatch]);

  return (
    <Counter number={number} onIncrease={increase} onDecrease={decrease} />
  );
};

export default CounterContainer;
```

### Redux Toolkit으로 API 비동기 작업 처리하기

API로 데이터를 가져와 화면에 출력하는 화면을 간단하게 구현해 보겠습니다

먼저 store를 전에 만들어 놓았던 Redux Toolkit의 store로 설정합니다

원래는 store에 redux-thunk 미들웨어를 등록해 놓아야 하지만 Redux Toolkit의 `configureStore` 함수를 사용하면 기본으로 포함이 되어 있기 때문에 따로 등록할 필요가 없습니다

```tsx
// 📁 index.tsx
...
import { store } from "./app/store";
...
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

api 호출을 담당하는 파일을 생성하고 https://jsonplaceholder.typicode.com/users 로 사용자 정보를 가져옵니다

```tsx
// 📁 lib/api.ts
import axios from "axios";
import { User } from "../features/user/userSlice";

export const getUsers = async () => {
  const response = await axios.get<User[]>(
    "<https://jsonplaceholder.typicode.com/users>"
  );
  return response.data;
};
```

사용자 상태를 관리하는 userSlice를 작성합니다

사용자 정보를 조회하는 thunk 함수도 함께 작성합니다

```tsx
// 📁 feature/user/userSlice.ts
import { createSlice, PayloadAction } from "@reduxjs/toolkit";
import { AppThunk } from "../../app/store";
import * as api from "../../lib/api";

export interface User {
  id: string;
  username: string;
  email: string;
}

export interface UserState {
  loading: boolean;
  users: User[];
}

const initialState: UserState = {
  loading: false,
  users: [],
};

const userSlice = createSlice({
  name: "user",
  initialState,
  reducers: {
    getUsersStarted: (state) => {
      state.loading = true;
    },
    getUsersSuccess: (state, action: PayloadAction<User[]>) => {
      state.loading = false;
      state.users = action.payload;
    },
    getUsersFailed: (state) => {
      state.loading = false;
    },
  },
});

export const getUsers = (): AppThunk => async (dispatch) => {
  dispatch(getUsersStarted());
  try {
    const users = await api.getUsers();
    dispatch(getUsersSuccess(users));
  } catch (e) {
    dispatch(getUsersFailed());
  }
};

export const { getUsersStarted, getUsersSuccess, getUsersFailed } =
  userSlice.actions;

export default userSlice.reducer;
```

사용자 정보를 출력하는 컴포넌트를 작성하고 화면을 확인합니다

```tsx
// 📁 feature/user/User.tsx
import { useEffect } from "react";
import { useAppDispatch, useAppSelector } from "../../app/hooks";
import { getUsers } from "./userSlice";

const User = () => {
  const loading = useAppSelector((state) => state.user.loading);
  const users = useAppSelector((state) => state.user.users);
  const dispatch = useAppDispatch();

  useEffect(() => {
    dispatch(getUsers());
  }, []);

  return (
    <div>
      <h1>사용자 목록</h1>
      {loading && "로딩 중..."}
      {!loading &&
        users.map((user) => (
          <li key={user.id}>
            {user.username} ({user.email})
          </li>
        ))}
    </div>
  );
};

export default User;
```

## 이외 비동기 처리 라이브러리

redux-thunk 외에도 `redux-saga` , `redux toolkit query` 등의 비동기 처리 라이브러리가 존재하며 또한 `react query` , `swr` , `recoil` , `zustand` 등의 상태 관리 라이브러리가 존재합니다. 나중에 개인 프로젝트를 할 때 이 중에서 어떤 기술을 선택할지 판단해야 하는데 각각의 장단점을 찾아보고 최대한 명확한 이유와 근거로 특정 라이브러리를 선택하는 것이 좋습니다

## 미들웨어를 쓰지 않고 비동기 처리하는 방법

사실 굳이 리덕스 미들웨어 라이브러리를 쓰지 않더라도 비동기를 처리할 수 있습니다. 그냥 컴포넌트에서 바로 api를 호출하면 됩니다

간단하고 작은 프로젝트면 상관이 없지만 규모가 커지면 라이브러리의 도움을 받아 더 좋은 디자인 패턴을 사용하는 것이 좋습니다

```tsx
// 📁 feature/user/User.tsx
...
import { getUsersFailed, getUsersStarted, getUsersSuccess } from "./userSlice";
import * as api from "../../lib/api";

const User = () => {
  ...

  useEffect(() => {
    (async () => {
      dispatch(getUsersStarted());
      try {
        const users = await api.getUsers();
        dispatch(getUsersSuccess(users));
      } catch (e) {
        dispatch(getUsersFailed());
      }
    })();
  }, []);
	...
};
```