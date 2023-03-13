---
layout: default
title: Entry
parent: ModuleBundler(WebPack)
nav_order: 3
---

# Entry

`entry` 속성은 웹팩에서 웹 자원을 변환하기 위해 필요한 최초 진입점이자 자바스크립트 파일 경로입니다

```jsx
// 📁 webpack.config.js
module.exports = {
  entry: './src/index.js'
}
```

entry 파일 자신과 entry에서 사용되는 모든 자바사크립트 파일을 분석하여 자동으로 알아서 함께 번들링합니다

```jsx
// 📁 index.js
import LoginView from './LoginView.js';
import HomeView from './HomeView.js';
import PostView from './PostView.js';

function initApp() {
  LoginView.init();
  HomeView.init();
  PostView.init();
}

initApp();
```

다음과 같이 모듈 간의 의존 관계가 생기는 구조를 `디펜던시 그래프(Dependency Graph)`라고 합니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/56233899-8d92-4085-9239-72ef65d7cfc9/Untitled.png)

entry 포인트 (진입점)은 여러 개 일 수도 있습니다. 여러 개인 경우 개별 진입점마다 번들링된 파일이 생성됩니다

```jsx
entry: {
  login: './src/LoginView.js',
  main: './src/MainView.js'
}
```