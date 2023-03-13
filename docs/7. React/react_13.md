---
layout: default
title: 12. immer를 사용하여 더 쉽게 불변성 유지하기
parent: React
nav_order: 13
---

# 12. immer를 사용하여 더 쉽게 불변성 유지하기

불변성을 유지하는 것은 리액트 성능 최적화에 있어 굉장히 중요한 일입니다. 하지만 객체의 구조가 깊어질 수록 불변성을 유지하면서 상태를 업데이트하는 것이 불편해 집니다

`immer` 라이브러리를 사용하면 이러한 불변성 유지가 더욱 쉬워 집니다

```tsx
const object = {
  somewhere: {
    deep: {
      inside: 3,
      array: [1, 2, 3, 4],
    },
    bar: 2,
  },
  foo: 1,
};

// somewhere.deep.inside 값을 4로 바꾸기
let nextObject = {
  ...object,
  somewhere: {
    ...object.somewhere,
    deep: {
      ...object.somewhere.deep,
      inside: 4,
    },
  },
};
```

# 1. immer를 설치하고 사용법 알아보기

먼저 immer를 설치합니다

```tsx
> npm install immer
```

### immer를 사용하지 않고 불변성 유지

전개 연산자와 배열 내장 함수를 사용하여 불변성을 유지해 주고 있는데 만약 상태가 복잡해 지면 이 작업이 불편해 집니다

```tsx
// 📁 ImmerApp.tsx
import { useRef, useCallback, useState, ChangeEvent, FormEvent } from "react";

interface Data {
  id: number;
  name: string;
  username: string;
}

const ImmerApp = () => {
  const nextId = useRef(1);
  const [form, setForm] = useState({ name: "", username: "" });
  const [data, setData] = useState<{ array: Data[]; uselessValue: any }>({
    array: [],
    uselessValue: null,
  });

  // input 수정을 위한 함수
  const onChange = useCallback(
    (e: ChangeEvent<HTMLInputElement>) => {
      const { name, value } = e.target;
      setForm({
        ...form,
        [name]: [value],
      });
    },
    [form]
  );

  // form 등록을 위한 함수
  const onSubmit = useCallback(
    (e: FormEvent<HTMLFormElement>) => {
      e.preventDefault();
      const info = {
        id: nextId.current,
        name: form.name,
        username: form.username,
      };

      // array 에 새 항목 등록
      setData({
        ...data,
        array: data.array.concat(info),
      });

      // form 초기화
      setForm({
        name: "",
        username: "",
      });
      nextId.current += 1;
    },
    [data, form.name, form.username]
  );

  // 항목을 삭제하는 함수
  const onRemove = useCallback(
    (id: number) => {
      setData({
        ...data,
        array: data.array.filter((info) => info.id !== id),
      });
    },
    [data]
  );

  return (
    <div>
      <form onSubmit={onSubmit}>
        <input
          name="username"
          placeholder="아이디"
          value={form.username}
          onChange={onChange}
        />
        <input
          name="name"
          placeholder="이름"
          value={form.name}
          onChange={onChange}
        />
        <button type="submit">등록</button>
      </form>
      <div>
        <ul>
          {data.array.map((info) => (
            <li key={info.id} onClick={() => onRemove(info.id)}>
              {info.username} ({info.name})
            </li>
          ))}
        </ul>
      </div>
    </div>
  );
};

export default ImmerApp;
```

### immer 사용법

immer는 다음과 같이 사용합니다. 첫번째 인자는 수정하고자 하는 상태 객체이고, 두 번째 인자는 상태를 어떻게 업데이트할지 정의하는 함수입니다

두 번째 인자의 함수에서는 기존처럼 불변성을 유지하기 위해 상태를 새로 만들어 내는 것이 아니라 `draft` 로 넘어오는 객체 자체를 수정하면 됩니다. 그러면 immer 내부에서 알아서 새로운 상태 객체를 만들어 줍니다

결국 불변성을 생각하지 않고 코드를 작성해도 되기 때문에 편리합니다

```tsx
import produce from "immer";
const nextState = produce(originalState, (draft) => {
  // 바꾸고 싶은 값 바꾸기
  draft.somewhere.deep.inside = 5;
});
```

### 컴포넌트에 immer 적용하기

```tsx
// 📁 ImmerApp.tsx
...
// input 수정을 위한 함수
const onChange = useCallback(
  (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setForm(
     **** produce(form, (draft) => {
        draft[name] = value;
      })
    );
  },
  [form]
);

...
// array 에 새 항목 등록
setData(
  ****produce(data, (draft) => {
    draft.array.push(info);
  })
);

...
// 항목을 삭제하는 함수
const onRemove = useCallback(
  (id: number) => {
    setData(
      ****produce(data, (draft) => {
        draft.array.splice(
          draft.array.findIndex((info) => info.id === id),
          1
        );
      })
    );
  },
  [data]
);
```

### useState의 함수형 업데이트와 immer 함께 쓰기

immer에서 첫번째 인자인 상태 객체를 넘기지 않고 상태 수정 함수만 넘기면 상태 업데이트 함수를 반환합니다. 그리고 이 함수를 useState의 함수형 업데이트에 사용할 수 있어 코드가 더 깔끔해 집니다. 또한 함수형 업데이트를 사용했기 때문에 상태 객체에 대한 의존성이 사라집니다

```tsx
// 📁 ImmerApp.tsx
...
// input 수정을 위한 함수
  const onChange = useCallback((e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setForm(
      produce((draft) => {
        draft[name] = value;
      })
    );
  }, []);

...
// array 에 새 항목 등록
setData(
  produce((draft) => {
    draft.array.push(info);
  })
);

...
// 항목을 삭제하는 함수
const onRemove = useCallback((id: number) => {
  setData(
    produce((draft) => {
      draft.array.splice(
        draft.array.findIndex((info) => info.id === id),
        1
      );
    })
  );
}, []);
```

# 2. 정리

immer 라이브러리는 편의를 위한 것이므로 만약 개발자가 오히려 불편하게 느껴진다면 사용할 필요는 없습니다