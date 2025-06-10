---
title: WPF 에서 Behavior를 이용한 XAML 내 컨트롤 속성 변경 방법
date: 2025-05-17 10:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, behavior, xaml]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

WPF 개발 시, XAML 에서 직접 다른 컨트롤의 속성을 변경하고 싶을 때가 있습니다. 이럴 때 ```System.Windows.Interactivity``` 네임스페이스의 ```Behavior``` 를 활용하면 코드 비하인드 없이도 이러한 작업이 가능합니다.

## 목표
```Button``` 클릭 시, ```ToggleButton``` 의 ```IsChecked``` 속성을 ```False``` 로 설정하여 토글 버튼을 해제합니다.

### 구현 방법
1. **XAML 네임스페이스 선언**
먼저 XAML 상단에 ```System.Windows.Interactivity``` 네임스페이스를 선언합니다.

```cs
xmlns:i="http://schemas.microsoft.com/expression/2010/interactivity"
xmlns:ei="http://schemas.microsoft.com/expression/2010/interactions"
```

2. **ToggleButton 및 Button 정의**
```ToggleButton```과 ```Button```을 XAML에 배치합니다.

```cs
<ToggleButton x:Name="filterToggleButton" Content="필터 토글" />

<Button Content="닫기">
    <i:Interaction.Triggers>
        <i:EventTrigger EventName="Click">
            <ei:ChangePropertyAction
                TargetObject="{Binding ElementName=filterToggleButton}"
                PropertyName="IsChecked"
                Value="False" />
        </i:EventTrigger>
    </i:Interaction.Triggers>
</Button>
```

위 코드에서 ```Interaction.Triggers```를 사용하여 ```Button```의 ```Click``` 이벤트에 반응하도록 설정하고, ```ChangePropertyAction``` 을 통해 ```filterToggleButton```의 ```IsChecked``` 속성을 ```False```로 변경합니다.

## 결과
이제 "닫기" 버튼을 클릭하면 ```filterToggleButton``` 의 선택이 해제됩니다. 이러한 방식으로 XAML 내에서 직접 컨트롤 간의 상호작용을 정의할 수 있어, 코드 비하인드를 최소화하고 MVVM 패턴을 더욱 깔끔하게 유지할 수 있습니다.

## 참고 사항
- ```System.Windows.Interactivity```는 Blend SDK에 포함되어 있으며, NuGet을 통해 설치할 수 있습니다.
- ```ChangePropertyAction``` 외에도 다양한 액션을 활용하여 복잡한 상호작용을 정의할 수 있습니다. 