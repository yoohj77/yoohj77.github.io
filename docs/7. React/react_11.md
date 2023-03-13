---
layout: default
title: 11. 컴포넌트 성능 최적화
parent: React
nav_order: 11
---

# 11. 컴포넌트 성능 최적화

# 1. 많은 데이터 렌더링하기

성능을 확인하기 위해 많은 데이터를 렌더링해 부하를 일으켜 보겠습니다. 다음 코드처럼 작성하면 2500개의 데이터가 나타나고 이전보다 많이 느려졌다는 것을 느낄 수 있습니다

```tsx
function createBulkTodos() {
  const array = [];
  for (let i = 1; i <= 2500; i++) {
    array.push({ id: i, text: `할 일 ${i}`, checked: false });
  }
  return array;
}
const App = () => {
  const [todos, setTodos] = useState(createBulkTodos);
	...
};
```

# 2. React DevTools를 사용한 성능 모니터링

얼마나 느린지, 그리고 왜 느린지를 `React DevTools` 를 사용하면 정확히 알 수 있습니다

하나의 할일 항목을 완료처리하면 굉장히 느린데 이를 분석해 보겠습니다

`Render duration` 은 리렌더링에 소요된 시간을 의미합니다. 그리고 어떤 작업들을 하느라 오래 걸렸는지 차트에서 확인해 볼 수 있는데 보면 모든 항목이 리렌더링이 이루어진 것을 볼 수 있습니다

할일 하나만 완료처리했기 때문에 나머지는 리렌더링이 일어날 필요가 없는데 불필요하게 발생하고 있는 상황입니다. 이는 최적화할 수 있는 요소가 됩니다

# 3. 느려지는 원인 분석

컴포넌트는 다음과 같은 상황에서 리렌더링이 발생합니다

1. props 또는 state가 바뀔 때
2. 부모 컴포넌트가 리렌더링될 때
3. forceUpdate 함수가 실행될 때

현재 상황은 할일 하나를 완료처리할 때 state가 바뀌어 부모 컴포넌트가 리렌더링되어서 모든 자식 컴포넌트들이 렌더링된 상황입니다

# 4. React.memo를 사용하여 컴포넌트 성능 최적화

`React.memo` 는 컴포넌트의 props가 바뀌지 않았다면 리렌더링하지 않도록 설정할 수 있는 최적화 함수입니다. 반환하는 컴포넌트를 인자로 받아 실행한 결과를 대신 컴포넌트로 반환하면 됩니다

이를 사용하면 할일 하나를 완료할 때 다른 컴포넌트들은 props가 변경되지 않기 때문에 리렌더링이 발생하지 않게 됩니다

```tsx
import React from 'react';
...
export default React.memo(TodoListItem);
```

# 5. onToggle, onRemove 함수가 바뀌지 않게 하기

### useState의 함수형 업데이트

기존에는 `setTodos(todos.concat(todo))` 형태로 state를 설정하고 있어서 todos가 변경될 때마다 함수를 새로 만들어 주어야 했습니다 (값의 참조주소가 바뀌므로)

이를 콜백함수 형태 `setTodos((todos) => todos.concat(todo))` 로 바꾸면 todos 참조가 사라지게 되어 함수를 매번 새로 만들 필요가 없어집니다

```tsx
const onInsert = useCallback((text) => {
  ...
  //setTodos(todos.concat(todo));
	setTodos((todos) => todos.concat(todo));
  ...
}, []);
```

### useReducer 사용하기

리듀서를 사용해도 todos state에 대한 직접적인 참조가 사라지고 대신 `dispatch` 함수에 대한 참조가 생기게 되는데 dispatch 함수는 변경되지 않기 때문에 함수가 매번 새로 생성되지 않게 됩니다

```tsx
const onInsert = useCallback((text) => {
  ...
  //setTodos(todos.concat(todo));
	dispatch({ type: 'INSERT', todo });
  ...
}, []);
```

# 6. 불변성의 중요성

state를 업데이트할 때 새로운 객체로 복사하여 state를 반환하지 않고 기존 state객체를 수정하여 반환하게 되면 할일 항목을 완료처리 하더라도 화면에 완료표시가 되지 않습니다

React DevTools로 state를 확인해 보면 state에서는 완료여부가 `true`로 바뀌어 있습니다. 결국 state가 바뀌었는데도 화면에 반영이 되지 않은 것입니다

```tsx
// reducer 코드 일부
...
case "CHANGE_COMPLETE": {
  const todo = state.find((x) => x.id === action.id);
  if (!todo) {
    return state;
  }
  todo.complete = action.complete; // 원본을 수정하고 그대로 반환
  return state;
}
```

그 이유는 이전 state와 변경된 state가 동일한 객체이기 때문에 `useReducer` 가 state가 바뀌었음을 인지하지 못했기 때문입니다. 그래서 리렌더링이 발생하지 않습니다

이전 state를 수정하는 방식이 아닌 새로운 state를 만들어 반환하도록 수정하면 리렌더링이 정상적으로 발생하게 됩니다

```tsx
// reducer 코드 일부
...
case "CHANGE_COMPLETE": {
  const todo = state.find((x) => x.id === action.id);
  if (!todo) {
    return state;
  }

  const newState = []; // 새로운 state 객체 생성
  for (const item of state) {
    if (item === todo) {
      newState.push({ ...item, complete: action.complete });
    } else {
      newState.push(item);
    }
  }
  return newState;
}
```

# 7. 화면에 보이는 항목만 렌더링하기

2500개의 항목들 중에 실제 화면에 보이는 것은 몇 개 되지 않습니다. 나머지 대부분은 스크롤에 의해 가려져 있습니다. 그렇기 때문에 사실 가려져 있는 부분들까지 전부 렌더링하는 것은 비효율적이라고 할 수 있습니다

그래서 스크롤함에 따라 화면에 보이는 항목들만 렌더링해 주면 성능이 더욱 최적화될 수 있습니다

이를 적용한 것이 `무한 스크롤` 같은 기능이며 `react-virtualized` 를 사용해도 이를 쉽게 적용할 수 있지만 현재 react 18에는 적용이 되지 않아 사용이 제한적입니다

# 8. 정리

위의 최적화 방법들을 무조건 항상 적용해야 할 필요는 없습니다. 리스트의 항목의 갯수가 100개 이하이고 렌더링 속도가 충분히 빠르다면 굳이 최적화를 적용할 필요가 없습니다. 단, 반대로 100개 이상의 항목이 출력되고 속도가 느리다고 판단되면 최적화를 고려해야 합니다