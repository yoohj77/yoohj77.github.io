---
layout: default
title: 이미지 로드
parent: ModuleBundler(WebPack)
nav_order: 9
---

# 이미지 로드

이미지 파일을 웹팩 의존성에 추가하여 동적으로 번들되도록 해 보겠습니다

### 이미지 파일 준비

다음의 이미지를 다운받거나 아무 이미지를 사용하여 `src/img/sample.png` 로 이미지를 생성합니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5139530d-2968-4e1c-b421-bb274acaf231/Untitled.png)

### 소스에서 이미지 파일 사용

`index.js`에서 이미지를 하나 추가하도록 하겠습니다. 이 때, 이미지를 직접 import한 후 요소의 src에 할당합니다

```jsx
import sample from "./img/sample.png";

...

const myIcon = new Image();
myIcon.src = sample;
element.appendChild(myIcon);
```

`css/cssLoader.css` 에서 배경이미지를 sample.png 로 추가합니다

```jsx
body {
    color: red;
    background: url('./img/sample.png');
}
```

### asset 로더 설정

당연히 이 상태에서 빌드를 하면 이미지를 해결할 수 없다고 에러가 발생합니다

웹팩에는 내장되어 있는 애셋 로더 모듈이 존재합니다. 이를 사용하여 이미지 loader를 설정합니다

```jsx
module: {
  rules: [
		...
    {
      test: /\\.(png|svg|jpg|jpeg|gif)$/i,
      type: "asset/resource",
    },
  ],
},
```

이후 웹팩 빌드는 성공적으로 실행되며 임의의 이름으로 이미지 파일이 생성되는 것을 볼 수 있습니다. 이는 이미지 파일이 웹팩에 의해 동적으로 번들되었기 때문입니다. 화면을 띄워보면 의도한 대로 정상적으로 이미지가 나오는 것을 확인할 수 있습니다