---
layout: default
title: 2. JSX
parent: React
nav_order: 2
---

# 2. JSX

# 1. 코드 이해하기

웹팩, 바벨, ESLint 등의 도구가 포함되어 있으며 기본으로는 이것들이 숨겨져 있다. 특별히 커스터마이징할 것이 아니면 그대로 사용하면 된다

전체 코드 훑어보고 빌드해 보기

# 2. JSX란?

자바스크립트 확장 문법으로써 마치 HTML 코드를 값처럼 다룰 수 있도록 해 줍니다. 자바스크립트 표준에는 당연히 존재하지 않는 문법이며 바벨에 의해 표준 자바스크립트 코드로 변환됩니다

`JSX`는 실제로는 하나의 자바스크립트 객체이며 이 객체를 `리액트 Element`라 하고 이를 리액트가 처리해서 화면에 반영해 줍니다

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

### 참고

`React.StrictMode` 컴포넌트는 앞으로 사라질 레거시 기능을 사용할 때 경고를 주는 등의 리액트 엄격모드를 적용하는 개발환경에서만 활성화되는 디버깅용 컴포넌트입니다

```jsx
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

# 3. JSX 문법

### 감싸인 요소

반드시 부모 요소 하나로 감싸야 합니다

```jsx
// Error
function App() {
	return (
		<h1>hello</h1>
		<h2>react</h2>
	)
}
function App() {
	return (
		<div>
			<h1>hello</h1>
			<h2>react</h2>
		</div>
	)
}
```

굳이 태그로 감싸고 싶지 않은 경우 `Fragment` 라는 빈 요소를 사용합니다

```jsx
function App() {
	return (
		<>
			<h1>hello</h1>
			<h2>react</h2>
		</>
	)
}
```

### 자바스크립트 표현

`{ }` 내부에서 자바스크립트 표현식을 쓸 수 있습니다

```jsx
function App() {
    const name = "react";
    return (
        <>
            <h1>hello</h1>
            <h2>{name}</h2>
        </>
    );
}
```

### if 문 대신 조건부 연산자

if문을 쓸 수는 없고 대신 필요한 경우 조건부 연산자를 사용합니다

```jsx
function App() {
    const name = "react";
    return (
        <>
            {name === "react" ? (
                <h1>리액트입니다</h1> //
            ) : (
                <h1>리액트가 아닙니다</h1>
            )}
						<h2>{name}</h2>
        </>
    );
}
```

### && 연산자를 사용한 조건부 렌더링

```jsx
function App() {
    const name = "react";
    return (
        <>
            {name === "react" && <h1>리액트입니다</h1>}
            <h2>{name}</h2>
        </>
    );
}
```

### undefined를 렌더링하지 않기

```jsx
function App() {
    const name = undefined;
    return name;
}
```

### 인라인 스타일링

인라인 스타일을 작성할 때 객체로 작성해야 하며 카멜 표기법으로 작성하면 됩니다

```jsx
function App() {
    const name = "react";
    return (
        <>
            <div
                style={{
                    backgroundColor: "black",
                    color: "aqua",
                    fontSize: "48px",
                    padding: 16,
                }}
            >
                {name}
            </div>
        </>
    );
}
```

### class 대신 className

```jsx
// App.css
.react {
    background-color: black;
    color: aqua;
    font-size: 48px;
    padding: 16;
}
function App() {
    const name = "react";
    return (
        <>
            <div className="react">{name}</div>
        </>
    );
}
```

### 꼭 닫아야 하는 태그

아래와 같이 닫지 않은 `<input>` 태그는 html에서는 문제가 없지만 JSX에서는 빌드시 에러가 발생합니다

```jsx
function App() {
    const name = "react";
    return (
        <>
            <div className="react">{name}</div>
            <input>
        </>
    );
}
```

`self-closing 태그` 로 작성할 수 있습니다

```jsx
function App() {
    const name = "react";
    return (
        <>
            <div className="react">{name}</div>
            <input />
        </>
    );
}
```

### 주석

```jsx
function App() {
    const name = "react";
    return (
        <>
            {/* 주석입니다 */}
            <div className="react">{name}</div>
            <input />
            // 이런 형식이나 
            /* 이런 형식은 JSX의 주석이 아닙니다 */
        </>
    );
}
```

# 4. ESLint와 Prettier 적용하기

ESLint와 Prettier VSCode 확장프로그램을 설치하여 적용합니다. 기본 설정은 `create-react-app` 에서 이미 되어 있습니다