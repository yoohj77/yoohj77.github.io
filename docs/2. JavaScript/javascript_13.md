---
layout: default
title: 프라미스와 async, await
parent: JavaScript
nav_order: 13
---


# 프라미스와 async, await

# 콜백

비동기 작업이 종료된 후 작업을 수행하기 위해 콜백함수를 사용할 수 있습니다

```jsx
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;
  script.onload = () => callback(null, script);
	script.onerror = () => callback(new Error(`${src}를 불러오는 도중에 에러가 발생했습니다.`));
  document.head.append(script);
}

loadScript('<https://cdnjs.cloudflare.com/ajax/libs/lodash.js/3.2.0/lodash.js>', (error, script) => {
	if (error) return handleError(error);

	alert(`${script.src}가 로드되었습니다.`);
  alert( _ ); // 스크립트에 정의된 함수
});
```

그런데 콜백 기반 비동기 프로그래밍은 콜백 지옥을 만듭니다

```jsx
loadScript('1.js', function(error, script) {

  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('2.js', function(error, script) {
      if (error) {
        handleError(error);
      } else {
        // ...
        loadScript('3.js', function(error, script) {
          if (error) {
            handleError(error);
          } else {
            // 모든 스크립트가 로딩된 후, 실행 흐름이 이어집니다. (*)
          }
        });

      }
    })
  }
});
```

# 프라미스

`Promise` 객체는 비동기 작업을 처리하기 위해 사용하는 객체입니다. Promise를 통해 비동기 메서드를 마치 동기 메서드처럼 값을 반환받을 수 있어 콜백 지옥으로부터 벗어날 수 있습니다

사용법은 다음과 같이 new 연산자로 Promise 객체를 생성합니다. 이 때 인자로 `executor` 함수를 넘기는데 이 함수는 Promise 생성시 곧바로 실행됩니다

이 `executor`함수에서 비동기 작업을 처리 하고 인자로 넘어온 `resolve` 또는 `reject` 함수를 실행해 주면 연결된 다음 코드들이 실행됩니다

```jsx
let promise = new Promise(function(resolve, reject) {
  // 프라미스가 만들어지면 executor 함수는 자동으로 실행됩니다.

  // 1초 뒤에 일이 성공적으로 끝났다는 신호가 전달되면서 result는 '완료'가 됩니다.
  setTimeout(() => resolve("완료"), 1000);
});

sdfgsdf
sdfgsdfh
sdfgsdfhsdf
g
```

- `resolve(value)` - 작업이 성공적으로 끝난 경우 결과와 함께 호출
- `reject(error)` - 에러 발생시 에러 객체와 함께 호출

### 소비자: then, catch, finally

Promise 객체의 결과를 then, catch, finally 메서드를 통해 소비할 수 있습니다

`.then` 은 Promise의 작업이 끝난 후 실행되며 첫번째 인수는 성공했을 때 두번째 인수는 실패했을 때 실행됩니다

```jsx
let promise = new Promise(function(resolve, reject) {
  setTimeout(() => resolve("완료!"), 1000);
});

// resolve 함수는 .then의 첫 번째 함수(인수)를 실행합니다.
promise.then(
  result => alert(result), // 1초 후 "완료!"를 출력
  error => alert(error) // 실행되지 않음
);
```

`.catch`는 실패한 경우에 실행됩니다. `.then` 에서 두번째 인수만 등록하는 것과 동일합니다

```jsx
let promise = new Promise((resolve, reject) => {
  setTimeout(() => reject(new Error("에러 발생!")), 1000);
});

// .catch(f)는 promise.then(null, f)과 동일하게 작동합니다
promise.catch(alert); // 1초 뒤 "Error: 에러 발생!" 출력
```

`.finally`는 어떠한 경우에도 실행됩니다. 단, resolve 또는 reject가 꼭 실행되어야 합니다. finally에서는 인수가 넘어오지 않습니다. 그러므로 성공인지 실패인지 알 수 없습니다

```jsx
new Promise((resolve, reject) => {
  setTimeout(() => resolve("결과"), 2000)
})
  .finally(() => alert("프라미스가 준비되었습니다."))
  .then(result => alert(result)); // <-- .then에서 result를 다룰 수 있음
```

### 예시: loadScript

```jsx
function loadScript(src) {
  return new Promise(function(resolve, reject) {
    let script = document.createElement('script');
    script.src = src;

    script.onload = () => resolve(script);
    script.onerror = () => reject(new Error(`${src}를 불러오는 도중에 에러가 발생함`));

    document.head.append(script);
  });
}

let promise = loadScript("<https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js>");

promise.then(
  script => alert(`${script.src}을 불러왔습니다!`),
  error => alert(`Error: ${error.message}`)
);

promise.then(script => alert('또다른 핸들러...'));
```

# 프라미스 체이닝

