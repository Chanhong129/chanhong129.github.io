---
title: Geometry 분석
date: 2025-05-10 09:00:00 +0900
categories: [Programming, WPF]
tags: [wpf, geometry]
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## ```Geometry Data``` 란?
```Path``` 나 ```Geometry```에서 쓰는 ```Data="..."``` 문자열은 **도형의 모양을 수학적으로표현한 경로(Path)**입니다.   
SVG의 ```<path d="...">``` 의 ```d``` 속성과 동일하며, XAML의 ```Path.Data```에서도 그대로 쓰임.

## 기본 명령어 구조
Geometry Data는 다음과 같은 **문자 + 숫자들**로 구성돼 있다.

|**명령어**|**의미**|**사용 예시**|
|--|--|--|
|```M```|Move to (이동)|```M 10,20``` → (10,20)으로 펜 위치 이동|
|```L```|Line to (직선 그리기)|```L 50,50``` → 현재 위치에서 (50,50)까지 선|
|```H```|Horizontal line|```H 100``` → x좌표 100 까지 수평선|
|```V```|Vertical line|```V 100``` → y좌표 100까지 수직선|
|```C```|Cubic Bezier curve|```C x1,y1 x2,y2 x,y```|
|```S```|Smooth cubic Bezier|```S x2,y2 x,y```|
|```Q```|Quadratic Bezier|```Q x1,y1 x,y```|
|```T```|Smooth quadratic Bezier|```T x,y```|
|```A```|Arc (타원 호 그리기)|```A rx,ry x-axis-rotation large-arc-flag sweep-flag x,y```|
|```Z```|Close path|시작점과 연결해 경로 닫기|

> 대문자: 절대좌표 / 소문자: 상대좌표 (예: ```M``` vs ```m```)

## 예시 분석
### 예시 1:

```cs
<Path Data="M 10,10 L 100,10 L 100,100 L 10,100 Z" />
```

**해석:**   
1. ```M 10,10``` → (10,10)으로 이동 (펜을 들어서 이동)
2. ```L 100,10``` → 직선으로 (100,10)까지
3. ```L 100,100``` → 직선으로 (100,100)까지
4. ```L 10,100``` → 직선으로 (10,100)까지
5. ```Z``` → 시작점 (10,10)까지 닫기 (사각형 완성)

즉, **사각형 하나를 정의하는 Path**야.

### 예시 2: (SVG 아이콘에서 자주 나오는 형태)

```cs
<Path Data="M12,2C6.48,2 2,6.48 2,12s4.48,10 10,10 10-4.48 10-10S17.52,2 12,2Z"/>
```

**해석:**   
1. ```M12,2``` → (12,2)로 이동
2. ```C6.48,2 2,6.48 2,12``` → 큐빅 베지어 커브(왼쪽 반원)
3. ```s4.48,10 10,10``` → 이전 커브에서 이어지는 오른쪽 반원
4. ```10-4.48 10-10``` → 아래쪽 반커브
5. ```S17.52,2 12,2``` → 시작점으로 커브 복귀
6. ```Z``` → 닫기

결국 이건 **원을 곡선으로 그린 Path**야. 아이콘이나 버튼 배경에 자주 쓰임.

## 실습용 Path 예제

```cs
<Path Stroke="Black" Fill="SkyBlue" StrokeThickness="1"
      Data="M 50,0 L 100,100 L 0,100 Z" />
```

**→ 정삼각형을 위로 향하기 그리는 예시**

## 마무리
- ```Geometry Data```는 **SVG 도형의 핵심 코드**로 WPF 에서 아이콘 UI 를 만드는 데 유용함
- 복잡한 Path도 대부분 ```M```, ```L```, ```C```, ```Z``` 의 조합으로 되어 있음
- 도구를 활용하면 SVG → XAML로 손쉽게 변환 가능