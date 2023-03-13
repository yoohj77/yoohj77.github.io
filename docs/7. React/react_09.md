---
layout: default
title: 8. Hooks
parent: React
nav_order: 9
---

# 8. Hooks

# 1. useState

함수형 컴포넌트에서도 state를 사용할 수 있도록 해 주는 Hook 입니다. 이전에 이미 학습하였으므로 생략합니다

# 2. useEffect

`useEffect`는 리액트 컴포넌트가 렌더링될 때마다 특정 작업을 수행하도록 설정할 수 있는 Hook입니다. 클래스형 컴포넌트의 `componentDidMound` 와 `componentDidUpdate` 를 대체할 수 있습니다

다음 컴포넌트는 리렌더링이 될 때마다 콘솔 로그가 찍히며, input 박스에 글자를 입력할 때마다 로그가 찍힙니다

```tsx
// 📁 Info.tsx
import { ChangeEvent, useEffect, useState } from "react";

const Info = () => {
  const [name, setName] = useState("");
  const [nickname, setNickname] = useState("");

  useEffect(() => {
    console.log("렌더링이 완료되었습니다", { name, nickname });
  });

  const onChangeName = (e: ChangeEvent<HTMLInputElement>) => {
    setName(e.target.value);
  };
  const onChangeNickname = (e: ChangeEvent<HTMLInputElement>) => {
    setNickname(e.target.value);
  };

  return (
    <div>
      <div>
        <input value={name} onChange={onChangeName} />
        <input value={nickname} onChange={onChangeNickname} />
      </div>
      <div>
        <div>
          <b>이름:</b> {name}
        </div>
        <div>
          <b>닉네임:</b> {nickname}
        </div>
      </div>
    </div>
  );
};

export default Info;
```

### 마운트될 때만 실행하고 싶을 때

`componentDidMount` 와 같이 최초 렌더링시에만 특정 작업을 하고 싶을 때는 `useEffect` 의 두 번째 파라미터로 비어 있는 배열을 넣어 주면 됩니다

다음과 같이 코드를 수정하면 최초 한번만 로그가 찍힙니다

```tsx
// 📁 Info.tsx
...
useEffect(() => {
  console.log("렌더링이 완료되었습니다", { name, nickname });
}, []);
```

### 특정 값이 업데이트될 때만 실행하고 싶을 때

특정 값이 변경될 때만 작업을 하고 싶은 경우 클래스형 컴포넌트에서는 다음과 같이 구현할 수 있습니다

```tsx
componentDidUpdate(prevProps, prevState) {
  if (prevProps.value !== this.props.value) {
    doSomething();
  }
}
```

이를 함수형 컴포넌트에서 구현하려면 `useEffect`의 두 번째 파라미터로 전달되는 배열에 변경을 감시할 값을 넣어 주면 됩니다

다음과 같이 코드를 수정하면 마운트시와 이름을 입력할 때만 로그가 찍힙니다

```tsx
// 📁 Info.tsx
...
useEffect(() => {
  console.log("이름이 변경되었습니다", { name });
}, [name]);
```

### 언마운트될 때만 실행하고 싶을 때

`useEffect` 에서 함수를 반환하면 이 함수는 리렌더링 직전이나 언마운트시에 실행됩니다. 언마운트시에만 실행하고 싶다면 두 번째 파라미터로 비어 있는 배열을 넣어주 면 됩니다

```tsx
// 📁 Info.tsx
...
useEffect(() => () => {
    console.log("Info 컴포넌트가 언마운트되었습니다");
}, []);
```

`InfoContainer` 컴포넌트를 만들어 `Info` 컴포넌트를 보이고 숨기는 기능을 구현합니다. 이렇게 하면 Info가 숨겨질 때만 언마운트되어 위의 useEffect가 함수가 실행되고 로그가 찍히게 됩니다

```tsx
// 📁 InfoContainer.tsx
import { useState } from "react";
import Info from "./Info";

const InfoContainer = () => {
  const [visible, setVisible] = useState(false);

  return (
    <>
      <button onClick={() => setVisible(!visible)}>
        {visible ? "숨기기" : "보이기"}
      </button>
      {visible && <Info></Info>}
    </>
  );
};

export default InfoContainer;
```

<aside> 💡 설명과 다르게 최초 렌더링시에도 로그가 찍힌다면 이는 `React.StrictMode` 컴포넌트 때문입니다. `React.StrictMode` 컴포넌트를 삭제하면 의도대로 언마운트시에만 실행됩니다

</aside>

### 특정 값이 업데이트되기 직전에 실행하고 싶을 때

