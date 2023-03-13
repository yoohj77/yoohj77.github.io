---
layout: default
title: 13. 리액트 라우터로 SPA 개발하기
parent: React
nav_order: 14
---

# 13. 리액트 라우터로 SPA 개발하기

# 1. 라우팅이란?

`라우팅` 이란 사용자가 요청한 URL에 따라 알맞은 페이지를 보여주는 것을 말합니다

리액트에서 라우팅 시스템을 구축하기 위해 사용할 수 있는 선택지가 여러 가지 있습니다

- `리액트 라우터(React Router)` :  이 라이브러리는 가장 오래됐고 가장 많이 사용되고 있습니다
- `Next.js` :  리액트 프레임워크로써 굉장히 많은 기능이 제공되며 많이 사용되고 있는 추세입니다
- 기타 라우팅 라이브러리

여기에서는 리액트 라우터를 사용해 보겠습니다

# 2. 싱글 페이지 어플리케이션이란?

`SPA(Single Page Application)`는 하나의 페이지로 이루어진 어플리케이션이라는 뜻입니다

기존 멀티 페이지 어플리케이션은 사용자가 다른 페이지로 이동할 때마다 새로운 html 문서와 기타 리소스를 새로 전달받았습니다. 이러한 방식은 다음의 문제가 있습니다

- HTML 문서를 서버에서 만들어 내기 때문에 서버에 부담이 됩니다
- 동일한 HTML내용과 리소스를 반복적으로 받으며 이에 대한 브라우저 렌더링이 다시 일어납니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e8c35571-2eae-445b-b082-90dd82725335/Untitled.png)

그래서 리액트 같은 라이브러리를 사용해 뷰 렌더링을 클라이언트가 담당하도록 하고 페이지를 이동할 때 전체 html문서를 받는 것이 아니라 필요한 부분만 데이터를 받아와 javascript로 렌더링하는 방식을 사용하게 되었는데 이것이 `SPA` 방식 입니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b2e72964-90d6-4c16-a2ac-2a32c4232cca/Untitled.png)

이러한 SPA 방식을 사용하면 사용자 입장에서는 마치 페이지가 이동한 것과 같은 효과가 발생하는데 하나의 문제가 URL이 바뀌지 않는다는 점입니다. 그래서 SPA 방식에서는 일반적으로 브라우저의 `History API` 를 사용하여 페이지 이동없이 브라우저의 주소창의 값만 변경해 줍니다

# 3. 리액트 라우터 적용 및 기본 사용법

### 라이브러리 설치

리액트 라우터를 설치합니다

```tsx
> npm install react-router-dom
```

### 프로젝트에 라우터 적용

최상단에 `BrowserRouter` 라는 컴포넌트를 사용하여 감싸면 리액트 라우터가 적용됩니다. 리액트 라우터는 여러 종류의 라우터가 있으며 그 중 `BrowserRouter`  는 HTML5의 History API를 사용하여 라우팅을 해 주는 라우팅 컴포넌트입니다

```tsx
// 📁 index.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import { BrowserRouter } from "react-router-dom";

const root = ReactDOM.createRoot(document.getElementById("root") as HTMLElement);
root.render(
    <React.StrictMode>
        <BrowserRouter>
            <App />
        </BrowserRouter>
    </React.StrictMode>
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: <https://bit.ly/CRA-vitals>
reportWebVitals();
```

### 페이지 컴포넌트 만들기

라우팅할 페이지 컴포넌트를 만듭니다

```tsx
// 📁 pages/Home.tsx
const Home = () => {
    return (
        <div>
            <h1>홈</h1>
            <p>가장 먼저 보여지는 페이지입니다</p>
        </div>
    );
};

export default Home;
// 📁 pages/About.tsx
const About = () => {
    return (
        <div>
            <h1>소개</h1>
            <p>리액트 라우터를 사용해 보는 프로젝트입니다</p>
        </div>
    );
};

export default About;
```

### Route 컴포넌트로 특정 경로에 원하는 컴포넌트 보여주기

이제 각 페이지에 대한 라우트 설정을 합니다

설정을 한 후 루트경로로 접근하면 Home 화면이 잘 나오고 /about 경로로 접근하면 About 화면이 잘 나와 라우팅이 잘 이루어 지는 것을 확인할 수 있습니다

```tsx
// 📁 App.tsx
import { Route, Routes } from "react-router-dom";
import About from "./pages/About";
import Home from "./pages/Home";

function App() {
    return (
        <Routes>
            <Route path="/" element={<Home />} />
            <Route path="/about" element={<About />} />
        </Routes>
    );
}

export default App;
```

### Link 컴포넌트를 사용하여 다른 페이지로 이동하는 링크 보여주기

만약 다음과 같이 about 링크를 a 태그로 추가하고 이동해 보면 SPA 방식으로 서버에 요청없이 클라이언트에서만 렌더링이 일어나지 않고 서버에 페이지 요청이 일어나게 됩니다

