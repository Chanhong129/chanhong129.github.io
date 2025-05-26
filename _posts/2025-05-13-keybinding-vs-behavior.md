---
title: WPF MVVM 에서 KeyBinding vs Behavior 완전 비교
date: 2025-05-13 10:00:00 +0900
categories: [Programming, WPF]
tags: [c#, keybinding, mousebinding, mvvm]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

WPF 앱을 MVVM 구조로 개발할 때, 사용자 입력(키보드/마우스)을 ViewModel 에서 처리하려면 어떻게 해야 할까?   
이번 포스팅에서는   
**KeyBinding / MouseBinding 만으로 가능한 작업**과   
**EventArgs 를 받기 위해 반드시 Behavior 가 필요한 상황**을 명확하게 비교해 보여드립니다.

## 1. KeyBinding & MouseBinding 만으로 가능한 것들
### 예제: Ctrl+S 누르면 저장

```cs
<Window.InputBindings>
    <KeyBinding Gesture="Ctrl+S" Command="{Binding SaveCommand}" />
</Window.InputBindings>
```

ViewModel:

```cs
public ICommand SaveCommand => new RelayCommand(() => Save());

private void Save()
{
    // 저장 로직
}
```

### 예제: 마우스 더블클릭

```cs
<Window.InputBindings>
    <MouseBinding MouseAction="LeftDoubleClick"
                  Command="{Binding DoubleClickCommand}" />
</Window.InputBindings>
```

ViewModel:

```cs
public ICommand DoubleClickCommand => new RelayCommand(() =>
{
    // 더블클릭 처리
});
```

> 간단한 입력 처리라면 이 방법이 **가볍고 MVVM 에 적합**합니다.

## 그런데 문제는 ?
KeyBinding 이나 MouseBinding 은 **이벤트 정보(EventArgs)를 받을 수 없습니다.   
예를 들어:   
- 어떤 **키를 눌렀는지** 알고 싶다.
- 마우스의 **클릭 좌표**가 필요하다.
- **마우스 우클릭인지 좌클릭인지** 구분하고 싶다.

**→ 불가능합니다.**

## 2. Behavior를 써야할 때: 이벤트 인자 필요
이럴 땐 ```Microsoft.Xaml.Behaviors.Wpf```를 설치해서   
```EventTriggerBehavior``` + ```InvokeCommandAction``` 을 써야 해요.

```
Install-Package Microsoft.Xaml.Behaviors.Wpf
```

### 예제: Enter 키를 눌렀을 때만 처리

```cs
<TextBox>
    <i:Interaction.Behaviors>
        <ei:EventTriggerBehavior EventName="KeyDown">
            <ei:InvokeCommandAction Command="{Binding KeyPressedCommand}" />
        </ei:EventTriggerBehavior>
    </i:Interaction.Behaviors>
</TextBox>
```

네임스페이스:

```cs
xmlns:i="http://schemas.microsoft.com/xaml/behaviors"
xmlns:ei="http://schemas.microsoft.com/expression/2010/interactions"
```

ViewModel:

```cs
public ICommand KeyPressedCommand => new RelayCommand<KeyEventArgs>(e =>
{
    if (e.Key == Key.Enter)
    {
        // 엔터 입력 처리
    }
});
```

### 예제: 마우스 클릭 좌표 알아내기

```cs
<Border Background="LightGray">
    <i:Interaction.Behaviors>
        <ei:EventTriggerBehavior EventName="MouseDown">
            <ei:InvokeCommandAction Command="{Binding MouseClickedCommand}" />
        </ei:EventTriggerBehavior>
    </i:Interaction.Behaviors>
</Border>
```

```cs
public ICommand MouseClickedCommand => new RelayCommand<MouseButtonEventArgs>(e =>
{
    Point point = e.GetPosition(null);
    // 클릭 좌표 사용 가능
});
```

## KeyBinding vs Behavior 비교 정리

|**항목**|**KeyBinding / MouseBinding**|**Behavior + EventTrigger**|
|--|--|--|
|MVVM 구조 지원|✅|✅|
|Command 바인딩|✅|✅|
|단축키 처리|✅|✅|
|마우스 클릭 구분(좌/우)|❌|✅|
|좌표/Key 정보 필요|❌|✅ (```EventArgs``` 전달됨)|
|ViewModel에서 ```KeyEventArgs``` 접근|❌|✅|
|외부 패키지 필요|❌|✅ (```Microsoft.Xaml.Behaviors.Wpf```|

## 마무리
- 단순한 입력 처리 (단축키, 간단 클릭)는 ```KeyBinding```, ```MouseBinding```이면 충분
- 이벤트 세부 정보가 필요하다면 **Behavior를 써야 MVVM 유지하면서 처리 가능**
- 두 방법은 **서로 대체제가 아니라 목적에 따라 병행 사용**하는게 가장 좋음