`useEffect` 에서 함수를 반환하고 두 번째 파라미터에 특정 값을 넣어주면 해당 값이 업데이트되기 직전에 반환된 함수가 실행됩니다. 또한, 언마운트시에도 실행됩니다

```tsx
// 📁 Info.tsx
...
useEffect(() => {
  console.log("이름이 변경되었습니다", { name });
  return () => {
    console.log("이름이 변경되기 직전입니다", { name });
  };
}, [name]);
```

# 3. useReducer

### reducer란?

리듀서는 변형자라는 뜻으로써 상태를 변형해주는 함수를 의미합니다. 현재의 상태인 `state` 와 상태를 변경할 특정 행위를 가리키는 `action` 을 받아서 그 행위에 따라 변경된 새로운 상태 `new-state` 를 반환하는 함수가 `reducer` 입니다

```tsx
const reducer = (state, action) => newState;
```

이러한 리듀서를 활용한 상태관리 패턴은 상태, 행위, 변형의 세 요소로 상태관련 로직이 독립적으로 분리되기 때문에 더욱 유연한 설계가 가능해 지고, 그래서 현재 많이 사용되고 있습니다

### useReducer 사용하기

`useReducer`는 reducer를 사용하여 상태를 관리할 수 있도록 도와주는 Hook 입니다

reducer에서 새로운 상태를 반환할 때 기존 상태는 불변성을 유지해 줘야 리액트가 최적화되어 동작합니다

```tsx
// 📁 Counter.tsx
import { useReducer } from "react";

interface CounterState {
  value: number;
}
interface CounterAction {
  type: "INCREMENT" | "DECREMENT";
}

function reducer(state: CounterState, action: CounterAction) {
  // action.type에 따라 다른 작업 수행
  switch (action.type) {
    case "INCREMENT":
      return { value: state.value + 1 };
    case "DECREMENT":
      return { value: state.value - 1 };
    default: // 아무것도 해당되지 않을 때 기존 상태 반환
      return state;
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, { value: 0 });
  return (
    <div>
      <p>
        현재 카운터 값은 <b>{state.value}</b>입니다.
      </p>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>+1</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>-1</button>
    </div>
  );
};
export default Counter;
```

# 4. useMemo

`useMemo` 를 사용하면 컴포넌트 내부에서 발생하는 연산을 최적화할 수 있습니다

다음은 입력한 값들의 평균을 계산하여 출력하는 예제입니다

```tsx
// 📁 Average.tsx
import { useState, ChangeEvent } from "react";

const getAverage = (numbers: number[]) => {
  console.log("평균값 계산중..");
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
};

const Average = () => {
  const [list, setList] = useState<number[]>([]);
  const [number, setNumber] = useState("");

  const onChange = (e: ChangeEvent<HTMLInputElement>) => {
    setNumber(e.target.value);
  };
  const onInsert = () => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber("");
  };

  return (
    <div>
      <input value={number} onChange={onChange} />
      <button onClick={onInsert}>등록</button>
      <ul>
        {list.map((value, index) => (
          <li key={index}>{value}</li>
        ))}
      </ul>
      <div>
        <b>평균값:</b> {getAverage(list)}
      </div>
    </div>
  );
};

export default Average;
```

그런데 숫자를 입력할 때 뿐만 아니라 input의 내용이 수정될 때도 state가 바뀌기 때문에 `getAverage` 함수가 호출되는 것을 확인할 수 있습니다

`useMemo` Hook을 사용하면 이러한 작업을 최적화할 수 있습니다. 특정값이 바뀌었을 때만 연산을 새로 실행하여 값을 가져오는 방식입니다

다음과 같이 수정하면 숫자가 추가되었을 때만 평균값을 계산합니다

```tsx
// 📁 Average.tsx
...
const avg = useMemo(() => getAverage(list), [list]);

return (
  ...
      <b>평균값:</b> {avg}
);
```

# 5. useCallback

`useCallback` 을 사용하면 만들어놨던 함수를 재사용할 수 있어 주로 렌더링 성능을 최적화해야 하는 상황에서 사용할 수 있습니다. `useMemo` 와 매우 비슷한 Hook입니다

위에서 구현한 `Average` 컴포넌트를 보면 `onChange` 와 `onInsert` 라는 함수가 있는데 이는 컴포넌트가 리렌더링될 때마다 새로운 함수가 생성이 됩니다. 일반적으로 이정도 비용은 크게 문제가 되지 않지만 렌더링이 자주 발생하거나 컴포넌트가 여러 번 사용되게 되면 이 부분을 `useCallback` 을 사용해 최적화해 주는 것이 좋습니다

