---
layout: default
title: Loader
parent: ModuleBundler(WebPack)
nav_order: 5
---

# Loader

`loader`는 웹팩이 웹 어플리케이션을 해석할 때 자바스크립트 파일이 아닌 웹 자원 (HTML, CSS, Images, 폰트 등)들을 변환할 수 있도록 도와주는 속성입니다

```jsx
// 📁 webpack.config.js
module.exports = {
  module: {
    rules: []
  }
}
```

웹팩은 모든 의존성을 동적으로 번들합니다. 이를 통해 모든 모듈이 의존성을 명확하게 명시하고 이를 통해 사용하지 않는 의존성은 번들에서 제외하는 등의 최적화를 할 수 있게 되고 유지보수성도 향상됩니다. 웹팩은 이를 `loader`를 통해 다양한 웹 자원들을 모두 웹팩이 이해할 수 있는 명시적 의존성(일반적으로 `import/export` 구문)에 포함할 수 있도록 합니다

# css-loader가 필요한 이유

css 파일을 로드할 때 일반적으로 HTML 파일에 link 태그로 넣습니다. 그런데 요즘에는 UI요소들을 컴포넌트 단위로 쪼개서 개발을 하다보니 css 파일 또한 컴포넌트 단위로 나누어 작성합니다.

그런데 이러한 쪼개진 css 파일들을 한꺼번에 HTML에 link 태그로 넣는 것은 매우 비효율적인 방법입니다. 왜냐하면 사용되지 않는 컴포넌트의 css 파일도 불러오기 때문입니다.

그렇기 떄문에 해당 컴포넌트가 사용될 때 관련된 css 파일을 불러오는 것이 가장 좋은 방법입니다. 그래서 다음과 같이 자바스크립트 파일에서 css 파일을 로드하는 패턴이 많이 사용됩니다

그런데 다음과 같이 css 파일을 import 하면 자바스크립트 표준은 당연히 이를 이해할 수 없으며 웹팩 또한 기본설정으로는 이를 이해하지 못합니다

```jsx
import './common.css';

console.log('css loaded');
```

웹팩 실행시 에러가 발생합니다. 위와 같은 패턴을 사용하려면 웹팩이 css 파일을 해석하기 위한 loader를 설정해 주어야 합니다

```jsx
ERROR in ./src/common.css 1:5
Module parse failed: Unexpected token (1:5)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See <https://webpack.js.org/concepts#loaders>
```

# style-loader, css-loader 적용해 보기

`css-loader`는 웹팩에서 css 파일을 불러올 수 있도록 해주는 loader 입니다

`style-loader` 는 css-loader를 이용해 가져온 css 값을 <style> 태그로 넣어주는 loader 입니다

이 두 로더를 사용하면 특정 컴포넌트(자바스크립트 파일)가 실행될 때 관련된 css 값을 <style> 태그로 동적으로 추가시킬 수 있게 됩니다

css-loader 적용을 위해 새로운 페이지를 하나 생성합니다. `css-loader.html` 파일을 추가로 생성합니다

```jsx
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <h1>CSS Loader</h1>
  </body>
</html>
```

`index.html`에서 a태그로 하이퍼링크를 연결합니다

```jsx
<a href="cssLoader.html">css-loader</a>
```

css-loader.html 파일에서 사용할 `src/css/cssLoader.css` 파일을 생성합니다

```jsx
body {
    color: red;
}
```

`src/cssLoader.js` 파일을 생성하고 cssLoader.css 파일을 import로 가져옵니다

```jsx
import "./css/cssLoader.css";

console.log("css loaded");
```

웹팩으로 빌드를 하는데 index.js와 cssLoader.js 가 따로 번들링되도록 멀티 entry를 설정합니다. 다음과 같이 설정하면 main.js와 cssLoader.js 번들 파일이 생성됩니다

```jsx
// 📁 webpack.config.js
entry: {
  main: "./src/index.js",
  cssLoader: "./src/cssLoader.js",
},
output: {
  path: path.resolve(__dirname, "dist"),
},
```

css-loader.html 에서 번들 파일을 로드하도록 script 태그를 추가합니다

```jsx
<script src="dist/cssLoader.js"></script>
```

바로 웹팩 빌드를 실행하면 에러가 발생하는데 아직 css-loader를 설정해 주지 않았기 때문에 css 파일을 읽지 못해서 발생한 것입니다

style-loader와 css-loader를 설치합니다

```jsx
> npm install style-loader css-loader --save-dev
```

설정에 loader를 추가합니다.

- `test` : 로더를 적용할 파일 유형 (일반적으로 정규 표현식 사용)
- `use` : 해당 파일에 적용할 로더의 이름

```jsx
// 📁 webpack.config.js
module: {
  rules: [
    {
      test: /\\.css$/,
      use: ['style-loader', 'css-loader']
    }
  ]
}
```

웹팩으로 빌드를 하면 성공적으로 번들 파일이 생성됩니다

화면을 띄워서 css-loader 링크를 클릭해 페이지를 이동해 보면 글자가 갑자기 빨간색으로 나오는 것을 확인하실 수 있습니다. index.html 로 돌아가면 글자색도 검정색으로 바뀌며 css-loader 페이지에서만 빨간색으로 나옵니다. 이것이 바로 css-loader를 사용하는 이유입니다

css-loader 페이지에서 개발자 도구를 통해 요소를 확인해 보면 head 태그에 style이 생성된 것을 확인할 수 있습니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b936e76a-1a41-4846-b5fd-fc4f0b539ddd/Untitled.png)

# 기타 loader

이외에도 다양한 loader가 존재합니다

- `ts loader` - 타입스크립트 파일 (.ts)을 불러와 컴파일 합니다
- `babel loader` - 최신 자바스크립트 문법을 es5 수준으로 낮추는 babel을 실행합니다
- `file loader` - 이미지 등의 파일을 불러와 번들링합니다. 이미지 파일을 import해 사용할 수 있습니다