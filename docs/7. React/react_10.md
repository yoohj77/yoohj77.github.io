---
layout: default
title: 9. 컴포넌트 스타일링
parent: React
nav_order: 10
---

# 9. 컴포넌트 스타일링

# 1. 가장 흔한 방식, 일반 CSS

소규모 프로젝트이거나 기존 CSS 스타일링이 불편하지 않다면 일반 CSS를 사용해도 문제되지 않습니다

웹팩을 사용하여 `import` 구문으로 로드하거나 HTML 태그로 로드하면 되며 자세한 설명은 생략하겠습니다

# 2. Sass 사용하기

`Sass` (Syntactically Awesome Style Sheets) 문법적으로 매우 멋진 스타일시트 라는 뜻의 Sass는 `CSS 전처리기` 로써 복잡한 CSS 작업을 쉽게 할 수 있도록 해 주고 스타일 코드의 재활용성을 높여 줄 뿐만 아니라 코드의 가독성을 높여 줍니다

Sass에서는 두 가지 확장자 `.scss` 와 `.sass` 를 지원합니다. 이 중 `.scss` 가 기존 CSS 문법과 비슷하여 더 많이 사용됩니다

```sass
// .sass
$font-stack: Helvetica, sans-serif
$primary-color: #333   

body
   font: 100% $font-stack
   color: $primary-color
// .scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
   font: 100% $font-stack;
   color: $primary-color;
}
```

Sass를 사용하기 위해서는 `sass` 라이브러리를 설치해 주어야 합니다

```scss
> npm install sass
```

scss 파일과 컴포넌트를 작성하고 화면을 확인하면 Sass 문법이 잘 적용된 것을 확인할 수 있습니다

```scss
// 📁 SassComponent.scss

// 변수 사용하기
$red: #fa5252;
$orange: #fd7e14;
$yellow: #fcc419;
$green: #40c057;
$blue: #339af0;
$indigo: #5c7cfa;
$violet: #7950f2;

// 믹스인 만들기 (재사용되는 스타일 블록을 함수처럼 사용 할 수 있음)
@mixin square($size) {
  $calculated: 32px * $size;
  width: $calculated;
  height: $calculated;
}

.SassComponent {
  display: flex;

  .box {
    // 일반 CSS 에선 .SassComponent .box 와 마찬가지
    background: red;
    cursor: pointer;
    transition: all 0.3s ease-in;

    &.red {
      // .red 클래스가 .box 와 함께 사용 됐을 때
      background: $red;
      @include square(1);
    }
    &.orange {
      background: $orange;
      @include square(2);
    }
    &.yellow {
      background: $yellow;
      @include square(3);
    }
    &.green {
      background: $green;
      @include square(4);
    }
    &.blue {
      background: $blue;
      @include square(5);
    }
    &.indigo {
      background: $indigo;
      @include square(6);
    }
    &.violet {
      background: $violet;
      @include square(7);
    }
    &:hover {
      // .box 에 마우스 올렸을 때
      background: black;
    }
  }
}
// 📁 SassComponent.tsx
import "./SassComponent.scss";

const SassComponent = () => {
  return (
    <div className="SassComponent">
      <div className="box red" />
      <div className="box orange" />
      <div className="box yellow" />
      <div className="box green" />
      <div className="box blue" />
      <div className="box indigo" />
      <div className="box violet" />
    </div>
  );
};

export default SassComponent;
```

### utils 함수 분리하기

여러 파일에서 사용될 수 있는 Sass 변수 및 믹스인은 다른 파일로 따로 분리하여 여러 곳에서 재사용될 수 있습니다

다음과 같이 변수와 믹스인을 util 파일로 이동하고 `@import` 구문으로 불러올 수 있습니다

```scss
// 📁 utils.scss

// 변수 사용하기
$red: #fa5252;
$orange: #fd7e14;
$yellow: #fcc419;
$green: #40c057;
$blue: #339af0;
$indigo: #5c7cfa;
$violet: #7950f2;

// 믹스인 만들기 (재사용되는 스타일 블록을 함수처럼 사용 할 수 있음)
@mixin square($size) {
  $calculated: 32px * $size;
  width: $calculated;
  height: $calculated;
}
// 📁 SassComponent.scss

@import "./styles/utils";

.SassComponent {
	...
```

### 라이브러리 불러오기

Sass의 장점 중 하나는 라이브러리를 쉽게 불러와서 사용할 수 있다는 점입니다

유용한 Sass 라이브러리 두 가지를 설치하고 사용해 보겠습니다. 반응형 디자인을 쉽게 만들어 주는 `include-media` 와 매우 편리한 색상 팔레트인 `open-color` 를 설치해 주세요

```scss
> npm install include-media open-color
```

Sass 라이브러리를 불러올 때는 `node_modules` 내부 라이브러리 경로 안에 들어 있는 scss 파일을 불러와야 하며 `~` 물결표시로 node_modules 에 쉽게 접근할 수 있습니다

```scss
// 📁 SassComponent.scss
@import "./styles/utils";

.SassComponent {
	display: flex;
  **background: $oc-gray-2;

  @include media("<768px") {
    background: $oc-gray-9;
  }**

	...
```

# 3. CSS Module

`CSS Module` 은 클래스 이름이 중첩되지 않도록 임의의 클래스명을 만들어 적용해 주는 기술입니다. 클래스명이 고유한 이름으로 바뀌기 때문에 클래스명을 지을 때 해당 컴포넌트 모듈 안에서만 고민하면 됩니다