```tsx
// 📁 Average.tsx
...
const onChange = useCallback((e: ChangeEvent<HTMLInputElement>) => {
  setNumber(e.target.value);
}, []);

const onInsert = useCallback(() => {
  const nextList = list.concat(parseInt(number));
  setList(nextList);
  setNumber("");
}, [number, list]);
```

두 번째 파라미터에 빈 배열 `[]` 을 넣으면 최초 렌더링시에만 함수가 생성됩니다

두 번째 파라미터에 특정한 값의 배열을 넣으면 해당 값이 변경될 때 함수가 새로 생성되게 됩니다

함수 내부에서 상태 값에 의존해야 할 때는 반드시 그 값을 두 번째 파라미터에 포함해 주어야 정상적으로 상태가 변경되었을 때 함수가 생성됩니다

# 6. useRef

`useRef` 는 함수형 컴포넌트에서 `ref` 를 사용할 수 있도록 해 주는 Hook 입니다

다음과 같이 사용할 수 있으며 생성한 ref 객체에서 `current` 에 DOM객체가 담겨 있습니다

```tsx
// 📁 Average.tsx
...
const Average = () => {
  ...
  **const inputEl = useRef<HTMLInputElement>(null);**
	...
	const onInsert = useCallback(() => {
		...
    **inputEl.current?.focus();**
  }, [number, list]);
	...
  return (
    <div>
      <input value={number} onChange={onChange} **ref={inputEl}** />
      ...
    </div>
  );
};
```

### 지역 변수 사용하기

추가적으로 DOM객체를 가져올 때 뿐만 아니라 컴포넌트의 지역 변수를 사용해야 할 때도 `useRef` 를 활용할 수 있습니다

지역 변수란 렌더링과 상관없이 바뀔 수 있는 값을 의미합니다. 클래스형 컴포넌트에서는 클래스의 프로퍼티로 값을 설정해 사용할 수 있지만 함수형 컴포넌트에서는 컴포넌트 객체가 존재하지 않아 값을 담을 공간이 없는데 이 때 `useRef` 를 대신 사용하면 됩니다

ref 객체의 값이 바뀌어도 컴포넌트는 리렌더링되지 않기 때문에 렌더링과 관련없는 값을 사용하고 싶을 때 사용하면 됩니다

```tsx
import { useRef } from "react";

const RefSample = () => {
  const id = useRef(1);
  const setId = (n: number) => {
    id.current = n;
  };
  const printId = () => {
    console.log(id.current);
  };
  return <div> refsample </div>;
};

export default RefSample;
```

# 7. 커스텀 Hook 만들기

여러 컴포넌트에서 비슷한 기능을 공유할 경우, `커스텀 Hook` 으로 로직을 재사용할 수 있습니다

커스텀 Hook은 사실상 **단순한 하나의 함수**일 뿐이며 이를 여러 컴포넌트에서 참조하여 재사용할 수 있습니다. 그렇기 때문에 커스텀 Hook은 리액트의 특별한 기능이라기 보다는 **기본적으로 Hook의 디자인을 따르는 관습** 으로써 Hook의 이름은 `use` 로 시작해야 합니다

위에서 작성한 `Counter` 컴포넌트에서 reducer 부분을 커스텀 Hook으로 만들어 보겠습니다. 이렇게 하면 Counter 컴포넌트에서는 손쉽게 증감으로 관리되는 상태를 얻을 수 있습니

```tsx
// 📁 useNumberState.tsx
import { useReducer } from "react";

export interface CounterState {
  value: number;
}
export interface CounterAction {
  type: "INCREMENT" | "DECREMENT";
}

function reducer(state: CounterState, action: CounterAction) {
  // action.type에 따라 다른 작업 수행
  switch (action.type) {
    case "INCREMENT":
      return { value: state.value + 1 };
    case "DECREMENT":
      return { value: state.value - 1 };
    default: // 아무것도 해당되지 않을 때 기존 상태 반환
      return state;
  }
}

const useNumberState = (value: number = 0) => {
  return useReducer(reducer, { value });
};

export default useNumberState;
// 📁 Counter.tsx
import useNumberState from "./useNumberState";

const Counter = () => {
  const [state, dispatch] = useNumberState(0);
  return (
    <div>
      <p>
        현재 카운터 값은 <b>{state.value}</b>입니다.
      </p>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>+1</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>-1</button>
    </div>
  );
};
export default Counter;
```

# 8. 다른 Hooks

다른 개발자가 만든 다양한 Hook을 라이브러리로 설치하여 사용할 수 있습니다

https://nikgraf.github.io/react-hooks/

https://github.com/rehooks/awesome-react-hooks