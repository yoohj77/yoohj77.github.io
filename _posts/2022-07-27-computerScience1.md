---
title : 컴퓨터동작원리
date : 2022-07-27 00:00:00 -09:00
categories : [computerscience, SUB_CATEGORIE]
tags : [computerscience] #소문자만 가능
---

> # 컴퓨터 과학 :
  	
## 1. 릴레이(계전기)
### 코일에 전류를 흘리면 자석이 되는 성질을 이용한 기능
### 릴레이의 기능
#### 	1) 신호의 증폭 
####	2) 신호의 변환
####	3) 신호의 반전
####	4) 메모리

![](https://velog.velcdn.com/images/yoohj77/post/6fa77cdf-0b35-4f3e-b2b9-b193a987d54e/image.gif)

## 2. 논리연산
### 위의 릴레이를 이용하여, 복합연산을 구현할 수 있다.

#### 1) AND 연산
![](https://velog.velcdn.com/images/yoohj77/post/0478cdc4-a6e6-44ec-89c8-de3d78ce3001/image.PNG)

A와 B가 모두 닫혀있을때 전기가 흐르게 되는 원리
A의 스위치가 닫히고, B의 스위치가 닫혀있어야, 전기가 흐를 수 있다.

#### 2) OR연산
![](https://velog.velcdn.com/images/yoohj77/post/0a1069ba-6311-48a6-8449-4e0cc335c896/image.PNG)

A또는 B의 스위치가 닫혀있을때 전기가 흐르는 구조

#### 3) XOR연산
![](https://velog.velcdn.com/images/yoohj77/post/754accc7-94b4-41e8-9129-3da7aea0365e/image.PNG)

A와 B가 같지 않을경우에만 전기가 흐르는 구조

> and연산과 or연산 xor연산을 이용하여 사칙연산이 가능해졌고,
> 이러한 계산기능을 가지고 있는 것이 바로 CPU이다.


## 3.기억과 저장
### 1) 래치 : 입력을 가지지 않는 기억소자
### 2) 플립플롭 : 클럭 입력을 가져 클럭 입력에 반응하여 출력의 상태를 바꾸는 기억소자

![](https://velog.velcdn.com/images/yoohj77/post/79d19503-6731-4eea-a3e8-c65351bf3cb8/image.gif)

전기신호의 되먹임(feedback)이 발생하여 이전 상태가 유지된다.

