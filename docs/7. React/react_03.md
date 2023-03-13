---
layout: default
title: 3. 컴포넌트
parent: React
nav_order: 3
---

# 3. 컴포넌트

# 1. 클래스형 컴포넌트

리액트에서 컴포넌트를 선언하는 방식은 함수 컴포넌트와 클래스형 컴포넌트가 있습니다

함수 컴포넌트 장점

- 선언하기가 편합니다
- 메모리 자원을 덜 사용합니다
- 빌드한 결과 파일의 크기가 작습니다

함수 컴포넌트 단점

- state와 라이프사이클 API의 사용이 불가능합니다
  - 이는 `Hooks` 기능을 통해 해결되었습니다

리액트 공식 매뉴얼에서는 위의 장점들 때문에 함수 컴포넌트를 사용하도록 권장합니다. 하지만 클래스 컴포넌트를 사용한 프로젝트가 많고 라이프사이클 API를 이해하고 있는 것이 리액트를 이해하는데 더 도움이 되기 때문에 클래스형 컴포넌트도 익히는 것이 좋습니다

# 2. 첫 컴포넌트 생성

다음과 같이 함수형 컴포넌트를 정의 합니다. JSX로 작성한 React Element를 반환하는 함수를 default로 export하는 모듈을 만듭니다

그러면 이 UI를 사용하고자 하는 곳에서 import 하여 원하는 곳에 삽입하여 사용하면 됩니다

```jsx
// 📁 MyComponent.tsx
const MyComponent = () => {
  return <div>My Component...</div>;
};
export default MyComponent;
```

App.js에서 다음과 같이 Mycomponent를 가져와 html 태그처럼 사용하면 됩니다

```jsx
// 📁 App.tsx
import MyComponent from "./MyComponent";

function App() {
    return (
        <>
            <h1>컴포넌트 입니다</h1>
            <MyComponent />
        </>
    );
}
```

# 3. props

`props` 는 properties의 줄임말로 컴포넌트의 속성을 설정할 때 사용하는 용소입니다. props 값은 해당 컴포넌트를 사용하는 쪽에서 설정할 수 있습니다

### props 사용하기

컴포넌트를 사용하는 쪽에서 html 어트리뷰트를 설정하는 것처럼 값을 설정합니다. 그리고 컴포넌트에서는 함수형 컴포넌트의 경우 `props` 가 인자로 넘어오기 때문에 이 값을 사용할 수 있습니다

```jsx
// 📁 App.tsx
...
<MyComponent **name="react"** />
// 📁 MyComponent.tsx
interface MyComponentProps {
  name?: string;
}

const MyComponent = (props: MyComponentProps) => {
  return <div>제 이름은 {props.name} 입니다</div>;
};
```

### 태그 사이의 내용을 보여주는 children

`children` 은 태그 사이의 내용을 담고 있는 값입니다. 결국 컴포넌트에 값을 전달할 때 props로 전달할 수도 있지만 태그 사이에 JSX를 작성하여 전달할 수도 있습니다

```jsx
// 📁 App.tsx
...
<MyComponent name="react">홍길동</MyComponent>
// 📁 MyComponent.tsx
interface MyComponentProps {
  name?: string;
  children?: JSX.Element | string;
}

const MyComponent = (props: MyComponentProps) => {
  return (
    <div>
      제 이름은 {props.name} 입니다
      <br />
      children은 {props.children} 입니다
    </div>
  );
};
```

### 구조분해 할당 문법 사용하기

구조분해 할당을 사용하면 props 사용하는 코드가 좀 더 간결해 집니다

```jsx
// 📁 MyComponent.tsx
const MyComponent = ({ name, children }: MyComponentProps) => {
  return (
    <div>
      제 이름은 {name} 입니다
      <br />
      children은 {children} 입니다
    </div>
  );
};
```

### props 기본값 설정

Javascript의 매개변수 기본값 문법을 사용하여 props의 기본값을 설정할 수 있습니다

