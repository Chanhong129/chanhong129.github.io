---
title: Nuget Package, Dependency Injection
date: 2025-04-22 10:00:00 +0900
categories: [Programming, MVVM]
tags: [c#, wpf, mvvm, di, dependency injection]
description: Dependecy Injection
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> MVVM 패턴을 쓰면서 같이 사용하면 좋은 Nuget Package 를 소개 한다.

## Dependency Injection(DI)란?
- **객체 간 의존성을 외부에서 주입**하는 설계 패턴

## 왜 쓰나?
- **코드를 유연하게** 만들고
- **구체 클래스에 대한 의존을 줄여서** 유지보수를 쉽게 하려고.

## 장점
1. **결합도 감소**: 코드가 느슨하게 연결되어 변경에 강함
2. **테스트 편의성**: Mock 객체를 주입해서 단위 테스트 가능
3. **유지보수성 향상**: 기능 변경 시 코드 영향 최소화
4. **재사용성 증가**: 코드가 다양한 상황에 재사용 가능
5. **구성 유연성**: 어떤 구현체를 사용할지 쉽게 바꿀 수 있음

---
**특히 WPF** 에서는 MVVM 패턴을 쓸 때 ViewModel 이나 Service를 DI 로 주입하면, 코드가 깔끔하고, 테스트 작성도 쉬어지고, 유지보수도 엄청 편해짐.

### 1. Microsoft.Extensions.DependencyInjection
Nuget Package 를 설치하여 준다.
역시 마이크로소프트에서 기능을 제공한다.

### 2. 서비스 등록
**App.xaml.xs** 에서 DI 컨테이너를 설정하고, 필요한 서비스와 ViewModel 등록
```cs
public partial class App : Application
{
    public static new App Current => (App)Application.Current;
    public IServiceProvider Services { get; }

    public App()
    {
        Services = ConfigureServices();
    }

    private static IServiceProvider ConfigureServices()
    {
        var services = new ServiceCollection();

        services.AddSingleton<MainViewModel>();
        services.AddSingleton<MyService>(); // 새로 만든 클래스

        return services.BuildServiceProvider();
    }
}
```

### 3. ViewModel 주입
```cs
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        DataContext = App.Current.Services.GetService(typeof(MainViewModel));
    }
}
```

### 4. 의존성 선언
```cs
public partial class MainViewModel : ObservableObject
{
    private readonly MyService _myService;

    public MainViewModel(MyService myService)
    {
        _myService = myService;
    }
	
	// 다른 속성, RelayCommand 숨김
}
```

## 핵심 포인트 요약
- **등록**: 서비스/뷰모델 등록
- **주입**: 필요한 곳에서 DI 컨테이너를 통해 인스턴스 주입
- **생성자**: 필요한 의존성은 생성자로 받기

> 쓰면서 느낀점은
> - ViewModel 에서 다른 ViewModel 의 값을 불러올 때, 편한 점이 있다.
>  **=>** ViewModel 간 의존선 주입이 깔끔해진다.
> - 현재 프로젝트에서 어떤 서비스를 사용하고 있는지 한 눈에 볼 수 있다.
> **=>** 서비스 사용 현황이 명확하다.
>> **요약**: DI 를 쓰면 **코드 구조가 투명해지고, 협업/유지보수가 편해진다**

### 참조
[.NET 속성 주입](https://learn.microsoft.com/ko-kr/dotnet/core/extensions/dependency-injection)