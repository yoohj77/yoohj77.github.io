---
layout: default
title: TCP란?
nav_order: 6
parent: ComputerScience
---

# TCP란?
> 전송 제어 프로토콜(Transmission Control Protocol, TCP)은 인터넷 프로토콜 스위트(IP)의 핵심 프로토콜 중 하나로, IP와 함께 TCP/IP라는 명칭으로도 널리 불린다.
TCP는 근거리 통신망이나 인트라넷, 인터넷에 연결된 컴퓨터에서 실행되는 프로그램간의 데이터를 안정적으로, 순서대로, 에러없이 교환할 수 있게 한다.

## 특징
> 패킷 교환 방식 네트워크에서 패킷(데이터의 조각)들이 안전하게 이동할 수 있도록 보장해주는 프로토콜이다.

### 1. 흐름제어
>** 송신측과 수신측의 데이터 처리 속도 차이를 해결하기 위한 기법**

수신측이 너무 많은 패킷을 수신받지 않도록 하기 위함.

＊ 수신측에는 패킷을 수신받는 버퍼의 크기가 정해져 있는데, 
＊ 송신측의 전송속도가 너무 빨라 한번에 수 많은 패킷을 수신받는다면, 버퍼가 가득차 손실되는 패킷들이 발생
＊ 송신측의 처리 속도가 더 빠를 경우 이를 제어할 수단이 필요함.

#### 흐름제어 방법 1)Stop-And-Wait