```jsx
// 📁 App.tsx
...
<MyComponent>홍길동</MyComponent>
// 📁 MyComponent.tsx
const MyComponent = ({ name = "홍길동", children }: MyComponentProps) => {
  return (
    <div>
      제 이름은 {name} 입니다
      <br />
      children은 {children} 입니다
    </div>
  );
};
```

### 클래스형 컴포넌트에서 props 사용하기

클래스형 컴포넌트는 다음과 같이 클래스를 선언하여 반환하면 되며 `render()` 함수에서 React Element를 반환하면 됩니다

props는 객체의 프로퍼티로 존재하기 때문에 `this.props` 로 접근할 수 있습니다

```tsx
// 📁 MyComponent.tsx
import { Component } from "react";

interface MyComponentProps {
  name?: string;
  children?: JSX.Element | string;
}

class MyComponent extends Component<MyComponentProps> {
  render() {
    const { name = "홍길동", children } = this.props;
    return (
      <div>
        제 이름은 {name} 입니다
        <br />
        children은 {children} 입니다
      </div>
    );
  }
}

export default MyComponent;
```

# 4. state

리액트에서 `state`는 컴포넌트의 상태로서 내부에서 바뀔 수 있는 값을 의미합니다. `props` 는 부모 컴포넌트가 설정하는 값이며 자신은 읽기만 가능하고 설정할 수 없습니다

## 클래스형 컴포넌트의 state

`Counter.tsx` 파일을 새로 생성하여 다음과 같이 카운터 화면을 작성합니다

state는 컴포넌트 객체의 프로퍼티로 생성되므로 `this.state` 로 사용할 수 있습니다

또한, 생성자에서 `state` 를 초기화할 수 있으며 항상 객체로 정의해야 합니다

state를 설정하고자 하는 경우 `this.setState()` 를 호출하여 변경할 state 객체를 넘겨주면 됩니다

다음과 같이 `onClick` 속성으로 클릭 이벤트 핸들러를 등록할 수 있습니다

```tsx
// 📁 Counter.tsx
import { Component } from "react";

interface CounterProps {}
interface CounterState {
  number: number;
  name: string;
}

class Counter extends Component<CounterProps, CounterState> {
  constructor(props: CounterProps) {
    super(props);
    this.state = { number: 0, name: "홍길동" };
  }

  render() {
    const { number, name } = this.state;
    return (
      <div>
        <h1>
          {name}: {number}
        </h1>
        <button onClick={() => this.onClickAdd()}>+1</button>
      </div>
    );
  }

  onClickAdd() {
    const { number } = this.state;
    this.setState({ number: number + 1 });
  }
}

export default Counter;
// 📁 App.tsx
function App() {
  return (
    <>
      <Counter></Counter>
    </>
  );
}
```

`+1` 버튼을 누르면 클릭 이벤트 핸들러 `onClickAdd()` 가 실행되고 거기서 `this.setState()` 로 `number` 값이 1이 올라갑니다.

이렇게 state 값이 변경되면 리액트는 값이 변경되었기 때문에 화면을 다시 그리기 위해 `render()` 함수를 호출하게 되고 그러면서 변경된 `number` 값으로 화면에 반영되게 됩니다

### state를 constructor에서 꺼내기

생성자에서 state를 초기화할 수도 있지만 프로퍼티로 직접 선언하여 초기화할 수도 있습니다

```tsx
// 📁 Counter.tsx
class Counter extends Component<CounterProps, CounterState> {
  state = {
    number: 0,
    name: "홍길동",
  };

	render() {
	...
}
```

### this.setState에 객체 대신 함수 인자 전달하기

this.setState를 사용하여 state를 업데이트할 때는 상태가 비동기적으로 업데이트됩니다

아래와 같이 number를 1씩 올리는 setState를 두 번 호출하더라도 this.setState를 호출했을 때 바로 state가 변경되지 않기 때문에 동일한 값으로 두 번 설정되어 number가 1씩만 올라갑니다

```tsx
// 📁 Counter.tsx
...
onClickAdd() {
  this.setState({ number: this.state.number + 1 });
  this.setState({ number: this.state.number + 1 });
}
```

