---
layout: default
title: 20. 서버 사이드 렌더링
parent: React
nav_order: 27
---


# 20. 서버 사이드 렌더링

현재까지 구현한 방식은 클라이언 사이드 렌더링 방식입니다. 리액트를 사용한 어플리케이션을 서버 사이드 렌더링 방식으로도 구현이 가능합니다

`react-dom/server` 에서 제공하는 API를 활용하면 브라우저가 아닌 서버 환경에서 리액트로 렌더링된 화면의 html을 추출해 낼 수 있습니다

`index.tsx` 파일의 내용을 다음과 같이 수정합니다

```tsx
import ReactDOMServer from "react-dom/server";

const html = ReactDOMServer.renderToString(
  <div>Hello Server Side Rendering</div>
);

console.log(html);
```

그리고 `npm run build` 를 한 이후에 build 폴더에 생성된 main js 파일을 `node main.xxx.js` 로 node.js로 실행시킵니다

그러면 html 문자열이 출력되는 것을 확인할 수 있습니다. 이러한 방식으로 서버에 http 요청이 왔을 때 서버에서 리액트를 렌더링하여 html을 생성하고 이를 응답하여 서버 사이드 렌더링을 구현할 수 있습니다

서버 사이드 렌더링은 구현하기 매우 복잡하고 관련한 이슈들이 많기 때문에 자세한 내용은 생략합니다.

Next.js 같은 서버 사이드 렌더링을 지원하는 프레임워크를 사용하면 편하게 구현할 수 있습니다