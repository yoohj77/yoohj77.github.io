---
layout: default
title: 17. 리덕스를 사용하여 리액트 어플리케이션 상태 관리하기
parent: React
nav_order: 19
---

# 17. 리덕스를 사용하여 리액트 어플리케이션 상태 관리하기

리액트 프로젝트가 커질수록 상태를 관리하는 것이 점점 복잡해집니다. 이를 해결할 수 있는 것이 `리덕스(Redux)` 이며 상태 관련 로직을 뷰와 분리할 수 있어 유지보수하는 데 도움이 됩니다. 또한, 전역 상태에 쉽게 접근하고 변경할 수 있어 유용합니다

리액트에서는 `react-redux` 라는 라이브러리로 리덕스를 사용할 수 있으며 사용방법이 약간 다릅니다

# 1. 작업 환경 설정

리액트에서 사용할 리덕스 라이브러리를 설치합니다

```tsx
> npm install redux react-redux
```

# 2. UI 준비하기

리액트 프로젝트에서 리덕스를 사용할 때 가장 많이 사용하는 패턴은 `presentational 컴포넌트` 와 `container 컴포넌트` 를 분리하는 것입니다

presentational 컴포넌트란 주로 상태 관리가 이루어지지 않고 그저 화면에 UI를 보여주기만 하는 컴포넌트를 말합니다. 이와 달리 container 컴포넌트는 리덕스와 연동되어 있는 컴포넌트로 상태 관리를 하는 컴포넌트입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/50ac33e2-089b-4fbd-9187-201c7ac21824/Untitled.png)

### 카운터 컴포넌트 만들기

```tsx
// 📁 components/Counter.tsx
interface CounterProps {
  number: number;
  onIncrease?: () => void;
  onDecrease?: () => void;
}
const Counter = ({ number, onIncrease, onDecrease }: CounterProps) => {
  return (
    <div>
      <h1>{number}</h1>
      <div>
        <button onClick={onIncrease}>+1</button>
        <button onClick={onDecrease}>-1</button>
      </div>
    </div>
  );
};

export default Counter;
```

### 할 일 목록 컴포넌트 만들기

```tsx
// 📁 components/Todos.tsx
import { FormEvent } from "react";

interface TodoItemProps {}
const TodoItem = ({}: TodoItemProps) => {
  return (
    <div>
      <input type="checkbox" />
      <span>예제 텍스트</span>
      <button>삭제</button>
    </div>
  );
};

interface TodosProps {}
const Todos = ({}: TodosProps) => {
  const onSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
  };

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input />
        <button type="submit">등록</button>
      </form>
      <div>
        <TodoItem />
        <TodoItem />
        <TodoItem />
        <TodoItem />
        <TodoItem />
      </div>
    </div>
  );
};

export default Todos;
// 📁 App.tsx
...
function App() {
  return (
    <div>
      <Counter number={0}></Counter>
      <hr />
      <Todos />
    </div>
  );
}
```

# 3. 리덕스 관련 코드 작성하기

### counter 모듈 작성하기

```tsx
// 📁 modules/counter.ts
interface IncreaseAction {
  type: "counter/INCREASE";
}
interface DecreaseAction {
  type: "counter/DECREASE";
}
export type CounterAction = IncreaseAction | DecreaseAction;

export interface CounterState {
  number: number;
}
const initialState: CounterState = {
  number: 0,
};

const counter = (state = initialState, action: CounterAction) => {
  switch (action.type) {
    case "counter/INCREASE":
      return {
        number: state.number + 1,
      };
    case "counter/DECREASE":
      return {
        number: state.number - 1,
      };
    default:
      return state;
  }
};

export default counter;
```

### todos 모듈 작성하기

```tsx
// 📁 modules/todos.ts
interface ChangeInputAction {
  type: "todos/CHANGE_INPUT";
  input: string;
}
interface InsertAction {
  type: "todos/INSERT";
  text: string;
}
interface ToggleAction {
  type: "todos/TOGGLE";
  id: number;
}
interface RemoveAction {
  type: "todos/REMOVE";
  id: number;
}
export type TodosAction =
  | ChangeInputAction
  | InsertAction
  | ToggleAction
  | RemoveAction;

export interface Todo {
  id: number;
  text: string;
  done: boolean;
}
export interface TodosState {
  input: string;
  todos: Todo[];
}
const initialState: TodosState = {
  input: "",
  todos: [
    {
      id: 1,
      text: "리덕스 기초 배우기",
      done: true,
    },
    {
      id: 2,
      text: "리액트와 리덕스 사용하기",
      done: false,
    },
  ],
};

let id = 3;
const todos = (state = initialState, action: TodosAction) => {
  switch (action.type) {
    case "todos/CHANGE_INPUT":
      return {
        ...state,
        input: action.input,
      };
    case "todos/INSERT":
      return {
        ...state,
        todos: [
          ...state.todos,
          {
            id: id++,
            text: action.text,
            done: false,
          },
        ],
      };
    case "todos/TOGGLE":
      return {
        ...state,
        todos: state.todos.map((todo) => {
          if (todo.id === action.id) {
            return { ...todo, done: !todo.done };
          }
          return todo;
        }),
      };
    case "todos/REMOVE":
      return {
        ...state,
        todos: state.todos.filter((todo) => todo.id !== action.id),
      };
    default:
      return state;
  }
};

export default todos;
```

