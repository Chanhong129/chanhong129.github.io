---
title: WPF DataTemplateSelector 완전 정복 - 조건에 따라 다른 UI 그리기
date: 2025-05-19 10:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, datatemplateselector]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

WPF에서는 ```DataTemplate``` 을 사용하여 View 와 ViewModel을 깔끔하게 분리할 수 있습니다. 그런데 하나의 리스트에 **다양한 형태의 데이터**가 들어오는 경우, 어떤 템플릿을 써야할까요?   
바로 그때 사용하는 게 ```DataTemplateSelector```입니다.

## 목표
- 하나의 ListBox에 여러 타입의 아이템 표시
- 혹은 속성 값에 따라 다른 템플릿 적용

## 예제 구조

```cs
abstract class Notification { }

class TextNotification : Notification
{
    public string Message { get; set; }
}

class ImageNotification : Notification
{
    public string ImagePath { get; set; }
}
```

### 1. DataTemplateSelector 클래스 만들기

```cs
using System.Windows;
using System.Windows.Controls;

public class NotificationTemplateSelector : DataTemplateSelector
{
    public DataTemplate? TextTemplate { get; set; }
    public DataTemplate? ImageTemplate { get; set; }

    public override DataTemplate? SelectTemplate(object item, DependencyObject container)
    {
        return item switch
        {
            TextNotification => TextTemplate,
            ImageNotification => ImageTemplate,
            _ => base.SelectTemplate(item, container)
        };
    }
}
```

> 타입 기반으로 템플릿을 선택합니다.

### 2. XAML 리소스 정의

```cs
<Window.Resources>
    <!-- 각각의 템플릿 정의 -->
    <DataTemplate x:Key="TextTemplate" DataType="{x:Type local:TextNotification}">
        <TextBlock Text="{Binding Message}" Foreground="DarkBlue"/>
    </DataTemplate>

    <DataTemplate x:Key="ImageTemplate" DataType="{x:Type local:ImageNotification}">
        <StackPanel Orientation="Horizontal">
            <Image Source="{Binding ImagePath}" Width="32" Height="32"/>
            <TextBlock Text="이미지 알림!" Margin="5,0,0,0"/>
        </StackPanel>
    </DataTemplate>

    <!-- 템플릿 선택기 선언 -->
    <local:NotificationTemplateSelector x:Key="NotificationSelector"
                                        TextTemplate="{StaticResource TextTemplate}"
                                        ImageTemplate="{StaticResource ImageTemplate}"/>
</Window.Resources>
```

### 3. ListBox에 적용

```cs
<ListBox ItemsSource="{Binding Notifications}"
         ItemTemplateSelector="{StaticResource NotificationSelector}"/>
```

### 4. ViewModel 샘플

```cs
public class MainViewModel
{
    public ObservableCollection<Notification> Notifications { get; } = new()
    {
        new TextNotification { Message = "새 메일이 도착했습니다." },
        new ImageNotification { ImagePath = "/Assets/info.png" },
        new TextNotification { Message = "업데이트가 완료되었습니다." }
    };
}
```

## 결과
- ```TextNotification``` 인 경우에는 파란색 텍스트로 메시지 출력
- ```ImageNotification``` 인 경우에는 아이콘 + 안내 문구 표시

## 고급 사용 팁
- ```DataTemplateSelector``` 는 타입뿐 아니라 속성 값으로도 구분 가능
예: ```item is Notification n && n.Priority == High```
- ```ContentControl```, ```ItemsControl```, ```TreeView```, ```ListView``` 어디서든 사용 가능
- MVVM 환경에서 ViewModel을 조건 분리하지 않고 View에서 깔끔하게 처리 가능

## 마무리
```DataTemplateSelector``` 는 WPF 에서 **동적인 UI 구성**이 필요할 때 매우 강력한 도구입니다.   
뷰모델을 수정하지 않고도 **다양한 시각적 표현을 유연하게 분기**할 수 있습니다.

### 이런 경우에 특히 유용:
- 채팅 앱에서 메시지 유형이 다를 때
- 파일 탐색기에서 파일, 폴더, 템플릿을 다르게 보여줄 때
- 알람 리스트에 텍스트/이미지/링크 등 다양한 형식을 보여줄 때 