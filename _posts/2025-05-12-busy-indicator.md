---
title: Nuget Package, Busy Indicator
date: 2025-05-12 10:00:00 +0900
categories: [Programming, Nuget Package]
tags: [c#, nuget, busyindicator, loading]
description: Project Structure
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## WPF 에서 BusyIndicator - 가볍고 심플한 로딩 컨트롤 소개 (by MOh3nGolshani)
WPF 앱을 만들다 보면 비동기 작업 중 사용자가 조작하지 못하게 막고, **"로딩 중입니다"** 같은 인디케이터를 띄워야 할 때가 많습니다.

## 1. 설치
NuGet에서 바로 설치할 수 있습니다.

```
Install-Package BusyIndicator
```

또는 패키지 관리 UI에서 "BusyIndicator" 검색 후 설치하세요.

## 2. 사용법
[https://github.com/Moh3nGolshani/BusyIndicator](https://github.com/Moh3nGolshani/BusyIndicator) 에 잘 설명 되어있다.

1. App.xaml 에 다음 추가

```cs
<Application.Resources>
     <ResourceDictionary Source="pack://application:,,,/BusyIndicator;component/Theme/Default.xaml"/>
</Application.Resources>
```

2. xaml 에 namespace 추가

```cs
xmlns:busyIndicator="https://github.com/moh3ngolshani/BusyIndicator"
```

3. 사용

```cs
<busyIndicator:BusyMask x:Name="BusyIndicator" 
                        IsBusy="False" 
                        IndicatorType="Dashes" 
                        BusyContent="Please wait..." 
                        BusyContentMargin="0,20,0,0" 
                        IsBusyAtStartup="False" >
         
         
          <... MAIN VIEW GOES HERE... >
         
         
</busyIndicator:BusyMask>
```

IndicatorType 의 변화에 따라서 애니메이션이 변화한다.

- 인디케이터 사이즈 변환 하는 법
1. xaml 에서 namespace 추가

```cs
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

2. Override ```IndicatorScaleX``` & ```IndicatorScaleY``` 속성

```cs
<Window.Resources>
    <sys:Double x:Key="IndicatorScaleX" >2</sys:Double>
    <sys:Double x:Key="IndicatorScaleY" >2</sys:Double>
</Window.Resources>
```

- 인디케이터 색상 변경 하는 법
1. Override ```IndecatorForeground``` & ```IndicatorBackground``` 속성

```cs
<Window.Resources>
    <SolidColorBrush x:Key="IndicatorForeground" Color="Orange" />
    <SolidColorBrush x:Key="IndicatorBackground" Color="WhiteSmoke" />
</Window.Resources>
```

2. Gradient 또한 가능

```cs
<LinearGradientBrush x:Key="IndicatorForeground" StartPoint="0.5,0" EndPoint="0.5,1">
    <GradientStop Offset="1" Color="#eaafc8" />
    <GradientStop Offset="0" Color="#654ea3" />
</LinearGradientBrush>
```