프라미스 체이닝은 `.then` 메서드를 계속 연결하는 방식입니다. 이 방식을 사용하면 콜백 지옥 문제를 해결할 수 있습니다

프라미스 체이닝시 then 메서드에서 반환한 값은 다음 메서드의 인자로 전달됩니다

중간에 비동기 작업이 필요하다면 Promise를 반환할 수도 있습니다. 그러면 해당 Promise가 완료되었을 때 다음 then 메서드가 실행됩니다

```jsx
new Promise(function(resolve, reject) {

  setTimeout(() => resolve(1), 1000);

}).then(function(result) {

  alert(result); // 1

  return new Promise((resolve, reject) => { // (*)
    setTimeout(() => resolve(result * 2), 1000);
  });

}).then(function(result) { // (**)

  alert(result); // 2

  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(result * 2), 1000);
  });

}).then(function(result) {

  alert(result); // 4

});
```

### loadScript 개선하기

프라미스 체이닝을 활용해서 콜백지옥을 개선할 수 있습니다

```jsx
loadScript("/article/promise-chaining/one.js")
  .then(script => loadScript("/article/promise-chaining/two.js"))
  .then(script => loadScript("/article/promise-chaining/three.js"))
```

### fetch와 체이닝 함께 응용하기

네트워크 요청시 자주 사용하는 `fetch`함수는 Promise 객체를 반환합니다. 이를 통해 다음과 같이 프라미스 체이닝을 활용할 수 있습니다

사용자 정보를 조회 → github의 사용자 정보 조회 → 아바타 표시

이는 모두 비동기 작업이기 때문에 Promise를 사용해서 다음과 같이 처리하면 좋습니다

```jsx
function loadJson(url) {
  return fetch(url)
    .then(response => response.json());
}

function loadGithubUser(name) {
  return fetch(`https://api.github.com/users/${name}`)
    .then(response => response.json());
}

function showAvatar(githubUser) {
  return new Promise(function(resolve, reject) {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => {
      img.remove();
      resolve(githubUser);
    }, 3000);
  });
}

// 함수를 이용하여 다시 동일 작업 수행
loadJson('/article/promise-chaining/user.json')
  .then(user => loadGithubUser(user.name))
  .then(showAvatar)
  .then(githubUser => alert(`Finished showing ${githubUser.name}`));
  // ...

asdgasdgasdf
```

# 프라미스와 에러 핸들링

Promise는 executor에서 오류가 발생시 자동으로 reject를 실행해 줍니다. 그렇기 때문에 특별히 reject 처리를 할 것이 아니라면 굳이 reject를 명시적으로 호출할 필요가 없습니다

```jsx
new Promise((resolve, reject) => {
  throw new Error("에러 발생!");
}).catch(alert); // Error: 에러 발생!
```

`.catch`는 이전 체이닝에서 발생하는 모든 오류를 처리해 줍니다. 하지만 이후 체이닝에서 발생하는 오류는 처리하지 않습니다. 이를 통해 promise에서 다양하게 에러를 핸들링할 수 있습니다

```jsx
fetch('/article/promise-chaining/user.json')
  .then(response => response.json())
  .then(user => fetch(`https://api.github.com/users/${user.name}`))
  .then(response => response.json())
  .catch(error => alert(`1차작업 오류: ${error.message}`)) // 위에 있는 체이닝에서 오류 발생시 실행됩니다. 아래에 있는 catch는 실행되지 않습니다

  .then(githubUser => new Promise((resolve, reject) => {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => {
      img.remove();
      resolve(githubUser);
    }, 3000);
  }))
  .catch(error => alert(`2차작업 오류: ${error.message}`)) // 위의 catch 이후에 있는 체이닝에서 오류 발생시 실행됩니다
```

### 처리되지 못한 거부

Promise에서 에러를 처리하지 못한 경우 일반적으로 호스트 환경에서 이벤트가 발생하여 이를 처리할 수 있습니다. 브라우저 환경에서는 `unhandlerejection` 이벤트로 처리할 수 있습니다

```jsx
window.addEventListener('unhandledrejection', function(event) {
  // unhandledrejection 이벤트엔 두 개의 특수 프로퍼티가 있습니다.
  alert(event.promise); // [object Promise] - 에러를 생성하는 프라미스
  alert(event.reason); // Error: 에러 발생! - 처리하지 못한 에러 객체
});

