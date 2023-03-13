---
layout: default
title: Output
parent: ModuleBundler(WebPack)
nav_order: 4
---

# Output

`output` 속성은 웹팩을 돌리고 난 결과물의 파일 경로를 의미합니다

```jsx
// 📁 webpack.config.js
module.exports = {
  output: {
    filename: 'bundle.js'
  }
}
```

# Output 속성 옵션 형태

- `filename` - 웹팩으로 빌드한 파일의 이름입니다
- `path` - 해당 파일의 경로입니다

```jsx
// 📁 webpack.config.js
var path = require('path');

module.exports = {
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist')
  }
}
```

# Output 파일 이름 옵션

`filename` 속성에 여러가지 옵션을 넣을 수 있습니다

- `name` - 청크 이름
- `id` - 웹팩 내부 모듈 ID
- `hash` - 빌드 고유 해시값
- `chunkhash` - 모듈 내용을 기준으로 생성된 해시값

```jsx
// 📁 webpack.config.js
module.exports = {
  entry: "./src/index.js",
  output: {
    filename: '[name].[id].[hash].[chunkhash].bundle.js'
  }
};

// main.179.1ea558f63855d90ce456.b06e54b058e7297a99d9.bundle.js 생성
```