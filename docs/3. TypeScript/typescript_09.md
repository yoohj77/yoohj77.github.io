---
layout: default
title: 타입 단언
parent: TypeScript
nav_order: 9
---

# 타입 단언

때로는 코드 작성자가 타입스크립트보다 어떤 값의 타입에 대한 정보를 더 잘 아는 경우도 존재합니다. 이런 경우 `타입 단언`을 사용하면 타입을 좀 더 구체적으로 명시할 수 있습니다

타입 단언은 `as` 키워드 또는 꺽쇠를 사용하여 타입을 명시합니다

```tsx
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;

// or

const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```

# 불가능한 타입 단언

모든 타입 단언이 가능한 것은 아닙니다. 타입스크립트에서는 보다 구체적이거나 덜 구체적인 버전으로 변환하는 타입 단언만 허용됩니다

```tsx
const x = "hello" as number; // Error
```

만약 타입 변환이 너무 보수적으로 작용하여 원하는 타입으로 변경하지 못한다면 다음과 같이 두 번의 단언을 사용할 수 있습니다. 하지만 이러한 사용이 권장되지는 않습니다

```tsx
const a = ("hello" as any) as number; // OK
```

# 타입 단언과 컴파일

타입 단언은 컴파일러에 의해 제거되며 코드의 런타임 동작에는 전혀 영향을 주지 않습니다. 타입 단언이 설사 틀렸더라도 런타임시에 에러가 발생하지 않습니다

```tsx
function add10(num: number) {
    console.log(num + 10);
}

add10("hello" as any as number);
```

컴파일 후에 다음과 같이 js 파일이 생성되며 정상적으로 실행됩니다