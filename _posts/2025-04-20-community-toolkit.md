---
title: WPF 에서 사용하는 MVVM (Community Toolkit)
date: 2025-04-20 10:00:00 +0900
categories: [Programming, WPF]
tags: [wpf, mvvm, communitytoolkit]
description: CommunityToolkit.Mvvm
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---
> WPF 프로젝트를 사용한다면 MVVM 을 한 번쯤은 들어보지 않았을까?

## MVVM 패턴이란?
> **MVVM** 은 **Model - View - ViewModel** 의 약자
> **UI 코드와 비즈니스 로직을 깔끔하게 분리**하기 위해 만들어진 소프트웨어 디자인 패턴

|구성 요소|역할|
|--|--|
|Model|데이터와 비즈니스 로직 담당. 데이터베이스, API 통신 등 실제 '정보'를 다루는 부분|
|View|화면에 보이는 부분. (XAML 파일 등) UI 만 신경 씀. 로직을 몰라야 함.|
|ViewModel|View 와 Model 을 연결하는 중간다리. 데이터 바인딩, 커맨트 처리 등을 맡음.|

## 왜 MVVM을 쓰냐?
|이유|설명|
|--|--|
|코드 깔끔|UI 코드와 로직 코드가 분리되어 유지보수가 쉬워짐
|테스트 용이|ViewModel 은 UI 없이 단독 테스트 가능|
|재사용성|ViewModel 은 여러 View 에서 재사용 가능|
|생산성 향상|바인딩만 설정하면, 값 변경이 UI에 자동 반영|

## MVVM 기본 문제

MVVM 패턴 자체는 좋은 구조지만, 직접 구현하면 반복 작업이 많다.
- ```INotifyPropertyChanged``` 매번 수동으로 작성해야함
- 커맨드(ICommand)도 매번 새로 만들어야 함
- ViewModel 생성자에 Depedency Injection 을 붙일 때 코드가 복잡해짐
**=> 매번 똑같은 코드 반복, 귀찮고 실수하기 쉬움**

## Community Tookit 은 이걸 "자동화" 해줌
C# 은 Nuget Pacakge 가 정말 편리한데, 역시 MVVM 패턴을 편리하게 사용하게 해주는 Nuget Package 도 존재한다. 여러가지가 있지만 그 중 나는 Community Toolkit 을 사용한다.

**CommunityToolkit.Mvvm** 은 마이크로소프트 공식 라이브러리 라서 믿고 사용한다.

주요 기능:
|기능|설명|
|--|--|
|```ObservableObject```|````INotifyPropertyChanged``` 를 자동 처리|
|```[ObservableProperty]```|필드만 만들면 자동으로 프로퍼티 생성|
|```RelayCommand```|커맨들 만들 때 ICommand 직접 구현 필요 없음|
|```ObservableValidator```|Validation (유효성 검사) 지원)|
|Dependency Injection 지원|ViewModel 생성자 주입을 간편하게 처리|

> 그래서 Community Toolkit MVVM 이 뭐라고...?