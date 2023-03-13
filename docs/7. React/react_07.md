---
layout: default
title: 7. 컴포넌트의 라이프사이클 메서드
parent: React
nav_order: 7
---

# 7. 컴포넌트의 라이프사이클 메서드

# 1. 라이프사이클 메서드의 이해

모든 리액트 컴포넌트에는 라이프사이클(수명 주기)가 존재합니다. 컴포넌트의 수명은 페이지에 렌더링 되기 전인 준비 과정에서 시작하여 페이지에서 사라질 때 끝납니다

라이프사이클 메서드는 클래스형 컴포넌트에서만 사용할 수 있으며 함수형 컴포넌트에서는 대신 Hooks로 같은 작업을 처리할 수 있습니다

라이프사이클은 총 세 가지인 `마운트`, `업데이트`, `언마운트` 세 종류로 나뉩니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/82be356d-1209-415d-a1cc-dd0c2334ba50/Untitled.png)

### 마운트

DOM이 생성되고 웹 브라우저상에 나타나는 것을 `마운트` 라고 합니다

### 업데이트

컴포넌트는 다음과 같은 총 네 가지 경우에 `업데이트`합니다

- props가 바뀔 때
- state가 바뀔 때
- 부모 컴포넌트가 리렌더링될 때
- this.forceUpdate로 강제로 렌더링을 트리거할 때

### 언마운트

마운트의 반대 과정, 즉 컴포넌트를 DOM에서 제거하는 것을 `언마운트` 라고 합니다

# 2. 라이프사이클 메서드 살펴보기

다음은 각 과정시 호출되는 라이프사이클 메서드입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e996cc1b-eef8-407d-b795-da811e8ee51c/Untitled.png)

### constructor

컴포넌트의 생성자로써 컴포넌트를 만들 때 최초 실행됩니다

```tsx
constructor(props) { ... }
```

### getDerivedStateFromProps

props로 받아 온 값을 state에 동기화시키는 용도로 사용하며 마운트, 업데이트될 때 마다 호출됩니다

```tsx
static getDerivedStateFromProps(nextProps: Props, prevState: Props) {
  if (nextProps.value !== prevState.value) {
    // 조건에 따라 특정 값 동기화
    return { value: nextProps.value };
  }
  return null; // state를 변경할 필요가 없다면 null을 반환
}
```

### render() 함수

필수 구현 메서드로써 렌더링할 Element를 반환합니다

이 메서드 안에서는 이벤트 설정이 아닌 곳에서 `setState`를 사용하면 안되며, 브라우저의 `DOM`에 접근해서도 안 됩니다. DOM 정보를 가져오거나 state에 변화를 줄 때는 `componentDidMount` 에서 처리해야 합니다

```tsx
render() { ... }
```

### componentDidMount 메서드

컴포넌트를 만들고 첫 렌더링을 다 마친 후 실행합니다. 이 메서드에서 다른 자바스크립트 라이브러리 또는 프레임워크의 함수를 호출하거나 이벤트 등록, setTimeout, 네트워크 요청 같은 비동기 작업을 처리하면 됩니다

```tsx
componentDidMount() { ... }
```

### shouldComponentUpdate 메서드

props 또는 state를 변경했을 때 리렌더링을 시작할지 여부를 지정하는 메서드이며 true/false 값을 반환해야 합니다. false를 반환한다면 업데이트 과정은 중지됩니다

이 메서드 안에서 현재 props와 state는 `this.props`와 `this.state`로 접근하고 새로 설정될 props 또는 state는 `nextProps` 와 `nextState` 로 접근할 수 있습니다

이 메서드를 통해 불필요한 리렌더링을 방지할 수 있어 성능 개선시 주요하게 사용됩니다

```tsx
shouldComponentUpdate(nextProps, nextState) { ... }
```

### getSnapshotBeforeUpdate 메서드

render에서 만들어진 결과물이 브라우저에 실제로 반영되기 직전에 호출됩니다. 이 메서드에서 반환하는 값은 `componentDidUpdate`에서 세 번째 파라미터인 snapshot 값으로 전달받을 수 있으며 주로 스크롤바 위치를 유지하는 등의 업데이트하기 직전의 값을 참고할 일이 있을 때 사용됩니다

```tsx
getSnapshotBeforeUpdate(prevProps, prevState) {
  if (prevState.array !== this.state.array) {
    const { scrollTop, scrollHeight } = this.list;
    return { scrollTop, scrollHeight };
  }
}
```

### componentDidUpdate 메서드

리렌더링을 완료한 후 실행됩니다. 업데이트가 끝난 직후이므로 DOM 관련 처리를 해도 무방합니다

여기서는 prevProps 또는 prevState를 사용하여 컴포넌트가 이전에 가졌던 데이터에 접근할 수 있습니다. `getSnapShotBeforeUpdate`에서 반환한 snapshot 값을 전달받을 수 있습니다

```tsx
componentDidUpdate(prevProps, prevState, snapshot) { ... }
```

### componentWillUnmount 메서드

컴포넌트를 DOM에서 제거할 때 실행됩니다. componentDidMount에서 등록한 이벤트, 타이머, 직접 생성한 DOM이 있다면 여기서 제거 작업을 해야 합니다

```tsx
componentWillUnmount() { ... }
```

### componentDidCatch 메서드

컴포넌트 렌더링 도중에 에러가 발생했을 때 어플리케이션이 먹통이 되지 않고 오류 UI를 보여줄 수 있게 해 줍니다