```tsx
// 📁 Home.tsx
const Home = () => {
    return (
        <div>
            <h1>홈</h1>
            <p>가장 먼저 보여지는 페이지입니다</p>
            <a href="/about">소개</a>
        </div>
    );
};

export default Home;
```

SPA 방식으로 페이지를 이동하려면 a태그가 아닌 리액트 라우터의 `Link` 컴포넌트를 사용해야 합니다. 다음과 같이 작성하고 링크를 눌러 페이지를 이동해 보면 서버에 요청없이 이동이 이루어지는 것을 볼 수 있습니다. 이것이 바로 `Link` 컴포넌트와 `BrowserRouter` 컴포넌트에 의한 SPA 라우팅입니다. 만약 링크로 이동하지 않고 브라우저 주소창에 바로 …/about url을 치고 접속하면 이때는 서버에 요청이 일어난 후에 About 화면이 정상적으로 나타나게 됩니다

- 화면에서 `Link` 컴포넌트로 이동할 때는 서버 요청없이 순수하게 클라이언트에서 이동하게 됩니다
- 직접 주소를 치고 접근하면 서버에 요청이 되면서 이동하게 됩니다

```tsx
// 📁 Home.tsx
import { Link } from "react-router-dom";

const Home = () => {
    return (
        <div>
            <h1>홈</h1>
            <p>가장 먼저 보여지는 페이지입니다</p>
            <Link to="/about">소개</Link>
        </div>
    );
};

export default Home;
```

# 4. URL 파라미터와 쿼리스트링

### URL 파라미터

URL 파라미터를 받아오기 위해서는 리액트 라우터의 `useParams` 훅을 사용할 수 있습니다. 라우트 설정시의 url 파라미터 설정대로 key/value 쌍의 데이터가 담겨 있습니다

```tsx
// 📁 pages/Profile.tsx
import { useParams } from "react-router-dom";

interface Data {
    [key: string]: {
        name: string;
        description: string;
    };
}

const data: Data = {
    velopert: {
        name: "김민준",
        description: "리액트를 좋아하는 개발자",
    },
    gildong: {
        name: "홍길동",
        description: "고전 소설 홍길동전의 주인공",
    },
};

const Profile = () => {
    const params = useParams();
    const profile = params.username && data[params.username];

    return (
        <div>
            <h1>사용자 프로필</h1>
            {profile ? (
                <div>
                    <h2>{profile.name}</h2>
                    <p>{profile.description}</p>
                </div>
            ) : (
                <p>존재하지 않는 프로필입니다</p>
            )}
        </div>
    );
};

export default Profile;
```

라우트에서 URL 파라미터 설정은 다음과 같이 `:` 으로 시작하는 키로 할 수 있습니다

```tsx
// 📁 App.tsx
...
<Routes>
    <Route path="/" element={<Home />} />
    <Route path="/about" element={<About />} />
    <Route path="/profiles/:username" element={<Profile />} />
</Routes>
```

Profile로 이동할 수 있도록 다음과 같이 링크를 추가해 줍니다

그러면 URL 파라미터가 잘 전달되어 화면이 나오는 것을 확인할 수 있습니다

```tsx
// 📁 Home.tsx
...
	<div>
	    <h1>홈</h1>
	    <p>가장 먼저 보여지는 페이지입니다</p>
	    <ul>
	        <li>
	            <Link to="/about">소개</Link>
	        </li>
	        <li>
	            <Link to="/profiles/velopert">velopert의 프로필</Link>
	        </li>
	        <li>
	            <Link to="/profiles/gildong">gildong 프로필</Link>
	        </li>
	        <li>
	            <Link to="/profiles/void">존재하지 않는 프로필</Link>
	        </li>
	    </ul>
	</div>
```

### 쿼리스트링

쿼리스트링을 사용할 때는 따로 라우트에 설정해줄 필요가 없습니다

쿼리스트링을 파싱하기 위해서는 리액트 라우터의 `useSearchParams` Hook을 사용하면 쉽습니다. 쿼리스트링의 값은 항상 문자열입니다

```tsx
// 📁 pages/About.tsx
import { useSearchParams } from "react-router-dom";

const About = () => {
  const [searchParams, setSearchParams] = useSearchParams();
  const detail = searchParams.get("detail") ?? "false";
  const mode = searchParams.get("mode") ?? "1";

  const onToggleDetail = () => {
    setSearchParams({ mode, detail: detail === "true" ? "false" : "true" });
  };

  const onIncreaseMode = () => {
    const nextMode = mode === null ? 1 : parseInt(mode) + 1;
    setSearchParams({ mode: nextMode.toString(), detail });
  };

  return (
    <div>
      <h1>소개</h1>
      <p>리액트 라우터를 사용해 보는 프로젝트입니다</p>
      <p>detail: {detail}</p>
      <p>mode: {mode}</p>
      <button onClick={onToggleDetail}>Toggle detail</button>
      <button onClick={onIncreaseMode}>mode + 1</button>
    </div>
  );
};

export default About;
```

# 5. 중첩된 라우트

여러 페이지끼리 공통적으로 보여줘야 하는 레이아웃이 있는 경우 중첩된 라우트를 사용할 수 있습니다