다음과 같이 `.module.css` 파일을 생성합니다. 기본적으로는 해당 컴포넌트에 고유한 클래스가 만들어 지며 전역적으로 사용하고 싶은 클래스의 경우 앞에 `:global`  키워드를 붙이면 됩니다

CSS Module 파일을 import 하면 객체가 넘어오며 작성한 클래스명의 프로퍼티를 가져오면 고유하게 변형된 클래스명이 나오게 됩니다

```css
// 📁 CSSModule.module.css
/* 자동으로 고유해질 것이므로 흔히 사용되는 단어를 클래스 이름으로 마음대로 사용가능*/

.wrapper {
  background: black;
  padding: 1rem;
  color: white;
  font-size: 2rem;
}

/* 글로벌 CSS 를 작성하고 싶다면 */
:global .something {
  font-weight: 800;
  color: aqua;
}
// 📁 CSSModule.tsx
import styles from "./CSSModule.module.css";

const CSSModule = () => {
  return (
    <div className={styles.wrapper}>
      안녕하세요, 저는 <span className="something">CSS Module!</span>
    </div>
  );
};

export default CSSModule;
```

### Sass와 함께 사용하기

Sass를 사용할 때도 파일 이름 뒤에 `.module.scss` 확장자를 사용해 주면 CSS Module로 사용할 수 있습니다

```scss
// 📁 CSSModule.module.scss
/* 자동으로 고유해질 것이므로 흔히 사용되는 단어를 클래스 이름으로 마음대로 사용가능*/

.wrapper {
  background: black;
  padding: 1rem;
  color: white;
  font-size: 2rem;

  &.inverted {
    color: black;
    background: white;
    border: 1px solid black;
  }
}

/* 글로벌 CSS 를 작성하고 싶다면 */
:global {
  .something {
    font-weight: 800;
    color: aqua;
  }
}
// 📁 CSSModule.tsx
import styles from "./CSSModule.module.scss";

const CSSModule = () => {
  return (
    <div className={`${styles.wrapper} ${styles.inverted}`}>
      안녕하세요, 저는 <span className="something">CSS Module!</span>
    </div>
  );
};

export default CSSModule;
```

# 4. styled-components

컴포넌트 스타일링의 또 다른 패러다임은 자바스크립트 파일 안에 스타일을 선언하는 방식인 `CSS-in-JS` 입니다. 이와 관련된 라이브러리가 많은데 이 중에 `styled-components` 가 많이 사용되고 있습니다

styled-components를 설치합니다

```tsx
> npm install styled-components
```

다음과 같이 스타일링된 컴포넌트를 만들어내는 방식으로 사용할 수 있습니다

그래서 `props` 를 넘겨줄 수 있으며 이에 따른 조건부 스타일링을 할 수 있습니다

또한 Sass 문법을 사용할 수 있습니다

```tsx
// 📁 StyledComponent.tsx
import styled, { css } from "styled-components";

interface BoxProps {
  color?: string;
}
const Box = styled.div<BoxProps>`
  /* props 로 넣어준 값을 직접 전달해줄 수 있습니다. */
  background: ${(props) => props.color || "blue"};
  padding: 1rem;
  display: flex;
`;

interface ButtonProps {
  inverted?: boolean;
}
const Button = styled.button<ButtonProps>`
  background: white;
  color: black;
  border-radius: 4px;
  padding: 0.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  font-size: 1rem;
  font-weight: 600;

  /* & 문자를 사용하여 Sass 처럼 자기 자신 선택 가능 */
  &:hover {
    background: rgba(255, 255, 255, 0.9);
  }

  /* 다음 코드는 inverted 값이 true 일 때 특정 스타일을 부여해줍니다. */
  ${(props) =>
    props.inverted &&
    css`
      background: none;
      border: 2px solid white;
      color: white;
      &:hover {
        background: white;
        color: black;
      }
    `};
  & + button {
    margin-left: 1rem;
  }
`;

const StyledComponent = () => (
  <Box color="black">
    <Button>안녕하세요</Button>
    <Button inverted={true}>테두리만</Button>
  </Box>
);

export default StyledComponent;
```

<aside> 💡 VS Code에서 `vscode-styled-components` 확장프로그램을 설치하면 styled-components의 css코드가 색상이 입혀져서 나옵니다

</aside>

### Tagged 템플릿 리터럴

styled-components의 문법이 생소할 수 있는데 이는 Tagged 템플릿 리터럴이라는 문법입니다

```tsx
function tagged(...args) {
  console.log(args);
}
tagged`hello ${{ foo: "bar" }} ${() => "world"}!`;
```

위의 코드를 실행하면 아래와 같이 콘솔 로그가 출력됩니다. 함수 뒤에 템플릿 리터럴을 쓰면 함수가 실행되면서 템플릿 리터럴의 개별 템플릿 정보가 인자로 넘어옵니다

이를 통해 styled-components가 스타일을 읽어 컴포넌트를 생성해낼 수 있는 것입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d651f5ae-02bd-4a06-ab39-cfcd99b9a3a1/Untitled.png)

### 스타일링된 컴포넌트 만들기

만약 사용해야 할 태그명이 유동적이거나 특정 컴포넌트 자체에 스타일링해 주고 싶다면 다음과 같이 styled 함수를 호출하여 사용할 수 있습니다

```tsx
// 태그의 타입을 styled 함수의 인자로 전달
const MyInput = styled("input")`
  background: gray;
`;

// 다른 컴포넌트 형식의 값을 넣어 줌
const StyledLink = styled(Link)`
  color: blue;
`;
```