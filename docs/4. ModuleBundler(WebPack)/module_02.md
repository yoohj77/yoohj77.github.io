---
layout: default
title: 웹팩 시작하기
parent: ModuleBundler(WebPack)
nav_order: 2
---

# 웹팩 시작하기

웹팩을 간단하게 사용해 보도록 하겠습니다

# 프로젝트 구성

프로젝트 폴더를 생성하고 `package.json` 파일을 생성합니다

```tsx
> npm init -y
```

`index.html` 파일을 생성합니다

```tsx
<!DOCTYPE html>
<html>
  <head>
    <title>Webpack Demo</title>
    <script src="<https://unpkg.com/lodash@4.16.6/lodash.js>"></script>
  </head>
  <body>
    <script type="module" src="src/index.js"></script>
  </body>
</html>
```

`src/calc.js` 파일을 생성합니다

```tsx
export function add(a, b) {
    return a + b;
}
```

`src/index.js` 파일을 생성합니다

```tsx
import { add } from "./calc.js";

function component() {
    var element = document.createElement("div");

    /* lodash is required for the next line to work */
    element.innerHTML = _.join(["Hello", "webpack", add(2, 3)], " ");

    return element;
}

document.body.appendChild(component());
```

로컬 서버를 통해 화면을 띄우고 크롬 개발자 도구의 네트워크 탭을 확인합니다. 다음과 같이 3개의 파일을 다운받고 있는데 파일 용량이 크지 않기 때문에 매우 비효율적 입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5ec828d6-6494-487d-b325-61ebc91d0268/Untitled.png)

# 웹팩 번들링

3개의 자바스크립트를 다운받는 대신 번들링된 하나의 파일만 다운받을 수 있도록 변경해 보겠습니다

먼저 `script` 태그를 통해 cdn에서 다운받고 있던 lodash를 로컬에서 함께 번들링하여 서비스하겠습니다. 이를 위해 npm으로 lodash를 설치합니다

```tsx
> npm install lodash
```

웹팩과 웹팩 cli를 설치합니다

```tsx
> npm install webpack webpack-cli --save-dev
```

`src/index.js` 에서 lodash를 import 합니다

```tsx
import _ from "lodash";
import { add } from "./calc.js";

function component() {
    var element = document.createElement("div");

    /* lodash is required for the next line to work */
    element.innerHTML = _.join(["Hello", "webpack", add(2, 3)], " ");

    return element;
}

document.body.appendChild(component());
```

웹팩을 실행해 번들링합니다. 그러면 기본 설정에 의해 자바스크립트 파일이 전부 번들링되어 `dist/main.js` 파일로 생성됩니다

```tsx
> npx webpack
```

`index.html` 에서 lodash script 태그를 삭제하고 index.js를 main.js로 변경합니다

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Webpack Demo</title>
        <!-- <script src="<https://unpkg.com/lodash@4.16.6/lodash.js>"></script> -->
    </head>
    <body>
        <script src="dist/main.js"></script>
    </body>
</html>
```

로컬 서버로 다시 화면을 띄워보면 다음과 같이 하나의 자바스크립트 파일만 다운받는 것을 확인할 수 있습니다. 심지어 파일 용량도 줄었고 시간도 대폭 감소하였습니다. 바로 이러한 효과를 위해 자바스크립트 모듈은 서비스시에 가급적 번들링해야 합니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/59436f5f-ee97-4284-bf65-46803d54fcc2/Untitled.png)

# 웹팩 설정

설정파일을 통해 상세하게 웹팩을 설정할 수 있습니다

`webpack.config.js` 파일을 생성하고 다음과 같이 작성합니다

```jsx
var path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

좀 더 빌드를 쉽게 하기 위해 `package.json`에  `build` 스크립트 명령을 추가합니다

```jsx
"scripts": {
  "build": "webpack"
}
```

npm run 명령으로 빌드를 실행하면 동일하게 번들링이 실행됩니다

```jsx
> npm run build
```