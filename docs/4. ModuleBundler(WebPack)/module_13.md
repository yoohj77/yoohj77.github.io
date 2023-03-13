---
layout: default
title: 트리 쉐이킹(Tree Shaking)
parent: ModuleBundler(WebPack)
nav_order: 12
---

# 트리 쉐이킹(Tree Shaking)

tree shaking은 말 그대로 나무 흔들기인데 나무를 흔들어서 죽은 잎사귀를 떨어뜨리는 행위를 말합니다. 이를 통해 사용되지 않는 코드는 번들링 과정에서 제외할 수 있습니다

`rollup` 번들러에서 최초 사용되었으며 웹팩에서도 이 기능을 사용할 수 있습니다.