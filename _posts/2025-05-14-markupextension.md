---
title: WPF 에서 MarkupExtension 을 활용한 Converter 만들기
date: 2025-05-14 10:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, converter, markupextension]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> WPF 에서는 ```IValueConverter``` 를 사용할 때 매번 리소스로 등록하고 StaticResource 또는 DynamicResource 로 참조하는 방식이 번거롭게 느껴질 수 있습니다.   
> 이럴 때 ```MarkupExtension```을 상속받아 **간편하게 XAML에서 직접 사용할 수 있는 Converter**를 만들 수 있습니다.

## 기존 방식의 단점

```cs
<Window.Resources>
    <local:BooleanToVisibilityConverter x:Key="BoolToVis"/>
</Window.Resources>

<TextBlock Visibility="{Binding IsVisible, Converter={StaticResource BoolToVis}}" />
```

하지만 리소스 등록이 반복되면 지저분해지고 유지보수가 어렵습니다.

## MarkupExtension을 활용한 Converter 만들기
```MarkupExtension 을 상속하면 이렇게 사용할 수 있습니다:

``cs
<TextBlock Visibility="{Binding IsVisible, Converter={local:BoolToVisConverter}}" />
```

### 예제 코드

```cs
public class BoolToVisConverter : MarkupExtension, IValueConverter
{
    private static BoolToVisConverter? _instance;

    public override object ProvideValue(IServiceProvider serviceProvider)
    {
        return _instance ??= new BoolToVisConverter();
    }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return value is bool b && b ? Visibility.Visible : Visibility.Collapsed;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return this;
    }
}
```

이제 별도 리소스 등록 없이 XAML 에서 직접 사용 가능:

```cs
<TextBlock Visibility="{Binding IsVisible, Converter={local:BoolToVisConverter}}" />
```

## 장점 요약
- 매번 리소스 등록할 필요 없음
- 코드 재사용성 높음 (static 인스턴스)
- 가독성 향상

## 마무리
WPF 에서는 XAML 이 길어지기 쉬운데, ```MarkupExtension``` 기반의 Converter 는 이를 깔끔하게 정리하는 좋은 방법입니다. 자주 사용하는 Converter라면 꼭 이 방법을 고려해보세요.