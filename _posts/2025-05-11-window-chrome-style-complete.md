---
title: WPF, Window Chrome Style
date: 2025-05-10 13:00:00 +0900
categories: [Programming, WPF]
tags: [wpf, window, chrome, style]
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> 제가 사용하고 있는 WindowChome Style 전체 코드를 공유합니다.   
이 스타일을 Template 으로 만들어 사용하면 빠르고 예쁜 윈도우 사용이 가능합니다.

## 1. MainWindow.xaml

```cs
<Window x:Class="WpfMyTemplate1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfMyTemplate1"
        
        xmlns:cv="clr-namespace:WpfMyTemplate1.Converters"
        
        WindowStyle="None"
        AllowsTransparency="True"
        Background="Transparent"
        
        mc:Ignorable="d"
        Title="{Binding Title}" Height="450" Width="800">

    <Window.Resources>
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
    </Window.Resources>

    <WindowChrome.WindowChrome>
        <WindowChrome x:Name="xWindowChomre"
                      CaptionHeight="40"
                      GlassFrameThickness="0"
                      ResizeBorderThickness="16"/>
    </WindowChrome.WindowChrome>

    <Border x:Name="xOuterBorder"
            Margin="10"
            CornerRadius="10"
            Background="Transparent">

        <Border.Effect>
            <DropShadowEffect BlurRadius="10"
                              ShadowDepth="4"
                              Opacity="0.5"/>
        </Border.Effect>

        <Border x:Name="xInnerBoder"
                Background="White"
                BorderThickness="2"
                BorderBrush="#B6B6B6"
                CornerRadius="10">
            <Grid Background="Transparent">
                <Grid.RowDefinitions>
                    <RowDefinition Height="40"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="*"/>
                </Grid.RowDefinitions>

                <Grid.Clip>
                    <RectangleGeometry RadiusX="{Binding Path=CornerRadius, ElementName=xInnerBoder, Converter={cv:GridRaidusClipConverter}}"
                                       RadiusY="{Binding Path=CornerRadius, ElementName=xInnerBoder, Converter={cv:GridRaidusClipConverter}}">
                        <RectangleGeometry.Rect>
                            <MultiBinding Converter="{cv:GridClipConverter}">
                                <Binding Path="ActualWidth" RelativeSource="{RelativeSource AncestorType={x:Type Grid}}"/>
                                <Binding Path="ActualHeight" RelativeSource="{RelativeSource AncestorType={x:Type Grid}}"/>
                            </MultiBinding>
                        </RectangleGeometry.Rect>
                    </RectangleGeometry>
                </Grid.Clip>

                <Grid Grid.Row="0">
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="*"/>
                        <ColumnDefinition Width="Auto"/>
                        <ColumnDefinition Width="Auto"/>
                        <ColumnDefinition Width="Auto"/>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Column="0"
                               Text="{Binding Title}"
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

                <!--Main Content-->
                <Grid Grid.Row="2">
                    
                </Grid>

            </Grid>
        </Border>
    </Border>

</Window>
```

## 2. MainWindow.xaml.cs

```cs
using System.ComponentModel;
using System.Windows;
using WpfMyTemplate1.ViewModels;

namespace WpfMyTemplate1
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
            if (DesignerProperties.GetIsInDesignMode(new DependencyObject()))
            {
                return;
            }
            DataContext = App.Current.Services.GetService(typeof(MainViewModel));

            MaxHeight = SystemParameters.WorkArea.Height + 7;
            StateChanged += MainWindow_StateChanged;
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
        }
    }
}
```

## 3. GridCipConverter.cs

```cs
using System.Globalization;
using System.Windows;
using System.Windows.Data;
using System.Windows.Markup;

namespace WpfMyTemplate1.Converters
{
    public class GridClipConverter : MarkupExtension, IMultiValueConverter
    {
        public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
        {
            if (values.Length == 2 && values[0] is double width && values[1] is double height)
            {
                return new Rect(0, 0, width, height);
            }
            return Rect.Empty;
        }

        public object[] ConvertBack(object value, Type[] targetTypes, object parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }

        public override object ProvideValue(IServiceProvider serviceProvider)
        {
            return this;
        }
    }

    public class GridRaidusClipConverter : MarkupExtension, IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            if (value is CornerRadius radius)
            {
                if (radius == new CornerRadius())
                {
                    return 0;
                }
                else
                {
                    return 10;
                }
            }
            return 10;
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }

        public override object ProvideValue(IServiceProvider serviceProvider)
        {
            return this;
        }
    }
}
```