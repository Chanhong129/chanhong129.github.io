---
title: WPF 에서 Scrollbar 꾸미기
date: 2025-10-30 13:00:00 +0900
categories: [Windows, Etc]
tags: [wpf, scrollbar, style]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> WPF 에서 Scrollbar Style 을 꾸미는 법을 간단하게 알아보겠습니다.

## 1. Scrollbar Style 비교
### Default Scrollbar Style
 
 ![Default Scrollbar](https://i.ibb.co/whLzVd89/image.png)
 
### Chrome Scrollbar Style
 
 ![Chrome Scrollbar](https://i.ibb.co/LhRRw83M/image.png)
 
## 2. Scrollbar 구조

 Scrollbar 의 구조는 Vertical 기준 **3행**으로 구성되어있다.
 
 - **1행**에는 **Lineup Button** 으로 `RepeatButton` 컨트롤을 사용한다.
 
 - **2행**에는 `Track` 컨트롤이 이 들어가며 Track 안에 `DecreaseRepeatButton / Thumb / IncreaseRepaetButton` 을 사용한다.
 
 - **3행**에는 **LineDown Button** 으로 `RepeatButton` 컨트롤을 사용한다.
 
 > `RepeatButton`**(Lineup)**   
 > `RepeatButton`**(Pageup)**   
 > `Thumb`**(스크롤 네모)**   
 > `RepaetButton`**(PageDown)**   
 > `RepeatButton`**(LineDown)**   
 
 으로 구성되어있다고 생각해도 될 것 같다.

 
## 3. Scrollbar Style Code
 
 ```csharp
<Style TargetType="{x:Type ScrollBar}" x:Key="ChromeScrollbarStyle">
    <Style.Triggers> 
        <Trigger Property="Orientation" Value="Vertical"> 
            <Setter Property="Width" Value="18" /> 
            <Setter Property="Height" Value="Auto" /> 
        </Trigger> 
    </Style.Triggers> 
    <Setter Property="Background" Value="#FCFCFC"/> 
    <Setter Property="Template"> 
        <Setter.Value> 
            <ControlTemplate TargetType="{x:Type ScrollBar}"> 
                <Grid Background="LightGray"> 
                    <Grid.RowDefinitions> 
                        <RowDefinition MaxHeight="{DynamicResource {x:Static SystemParameters.VerticalScrollBarButtonHeightKey}}" /> 
                        <RowDefinition Height="0.00001*" /> 
                        <RowDefinition MaxHeight="{DynamicResource {x:Static SystemParameters.VerticalScrollBarButtonHeightKey}}" /> 
                    </Grid.RowDefinitions> 
                    <RepeatButton Content="▲"  
                              Command="ScrollBar.LineUpCommand" > 
                        <RepeatButton.Style> 
                            <Style TargetType="{x:Type RepeatButton}"> 
                                <Setter Property="Focusable" Value="False" /> 
                                <Setter Property="TextElement.Foreground" Value="#8B8B8B" /> 
                                <Setter Property="Template"> 
                                    <Setter.Value> 
                                        <ControlTemplate TargetType="{x:Type RepeatButton}"> 
                                            <Border Background="#FCFCFC" 
                                                BorderBrush="Black" 
                                                BorderThickness="0"> 
                                                <ContentPresenter HorizontalAlignment="Center" 
                                                               VerticalAlignment="Center" /> 
                                            </Border> 
                                        </ControlTemplate> 
                                    </Setter.Value> 
                                </Setter> 
                            </Style> 
                        </RepeatButton.Style> 
                    </RepeatButton> 
                    <Track Grid.Row="1" 
                       x:Name="PART_Track" 
                       IsDirectionReversed="True"> 
                        <Track.DecreaseRepeatButton> 
                            <RepeatButton Command="ScrollBar.PageUpCommand"> 
                                <RepeatButton.Style> 
                                    <Style TargetType="{x:Type RepeatButton}"> 
                                        <Setter Property="Focusable" Value="False" /> 
                                        <Setter Property="Template"> 
                                            <Setter.Value> 
                                                <ControlTemplate TargetType="{x:Type RepeatButton}"> 
                                                    <Border Background="#FCFCFC" /> 
                                                </ControlTemplate> 
                                            </Setter.Value> 
                                        </Setter> 
                                    </Style> 
                                </RepeatButton.Style> 
                            </RepeatButton> 
                        </Track.DecreaseRepeatButton> 
 
                        <Track.Thumb> 
                            <Thumb> 
                                <Thumb.Style> 
                                    <Style TargetType="{x:Type Thumb}"> 
                                        <Setter Property="Template"> 
                                            <Setter.Value> 
                                                <ControlTemplate TargetType="{x:Type Thumb}"> 
                                                    <Grid Background="#FCFCFC"> 
                                                        <Border BorderThickness="{TemplateBinding BorderThickness}" 
                                                            Margin="4 0" 
                                                            Background="#8B8B8B" 
                                                            CornerRadius="5"> 
 
                                                        </Border> 
                                                    </Grid> 
                                                </ControlTemplate> 
                                            </Setter.Value> 
                                        </Setter> 
                                    </Style> 
                                </Thumb.Style> 
                            </Thumb> 
                        </Track.Thumb> 
 
                        <Track.IncreaseRepeatButton> 
                            <RepeatButton Command="ScrollBar.PageDownCommand"> 
                                <RepeatButton.Style> 
                                    <Style TargetType="{x:Type RepeatButton}"> 
                                        <Setter Property="Focusable" Value="False" /> 
                                        <Setter Property="Template"> 
                                            <Setter.Value> 
                                                <ControlTemplate TargetType="{x:Type RepeatButton}"> 
                                                    <Border Background="#FCFCFC" /> 
                                                </ControlTemplate> 
                                            </Setter.Value> 
                                        </Setter> 
                                    </Style> 
                                </RepeatButton.Style> 
                            </RepeatButton> 
                        </Track.IncreaseRepeatButton> 
                    </Track> 
                    <RepeatButton Grid.Row="2" 
                              Content="▼" 
                              Command="ScrollBar.LineDownCommand" > 
                        <RepeatButton.Style> 
                            <Style TargetType="{x:Type RepeatButton}"> 
                                <Setter Property="Focusable" Value="False" /> 
                                <Setter Property="TextElement.Foreground" Value="#8B8B8B" /> 
                                <Setter Property="Template"> 
                                    <Setter.Value> 
                                        <ControlTemplate TargetType="{x:Type RepeatButton}"> 
                                            <Border Background="#FCFCFC" 
                                                BorderBrush="Black" 
                                                BorderThickness="0"> 
                                                <ContentPresenter HorizontalAlignment="Center" 
                                                               VerticalAlignment="Center" /> 
                                            </Border> 
                                        </ControlTemplate> 
                                    </Setter.Value> 
                                </Setter> 
                            </Style> 
                        </RepeatButton.Style> 
                    </RepeatButton> 
                </Grid> 
            </ControlTemplate> 
        </Setter.Value> 
    </Setter> 
</Style> 
 ```


