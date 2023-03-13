---
layout: default
title: 모듈
parent: TypeScript
nav_order: 22
---
# 모듈

타입스크립트에서는 코드 작성시 모듈 시스템으로 `commonjs`와 `es module` 모두 사용 가능합니다.

esm 으로 작성했더라도 컴파일시 cjs로 변환이 가능합니다. 하지만 cjs를 esm으로 변환은 불가하기 때문에 가급적 esm 방식으로 코드를 작성하는 것이 좋습니다

이외에도 esm으로 작성된 코드를 AMD, UMD 등의 모듈 시스템으로 변환이 가능하며 이는 컴파일 설정 중 `module` 속성을 통해 설정할 수 있습니다

# 다른 모듈 시스템으로 변환 예시

다음은 작성자가 작성한 원본 코드입니다

```tsx
// SimpleModule.ts
import m from "mod";
export let t = m.something + 1
```

이를 다음과 같이 다양한 모듈 시스템으로 컴파일시 변환이 가능합니다

```tsx
// AMD / RequireJS SimpleModule.js 
define(["require", "exports", "./mod"], function (require, exports, mod_1) {
  exports.t = mod_1.something + 1;
});
// CommonJS / Node SimpleModule.js
var mod_1 = require("./mod");
exports.t = mod_1.something + 1;
// UMD SimpleModule.js
(function (factory) {
  if (typeof module === "object" && typeof module.exports === "object") {
    var v = factory(require, exports); if (v !== undefined) module.exports = v;
  }
  else if (typeof define === "function" && define.amd) {
    define(["require", "exports", "./mod"], factory);
  }
})(function (require, exports) {
  var mod_1 = require("./mod");
  exports.t = mod_1.something + 1;
});
// System SimpleModule.js
System.register(["./mod"], function(exports_1) {
  var mod_1;
  var t;
  return {
    setters:[
      function (mod_1_1) {
        mod_1 = mod_1_1;
      }],
    execute: function() {
      exports_1("t", t = mod_1.something + 1);
    }
  }
});
```