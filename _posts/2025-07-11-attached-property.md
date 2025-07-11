---
title: WPF Attached Property 사용하기
date: 2025-07-11 12:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, attached property]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## WPF Attached Property 란?

WPF 의 **Attached Property**는 **다른 객체의 속성처럼 사용할 수 있도록 종속 속성(DependencyProperty)** 입니다.   
주로 **레이아웃 정보 전달**이나 **행동 주입(Behavior)** 등에 사용 됩니다.

```cs
<Grid>
    <Button Grid.Row="1" Grid.Column="2" Content="Click me!" />
</Grid>
```

위 코드에서 ```Grid.Row```, ```Grid.Column```이 바로 대표적인 **Attached Property** 입니다.   
```Button```은 ```Row``` 속성을 직접 가지지 않지만, ```Grid```가 제공한 속성을 "첨부" 받아 사용합니다.

## 언제 쓰나?

|**사용 시기**|**예시**|
|--|--|
|특정 컨트롤이 다른 컨트롤의 속성을 사용할 때|```Grid.Row```,```Canvas.Left```|
|Behavior 나 Interaction 주입할 때|```ScrollViewer.HorizontalScrollBarVisibility```|
|MVVM에서 ViewModel 바인딩 외 기능 부여|커스텀 속성 활용(예: ```WatermarkHelper.Text```|

## Attached Property 만들기

```cs
public class MyAttachedProperty
{
    public static bool GetMyProperty(DependencyObject obj)
    {
        return (bool)obj.GetValue(MyPropertyProperty);
    }

    public static void SetMyProperty(DependencyObject obj, bool value)
    {
        obj.SetValue(MyPropertyProperty, value);
    }

    // Using a DependencyProperty as the backing store for MyProperty.  This enables animation, styling, binding, etc...
    public static readonly DependencyProperty MyPropertyProperty =
        DependencyProperty.RegisterAttached("MyProperty", typeof(bool), typeof(MyAttachedProperty), new PropertyMetadata(false, OnChanged));

    private static void OnChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
    {
        var control = d as Button;
        if (control != null)
        {
            if ((bool)e.NewValue)
            {
                control.Background = System.Windows.Media.Brushes.LightGreen;
            }
            else
            {
                control.Background = System.Windows.Media.Brushes.LightCoral;
            }
        }
        else
        {
            throw new InvalidOperationException("MyAttachedProperty can only be used on Controls.");
        }

    }
}
```

```propa``` 코드 조각을 통해 자동 완성으로 보다 쉽게 위 코드를 작성할 수 있다.   
MyProperty 를 일반 컨트롤에 추가 할 수 있고 그 값이 변경되면 배경색이 바뀌는 아주 간단한 코드이다.

## 사용 예시 (XAML)

```cs
	<Button local:MyAttachedProperty.MyProperty="True"
			Height="50"/>
```

위와 같이 사용하면 ```MyProperty``` 값 설정에 따라서 버튼 배경 색상이 변경되는 것을 확인할 수 있다.

## 활용 사례
 - **유효성 검사 결과를 스타일로 표시**
 - **마우스 오버/포커스 등 동작 제어**
 - **디자인 타임 기능 주입**
 - **Behavior 처럼 비즈니스 로직 주입**
 
## 주의할 점
  - Attached Property 는 **속성의 소유 클래스가 아니더라도 다른 컨트롤에 사용 가능**합니다.
  성능 이슈는 거의 없지만, **불필요하게 많이 쓰면 가독성이 떨어질 수 있습니다.
  
## 마무리
Attached Property 는 WPF 의 강력한 기능 중 하나로,   
**XAML 에서 더 유연한 레이아웃 구성과 동작 확장을 가능하게** 해줍니다.   
```DependencyProperty```를 이해했다면, ```AttachedProperty```는 확장된 버전이라 볼 수 있습니다.   
MVVM 에서도 뷰 단의 추가 정보를 표현하는 데 유용하니 꼭 익혀두세요!

