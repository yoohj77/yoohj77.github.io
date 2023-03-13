---
layout: default
title: Tailwind CSS 기본
parent: css
nav_order: 2
---

# Tailwind CSS 기본

# 개발 환경 설치하기

### VSCode 확장 프로그램 설치

`Tailwind CSS IntelliSense` 확장 프로그램을 설치합니다. Tailwind CSS 클래스가 가이드 및 자동완성됩니다

### tailwindcss 설치 및 설정 파일 생성

일단은 tailwind css CDN을 사용할 예정이라 tailwindcss를 설치하거나 설정할 필요가 없지만 위에서 설치한 확장 프로그램의 intellisense가 동작하려면 설치와 설정이 필요합니다

```tsx
> npm install tailwindcss
```

tailwindcss 설정파일을 다음의 명령으로 생성합니다

```tsx
> npx tailwindcss init
```

생성된 설정 파일에서 `content` 에 tailwindcss를 사용할 파일을 설정합니다

```tsx
// 📁 tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./*.html"],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

### tailwind css CDN 사용하기

`index.html` 을 생성하고 tailwind css CDN 스크립트 태그를 작성합니다

```tsx
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <script src="<https://cdn.tailwindcss.com>"></script>
  </head>
  <body>
    <h1>Hello Tailwind CSS</h1>
  </body>
</html>
```

# Sizing

```tsx
<h1>Sizing</h1>
<div>
  <div class="w-96 h-10 bg-slate-300">w-96</div>
  <div class="w-80 h-10 bg-slate-400">w-80</div>
  <div class="w-40 h-10 bg-slate-500">w-40</div>
  <div class="w-1/4 h-10 bg-slate-300">w-1/4</div>
  <div class="w-2/4 h-10 bg-slate-400">w-2/4</div>
  <div class="w-3/4 h-10 bg-slate-500">w-3/4</div>
</div>
```

# Color

```tsx
<h1>Color</h1>
<div>
  <div class="w-40 h-10 bg-orange-400 text-blue-800">color1</div>
  <div class="w-40 h-10 bg-lime-200 text-red-700">color1</div>
</div>
```

# Typography

```tsx
<h1>Typography</h1>
<div>
  <p class="text-2xl font-mono">Hello Tailwind CSS!</p>
  <p class="text-sm font-serif font-bold">this is typography</p>
</div>
```

# Spacing

```tsx
<h1>Margin & Padding</h1>
<div>
  <div class="w-40 p-5 bg-emerald-300">p-5</div>
  <div class="w-40 h-10 m-5 bg-emerald-400">m-5</div>
</div>
```

# Shadow

```tsx
<h1>Shadow</h1>
<div class="bg-amber-200 p-5">
  <div class="w-40 h-10 bg-white shadow">shadow</div>
  <div class="w-40 h-10 bg-white shadow-lg mt-4">shadow-lg</div>
</div>
```

# List

```tsx
<h1>List</h1>
<ul class="list-disc list-inside">
  <li>item1</li>
  <li>item2</li>
</ul>
<ul class="list-decimal list-inside">
  <li>item1</li>
  <li>item2</li>
</ul>
```

# Round

```tsx
<h1>Round</h1>
<div>
  <div class="w-20 h-20 bg-slate-300 m-4 rounded"></div>
  <div class="w-20 h-20 bg-slate-400 m-4 rounded-full"></div>
</div>
```

# Border

```tsx
<h1>Border</h1>
<div>
  <div class="w-40 h-10 m-4 border-4 border-red-400"></div>
</div>
```

# Custom **Style**