### 루트 리듀서 만들기

리덕스 store는 한 개만 생성해야 하는데 현재 리듀서는 두 개 생성했습니다. 그렇기 때문에 생성한 두 개의 리듀서를 하나의 리듀서로 합쳐 주어야 하는데 이를 `루트 리듀서` 라 하고 `combineReducers` 라는 유틸 함수를 사용하여 만들 수 있습니다

합성된 state와 dispatch의 타입도 외부에서 사용할 수 있도록 export 해 줍니다

```tsx
// 📁 modules/index.ts
import { combineReducers, Dispatch } from "redux";
import counter, { CounterAction } from "./counter";
import todos, { TodosAction } from "./todos";

const rootReducer = combineReducers({
  counter,
  todos,
});

export default rootReducer;

export type AppState = ReturnType<typeof rootReducer>;

export type AppDispatch = Dispatch<CounterAction | TodosAction>;
```

# 4. 리액트 어플리케이션에 리덕스 적용하기

### 스토어 생성하고 Provider 컴포넌트로 적용하기

index.tsx 에 store를 생성하고 Provider 컴포넌트로 적용합니다

```tsx
// 📁 index.tsx
...
**import { createStore } from "redux";
import rootReducer from "./modules";
import { Provider } from "react-redux";**

**const store = createStore(rootReducer);**

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

### Redux DevTools 설치 및 적용

크롬 웹스토어에서 `Redux DevTools` 를 검색하여 설치합니다

이 확장프로그램과 연결하기 위해서 `redux-devtools-extension` 라이브러리를 설치하고 다음과 같이 수정합니다

```tsx
> npm install redux-devtools-extension
// 📁 index.tsx
...
import { composeWithDevTools } from "redux-devtools-extension";

const store = createStore(rootReducer, composeWithDevTools());
```

화면을 브라우저에 띄우고 개발자 도구를 확인해 보면 `Redux` 탭이 보이며 Redux 탭에서 리덕스 상태를 확인할 수 있습니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/518302c7-5e92-4bd6-8bb2-da6693868f07/Untitled.png)

# 5. 컨테이너 컴포넌트 만들기

### CounterContainer 만들기

presentational 컴포넌트로 만든 Counter 컴포넌트를 리덕스와 연결해주는 container 컴포넌트를 생성합니다

리덕스와 연결하기 위해 `connect` 함수를 사용합니다.

첫번째 인자로 리덕스의 state를 props로 주입하기 위한 함수를 넘깁니다

두번째 인자로 리덕스의 dispatch를 props로 주입하기 위한 함수를 넘깁니다

```tsx
// 📁 containers/CounterContainer.tsx
import { connect } from "react-redux";
import Counter from "../components/Counter";
import { AppDispatch, AppState } from "../modules";

interface CounterContainerProps
  extends ReturnType<typeof mapStateToProps>,
    ReturnType<typeof mapDispatchToProps> {}

const CounterContainer = ({
  number,
  increase,
  decrease,
}: CounterContainerProps) => {
  return (
    <Counter number={number} onIncrease={increase} onDecrease={decrease} />
  );
};

const mapStateToProps = ({ counter }: AppState) => ({
  number: counter.number,
});

const mapDispatchToProps = (dispatch: AppDispatch) => ({
  increase: () => {
    dispatch({ type: "counter/INCREASE" });
  },
  decrease: () => {
    dispatch({ type: "counter/DECREASE" });
  },
});

export default connect(mapStateToProps, mapDispatchToProps)(CounterContainer);
```

### TodosContainer 만들기

```tsx
// 📁 containers/TodosContainer.tsx
import { connect } from "react-redux";
import Todos from "../components/Todos";
import { AppDispatch, AppState } from "../modules";

interface TodosContainerProps
  extends ReturnType<typeof mapStateToProps>,
    ReturnType<typeof mapDispatchToProps> {}

const TodosContainer = ({
  input,
  todos,
  changeInput,
  insert,
  toggle,
  remove,
}: TodosContainerProps) => {
  return (
    <Todos
      input={input}
      todos={todos}
      onChangeInput={changeInput}
      onInsert={insert}
      onToggle={toggle}
      onRemove={remove}
    />
  );
};

const mapStateToProps = ({ todos }: AppState) => ({
  input: todos.input,
  todos: todos.todos,
});

const mapDispatchToProps = (dispatch: AppDispatch) => ({
  changeInput: (input: string) => {
    dispatch({ type: "todos/CHANGE_INPUT", input });
  },
  insert: (text: string) => {
    dispatch({ type: "todos/INSERT", text });
  },
  toggle: (id: number) => {
    dispatch({ type: "todos/TOGGLE", id });
  },
  remove: (id: number) => {
    dispatch({ type: "todos/REMOVE", id });
  },
});

