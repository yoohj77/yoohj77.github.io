---
layout: default
title: 타입스크립트 로드
parent: ModuleBundler(WebPack)
nav_order: 10
---

# 타입스크립트 로드

기본설정으로는 웹팩은 타입스크립트 파일을 이해하지 못합니다. 웹팩이 타입스크립트 파일을 이해하고 컴파일하여 번들링할 수 있도록 하려면 `ts-loader`를 사용해야 합니다

### typescript, ts-loader 설치

```jsx
> npm install --save-dev typescript ts-loader
```

### tsconfig.json 생성

```jsx
> npx tsc --init
```

브라우저에서 동작하는 코드이기 때문에 es module 시스템을 사용해야 합니다. 이를 위해 `module`을 `es6`로 설정합니다

```jsx
// 📁 tsconfig.json
{
  "compilerOptions": {
		...
		"module": "es6",
		...
	}
}
```

### ts 파일로 변환

모든 자바스크립트 파일(.js)을 타입스크립트 파일(.ts)로 확장자를 변경합니다

**calc.ts**

타입 오류가 나지 않도록 수정합니다

**index.ts**

lodash 모듈에 타입 선언 파일이 없어 빨간줄이 생깁니다. `@types/lodash` 를 설치해 줍니다

```jsx
> npm i --save-dev @types/lodash
```

calc.js 파일은 이제 소스폴더에 존재하지 않습니다. 컴파일시에는 calc.ts 파일을 읽어야 하고 런타임시에는 calc.js 파일을 읽어야 하기 때문에 calc.js 대신 calc로 import 구문을 수정합니다

```tsx
// 📁 index.ts
...
import { add } from "./calc";
```

그리고 이 import 구문에 대해서 웹팩이 파일을 잘 찾을 수 있도록 가져올 확장자를 알려주어야 합니다. 웹팩 설정에 다음과 같이 resolve를 설정합니다

```tsx
// 📁 webpack.config.js
module.exports = {
	...
  resolve: {
    extensions: [".tsx", ".ts", ".js"],
  },
	...
};
```

sample.png import 구문에 빨간줄이 생기는데 타입스크립트가 이 파일을 가져오지 못해서 발생하는 에러입니다. 이 import 구문은 asset 로더가 해결해 주기 때문에 타입스크립트 컴파일시에는 이 구문을 그대로 유지해 주면 됩니다. 이를 위해 `png파일` 모듈을 any 타입으로 선언해 주어야 합니다

다음과 같이 `custom.d.ts` 파일을 생성합니다. 그러면 빨간줄이 사라집니다

```tsx
// 📁 custom.d.ts
declare module "*.png" {
  export default any;
}
```

### ts-loader 설정

마지막으로 웹팩에  ts-loader를 설정합니다. 또한 entry 설정을 js파일에서 ts파일로 변경합니다

```tsx
// 📁 webpack.config.js
module.exports = {
	...
	entry: {
    main: "./src/index.ts",
    cssLoader: "./src/cssLoader.ts",
  },
  ...
  module: {
    rules: [
      {
        test: /\\.tsx?$/,
        use: "ts-loader",
        exclude: /node_modules/,
      },
      {
        test: /\\.css$/,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /\\.(png|svg|jpg|jpeg|gif)$/i,
        type: "asset/resource",
      },
    ],
  },
  ...
};
```

웹팩을 실행하여 번들링을 한 후 화면을 띄워 정상적으로 나오는지 확인합니다