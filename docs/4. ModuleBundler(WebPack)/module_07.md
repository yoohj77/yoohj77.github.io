---
layout: default
title: Mode
parent: ModuleBundler(WebPack)
nav_order: 7
---

# Mode

`mode` 옵션을 사용하면 webpack에 내장된 최적화 기능을 사용할 수 있습니다. 다음의 세 가지 값 중 하나를 설정할 수 있습니다. 기본은 `production` 입니다

```jsx
module.exports = {
  mode: 'production',
};
```

| 옵션        | 설명                                                         |
| ----------- | ------------------------------------------------------------ |
| development | 개발모드. 개발자들이 좀 더 보기 편하게 웹팩 로그나 결과물이 보여집니다 |
| production  | 배포모드. 성능 최적화를 위해 기본적인 파일 압축 등의 빌드 과정이 추가됩니다 |
| none        | 최적화하지 않음                                              |