`error` 는 어떤 에러가 발생했는지 알려주며 `info` 는 어디에 있는 코드에서 오류가 발생했는지에 대한 정보를 알려 줍니다

단, 컴포넌트 자신에게 발생하는 에러를 잡아낼 수 없고 자신의 `this.props.children` 으로 전달되는 컴포넌트에서 발생하는 에러만 잡아낼 수 있습니다

```tsx
componentDidCatch(error, info) {
  this.setState({ error: true });
  console.log({ error, info });
}
```

# 3. 라이프사이클 메서드 사용하기

각각의 라이프사이클시에 로그를 찍는 컴포넌트를 다음과 같이 작성합니다

숫자의 마지막 자릿수가 4이면 렌더링이 취소됩니다

```tsx
// 📁 LifeCycleSample.tsx
import { Component } from "react";

interface LifeCycleSampleProps {
  color: string;
}
interface LifeCycleSampleState {
  number: number;
  color: string;
}
type LifeCycleSampleSnapshot = string | null;

class LifeCycleSample extends Component<
  LifeCycleSampleProps,
  LifeCycleSampleState
> {
  state = { number: 0, color: "" };
  myRef: HTMLHeadElement | null = null; // ref를 설정할 부분

  constructor(props: LifeCycleSampleProps) {
    super(props);
    console.log("constructor");
  }

  static getDerivedStateFromProps(
    nextProps: LifeCycleSampleProps,
    prevState: LifeCycleSampleState
  ) {
    if (nextProps.color !== prevState.color) {
      return { color: nextProps.color };
    }
    return null;
  }

  componentDidMount() {
    console.log("componentDidMount");
  }

  shouldComponentUpdate(
    nextProps: LifeCycleSampleProps,
    nextState: LifeCycleSampleState
  ) {
    console.log("shouldComponentUpdate", nextProps, nextState); // 숫자의 마지막 자리가 4면 리렌더링하지 않습니다.
    return nextState.number % 10 !== 4;
  }

  componentWillUnmount() {
    console.log("componentWillUnmount");
  }

  handleClick = () => {
    this.setState({ number: this.state.number + 1 });
  };

  getSnapshotBeforeUpdate(
    prevProps: LifeCycleSampleProps,
    prevState: LifeCycleSampleState
  ): LifeCycleSampleSnapshot {
    console.log("getSnapshotBeforeUpdate");
    if (prevProps.color !== this.props.color) {
      return this.myRef?.style.color ?? null;
    }
    return null;
  }

  componentDidUpdate(
    prevProps: LifeCycleSampleProps,
    prevState: LifeCycleSampleState,
    snapshot: LifeCycleSampleSnapshot
  ) {
    console.log("componentDidUpdate", prevProps, prevState);

    if (snapshot) {
      console.log("업데이트되기 직전 색상: ", snapshot);
    }
  }

  render() {
    console.log("render");
    const style = { color: this.props.color };

    return (
      <div>
        <h1 style={style} ref={(ref) => (this.myRef = ref)}>
          {this.state.number}
        </h1>
        <p>color: {this.state.color}</p>
        <button onClick={this.handleClick}>더하기</button>
      </div>
    );
  }
}

export default LifeCycleSample;
```

버튼을 누르면 색상을 변경해주는 컴포넌트를 다음과 같이 생성합니다. 그러면 LifeCycleSample 컴포넌트는 props가 변경되었기 때문에 업데이트(리렌더링)이 일어납니다

동작하면서 라이프사이클 메서드가 정상적으로 실행되는지 살펴봅니다

```tsx
// 📁 LifeCycleSampleContainer.tsx
import { Component } from "react";
import LifeCycleSample from "./LifeCycleSample";

// 랜덤 색상을 생성합니다
function getRandomColor() {
  return "#" + Math.floor(Math.random() * 16777215).toString(16);
}

class LifeCycleSampleContainer extends Component {
  state = { color: "#000000" };

  handleClick = () => {
    this.setState({ color: getRandomColor() });
  };

  render() {
    return (
      <div>
        <button onClick={this.handleClick}>랜덤 색상</button>
        <LifeCycleSample color={this.state.color} />
      </div>
    );
  }
}

export default LifeCycleSampleContainer;
```

### 에러 잡아내기

render 함수에서 의도적으로 에러를 발생시킵니다. 콘솔에 에러 로그가 찍혀 있고 화면은 흰 공백 화면만 나와 에러가 발생했는지 알 수가 없습니다

```tsx
// 📁 LifeCycleSample.tsx
...
render() {
    ...
    return (
      <div>
        **{(this.props as any).missing.value}**
       ...
      </div>
    );
  }
```

다음과 같이 `componentDidCatch` 메서드를 구현한 컴포넌트를 작성하여 에러가 발생하면 화면에 알려주도록 합니다

```tsx
// 📁 ErrorBoundary.tsx
import { Component, ErrorInfo, ReactNode } from "react";

interface ErrorBoundaryProps {
  children?: ReactNode;
}

class ErrorBoundary extends Component<ErrorBoundaryProps> {
  state = { error: false };

  componentDidCatch(error: Error, info: ErrorInfo) {
    this.setState({ error: true });
    console.log({ error, info });
  }

  render() {
    if (this.state.error) return <div>에러가 발생했습니다!</div>;
    return this.props.children;
  }
}

export default ErrorBoundary;
```

다음과 같이 컴포넌트를 감싸도록 하면 자식 컴포넌트에서 에러가 발생했을 때 catch가 되는 것을 확인할 수 있습니다