>매번 전송한 패킷에 대한 확인 응답을 받아야만 그 다음 패킷을 전송하는 방법
![](https://velog.velcdn.com/images/yoohj77/post/93164af9-4445-4b1e-ad67-b31d4b6b2521/image.PNG)


#### 흐름제어 방법 2)Sliding Window(Go-Back-n ARQ)

>수신측에서 설정한 윈도우 크기만큼 송신측에서 패킷 각각에 대한 확인 응답 없이 세그먼트를 전송하게 하고, 데이터 흐름을 동적으로 조절하는 기법
![](https://velog.velcdn.com/images/yoohj77/post/5fcf3a46-d3ee-4120-a8b7-ad8267d9b0ed/image.PNG)

1. 초기화 : 3Way Handsahking을 통해 수신 측 윈도우 크기로 설정되며, 수신측에 버퍼에 남아있는 공간에 따라 변한다. 윈도우 크기는 수신측에서 송신측으로 확인응답(ACK)를 보낼때 TCP헤더에 담아서 보낸다.

2. 동작방식 : 윈도우에 포함된 패킷을 계속 전송하고, 수신 측으로부터 확인응답(ACK)가 오면 윈도우를 옆으로 옮겨 다음 패킷들을 전송한다.

3. 재전송 : 소신측은 일정 시간 동안 수신 측으로부터 확인응답(ACK)가 없으면, 패킷을 재전송한다. 만약 송신측에서 재전송을 했는데 소실된 경우가 아니라 수신측에 버퍼에 남는 공간이 없는 경우면 문제가 생긴다. 이를 해결하기 위해 송신 측은 해결응답(ACK)를 보내면서 남은 버퍼의 크기를 함께 보낸다.


### 2. 혼잡제어

#### AIMD(Additive Increase/Multicative Decrease)


> 처음에 패킷을 하나씩 보내고 문제가 없으면 윈도우의 크기를 1씩 증가시켜 전송
만약, 문제가 생기면 윈도우의 크기를 반으로 줄인다.
윈도우의 크기를 너무 조금씩 늘리기 때문에 네트워크의 모든 대역을 활용하여 제대로된 속도로 통신하기 까지 시간이 오래걸리는 단점이 있다.


#### Slow Start(느린시작)
> AIMD방식은 윈도우의 크기를 선형적으로 증가시키기 때문에 제대로된 속도를 내기까지 시간이 오래걸린다.
slow Start는 윈도우의크기를 1,2,4,8과 같이 지수적으로 증가시키다가 혼잡이 감지되면 윈도우의 크기를 1로 줄이는 방식이다.
이 방식은 보낸 데이터릐 ACK가 도착할때마다 윈도우 크기를 증가시키기 때문에 처음에는 윈도우의 크기가 조금 느리가 증가할 지라도, 시간이 지나면 갈수록 윈도우의 크기가 점점 빠르게 증가하는 장점이 있다.

### Fast Retransmit(빠른재전송)
> 패킷을 받는 수신자 입장에서는 세그먼트로 분할된 내용들이 순서대로 도착하지 않는 경우가 생길 수 있다.
이런 상황이 발생했을때 수신측에서는 순서대로 잘 도착한 마지막 패킷의 다음 순번을 ACK패킷에 실어서 보낸다. 그리고 이런 중복 ACK를 3개 받으면 재전송이 이루어진다.
송신측인 자신이 설정한 타임아웃 시간이 지나지 않았어도 바로 해당 패킷을 재전송할 수 있기 때문에 보다 빠른 재전송을 유지할 수 있다.


### Fast Recovery(빠른회복)
> 빠른 회복은 혼잡한 상태가 되면 윈도우 크기를 1로 줄이지 않고 반으로 줄이고 선형증가시키는방법이다. 이 방법을 적용하면 혼잡상황을 한 번 겪고 나서부터는 AIMD방식으로 동작한다.

### 혼잡 제어 정책

> Tahoe / Reno : 기본적으로 처음에는 Slow Start 방식을 사용하다가 네트워크가 혼잡하다고 느낄때에는 AIMD방식으로 전환하는 방법을 사용하는 정책


> TCP Tahoe
![](https://velog.velcdn.com/images/yoohj77/post/43f5a45e-5794-4133-ac84-adfd6b9fa194/image.PNG)
Slow Start를 사용하여 자신의 윈도우 크기를 지수적으로 빠르게 증가시키다가
ACK Duplicated, Timeout이 발생하면, 네트워크 혼잡이 발생했다고 판단하고 자신의 윈도우 크기를 1로 수정한다

>TCP Reno
![](https://velog.velcdn.com/images/yoohj77/post/b82b763a-a812-4f8e-a75b-3f9da752adc4/image.PNG)
Tahoe와 같이 ACK Duplicated, Time Out이 발생하면 자신의 윈도우 크기를 줄이는데,
duplicated일때는 반으로 줄이고, TimeOut일때에는 1로 줄이는 방식

### 3. 오류제어

1) Stop And Wait
흐름제어 방식과 동일하게 송신측에서 패킷을 보내고 보낸 패킷에 대한 응답이 오면 다음 패킷을 보내는 방식
오류가 발생한다면, 송신한 패킷을 재전송하는 방식
![](https://velog.velcdn.com/images/yoohj77/post/23e19162-d44e-43d2-8cdd-bbc9d707808e/image.PNG)


2) ARQ(Automatic Repeat Request)

재전송을 최대한 적게하는 방식 중 하나로 Go Back N과 Selective Repeat이 있다.

Go Back N
![](https://velog.velcdn.com/images/yoohj77/post/33901fce-a316-41f0-a5c6-da9bca41bb6c/image.PNG)

오류가 발생한 데이터부터 다시보내는 방식
이미 전송한 데이터를 다시 보내야 하는 비효율


Selecttive Repeat
![](https://velog.velcdn.com/images/yoohj77/post/81de9ff9-b4e8-4e94-9481-4ae0331729ee/image.PNG)

오류가 발생한 패킷만 재전송하는 방식
단점 : 수신측의 버퍼에 순서가 보장되지 않는 단점
 => 이를 해결하기 위해 수신 버퍼에 대한 재정렬이 필요하고, 또 다른 버퍼 공간을 필요로 한다는 단점이 있다.