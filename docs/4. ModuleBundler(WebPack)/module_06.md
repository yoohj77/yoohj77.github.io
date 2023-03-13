---
layout: default
title: Plugin
parent: ModuleBundler(WebPack)
nav_order: 6
---

# Plugin

`plugin`은 웹팩의 기본적인 동작에 추가적인 기능을 제공하는 속성입니다. 로더는 특정 유형의 모듈을 변환하는 데 사용되지만, 플러그인을 활용하여 번들을 최적화하거나, 애셋을 관리하고, 또 환경 변수 주입등과 같은 광범위한 작업을 수행 할 수 있습니다

# plugin 사용해 보기

웹팩의 빌드 진행률을 알려주는 progressPlugin을 다음과 같이 설정하면 빌드시 진행률이 출력됩니다

```jsx
// 📁 webpack.config.js
var webpack = require('webpack');

module.exports = {
  plugins: [
    new webpack.ProgressPlugin()
  ]
}
```

# 기타 plugin

이외에도 다양한 plugin이 존재합니다

- `html-webpack-plugin` - 생성된 모든 번들을 자동으로 삽입하여 HTML 파일을 생성해 줍니다
- 기타 plugin 목록 https://webpack.kr/plugins