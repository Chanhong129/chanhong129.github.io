---
title: 프로그램 소개, ComAnalyzer
date: 2025-05-02 14:00:00 +0900
categories: [Programming, Software Introduction]
tags: [Program, freeware,comanalyzer, serial, tcp, test]
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> RS232 / TCP Server / TCP Client 를 사용하는 장치나 다른 프로그램과 통신할 때, 정상 동작하고 있는지 확인하기 위해서는 통신 테스트용 프로그램이 필요합니다. 프로그램을 직접 구현하고 원하는 기능을 추가 하는 방법도 있지만 많은 사람들이 수년 간 사용한 무료 프로그램을 사용하는 방법이 도 쉽고 간단합니다.

## ComAnalyzer
이 프로그램은 위 내용을 충족하는 간단하고 강력한 프로그램 입니다.   
2002년 대학생이 제작한 이후 추가 업데이트는 되고 있지 않지만 현재 버전으로도 사용하기에 충분한 기능들을 가지고 있습니다. 수년 간 사용해본 결과 가장 편리하다고 생각되는 프로그램입니다.

[ComAnalyzer 소개하는 블로그 및 다운로드](https://jeong-f.tistory.com/43)   
![ComAnalyzer](https://i.ibb.co/pvypgD5z/image.png)


## 지원하는 통신 목록
- COM(RS-232C)
- TCP/IP 서버 및 클라이어트
- UDP
- IPX 서버 및 클라이언트

이 중 가장 많이 사용하는 것은 COM(RS-232C) 와 TCP/IP 서버 및 클라이언트이다.

## 통신을 위해 지원하는 기능
1. [하단] Sum Check, CRC-16 Check, 바이너리 ↔ 아스키코드 변환
2. [우측] RX(수신) 및 TX(송신) 의 아시크 또는 바이너리 표시 기능

## TCP/IP 통신 사용하기
 - **통신 설정** - **TCP/IP (SERVER)**
 - **통신 설정** - **TCP/IP (CLIENT)**

IP, Port 르 입력하고 확인을 누르면 통신 상태 색상으로 (**빨강:끊김** / **초록:연결**) 연결 상태를 확인 할 수 있다.

## COM(RS-232C) 사용하기
- **통신 설정** - **COM (RS-232C)**

COM, Baud rate, Stop bit, data bit, parity 설정 후 통신이 가능 하다.

> 이 프로그램으로 초기 통신 테스트를 할 때나 프로젝트 작성 중 어떤 문제로 통신이 되지 않을 때 확인 용으로 유용하게 사용하고 있다.   
> **간단한 통신 테스트**가 필요하다면 **Com Analyzer** 를 사용해 보자
