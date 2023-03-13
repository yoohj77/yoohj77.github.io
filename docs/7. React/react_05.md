---
layout: default
title: 5.ref- DOM에 이름 달기
parent: React
nav_order: 5
---

# 5. ref: DOM에 이름 달기

# 1. ref는 어떤 상황에서 사용해야 할까?

리액트 컴포넌트에서는 가급적 HTML요소에 `id` 속성을 넣지 않아야 합니다. 왜냐하면 하나의 컴포넌트가 여러 번 사용될 수 있어 id가 중복될 수 있기 때문입니다

리액트에서는 일반적으로 DOM 객체를 굳이 가져올 필요가 없고 다른 기능들로 대부분 대체가 가능합니다. 하지만 대체가 안되는 경우 DOM 객체를 가져와야 하는데 id가 존재하지 않기 때문에 가져오기가 어렵습니다. 이 때 리액트에서 DOM 객체를 가져올 때 사용할 수 있는 것이 `ref(reference)` 입니다

### 예제 컴포넌트 생성

`change` 이벤트시에 password를 state에 동기화합니다

버튼을 클릭하면 유효성 검사를 하여 `validated` 에 값을 설정하고 이에 따라 class가 결정됩니다

```tsx
// 📁 ValidationSample.css
.success {
    background-color: lightgreen;
}
.failure {
    background-color: lightcoral;
}
// 📁 ValidationSample.tsx
import { ChangeEvent, Component } from "react";
import "./ValidationSample.css";

interface ValidationSampleProps {}
interface ValidationSampleState {
    password: string;
    clicked: boolean;
    validated: boolean;
}

class ValidationSample extends Component<ValidationSampleProps, ValidationSampleState> {
    state = {
        password: "",
        clicked: false,
        validated: false,
    };

    handleChange = (e: ChangeEvent<HTMLInputElement>) => {
        this.setState({ password: e.target.value });
    };
    handleButtonClick = () => {
        this.setState({ clicked: true, validated: this.state.password === "0000" });
    };
    render() {
        return (
            <div>
                <input type="password" value={this.state.password} onChange={this.handleChange} className={this.state.clicked ? (this.state.validated ? "success" : "failure") : ""} />
                <button onClick={this.handleButtonClick}>검증하기</button>
            </div>
        );
    }
}
export default ValidationSample;
```

### DOM을 꼭 사용해야 하는 상황

- 특정 input에 포커스 주기
- 스크롤 박스 조작하기
- Canvas 요소에 그림 그리기 등

이러한 기능은 리액트로 구현할 수 없고 DOM 객체를 가져와야 합니다. 이 때, 리액트에서 `ref` 를 사용합니다

# 2. ref 사용

### 콜백 함수를 통한 ref 설정

다음과 같이 해당 요소의 DOM 객체를 컴포넌트 객체의 프로퍼티에 담아 놓을 수 있습니다. 프로퍼티명은 자유롭게 생성할 수 있습니다

```tsx
<input ref={(ref) => {this.input=ref}} />
```

### createRef를 통한 ref 설정

`createRef` 로 ref 객체를 만들어서 ref에 넣어 주면 내부적으로 ref 객체에 DOM 객체를 넣어 줍니다. `current` 프로퍼티로 현재 DOM 객체를 담아 놓기 때문에 current로 접근하여 사용할 수 있습니다

```tsx
class RefSample extends Component {
    input = React.createRef<HTMLInputElement>();

    handleFocus = () => {
        this.input.current?.focus();
    };

    render() {
        return (
            <div>
                <input ref={this.input} />
            </div>
        );
    }
}
export default RefSample;
```

### 예제에 ref 적용하기

ValidationSample 예제에서 검증하기 버튼을 누르면 input 박스에 커서가 들어갈 수 있도록 포커스를 주도록 하겠습니다

```tsx
// 📁 ValidationSample.tsx
class ValidationSample extends Component<ValidationSampleProps, ValidationSampleState> {
    ...

    **input?: HTMLInputElement | null;**

    ...
    handleButtonClick = () => {
        this.setState({ clicked: true, validated: this.state.password === "0000" });
        **this.input?.focus();
        this.input?.select();**
    };
    render() {
        return (
            <div>
                <input
                    type="password"
                    **ref={(ref) => (this.input = ref)}**
                    value={this.state.password}
                    onChange={this.handleChange}
                    className={this.state.clicked ? (this.state.validated ? "success" : "failure") : ""}
                />
                <button onClick={this.handleButtonClick}>검증하기</button>
            </div>
        );
    }
}
```

# 3. 컴포넌트에 ref 달기

박스를 두개 만들고 스크롤이 될 수 있도록 합니다

최하단으로 스크롤시키는 `scrollToBottom` 함수를 만듭니다

```tsx
// 📁 ScrollBox.tsx
import { Component } from "react";

class ScrollBox extends Component {
    box?: HTMLDivElement | null;

    scrollToBottom = () => {
        if (this.box) {
            const { scrollHeight, clientHeight } = this.box;
            this.box.scrollTop = scrollHeight - clientHeight;
        }
    };

    render() {
        const innerStyle = { width: "100%", height: "650px", background: "linear-gradient(white, black)" };
        return (
            <div
                style={{ border: "1px solid black", height: "300px", width: "300px", overflow: "auto", position: "relative" }}
                ref={(ref) => {
                    this.box = ref;
                }}
            >
                <div style={innerStyle} />
            </div>
        );
    }
}

export default ScrollBox;
```

`ref` 를 통해 ScrollBox 객체를 참조합니다. 이 객체는 말그대로 ScrollBox 컴포넌트 객체이며 DOM 객체가 아닙니다

버튼을 눌렀을 때 ScrollBox의 scrollToBottom 함수를 실행하여 스크롤이 내려갈 수 있도록 합니다

```tsx
// 📁 ScrollBoxContainer.tsx
import { Component } from "react";
import ScrollBox from "./ScrollBox";

class ScrollBoxContainer extends Component {
    scrollBox?: ScrollBox | null;

    render() {
        return (
            <div>
                <ScrollBox ref={(ref) => (this.scrollBox = ref)}></ScrollBox>
                <button onClick={this.onClickScrollBox}>맨 밑으로</button>
            </div>
        );
    }

    onClickScrollBox = () => {
        this.scrollBox?.scrollToBottom();
    };
}

export default ScrollBoxContainer;
```

위와 같이 HTML 요소가 아니라 컴포넌트에 ref를 사용하는 것은 좋지 않은 방법입니다. 이러한 패턴은 두 컴포넌트를 강하게 결합시키기 때문에 이러한 직접적인 컴포넌트 객체 참조는 피해야 합니다