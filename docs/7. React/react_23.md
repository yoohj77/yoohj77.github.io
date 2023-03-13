---
layout: default
title: 19. 코드 스플리팅
parent: React
nav_order: 23
---

# 19. 코드 스플리팅

create-react-app은 기본적으로 웹팩을 통해 파일을 번들링하며 라이브러리 파일을 제외한 직접 작성한 코드들을 모두 하나의 파일로 번들링해 줍니다. 그렇기 때문에 하나의 페이지에 접근하더라도 모든 페이지의 내용이 담겨 있는 파일을 다운받게 되어 효율적이지 않습니다

이러한 문제점을 해결하기 위해 파일을 하나로 번들링하는 것이 아니라 여러 개의 조각으로 쪼개서 번들링하는 것을 `코드 스플리팅` 이라 합니다. 그리고 그 방법 중의 하나가 바로 코드 비동기 로딩입니다. 코드 비동기 로딩을 통해 자바스크립트 함수, 객체 혹은 컴포넌트를 필요한 시점에 불러올 수 있습니다

# 1. 자바스크립트 함수 비동기 로딩

파일을 생성하고 alert을 띄우는 함수를 하나 작성합니다

```tsx
// 📁 notify.ts
export function notify() {
  alert("안녕하세요");
}
```

클릭시 위의 함수를 실행하는 컴포넌트를 만들고 화면을 띄워 확인합니다

```tsx
// 📁 NotifyComponent.tsx
import { notify } from "./notify";

const NotifyComponent = () => {
  const onClick = () => {
    notify();
  };
  return (
    <div>
      <button onClick={onClick}>Hello</button>
    </div>
  );
};

export default NotifyComponent;
```

`npm run build` 를 실행하고 생성된 번들 파일을 확인해 보면 두 개의 js 파일이 생성된 것을 확인할 수 있습니다. `main.xxx.js` 파일에는 직접 작성한 모든 파일 내용이 들어 있으며 다른 파일에는 라이브러리 내용이 들어 있습니다

그런데 만약 notify 함수를 다른 파일로 따로 분리하고 싶다면 해당 파일을 `동적 import` 하면 됩니다

```tsx
// 📁 NotifyComponent.tsx
...
const onClick = async () => {
  const { notify } = await import("./notify");
  notify();
};
```

위와 같이 수정을 하고 다시 빌드를 해 보면 파일이 하나 더 생성되어 있는 것을 확인할 수가 있는데 이 파일이 notify.ts 파일이 분리되어 번들링된 파일입니다.

이러한 방식으로 자바스크립트 함수를 스플리팅할 수 있습니다

# 2. React.lazy와 Suspense를 통한 컴포넌트 코드 스플리팅

코드 스플리팅을 위해 리액트에 내장된 기능으로 유틸 함수인 `React.lazy` 와 컴포넌트인 `Suspense` 가 있습니다

### React.lazy와 Suspense 사용하기

`React.lazy` 는 컴포넌트를 렌더링하는 시점에서 비동기적으로 로딩할 수 있게 해 주는 유틸 함수입니다

```tsx
const SplitMe = React.lazy(() => import('./SplitMe'));
```

`Suspense` 는 리액트 내장 컴포넌트로서 코드 스플리팅된 컴포넌트를 로딩할 수 있고, 로딩이 끝나지 않았을 때 보여줄 UI를 설정할 수 있습니다

`fallback` props로 로딩 중에 보여줄 내용을 설정할 수 있습니다

```tsx
import { Suspense } from "react";
...
<Suspense fallback={<div>loading...</div>}>
  <SplitMe />
</Suspense>
```

다음과 같이 사용할 수 있습니다

```tsx
// 📁 SplitMe.tsx
const SplitMe = () => {
  return <div>Split Me!</div>;
};

export default SplitMe;
// 📁 LazyComponent.tsx
import { lazy, Suspense } from "react";

const SplitMe = lazy(() => import("./SplitMe"));

const LazyComponent = () => {
  return (
    <div>
      <Suspense fallback={<div>loading...</div>}>
        <SplitMe />
      </Suspense>
    </div>
  );
};

export default LazyComponent;
```

빌드를 해보면 chunk 파일이 하나 더 생긴 것을 확인할 수 있는데 이 번들 파일이 SplitMe 컴포넌트가 분리된 파일입니다

### Loadable Components 를 통한 코드 스플리팅

`Loadable Components` 는 코드 스플리팅을 편하게 하도록 도와주는 라이브러리입니다. 특히 서버 사이드 렌더링을 지원하기 때문에 많이 사용됩니다. 자세한 사용법은 생략합니다