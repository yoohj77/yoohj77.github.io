---
layout: default
title: 캐싱
parent: ModuleBundler(WebPack)
nav_order: 11
---

# 캐싱

정적 파일들은 브라우저에 의해 캐싱이 됩니다. get 메소드로 동일한 url 요청이 이루어 지면 브라우저는 네트워크 통신을 하지 않고 캐싱된 값으로 응답합니다.

그렇기 때문에 만약 파일의 내용이 수정되었더라도 동일한 url로 요청이 된다면 캐싱된 파일을 사용하기 때문에 수정된 파일이 다운되지 않아 문제가 됩니다.

이 문제를 해결하기 위해 파일의 내용이 수정될 때마다 파일명을 바꿔주는 것이 좋은 해결방법입니다. 이를 통해 파일 내용이 바뀔 때마다 새로운 url로 요청을 하게 되어 수정된 파일을 새로 다운받을 수 있습니다.

웹팩은 파일의 내용이 변경될 때마다 새로운 파일명을 생성할 수 있도록 `contenthash` 라는 파일명 옵션을 제공합니다

### 파일명에 contenthash 포함하기

웹팩 설정에서 output.filename 을 다음과 같이 추가합니다. `name`은 청크 이름이며 `contenthash`는 파일 내용에 따라 생성되는 해쉬값입니다

```tsx
// 📁 webpack.config.js
module.exports = {
  ...
  output: {
    filename: "[name].[contenthash].bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  ...
};
```

빌드해 보면 다음과 같이 파일이 생성되는 것을 볼 수 있습니다. 파일 내용을 수정하지 않으면 계속 동일한 이름으로 번들이 생성되며 파일을 수정하는 순간 해쉬값이 바뀌어 새로운 이름으로 생성됩니다

```tsx
main.be6516cb6c706e8d9fdd.bundle.js
cssLoader.ba62cdb1cf6951bc312d.bundle.js
```

캐싱을 고려하여 동적인 이름의 파일을 생성한 것은 좋은데 이를 html에서 로드하려면 빌드할 때마다 매번 html 에서 번들 파일의 파일명을 바꿔 주어야 합니다.

이를 해결하기 위해 html 파일을 자동으로 생성해 주는 플러그인을 사용할 수 있습니다

### ***\*HtmlWebpackPlugin 사용하기\****

먼저 `html-webpack-plugin`을 설치합니다

```tsx
> npm install --save-dev html-webpack-plugin
```

다음과 같이 웹팩에 html-webpack-plugin을 설정합니다. 기존과 동일하게 index.html과 cssLoader.html을 생성해야 하기 때문에 각각 설정해 줍니다

- `filename` - 생성되는 html 의 파일명입니다
- `template` - 생성할 html의 템플릿 파일입니다
- `chunks` - 포함할 번들 파일의 청크 이름입니다

```tsx
// 📁 webpack.config.js
...
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  ...
  plugins: [
    ...
    new HtmlWebpackPlugin({
      filename: "index.html",
      template: "index.html",
      chunks: ["main"],
    }),
    new HtmlWebpackPlugin({
      filename: "cssLoader.html",
      template: "cssLoader.html",
      chunks: ["cssLoader"],
    }),
  ],
};
```

index.html과 cssLoader.html 에서 기존의 script 태그는 삭제합니다

```html
// 📁 index.html
<!DOCTYPE html>
<html>
  <head>
    <title>Webpack Demo</title>
    <!-- <script src="<https://unpkg.com/lodash@4.16.6/lodash.js>"></script> -->
  </head>
  <body>
    <a href="cssLoader.html">css-loader</a>
    <!-- <script src="dist/main.js"></script> -->
  </body>
</html>
// 📁 cssLoader.html
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
    <!-- <script src="dist/cssLoader.js"></script> -->
  </body>
</html>
```

추가적으로 `dist` 폴더에 계속 번들 파일이 쌓이기 때문에 빌드시 깨끗이 정리하는 옵션을 설정할 수 있습니다. `output.clean` 을 true로 설정하면 됩니다

```tsx
// 📁 webpack.config.js
module.exports = {
  ...
  output: {
    filename: "[name].[contenthash].bundle.js",
    path: path.resolve(__dirname, "dist"),
    clean: true,
  },
};
```

빌드 후에 산출물을 확인해 보면 index.html과 cssLoader.html 파일이 생성된 것을 볼 수 있습니다. 생성된 index.html로 화면을 띄워보면 정상적으로 출력됩니다

### 라이브러리 분리하기

현재는 lodash 라이브러리가 main 번들 파일에 포함되어 있습니다. 그렇기 때문에 index.ts 파일이 수정되어도 수정되지 않은 lodash 라이브러리도 번들에 포함되어 새로 다운받게 됩니다. 타사 라이브러리는 변경이 잘 일어나지 않기 때문에 번들을 따로 분리해 주는 것이 좋습니다.

`optimization.splitChunks`를 다음과 같이 설정하면 node_modules 에 있는 모든 파일들은 `vendor` 번들로 따로 생성되게 됩니다. 결국 index.ts 파일이 수정되어도 lodash 라이브러리는 새로 다운받지 않고 캐싱된 값을 사용하게 됩니다

```tsx
// 📁 webpack.config.js
module.exports = {
  ...
	optimization: {
    splitChunks: {
      cacheGroups: {
        vendor: {
          test: /[\\\\/]node_modules[\\\\/]/,
          name: "vendors",
          chunks: "all",
        },
      },
    },
  },
};
```

빌드 해 보면 vendors 번들 파일이 생성되어 있는 것을 확인할 수 있고 html에도 추가되어 있습니다. 화면을 띄우면 기존과 동일하게 정상적으로 출력되는 것을 확인할 수 있습니다