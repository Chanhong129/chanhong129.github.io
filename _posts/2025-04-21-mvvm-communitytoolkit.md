---
title: Nuget Package, Community Toolkit Mvvm
date: 2025-04-21 15:00:00 +0900
categories: [Programming, MVVM]
tags: [c#, wpf, mvvm]
description: CommunityToolkit.Mvvm
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---
## Community Toolkit 설치
**Visual Studio** 에서 제공하는 강력한 기능 **Nuget Package** 에서 **CommunityToolkit.Mvvm** 을 검색하여 설치한다.

## ViewModel 수정하기
1. ViewModel 을 **ObservableObject** 클래스를 상속받는다.
 - **ObservableObject** 클래스에서 **OnPropertyChanged** 지원해주기 때문에 **INotifyPropertyChanged** 인터페이스와 관련 함수를 삭제 한다.
```cs
public class MainViewModel : ObservableObject
```

2. **ButtonText** 속성을 수정하고 **[ObservableProperty]** 특성을 붙인다.
```cs
[ObservableProperty]
private string buttonText = "Button";
```

3. 직접 만든 RelayCommand 클래스를 사용하지 않고CommunityToolkit 에서 지원해주는 **RelayCommand** 를 사용한다.
- 특성에 **[RelayCommand]** 추가
- **xaml** 에서 **ButtonClickCommand** 였다면 **Command 를 제외**한 **ButtonClick** 을 찾아 실행한다.
```cs
[RelayCommand]
private void ButtonClick()
{
    ButtonText = "Clicked!";
}
```

ViewModel 전체 코드

```cs
public partial class MainViewModel : ObservableObject
{
    [ObservableProperty]
    private string buttonText = "Button";

    public MainViewModel()
    {
        
    }

    [RelayCommand]
    private void ButtonClick()
    {
        ButtonText = "Clicked!";
    }
}
```

- **MainWindow.xaml.cs** 의 DataContext 연결은 있어야 함
- **MainWindow.xaml** 은 이전과 동일

> 어때? 엄청 간단해지지 않았어?
> 이게 마소의 힘이다...!

**ObservableObject** 와 **[ObservableProperty]** 와 **[RelayCommand]** 만 이용해도 충분히 코딩할 수 있다고 본다.
앞으로 더 궁금한 것은 찾아보면서 성장할 수 있다고 생각한다.

> 그런데 **xaml** 에서 **ViewModel** 의 속성들이 인텔리전스되지 않아서 불편하다.