new Promise(function() {
  throw new Error("에러 발생!");
}); // 에러를 처리할 수 있는 .catch 핸들러가 없음
```

# 프라미스 API

- `Promise.all` - 비동기 작업을 동시에 실행시키고 모두 완료될 때까지 기다립니다. 하나라도 실패하면 전체가 실패합니다
- `Promise.allSettled` - 비동기 작업을 동시에 실행시키고 모두 완료될 때까지 기다립니다. 하나가 실패하더라도 나머지는 영향받지 않습니다
- `Promise.race` - 비동기 작업을 동시에 실행시키고 가장 먼저 처리되는 결과를 반환합니다
- `Promise.resolve` - resolve가 실행된 Promise를 생성합니다. 간단하게 Promise 객체를 생성할 때 사용하기 좋습니다
- `Promise.reject` - reject가 실행된 Promise를 생성합니다. 거의 사용되지 않습니다

```jsx
// Promise.all
Promise.all([
  fetch('/template.html'),
  fetch('/style.css'),
  fetch('/data.json')
]).then(render); // render 메서드는 fetch 결과 전부가 있어야 제대로 동작합니다.

// Promise.allSettled
Promise.all([
  fetch('/template.html'),
  fetch('/style.css'),
  fetch('/data.json')
])
.then(results => {
		//results(결과)는 다음과 같이 나옵니다
		results = [
		  {status: 'fulfilled', value: ...응답...},
		  {status: 'fulfilled', value: ...응답...},
		  {status: 'rejected', reason: ...에러 객체...}
		]
		...
});

// Promise.race
Promise.race([
  new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
  new Promise((resolve, reject) => setTimeout(() => reject(new Error("에러 발생!")), 2000)),
  new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
]).then(alert); // 1

// Promise.resolve
let cache = new Map();

function loadCached(url) {
  if (cache.has(url)) {
    return Promise.resolve(cache.get(url)); // (*)
  }

  return fetch(url)
    .then(response => response.text())
    .then(text => {
      cache.set(url,text);
      return text;
    });
}

// Promise.reject
let promise = new Promise((resolve, reject) => reject(error));
```

# 마이크로태스크

```jsx
let promise = Promise.resolve();

promise.then(() => alert("프라미스 성공!"));

alert("코드 종료"); // 얼럿 창이 가장 먼저 뜹니다.
```

Promise.resolve의 경우 바로 실행이 됨에도 불구하고 왜 아래에 있는 코드가 먼저 실행이 될까요? 이는 자바스크립트 엔진이 비동기 작업을 처리하는 방식 때문에 그렇습니다

### 마이크로태스크 큐

비동기 작업을 처리하기 위해서 `V8엔진`에서는 `마이크로태스크 큐`를 사용합니다

- 마이크로태스크 큐는 먼저 들어온 작업을 먼저 실행합니다
- 실행할 것이 아무것도 남아있지 않을 때만 마이크로태스크 큐에 있는 작업이 실행되기 시작합니다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3405087f-9a92-404b-889b-4b2c4519e7a2/Untitled.png)

코드의 실행순서를 유지하기 위해서는 다음과 같이 then으로 계속 체이닝을 하면 됩니다

```jsx
Promise.resolve()
  .then(() => alert("프라미스 성공!"))
  .then(() => alert("코드 종료"));
```

# async와 await

프라미스를 좀 더 편하게 사용할 수 있도록 `async`와 `await` 문법을 지원합니다. 이 문법을 사용하면 비동기 작업이 정말 동기작업처럼 쉬워지고 코드를 이해하기도 매우 쉬워집니다

### async 함수

`async` 키워드는 function 앞에 위치하며 해당 함수는 항상 Promise 객체를 반환합니다. 사실상 해당 함수가 Promise의 executor가 되도록 자동으로 만들어 줍니다. 이를 통해 비동기 함수를 작성할 때 매번 번거롭게 Promise를 사용하지 않아도 되도록 해줍니다

```jsx
async function someFunction() { // Promise 객체를 반환합ㄴ니다
  return 1;
}

someFunction().then(alert); // 1

=> 다음과 동일합니다

function someFunction() {
	return new Promise((resolve) => {
		resolve(1);
	});
}

someFunction().then(alert); // 1
```

### await

`await` 를 사용하면 프라미스가 처리될 때까지 기다립니다. 그리고 프라미스의 결과값이 반환됩니다. 이를 통해 Promise.then 을 사용하지 않고 마치 동기함수인 것처럼 코드를 작성할 수 있습니다

await 를 사용하려면 항상 async 함수 안에서만 사용이 가능합니다

에러 핸들링은 일반적인 try..catch 구문을 사용하면 됩니다

```jsx
async function showAvatar() {
	try {
	  // JSON 읽기
	  let response = await fetch('/article/promise-chaining/user.json');
	  let user = await response.json();
	
	  // github 사용자 정보 읽기
	  let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
	  let githubUser = await githubResponse.json();
	
	  // 아바타 보여주기
	  let img = document.createElement('img');
	  img.src = githubUser.avatar_url;
	  img.className = "promise-avatar-example";
	  document.body.append(img);
	
	  // 3초 대기
	  await new Promise((resolve, reject) => setTimeout(resolve, 3000));
	
	  img.remove();
	
	  return githubUser;

	} catch (err) {
		alert(err.message);
	}
}

showAvatar();
```