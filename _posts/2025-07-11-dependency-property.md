---
title: WPF Dependency Property 사용하기
date: 2025-07-11 10:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, dependency property]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---
## WPF DependencyProperty 란?
WPF 에서 속성은 일반 C# 속성과 달리, 특별한 시스템에서 동작하는 ```DependencyProperty```**(종속 속성)** 을 많이 사용합니다.   
예를 들어 ```TextBox.Text```, ```Button.Width```, ```Opacity``` 같은 속성은 전부 ```DependencyProperty``` 입니다.

## 왜 필요할까?
WPF는 단순 UI 라이브러리가 아니라 **데이터 바인딩, 스타일, 애니메이션, 트리거, 디자인 타임 속성 처리** 등 다양한 기능을 제공합니다.   
하지만 일반 C# 속성(```public string Text { get; set; }```)만으로는 아래 기능들을 쓸 수 없습니다.

|**기능**|**일반 속성**|**DependencyProperty**|
|--|--|--|
|데이터 바인딩 (Binding)|❌|✅|
|스타일(Style), 템플릿(Template)|❌|✅|
|애니메이션|❌|✅|
|속성 값 유효성 검증|❌|✅|
|값 상속 (Value Inheritance)|❌|✅|
|메모리 절약 (PropertyStorage 최적화)|❌|✅|

## DependencyProperty 선언 방법
 - **Step 1. 클래스는 ```DependencyObject```를 상속**
 
 ```cs
 public class MyControl : DependencyObject
 ```
 
 - **Step 2. DependencyProperty 등록**
 
 ```cs
 public static readonly DependencyProperty MyTextProperty =
    DependencyProperty.Register(
        "MyText",                   // 속성명
        typeof(string),             // 타입
        typeof(MyControl),          // 소유자 클래스
        new PropertyMetadata("기본값")); // 초기값 등 옵션

 ```
 
 - **Step 3. 래퍼 속성 만들기 (C# 스타일 접근용)
 
 ```cs
 public string MyText
{
    get => (string)GetValue(MyTextProperty);
    set => SetValue(MyTextProperty, value);
}
 ```
 
 ## 사용 예시
 
 ```cs
 <MyControl local:MyControl.MyText="Hello World" />
 ```
 
 ## 속성 변경 시 콜백 받기
 
 ```cs
 new PropertyMetadata("기본값", OnMyTextChanged)

private static void OnMyTextChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
{
    string oldVal = (string)e.OldValue;
    string newVal = (string)e.NewValue;
    Debug.WriteLine($"값 변경됨: {oldVal} → {newVal}");
}
 ```

## 실수 주의

| 실수                                | 설명                       |
| --------------------------------- | ------------------------ |
| `SetValue(...)` 없이 필드에 직접 값 할당    | ❌ 작동 안 함                 |
| 래퍼 속성 없이 `DependencyProperty`만 선언 | 불편함                      |
| 일반 클래스에서 정의                       | `DependencyObject` 상속 필수 |

## 일반 속성과 차이점 요약


| 항목          | 일반 속성       | DependencyProperty              |
| ----------- | ----------- | ------------------------------- |
| 선언 방식       | `get; set;` | `Register()` + `Get/SetValue()` |
| 바인딩         | ❌           | ✅                               |
| 스타일, 트리거 적용 | ❌           | ✅                               |
| 런타임 값 변경 추적 | ❌           | ✅                               |
| 리소스 효율성     | 보통          | 매우 효율적                          |


## 마무리
 - WPF 의 거의 모든 컨트롤 속성은 ```DependencyProperty``` 입니다.
 - MVVM, 바인딩, 스타일 등을 활용하려면 반드시 익혀야 할 개념입니다.
 - 처음에는 복잡해 보여도, 실제로는 **패턴이 정해져 있어서 금방 익숙해집니다.**