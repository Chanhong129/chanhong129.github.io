---
title: WPF, Window Chrome Style
date: 2025-05-10 09:00:00 +0900
categories: [Programming, WPF]
tags: [wpf, window, chrome, style]
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## WPF 에서 커스텀 윈도우 스타일 만들기 (Window Chrome)
기본 WPF Window 는 타이틀바와 경계선(Border)이 시스템 스타일로 정해져 있습니다. 하지만 **더 자유로운 디자인**, 예를 들어 카카오톡이나 [VS Code]처럼 커스텀 된 UI를 만들고 싶다면?   
바로 ```WindowChrome``` 을 사용해 기본 스타일을 제거하고 원하는 모습으로 변경할 수 있습니다.

## 기본 구조
### 타이틀바 제거하기

```cs
<Window
    x:Class="MyApp.MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="clr-namespace:MyApp"
    xmlns:shell="clr-namespace:Microsoft.Windows.Shell;assembly=PresentationFramework"
    WindowStyle="None"
    AllowsTransparency="False"
    ResizeMode="CanResize"
    Title="Custom Window"
    Height="450" Width="800">
```

- ```WindowStyle="None"```: 기본 타이틀바 제거
- ```ResizeMode="CanResize"```:창 크기 조절 가능
- ```AllowTransparency="False"```: 이걸 ```True```로 하면 그림자도 사라짐 (성능 이슈 주의)

### WindowChrome 적용하기
XAML 에서 ```WindowChrome```을 설정해주면 **창 가장자리 드래그, 그림자, 리사이즈 등** 시스템 동작을 유지한 채 UI만 커스터마이징할 수 있습니다.

```cs
<Window.Resources>
    <Style TargetType="Window">
        <Setter Property="shell:WindowChrome.WindowChrome">
            <Setter.Value>
                <shell:WindowChrome
                    CaptionHeight="40"
                    CornerRadius="10"
                    GlassFrameThickness="0"
                    ResizeBorderThickness="16"/>
            </Setter.Value>
        </Setter>
    </Style>
</Window.Resources>
```

- ```CaptionHeight```: 타이틀바 영역으로 간주되는 높이
- ```CornerRadius```: 창의 모서리 둥글기
- ```ResizeBorderThickness```:창 크기 조절 가능한 가장자리 두께
- ```GlassFrameThickness```: 글래스(투명) 영역 설정

> 참고: 위 설정은 ```PresentationFramework.dll```의 ```Microsoft.Windows.Shell``` 네임스페이스를 사용해야 하며, ```WindowChrome```은 ```.Net Core```에서는 기본 제공되지 않아 WIndows Community Toolkit 등을 사용하거나 직접 스타일을 구성해야 할 수 있습니다.

### 그림자 적용

```cs
    <Border Margin="10"
            CornerRadius="10"
            Background="Transparent">

        <Border.Effect>
            <DropShadowEffect BlurRadius="10"
                              ShadowDepth="4"
                              Opacity="0.5"/>
        </Border.Effect>

        <Border Background="White"
                BorderThickness="2"
                BorderBrush="#B6B6B6"
                CornerRadius="10">
            <Grid Background="Transparent">
                <!--Content-->
            </Grid>
        </Border>
    </Border>
```

### 타이틀바 꾸미기

Control Box 에 사용될 Icon

```
🗕  🗖 🗗 🗙
```

ControlBox Button Template 을 생성.

```cs
    <Style TargetType="{x:Type Button}" x:Key="ControlBoxButtonStyle">
        <Setter Property="Width" Value="40"/>
        <Setter Property="Cursor" Value="Hand"/>
        <Setter Property="Background" Value="Transparent"/>
        <Setter Property="WindowChrome.IsHitTestVisibleInChrome" Value="True"/>
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="{x:Type Button}">
                    <Border x:Name="border"
                        BorderThickness="0"
                        Background="{TemplateBinding Background}">
                        <TextBlock Text="{TemplateBinding Content}"
                               VerticalAlignment="Center"
                               HorizontalAlignment="Center"
                               TextAlignment="Center"/>
                    </Border>
                    <ControlTemplate.Triggers>
                        <Trigger Property="IsMouseOver" Value="True">
                            <Setter Property="Background" Value="#EFEFEF"/>
                        </Trigger>
                        <MultiTrigger>
                            <MultiTrigger.Conditions>
                                <Condition Property="IsMouseOver" Value="True"/>
                                <Condition Property="Content" Value="🗙"/>
                            </MultiTrigger.Conditions>
                            <Setter TargetName="border" Property="CornerRadius" Value="0 10 0 0"/>
                            <Setter Property="Background" Value="#FF0000"/>
                        </MultiTrigger>
                    </ControlTemplate.Triggers>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
```

