---
layout: default
title: 모듈
parent: JavaScript
nav_order: 15
---

# 모듈

자바스크립트에서 `모듈`이란 분리된 자바스크립트 파일을 말합니다. 과거의 모듈 시스템은 다음과 같이 발전했습니다

- AMD - 가장 오래된 모듈 시스템 중 하나로 require.js 라는 라이브러리를 통해 개발되었습니다
- CommonJS - Node.js 서버를 위해 만들어진 모듈 시스템입니다
- UMD - AMD와 CommonJS와 같은 다양한 모듈 시스템을 함께 사용하기 위한 패턴입니다

이후  2015년에 모듈 시스템이 자바스크립트 표준으로 등재되었습니다. 이를 `es module` 줄여서 `ESM`이라 부릅니다

esm은 `export` 와 `import` 지시자를 사용합니다

```jsx
// 📁 sayHi.js
export function sayHi(user) {
  alert(`Hello, ${user}!`);
}

// 📁 main.js
import {sayHi} from './sayHi.js';

alert(sayHi); // 함수
sayHi('John'); // Hello, John!
```

브라우저에서 모듈 시스템을 사용하려면 `<script type="module">` 로 스크립트를 가져와야 합니다

```jsx
<script type="module" src="main.js">
```

### 모듈의 특징

- 모듈을 사용하면 자동으로 엄격모드(use strict) 로 실행됩니다
- 모듈은 자신만의 스코프를 가집니다. 모듈 내부에서 정의한 변수나 함수는 다른 스크립트에서 접근할 수 없습니다
- 동일한 모듈이 여러 곳에서 사용되더라도 최초 호출 시 단 한번만 실행됩니다. 처음 실행된 결과가 나머지 호출시 동일하게 사용됩니다

### 지연 실행

- 모듈 스크립트를 다운로드할 때 브라우저의 HTML 처리가 멈추지 않습니다. 브라우저는 외부 모듈 스크립트와 기타 리소스를 병렬적으로 불러옵니다
- 모듈 스크립트는 HTML 문서가 완전히 만들어진 이후에 실행됩니다

### 경로가 없는 모듈은 금지

```jsx
import {sayHi} from '../sayHi'; // Error!
// './sayHi.js'와 같이 경로 정보를 지정해 주어야 합니다.
```

### 빌드 툴

브라우저 환경에서 모듈을 단독으로 사용하는 경우는 흔치 않습니다. 대개 웹팩과 같은 툴을 사용하여 모듈을 한데 묶는 번들링 방식을 사용합니다

자바스크립트 파일이 많은 경우 이를 그대로 HTML에 script 태그로 넣으면 많은 파일을 다운받아야 하기 때문에 성능이 저하됩니다. 이러한 문제를 해결하기 위해 자바스크립트 파일을 적당한 크기로 묶어 주는데 이를 `번들링`이라 합니다

이 번들링 과정에서는 단순히 묶어주는 역할 외에도 코드를 최적화하는 등의 다양한 역할을 수행합니다.

보통 번들링 과정을 거치면 `import`, `export` 구문이 사라지기 때문에 일반 스크립트로 취급할 수 있습니다

```jsx
<!-- 웹팩과 같은 툴로 번들링 과정을 거친 스크립트인 bundle.js -->
<script src="bundle.js"></script>
```

# 모듈 내보내고 가져오기

### export 하기

```jsx
// 배열 내보내기
export let months = ['Jan', 'Feb', 'Mar','Apr', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];

// 선언된 변수나 함수를 내보내기
function sayHi(user) {
  alert(`Hello, ${user}!`);
}
export {sayHi}; // 함수를 내보냄

// export 'as'
export {sayHi as hi, sayBye as bye};
import { hi } from './say.js'

// export default
export default class User {
  constructor(name) {
    this.name = name;
  }
}
import User from './user.js'

// export 'as default'
export {sayHi as default};  -> export default sayHi 와 동일합니다
```

### import 하기

```jsx
// 기본 import
import {sayHi, sayBye} from './say.js';

// 한꺼번에 가져오기
import * as say from './say.js'

// import 'as'
import {sayHi as hi, sayBye as bye} from './say.js';
hi('John');

// import default
import {default as User, sayHi} from './user.js'
```

### 모듈 다시 내보내기

```jsx
export {sayHi} from '.say.js'; // sayHi를 다시 내보내기 함
export {default as User} from './user.js'; // default export를 다시 내보내기 함

export * from './user.js'; // named export를 다시 내보내기
export {default} from './user.js'; // default export를 다시 내보내기
```

위의 코드들을 보면 default export, import의 경우 굉장히 특이하고 애매하다는 것을 알 수 있습니다. 이러한 이유로 default export, import를 선호하지 않는 경우가 많습니다

# 동적으로 모듈 가져오기

위의 방식들은 정적으로 모듈을 가져오는 방식이었습니다 동적으로 모듈을 가져오려면 `import()` 표현식을 사용하면 됩니다

`import(module)` 표현식은 모듈을 읽고 이 모듈이 내보내는 것들을 모두 포함하는 개체를 담은 프라미스를 반환합니다