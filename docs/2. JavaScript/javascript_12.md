---
layout: default
title: 과제 - 에러 핸들링
parent: JavaScript
nav_order: 12
---


# 에러 핸들링

# ‘try..catch’와 에러 핸들링

```jsx
try {
   ... 코드를 실행 ...
} catch(e) {
   ... 에러 핸들링 ...
} finally {
   ... 항상 실행 ...
}
```

### finally 구문

`finally` 구문은 어떤 일이 발생하더라도 마지막에 항상 실행됩니다. `try` 구문에서 `return`이 일어나더라도 finally는 실행됩니다

```jsx
function func() {

  try {
    return 1;

  } catch (e) {
    /* ... */
  } finally {
    alert( 'finally' );
  }
}

alert( func() ); // finally 안의 alert가 실행되고 난 후, 실행됨
```

### 비동기 코드

try..catch 는 동기적으로 동작하므로 비동기 코드 작성시 유의해야 합니다

```jsx
try {
  setTimeout(function() {
    noSuchVariable; // 스크립트는 여기서 죽습니다, 아래 catch 구문이 실행되지 않습니다
  }, 1000);
} catch (e) {
  alert( "작동 멈춤" );
}
```

### 에러 객체

에러가 발생하면 자바스크립트는 에러 상세내용이 담긴 객체를 생성하여 catch 블록에 이 객체를 인수로 전달합니다

```jsx
try {
  lalala; // 에러, 변수가 정의되지 않음!
} catch(err) {
  alert(err.name); // ReferenceError
  alert(err.message); // lalala is not defined
  alert(err.stack); // ReferenceError: lalala is not defined at ... (호출 스택)

  // 에러 전체를 보여줄 수도 있습니다.
  // 이때, 에러 객체는 "name: message" 형태의 문자열로 변환됩니다.
  alert(err); // ReferenceError: lalala is not defined
}
```

### ‘throw’ 연산자

`throw` 연산자는 에러를 생성합니다

자바스크립트는 에러 객체 생성을 위해 다양한 에러 객체 관련 생성자를 지원합니다

```jsx
let error = new Error(message);
let error = new SyntaxError(message);
let error = new ReferenceError(message);
```

이를 활용해 다음과 같이 에러를 직접 던질 수 있습니다

```jsx
let json = '{ "age": 30 }'; // 불완전한 데이터

try {

  let user = JSON.parse(json); // <-- 에러 없음

  if (!user.name) {
    throw new SyntaxError("불완전한 데이터: 이름 없음"); // (*)
  }
  alert( user.name );

} catch(e) {

	if (e instanceof SyntaxError) {
    alert( "JSON Error: " + e.message ); // 특정 에러만 처리
  } else {
    throw e; // 에러 다시 던지기 (*)
  }

}
```

### 전역 catch

예상치 못한 오류가 발생한 경우 이를 제어할 수 있도록 전역 catch 구문을 제공합니다. 이는 자바스크립트가 제공하는 것이 아닌 호스트 환경에서 제공합니다. 브라우저에서는 `window.onerror`, Node.js에서는 `process.on("uncaughtException")`을 사용할 수 있습니다

```jsx
<script>
  window.onerror = function(message, url, line, col, error) {
    alert(`${message}\\n At ${line}:${col} of ${url}`);
  };

  function readData() {
    badFunc(); // 에러가 발생한 장소
  }

  readData();
</script>
```

# 커스텀 에러와 에러 확장

자체 에러 클래스가 필요한 경우 `Error`클래스를 상속받아 커스텀 에러를 구현할 수 있습니다