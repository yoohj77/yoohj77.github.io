---
layout: default
title: 개발환경
parent: devEnv
nav_order: 1
---

# 개발환경

# Prettier

`prettier`는 코드 formatter 로써 코드 포맷을 일관되게 유지하도록 도와줍니다

### 설치방법

`prettier-vscode` VSCode 확장 프로그램을 설치하면 적용 가능합니다

옵션을 설정하기 위해 `.prettierrc.json` 파일을 생성하여 설정합니다

구체적인 옵션은 다음 페이지를 참고하세요

https://prettier.io/docs/en/options.html

vscode에서 기본 기본 포맷터를 prettier로 설정하고 설정에서 저장시 포매팅을 하도록 설정하면 편하게 개발할 수 있습니다

https://shoney.tistory.com/156

# ESLint

`eslint` 는 자바스크립트 코드에서 발견되는 문제시되는 패턴들을 식별하기 위한 정적 코드 분석 도구입니다. prettier는 겉모습만 바꿔준다면 eslint는 내용을 보고 바꿔줍니다

다음과 같은 경우를 체크해 줍니다 https://eslint.org/docs/latest/rules/

- no-unused-var
- no-cond-assign
- no-empty

### 설치방법

`eslint` VSCode 확장 프로그램을 설치합니다

적용할 프로젝트에서 다음 명령어를 실행하면 자동으로 설치가 되고 설정도 할 수 있습니다

```jsx
> npm init @eslint/config
```

prettier를 함께 사용하는 경우에는 코드 스타일과 관련된 룰이 prettier와 충돌이 될 수 있습니다. 그렇기 때문에 `eslint-config-prettier` 를 eslint에 적용을 해서 prettier와 관련된 룰을 eslint에서는 끄도록 설정해 주는 것이 좋습니다

다음과 같이 설치하고 eslint 설정을 해주면 됩니다

```tsx
> npm i -D eslint-config-prettier
// 📁 .eslintrc.*
...
{
  "extends": [
    "some-other-config-you-use",
    "prettier"
  ]
}
```

.js 파일을 생성하고 다음과 같이 코드를 작성하면 eslint가 동작하는 것을 확인할 수 있습니다

```jsx
const a = 1;
if (a = 2) {

}

const b = 1;
b = 2;
```

CLI를 통해 체크할 수도 있습니다

```jsx
> npx eslint ./src
```

이를 빌드 전에 항상 체크하거나 commit 전에 체크하도록 설정할 수도 있습니다