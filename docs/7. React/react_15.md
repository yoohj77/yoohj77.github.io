---
layout: default
title: 14. 외부 API를 연동하여 뉴스 뷰어 만들기
parent: React
nav_order: 15
---

# 14. 외부 API를 연동하여 뉴스 뷰어 만들기

# 1. axios로 API 호출해서 데이터 받아 오기

`axios` (https://axios-http.com/kr/) 는 현재 가장 많이 사용되고 있는 자바스크립트 HTTP 클라이언트입니다. Promise 기반으로 처리하여 편리하게 사용할 수 있습니다

axios를 설치합니다

```tsx
> npm install axios
```

https://jsonplaceholder.typicode.com/ 의 API를 호출하여 데이터를 가져오고 이를 화면에 출력하는 예시입니다

```tsx
// 📁 AxiosComponent.tsx
import axios from "axios";
import { useState } from "react";

interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

const AxiosComponent = () => {
  const [data, setData] = useState<Todo | null>(null);

  const onClick = async () => {
    const response = await axios.get<Todo>(
      "<https://jsonplaceholder.typicode.com/todos/1>"
    );
    setData(response.data);
  };

  const { id, title, completed } = data ?? {};

  return (
    <div>
      <div>
        <button onClick={onClick}>불러오기</button>
      </div>
      <div>id: {id}</div>
      <div>title: {title}</div>
      <div>completed: {completed?.toString()}</div>
    </div>
  );
};

export default AxiosComponent;
```

# 2. newapi API 키 발급받기

최신 뉴스 정보를 가져오기 위해 뉴스 API를 사용합니다

https://newsapi.org/register 에서 가입하고 API키를 발급받습니다

https://newsapi.org/s/south-korea-news-api 에서 한국 뉴스를 가져오는 API 설명서를 확인합니다

사용할 API는 두 가지 형태입니다

- 전체 뉴스 불러오기
  - GET https://newsapi.org/v2/top-headlines?country=kr&apiKey=xxx
- 특정 카테고리 뉴스 불러오기
  - GET https://newsapi.org/v2/top-headlines?country=kr&category=business&apiKey=xxx

다음과 같이 axios로 API를 호출하고 뉴스를 출력할 수 있습니다