---
title: C# Socket vs TcpClient/TcpListener - 뭐가 다를까?
date: 2025-04-30 10:10:00 +0900
categories: [Programming, Communication]
tags: [c#, wpf, tcp/ip, socket, tcp, tcpclient, tcplistener]
description: Socket vs TcpClient/TcpListener
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> .NET에서 TCP 통신을 구현할 때, 우리는 크게 두 가지 방법을 선택할 수 있습니다.   
> - ```Socket``` 클래스 (저수준, 유연함)
> - ```TcpClient``` / ```TcpListener``` 클래스 (고수준, 간단함)    
> 이번 글에서는 두 방식의 차이점을 **기능, 코드 구조, 사용 목적** 중심으로 비교해보겠습니다.

## 공통점
- 둘 다 **TCP 통신을 위한 클래스**
- 내부적으로 ``Socket``을 사용
- 모두 ```System.Net.Sockets``` 네임스페이스에 포함

## 주요 차이점

|**항목**|**Socket**|**TcpClient / TcpListener**|
|--|--|--|
|추상화 수준|저수준 (Low-level)|고수준 (High-level)|
|코드량|많음|적음|
|사용편의성|복잡함|간편함|
|유연성|매우 높음|제한적|
|지원 프로토콜|TCP / UDP 모두 가능|TCP 전용|
|다중 클라이언트 처리|직접 구현해야 함 (멀티 쓰레드 등)|기본적 단일, 추가 구현 필요|
|설정 제어|SocketOption, KeepAlive 등 다양|제한적|
|대표 사용처|게임 서버, 커스텀 프로토콜, 성능 튜닝|간단한 클라이언트/서버, 내부 도구|

## 언제 무엇을 쓰면 좋을까?

|**상황**|**추천 방식**|
|--|--|
|간단한 TCP 채팅, 도구 개발|TcpClient / TcpListener|
|성능 튜닝 필요, 대규모 서버|Socket|
|UDP 통신 필요|Socket (UDP 지원)|
|빠르게 테스트용 서버 구성|TcpClient / TcpListener|
|커스텀 통신 프로토콜 사용|Socket|

## 코드 비교 예시

**TcpClient 방식**

```cs
TcpClient client = new TcpClient("127.0.0.1", 9000);
NetworkStream stream = client.GetStream();
```


**Socket 방식**

```cs
Socket socket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
socket.Connect("127.0.0.1", 9000);
```

## 마무리
- 초바자나 빠른 프로토타이핑 목적이라면 **TcpClient / TcpListener** 가 훨씬 적합합니다.
- 성능, 확장성, 커스터마이징이 필요하다면 **Socket 클래스**를 직접 사용하는 것이 유리합니다.
- 두 방식 모두 장단점이 뚜렷하므로 **목적에 따라 선택하는 것**이 가장 중요합니다.