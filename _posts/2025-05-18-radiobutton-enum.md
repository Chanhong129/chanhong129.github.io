---
title: WPF 에서 RadioButton 을 Enum 값으로 깔끔하게 바인딩하기 (MVVM 방식)
date: 2025-05-18 10:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, radiobutton, enum, mvvm, binding]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

WPF에서 ```RadioButton``` 여러 개를 만들고, 선택된 항목을 ```ViewModel``` 에서 관리하고 싶을 때, 보통 ```bool``` 바인딩을 각각 하게 됩니다. 하지만 이보다는 ```Enum```을 사용해서 하나의 속성으로 관리하는 것이 훨씬 깔끔하고 유지보수에도 유리합니다.   
   
이 글에서는 ```Enum```과 ```IValueConverter```를 이용해 ```RadioButton``` 여러 개를 하나의 ```Enum``` 속성으로 바인딩하는 방법을 소개합니다.

## 목표
- ```RadioButton``` 여러 개를 ```enum``` 값으로 연결
- 선택된 항목을 ViewModel 의 하나의 ```SelectedOption``` 속성으로 바인딩

### 1. Enum 정의

```cs
public enum Mode
{
    Easy,
    Normal,
    Hard
}
```

### 2. ViewModel 구성

```cs
public class MainViewModel : ObservableObject
{
    private Mode _selectedMode;
    public Mode SelectedMode
    {
        get => _selectedMode;
        set => SetProperty(ref _selectedMode, value);
    }

    public MainViewModel()
    {
        SelectedMode = Mode.Normal;
    }
}
```

> ```ObservableObject``` 는 MVVM Toolkit의 클래스 입니다. ```INotifyPropertyChanged``` 구현 포함.

### 3. Enum → bool 컨버터 만들기

```cs
using System;
using System.Globalization;
using System.Windows.Data;
using System.Windows.Markup;

public class EnumEqualsConverter : MarkupExtension, IValueConverter
{
    public object EnumValue { get; set; } = default!;

    public EnumEqualsConverter() { }

    public EnumEqualsConverter(object enumValue)
    {
        EnumValue = enumValue;
    }

    public override object ProvideValue(IServiceProvider serviceProvider)
    {
        return this;
    }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return Equals(value, EnumValue);
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (value as bool?) == true ? EnumValue : Binding.DoNothing;
    }
}

```

> ```EnumValue``` 는 ```object```로 선언되어 있어 어떤 enum 타입이든 재사용 가능합니다.

### 4. XAML에서 RadioButton 바인딩

```cs
<Window ...
        xmlns:local="clr-namespace:YourAppNamespace"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">

<StackPanel>
    <RadioButton Content="쉬움"
                 IsChecked="{Binding SelectedMode, Converter={local:EnumEqualsConverter EnumValue={x:Static local:Mode.Easy}}}" />

    <RadioButton Content="보통"
                 IsChecked="{Binding SelectedMode, Converter={local:EnumEqualsConverter EnumValue={x:Static local:Mode.Normal}}}" />

    <RadioButton Content="어려움"
                 IsChecked="{Binding SelectedMode, Converter={local:EnumEqualsConverter EnumValue={x:Static local:Mode.Hard}}}" />
</StackPanel>
```

## 장점 요약

|**항목**|**설명**|
|--|--|
|타입 안정성|EnumValue에 실제enum 값을 넘기므로 오타 위험 없음|
|코드 재사용성|어떤 enum 타입이든 범용적으로 사용 가능|
|XAML 간결성|ConveterParameter 없이 바로 enum 값 사용|
|MVVM 친화적|ViewModel과 뷰가 깔끔하게 분리됨|

## 마무리
이 방식은 라디오 버튼 그룹을 enum 값으로 관리할 때 가장 깔끔하고 안전한 방법 중 하나입니다.   
특히 여러 enum을 사용하는 프로젝트에서 하나의 ```EnumEqualsConverter```로 **모든 라디오 바인딩을 통일**할 수 있습니다.    