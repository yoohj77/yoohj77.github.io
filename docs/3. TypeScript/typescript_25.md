---
layout: default
title: 설정(tsconfig)
parent: TypeScript
nav_order: 24
---
# tsconfig

타입스크립트 파일을 컴파일할 때의 설정을 `tsconfig.json` 파일에 정의해 놓을 수 있습니다. `tsc` 컴파일러는 기본적으로 tsconfig.json 파일을 읽어서 컴파일을 수행합니다

# 기본 tsconfig.json 파일 생성

처음부터 tsconfig.json파일을 작성하기는 쉽지 않기 때문에 기본 설정 파일을 생성해 주는 tsc CLI 명령이 있습니다.

다음의 명령어를 실행하면 기본 설정되어 있는 tsconfig.json 파일이 생성되며 사용할 수 있는 모든 옵션이 주석처리 되어 있어 필요한 경우 주석만 풀어서 편하게 사용할 수 있습니다

```bash
> tsc --init
```

# 타입스크립트 설정 파일 인식 기준

tsc 명령어를 대상 파일을 지정하지 않고 실행하면 현재 폴더에 있는 타입스크립트 설정 파일을 기준으로 변환 작업을 수행합니다. 만약 현재 폴더에 타입스크립트 설정 파일이 없다면 프로젝트 폴더 내에서 상위 폴더의 경로를 검색해 나갑니다

```bash
> tsc
```

tsconfig.json 파일의 경로를 아래와 같이 명시적으로 지정할 수 있습니다

```bash
> tsc --project ./src/tsconfig.json
```

# 타입스크립트 설정 파일 속성

자주 사용되는 속성 몇 가지만 예시로 설명합니다

- tsconfig 전체 레퍼런스 https://www.typescriptlang.org/tsconfig

## 기본

### files

컴파일할 대상 파일입니다

```json
{
  "files": ["app.ts", "./utils/math.ts"]
}
```

### include

컴파일할 대상 폴더입니다. 와일드 카드 패턴을 사용할 수 있습니다

```json
{
  "include": ["src/**/*"]
}
```

### exclude

컴파일하지 않을 대상 폴더입니다

```json
{
  "exclude": ["node_modules"]
}
```

### extends

다른 tsconfig.json 파일을 상속받을 수 있습니다

```json
{
  "extends": "./config/base/tsconfig.json"
}
```

## 컴파일 옵션

컴파일과 관련된 옵션은 `compileOptions` 속성 밑에 설정합니다

### target

컴파일시 생성되는 자바스크립트 버전입니다. es3 부터 가장 최신인 esnext 까지 있습니다

```json
{
  "compilerOptions": {
    "target": "esnext"
  }
}
```

### lib

컴파일시 포함될 라이브러리의 목록입니다. 타입스크립트는 해당 코드가 실행될 호스트가 어떠한 환경인지 알 수 없기 때문에 해당 호스트에서 사용할 수 있는 기본 라이브러리를 추가해 주어야 합니다. 그래야 관련 타입이 로드되어 기능을 사용할 수 있습니다

```json
{
  "compilerOptions": {
    "lib": ["es2015", "dom", "dom.iterable"]
  }
}
```

### module

컴파일시 변환할 모듈 시스템입니다

```json
{
  "compilerOptions": {
    "module": "CommonJS"
  }
}
```

### allowJs

타입스크립트 컴파일 작업을 진행할 때 자바스크립트 파일도 포함될 수 있는지를 설정하는 속성입니다. 주로 이미 기존에 존재하는 자바스크립트 프로젝트에 타입스크립트를 점진적으로 적용할 때 사용하면 좋은 속성입니다

```json
{
  "compilerOptions": {
    "allowJs": true
  }
}
```