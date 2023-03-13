---
layout: default
title: 기본정리
parent: ModuleBundler(WebPack)
nav_order: 8
---

# 기본 정리

웹팩의 동작을 다음과 같이 정리할 수 있습니다

1. **Entry** 속성은 웹팩을 실행할 대상 파일. 진입점
2. **Output** 속성은 웹팩의 결과물에 대한 정보를 입력하는 속성. 일반적으로 `filename`과 `path`를 정의
3. **Loader** 속성은 CSS, 이미지와 같은 비 자바스크립트 파일을 웹팩이 인식할 수 있게 추가하는 속성. 로더는 오른쪽에서 왼쪽 순으로 적용
4. **Plugin** 속성은 웹팩으로 변환한 파일에 추가적인 기능을 더하고 싶을 때 사용하는 속성. 웹팩 변환 과정 전반에 대한 제어권을 갖고 있음

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cc03aeda-8f5f-4459-9c5b-1f74b0df264a/Untitled.png)