---
layout: default
title: 소개
parent: JavaScript
nav_order: 1
---

# 소개

자바스크립트는 웹페이지에 기능을 넣기 위해 브라우저에서 쓸 목적으로 고안된 언어입니다. 자바스크립트는 실용적 측면을 고려하여 생각해 보면 두 가지 요소로 나누어 볼 수 있습니다

1. 프로그래밍 언어
2. 브라우저를 다루는 기능

사실 첫번째인 프로그래밍 언어가 자바스크립트이며 두번째인 브라우저와 관련된 기능은 자바스크립트가 아니라 `Web API` 입니다. Web API를 통해 브라우저를 다룰 수 있는 프로그래밍 언어가 자바스크립트이다 보니 마치 자바스크립트의 고유 기능처럼 생각할 수 있는데 그렇지는 않고 별개의 요소입니다.

브라우저에서 사용하기 위해 자바스크립트를 만들고 보니 언어 자체가 생각보다 괜찮아 보였습니다. 그래서 프로그래밍 언어로서의 자바스크립트를 떼다가 브라우저가 아닌 다른 곳에서도 사용하기 시작하였으며 대표적인 것이 `Node.js` 입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/529bfec3-4be7-440e-b62c-64d0feaf5ffe/Untitled.png)

Web API 예시

```jsx
document.getElementById('element-id');
alert(window.location.href);
```

Node.js API 예시