이를 해결하기 위해서는 콜백함수를 사용하면 되는데 첫번째 인자로 이전 state 객체가 넘어오기 때문에 비동기적 업데이트가 문제되지 않으며 의도한 대로 number가 2씩 올라갑니다

```tsx
// 📁 Counter.tsx
...
onClickAdd() {
  this.setState(({ number }) => {
    return { number: number + 1 };
  });
  this.setState(({ number }) => {
    return { number: number + 1 };
  });
}
```

### this.setState가 끝난 후 특정 작업 실행하기

setState를 사용하여 값을 업데이트하고 난 후 특정 작업을 하고 싶을 때는 setState의 두번째 인자로 콜백 함수를 등록하여 작업을 처리할 수 있습니다

```tsx
// 📁 Counter.tsx
...
onClickAdd() {
    this.setState(
      ({ number }) => {
        return { number: number + 1 };
      },
      () => {
        alert("number가 변경되었습니다: " + this.state.number);
      }
    );
  }
```

## 함수형 컴포넌트의 state

함수형 컴포넌트는 객체가 존재하지 않기 때문에 자체적으로 state를 가지지 않습니다. 대신 `Hooks` 라는 기능을 이용하여 state를 사용할 수 있습니다

### useState Hook 사용하기

클래스형 컴포넌트로 만든 Counter를 동일하게 함수형 컴포넌트로 구현해 보도록 하겠습니다. 다음과 같이 `CounterFunc.tsx` 를 작성합니다

`useState()` 함수를 호출하면 튜플로 state값과 state setter 함수가 반환됩니다. useState 호출시 매개변수로 초기값을 넘길 수 있으며 어떠한 자료구조도 사용할 수 있습니다

반환된 state setter 함수를 호출하여 state를 변경할 수 있습니다

클래스형 컴포넌트에서 setState를 호출할 때 끝난 후 실행되는 콜백을 넘길 수 있었는데 useState에서는 그런 기능이 존재하지 않습니다. 대신 `useEffect` Hook을 사용해야 하는데 이는 다음에 학습하도록 하겠습니다

```tsx
// 📁 CounterFunc.tsx
...
import { useState } from "react";

const CounterFunc = () => {
  const [number, setNumber] = useState(0);
  const [name, setName] = useState("홍길동");

  const onClickAdd = () => {
    setNumber(number + 1);
  };

  return (
    <div>
      <h1>
        {name}: {number}
      </h1>
      <button onClick={onClickAdd}>+1</button>
    </div>
  );
};

export default CounterFunc;
```

# 5. state를 사용할 때 주의 사항

state 값으로 객체(참조형)을 사용하는 경우에는 state 값 설정시 항상 기존과 다른 객체로 설정해야 합니다

기존과 동일한 객체로 설정하는 경우 리액트는 이전과 변경하려는 값을 비교하여 값이 같은 경우 상태가 같다고 판단하여 업데이트를 실행하지 않습니다. 객체는 참조형 자료이기 때문에 객체의 내용을 수정하더라도 그 객체를 복사하여 객체 자체의 참조 주소를 바꿔 주어야만 state 값 설정시 변경되었음을 리액트가 인지할 수 있습니다

```tsx
onClickAdd() {
  this.state.number = this.state.number + 1;
  this.setState(this.state);
}
```

위의 코드는 state의 참조값이 변경되지 않기 때문에 아래와 같이 객체를 새로 만들어 주어야 합니다

```tsx
onClickAdd() {
  this.setState({ ...this.state, number: this.state.number + 1 });
}
```

<aside> 💡 그런데 이상하게도 저 코드가 잘 동작됩니다. 리렌더링이 잘 이루어지는데 그 이유를 도저히 모르겠습니다. 분명히 책에서나 구글링을 해보아도 마치 객체 주소가 바뀌지 않으면 리렌더링이 일어나지 않는 것처럼 나오는데 잘만 됩니다. 이는 나중에 더 알아봐야겠습니다

</aside>