Grid 에 적용한 모습

```cs
    <Grid Grid.Row="0">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="Auto"/>
            <ColumnDefinition Width="Auto"/>
            <ColumnDefinition Width="Auto"/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Column="0"
               Text="Window Chorme Style Test"
               VerticalAlignment="Center"
               Margin="10 0 0 0"/>
        <Button Grid.Column="1"
             Style="{StaticResource ControlBoxButtonStyle}"
             Content="🗕" 
             Click="MinimizeButton_Click"/>
        <Button x:Name="xMaximizeButton"
             Grid.Column="2"
             Style="{StaticResource ControlBoxButtonStyle}"
             Content="🗖"
             Click="MaximizeButton_Click"/>
        <Button Grid.Column="3"
             Style="{StaticResource ControlBoxButtonStyle}"
             Content="🗙"
             Click="ExitButton_Click"/>
    </Grid>
```

### 경계선 만들기

```cs
    <Border Grid.Row="1" Height="6" BorderThickness="0 0.2 0 0">
        <Border.BorderBrush>
            <LinearGradientBrush StartPoint="0,0" EndPoint="1, 0">
                <GradientStop Color="#FFFFFF" Offset="0.0" />
                <GradientStop Color="#686868" Offset="0.5" />
                <GradientStop Color="#FFFFFF" Offset="1.0" />
            </LinearGradientBrush>
        </Border.BorderBrush>

        <Border.Background>
            <LinearGradientBrush StartPoint="0,0" EndPoint="0, 1">
                <GradientStop Color="#FAFAFA" Offset="0.0" />
                <GradientStop Color="#FFFFFF" Offset="1.0" />
            </LinearGradientBrush>
        </Border.Background>
    </Border>
```

![Window Chrom Style Image](https://i.ibb.co/3yfCgB90/image.png)

### ControlBox 기능 만들기
```MainWindow.xaml.cs``` 를 수정.

```cs
        public MainWindow()
        {
            InitializeComponent();

            MaxHeight = SystemParameters.WorkArea.Height + 7;
            StateChanged += MainWindow_StateChanged;
        }

        private void MainWindow_StateChanged(object? sender, EventArgs e)
        {
            if (WindowState == WindowState.Maximized)
            {
                xMaximizeButton.Content = "🗗";
                xOuterBorder.Margin = new Thickness(7, 0, 7, 0);
                xOuterBorder.CornerRadius = new CornerRadius(0);
                xInnerBoder.CornerRadius = new CornerRadius(0);

                xWindowChomre.ResizeBorderThickness = new Thickness(0);
            }
            else
            {
                xMaximizeButton.Content = "🗖";
                xOuterBorder.Margin = new Thickness(10);
                xOuterBorder.CornerRadius = new CornerRadius(10);
                xInnerBoder.CornerRadius = new CornerRadius(10);

                xWindowChomre.ResizeBorderThickness = new Thickness(16);
            }

     private void MinimizeButton_Click(object sender, RoutedEventArgs e)
        {
            WindowState = WindowState.Minimized;
        }
        private void MaximizeButton_Click(object sender, RoutedEventArgs e)
        {
            if (WindowState == WindowState.Maximized)
            {
                WindowState = WindowState.Normal;
            }
            else
            {
                WindowState = WindowState.Maximized;
            }
        }
        private void ExitButton_Click(object sender, RoutedEventArgs e)
        {
            Application.Current.Shutdown();
        }
        private void MinimizeButton_Click(object sender, RoutedEventArgs e)
        {
            WindowState = WindowState.Minimized;
        }
        private void MaximizeButton_Click(object sender, RoutedEventArgs e)
        {
            if (WindowState == WindowState.Maximized)
            {
                xMaximizeButton.Content = "🗖";
                WindowState = WindowState.Normal;
            }
            else
            {
                xMaximizeButton.Content = "🗗";
                WindowState = WindowState.Maximized;
            }
        }
        private void ExitButton_Click(object sender, RoutedEventArgs e)
        {
            Application.Current.Shutdown();
        }
```