export default connect(mapStateToProps, mapDispatchToProps)(TodosContainer);
// 📁 components/Todos.tsx
import { ChangeEvent, FormEvent } from "react";
import { Todo } from "../modules/todos";

interface TodoItemProps {
  todo: Todo;
  onToggle?: (id: number) => void;
  onRemove?: (id: number) => void;
}
const TodoItem = ({ todo, onToggle, onRemove }: TodoItemProps) => {
  return (
    <div>
      <input
        type="checkbox"
        onClick={() => onToggle?.(todo.id)}
        checked={todo.done}
        readOnly={true}
      />
      <span style={{ textDecoration: todo.done ? "line-through" : "none" }}>
        {todo.text}
      </span>
      <button onClick={() => onRemove?.(todo.id)}>삭제</button>
    </div>
  );
};

interface TodosProps {
  input: string;
  todos: Todo[];
  onChangeInput?: (text: string) => void;
  onInsert?: (input: string) => void;
  onToggle?: (id: number) => void;
  onRemove?: (id: number) => void;
}
const Todos = ({
  input,
  todos,
  onChangeInput,
  onInsert,
  onToggle,
  onRemove,
}: TodosProps) => {
  const onSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    onInsert?.(input);
    onChangeInput?.("");
  };

  const onChange = (e: ChangeEvent<HTMLInputElement>) => {
    onChangeInput?.(e.target.value);
  };

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input value={input} onChange={onChange} />
        <button type="submit">등록</button>
      </form>
      <div>
        {todos.map((todo) => (
          <TodoItem
            key={todo.id}
            todo={todo}
            onToggle={onToggle}
            onRemove={onRemove}
          />
        ))}
      </div>
    </div>
  );
};

export default Todos;
```

### App에 적용

생성한 container 컴포넌트로 기존 컴포넌트를 교체합니다

```tsx
// 📁 App.tsx
...
function App() {
  return (
    <div>
      <CounterContainer></CounterContainer>
      <hr />
      <TodosContainer></TodosContainer>
    </div>
  );
}
```

# 6. 리덕스 더 편하게 사용하기

### redux-actions

`redux-actions` 라이브러리로 액션 관련 코드를 더 간편하게 작성할 수 있습니다. 이에 대한 설명은 생략합니다

### immer

상태 불변성을 유지하기 위해 `immer` 라이브러리를 사용할 수 있습니다. 이에 대한 설명도 역시 생략합니다

# 7. Hooks를 사용하여 컨테이너 컴포넌트 만들기

`connect` 함수를 사용하는 대신 Hook을 사용할 수 있으며 이로 인해 코드가 더욱 단순해 질 수 있습니다

### useSelector / useDispatch 사용하기

`useSelector` 로 리덕스의 상태를 조회할 수 있습니다. 이는 connect 함수의 mapStateToProps 함수를 대체합니다

`useDispatch` 로 스토어의 내장 함수 dispatch를 사용할 수 있게 해줍니다

`useCallback` 을 써서 가급적 최적화를 해 줍니다

```tsx
// 📁 containers/CounterContainer.tsx
import { useCallback } from "react";
import { useSelector, useDispatch } from "react-redux";
import Counter from "../components/Counter";
import { AppDispatch, AppState } from "../modules";

const CounterContainer = () => {
  const number = useSelector<AppState, number>(({ counter }) => counter.number);
  const dispatch = useDispatch<AppDispatch>();

  const increase = useCallback(() => {
    dispatch({ type: "counter/INCREASE" });
  }, [dispatch]);

  const decrease = useCallback(() => {
    dispatch({ type: "counter/DECREASE" });
  }, [dispatch]);

  return (
    <Counter number={number} onIncrease={increase} onDecrease={decrease} />
  );
};

export default CounterContainer;
```

### useStore를 사용하여 리덕스 스토어 사용하기

`useStore` 를 사용하면 리덕스 store 객체를 직접 사용할 수 있으나 이는 가급적 사용하지 않아야 하는 방식입니다. 대신 useSelector나 useDispatch를 사용해야 합니다

```tsx
const store = useStore();
store.dispatch({ type: "SAMPLE_ACTION " });
store.getState();
```

### connect 함수와의 주요 차이점

리덕스를 사용할 때 connect 함수를 사용할지 Hook을 사용할지는 사용자가 자유롭게 선택하면 됩니다

하지만 꼭 알아두어야 할 차이점이 한 가지 있는데 connect 함수를 사용한 경우 부모 컴포넌트가 리렌더링될 때 props 가 변경되었는지 체크하는 최적화가 되어 있는데 useSelector를 사용한 경우에는 이러한 최적화가 되지 않기 때문에 필요한 경우 `React.memo` 를 직접 사용해야 합니다