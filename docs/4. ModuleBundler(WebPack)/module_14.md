---
layout: default
title: 개발 환경
parent: ModuleBundler(WebPack)
nav_order: 14
---

# 개발 환경

웹팩을 사용하여 개발을 더욱 편하게 할 수 있는 환경을 구축할 수 있습니다

### 소스맵 (sourceMap) 생성하기

현재 상태로 브라우저에서 디버깅을 하면 번들된 파일이기 때문에 디버깅이 매우 어렵습니다. 그래서 실제 실행되는 파일은 번들된 파일이더라도 디버깅은 원본 소스에서 실행하는 것이 편한데 이를 가능하게 하는 것이 바로 `소스맵(sourceMap)` 입니다

소스맵은 번들된 파일과 원본 소스의 맵 정보를 가지고 있으며 브라우저는 이 소스맵을 사용해서 디버깅을 실행합니다

타입스크립트가 적용된 경우에는 먼저 타입스크립트이 소스맵을 먼저 생성해야 합니다. tsconfig.json에서 sourceMap의 값을 true로 설정합니다

```tsx
// 📁 tsconfig.json
{
  "compilerOptions": {
		...
		"sourceMap": true,
		...
	}
}
```

웹팩 번들 파일의 소스맵을 생성하도록 설정합니다

```tsx
// 📁 webpack.config.js
module.exports = {
  ...
  devtool: "source-map",
  ...
};
```

빌드를 하면 번들 파일 외에 같은 이름의 `(번들파일).map`파일이 생성됩니다.

브라우저에 화면을 띄우고 개발자 도구에서 원본 코드의 일부를 검색해 보면 검색 결과에 `.ts` 파일의 원본 소스가 검색이 되는 것을 확인할 수 있습니다. 이 소스에서 디버깅도 가능합니다

### 웹팩 개발 서버 (webpack-dev-server) 사용하기

`webpack-dev-server`는 간단한 웹 서버와 실시간 다시 로딩 기능을 제공합니다

먼저 webpack-dev-server를 설치합니다

```tsx
> npm install --save-dev webpack-dev-server
```

웹팩 개발 서버를 사용하기 위해 다음과 같이 설정합니다

- `static` - 제공할 정적 파일의 경로입니다
- `port` - 웹서버 포트 입니다

```tsx
// 📁 webpack.config.js
module.exports = {
  ...
  devServer: {
    static: "./dist",
    port: 3000,
  },
  ...
};
```

웹팩 cli 명령어를 통해 웹서버를 실행합니다. 직접 실행해도 되고 package.json에 스크립트로 등록하여 실행해도 좋습니다

실행하면 브라우저에 화면이 뜨는 것을 확인할 수 있습니다

```tsx
// 📁 package.json
"scripts": {
  ...
  "start": "webpack serve --open"
},
```