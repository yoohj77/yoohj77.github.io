---
layout: default
title: 프로젝트(모듈,d.ts파일)
parent: TypeScript
nav_order: 23
---
# d.ts 파일

d.ts 파일은 기본적으로 타입스크립트가 사용할 수 있는 타입을 선언해 놓은 파일입니다. 이는 기본적으로 두 가지 역할로 나뉩니다

- 전역에서 사용할 수 있는 변수 및 기능에 대한 타입 선언
- 라이브러리(모듈)에서 사용할 수 있는 변수 및 기능에 대한 타입 선언

# 전역 타입 선언

다음과 같은 코드에서 document.URL에 커서를 올리고 F12키를 누르면 `lib.dom.d.ts` 파일로 이동됩니다. 이 파일은 브라우저 환경에서 DOM과 관련하여 전역에서 사용할 수 있는 전역 변수와 전역 함수에 대해 타입을 선언해 놓았습니다. 타입스크립트는 기본설정으로 이 파일을 로드하여 전역에서 document 같은 변수를 사용할 수 있도록 지원합니다

```tsx
console.log(document.URL);
// 📁 lib.dom.d.ts

/** Any web page loaded in the browser and serves as an entry point into the web page's content, which is the DOM tree. */
interface Document extends Node, DocumentAndElementEventHandlers, DocumentOrShadowRoot, FontFaceSource, GlobalEventHandlers, NonElementParentNode, ParentNode, XPathEvaluatorBase {
    /** Sets or gets the URL for the current document. */
    readonly URL: string;
	
		//...
}

declare var document: Document;
```

이외에도 전역에서 사용할 수 있는 변수나 함수가 추가적으로 있다면 직접 d.ts 파일을 생성하여 tsconfig.json을 통해 설정해 놓으면 해당 변수, 함수를 전역에서 사용할 수 있게 됩니다

# 라이브러리 타입 선언

라이브러리는 일반적으로 js파일로 구성되어 있습니다. 그렇기 때문에 타입스크립트에서 라이브러리를 import하면 어떠한 타입도 알 수가 없습니다. 그래서 라이브러리에서 사용할 수 있는 변수 또는 함수의 타입을 정의한 d.ts 파일을 제공합니다

다음과 같이 gulp 라이브러리를 설치합니다. gulp는 빌드 자동화 툴입니다

```tsx
> npm install gulp
```

그리고 gulp를 import 하면 빨간 줄이 생기는데 확인해 보면 선언 파일을 찾을 수 없다는 오류 메세지가 뜹니다. 이는 기본적으로 gulp가 d.ts 파일을 포함하고 있지 않기 때문에 발생하는 오류입니다.

```tsx
import gulp from "gulp";
```

이러한 선언 파일을 제공하지 않는 라이브러리들을 위해 선언파일만 제공하는 @types 라이브러리가 있습니다. 오류 메세지에도 관련 내용이 나와 있습니다. @types/gulp 를 설치합니다

```tsx
> npm i --save-dev @types/gulp
```

설치를 하고 나면 빨간 줄이 사라집니다. 그리고 gulp에 커서를 올리고 F12를 눌러 코드를 따라 올라가면 방금 설치한 gulp의 d.ts 파일을 확인할 수 있습니다



```tsx
// 📁 node_modules/@types/gulp/index.d.ts

declare namespace GulpClient {
    type Globs = string | string[];

    type TaskFunction = Undertaker.TaskFunction;

    /**
     * @deprecated - Now use `TaskFunction`.
     */
    type TaskCallback = TaskFunction;

    type TaskFunctionCallback = Undertaker.TaskCallback;

    interface Gulp extends Undertaker {
        /**
         * Emits files matching provided glob or array of globs. Returns a stream of Vinyl files that can be piped to plugins.
         * @param globs Glob or array of globs to read.
         * @param options Options to pass to node-glob through glob-stream.
         */
        src: SrcMethod;

        /**
         * Can be piped to and it will write files. Re-emits all data passed to it so you can pipe to multiple folders.
         * Folders that don't exist will be created.
         * @param path The path (output folder) to write files to. Or a function that returns it, the function will be provided a vinyl File instance.
         */
        dest: DestMethod;

        /**
         * Functions exactly like gulp.dest, but will create symlinks instead of copying a directory.
         * @param folder A folder path or a function that receives in a file and returns a folder path.
         */
        symlink: typeof vfs.symlink;

        /**
         * Takes a path string, an array of path strings, a glob string or an array of glob strings as globs to watch on the filesystem.
         * Also optionally takes options to configure the watcher and a fn to execute when a file changes.
         * @globs A path string, an array of path strings, a glob string or an array of glob strings that indicate which files to watch for changes.
         * @opts Options that are passed to chokidar.
         * @fn Once async completion is signalled, if another run is queued, it will be executed.
         */
        watch: WatchMethod;
    }

		...
}
```