Home, About, Profile 페이지 상단에 공통된 헤더를 출력하려면 물론 헤더 컴포넌트를 만들어 동일하게 사용할 수도 있겠으나 중첩된 라우트를 사용하는 패턴도 있으며 꽤 유용합니다

라우트 컴포넌트 안에 자식으로 라우트를 설정하면 부모 라우트의 element 컴포넌트가 항상 적용이 되며 부모 라우트는 사실상 `레이아웃` 이 됩니다

레이아웃 파일에서 자식 라우트 컴포넌트를 출력하기 위해서는 `Outlet` 컴포넌트를 사용하면 됩니다

```tsx
// 📁 Layout.tsx
import { Outlet } from "react-router-dom";

const Layout = () => {
  return (
    <div>
      <header style={{ background: "lightgray", padding: 16, fontSize: 24 }}>
        Header
      </header>
      <main>
        <Outlet />
      </main>
    </div>
  );
};

export default Layout;
// 📁 App.tsx
...
function App() {
  return (
    <Routes>
      <Route element={<Layout />}>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/profiles/:username" element={<Profile />} />
      </Route>
    </Routes>
  );
}
```

### index props

중첩된 라우트를 사용할 때 부모 라우트 경로에 대한 기본 자식 컴포넌트인 경우, 즉 부모 라우트 경로로 접근했을 때 보여줘야 하는 자식 컴포넌트라면 `index` props 로 지정할 수 있습니다

```tsx
// 📁 App.tsx
...
<Routes>
  <Route path="/" element={<Layout />}>
    <Route index element={<Home />} />
    <Route path="/about" element={<About />} />
    <Route path="/profiles/:username" element={<Profile />} />
  </Route>
</Routes>
```

# 6. 리액트 라우터 부가 기능

리액트 라우터는 라우팅과 관련된 우용한 API들을 제공합니다

### useNavigate

`useNavigate`는 Link 컴포넌트를 사용하지 않고 다른 페이지로 이동할 수 있습니다

```tsx
// 📁 Layout.tsx
import { Outlet, useNavigate } from "react-router-dom";

const Layout = () => {
  const navigate = useNavigate();

  const goBack = () => {
    navigate(-1);
  };

  const goAbout = () => {
    navigate("/about");
  };

  return (
    <div>
      <header style={{ background: "lightgray", padding: 16, fontSize: 24 }}>
        <button onClick={goBack}>뒤로가기</button>
        <button onClick={goAbout}>소개</button>
      </header>
      <main>
        <Outlet />
      </main>
    </div>
  );
};

export default Layout;
```

### NavLink

`NavLink` 컴포넌트는 링크에서 사용하는 경로가 현재 라우트의 경로와 일치하는 경우 특정 스타일을 적용하는 컴포넌트입니다

이 컴포넌트의 style과 classname은 함수로 값을 설정할 수 있는데 현재 라우트 경로와 일치하는지 여부를 알려주는 `isActive` 값이 인자로 넘어옵니다. 이 값을 활용해 동적인 스타일링을 할 수 있습니다

```tsx
// 📁 Layout.tsx
...
const activeStyle: CSSProperties = {
  fontWeight: 700,
  color: "green",
};

...
<header style={{ background: "lightgray", padding: 16, fontSize: 24 }}>
  <button onClick={goBack}>뒤로가기</button>
  <button onClick={goAbout}>소개</button>
  <NavLink
    to="/profiles/velopert"
    style={({ isActive }) => (isActive ? activeStyle : undefined)}
  >
    velopert 프로필
  </NavLink>
</header>
```

### NotFound 페이지 만들기

라우트의 경로에 와일드카드인 `*` 을 사용하면 모든 경로가 매핑됩니다. 이를 통해 NotFound 페이지를 제공할 수 있습니다

```tsx
// 📁 NotFound.tsx
const NotFound = () => {
  return (
    <div
      style={{
        display: "flex",
        alignItems: "center",
        justifyContent: "center",
        fontSize: 64,
        position: "absolute",
        width: "100%",
        height: "100%",
      }}
    >
      404
    </div>
  );
};

export default NotFound;
// 📁 App.tsx
...
<Routes>
  <Route path="/" element={<Layout />}>
    <Route index element={<Home />} />
    <Route path="/about" element={<About />} />
    <Route path="/profiles/:username" element={<Profile />} />
  </Route>
  **<Route path="*" element={<NotFound />} />**
</Routes>
```

### Navigate 컴포넌트

`Navigate` 컴포넌트는 페이지를 리다이렉트하고 싶을 때 사용할 수 있습니다

replace 속성은 현재 페이지를 브라우저 History에 남기면서 리다이렉트 페이지로 이동할지 아니면 남기지 않고 교체하면서 이동할지 여부입니다

```tsx
// 📁 pages/About.tsx
const About = () => {
  const [searchParams, setSearchParams] = useSearchParams();
  const detail = searchParams.get("detail");
  const mode = searchParams.get("mode");

  if (!detail || !mode) {
    return <Navigate to="/" replace={true}></Navigate>;
  }
	...
}
```