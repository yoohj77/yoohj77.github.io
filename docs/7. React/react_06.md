---
layout: default
title: 6. 컴포넌트 반복
parent: React
nav_order: 6
---

# 6. 컴포넌트 반복

자바스크립트의 `map()` 함수를 사용하여 리액트 Element의 배열을 만들어내고 이를 출력할 수 있습니다

# 1. 리액트 Element 배열 출력하기

```tsx
// 📁 IterationSample.tsx
const IterationSample = () => {
    const names = ["눈사람", "얼음", "눈", "바람"];
    return (
        <ul>
            {names.map((name) => (
                <li>{name}</li>
            ))}
        </ul>
    );
};
export default IterationSample;
```

화면은 문제없이 나오겠지만 개발자 도구를 열어보면 `key` prop이 없다는 에러가 발생해 있습니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9b03cc5c-3c2c-4bdb-b3e6-7d07f94a7c09/Untitled.png)

# 2. key

리액트에서 key는 Element 배열을 렌더링했을 때 어떠한 항목에 변동이 있었는지 알아내려고 사용합니다. key값을 이용해 Virtual DOM에서 배열의 항목들을 비교할 수 있게 됩니다

다음과 같이 index로 key값을 설정해 주도록 하겠습니다. 항목을 구별할 수 있는 유일한 값이라면 어떠한 값이든 상관없습니다. 화면을 다시 확인하면 에러가 사라져 있습니다

```tsx
// 📁 IterationSample.tsx
...
{names.map((name, index) => (
    <li key={index}>{name}</li>
))}
```

# 3. 응용해 보기

출력할 목록의 배열을 변수에 담아 관리하도록 합니다

input 요소를 추가하여 항목을 추가합니다

항목을 더블클릭하면 목록에서 삭제합니다

배열 state를 설정할 때는 원본 state를 수정하는 것이 아니라 새로운 state 객체를 생성해야 합니다. 원본 state 객체의 `불변성` 을 유지해 주어야 리액트의 성능이 최적화 됩니다

```tsx
// 📁 IterationSample.tsx
import { ChangeEvent, useState } from "react";

const IterationSample = () => {
    const [names, setNames] = useState([
        { id: 1, text: "눈사람" },
        { id: 2, text: "얼음" },
        { id: 3, text: "눈" },
        { id: 4, text: "바람" },
    ]);
    const [inputText, setInputText] = useState("");
    const [nextId, setNextId] = useState(5); // 새로운 항목을 추가할 때 사용할 id

    const onChange = (e: ChangeEvent<HTMLInputElement>) => setInputText(e.target.value);

    const onClick = () => {
        const nextNames = names.concat({
            id: nextId,
            text: inputText,
        });
        setNextId(nextId + 1);
        setNames(nextNames);
        setInputText("");
    };

    const onRemove = (id: number) => {
        const nextNames = names.filter((name) => name.id !== id);
        setNames(nextNames);
    };

    return (
        <>
            <input value={inputText} onChange={onChange} />
            <button onClick={onClick}>추가</button>
            <ul>
                {names.map((name) => (
                    <li key={name.id} onDoubleClick={() => onRemove(name.id)}>
                        {name.text}
                    </li>
                ))}
            </ul>
        </>
    );
};
export default IterationSample;
```