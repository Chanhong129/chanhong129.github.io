---
title: Mitsubishi PLC 통신 기초 (MX Component + WPF)
date: 2025-04-29 08:00:00 +0900
categories: [Programming, Communication]
tags: [c#, wpf, mitsubishi, plc]
description: GX Works2, MX Component 소개 + 통신 예제
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## 1. Mitsubishi PLC 란 ?
- Mitsubishi Electric 사의 대표적인 산업용 제어기기 (PLC: Programmable Logic Controller)
- 제조 공정 자동화, 설비 제어 등에 사용
- 주요 시리즈: FX, Q, L, iQ-R 시리즈 등
- 특징: 안정성, 다양한 통신 지원 (RS232/RS485, Ethernet, CC-Link 등)

## 2. 준비물 (사전 설치)
### 2-1. GX Works2
- Mitsubishi PLC 용 프로그램 작성 및 다운로드 소프트웨어
- PLC 프로그램 작성, 모니터링, 시뮬레이션 지원
- 설치 이유: PLC 프로그램을 만들고 시뮬레이터로 테스트하기 위함
### 2-2. Mx Component V4
- Mitsubishi 제공 통신 라이브러리 (ActiveX 기반)
- PLC 와 PC(WPF 앱) 간 통신을 쉽게 해주는 라이브러리
- 설치 이유: WPF 프로그램에서 PLC에 접속하고 데이터 읽기/쓰기를 하기 위함

> GX Works2로 PLC 프로그램을 만들고,   
> MX Component 로 WPF 프로그램과 통신하는 구조.

## 3. MxComponent V4 설정
#### 1. **Communication Setup Utility** 프로그램을 관리자 권한으로 실행한다.   
#### 2. Wizard 를 누르고 Logical station number 를 1로 설정한 후 Next 를 누릅니다.
![MxComponetV4 -1](https://i.ibb.co/1YTkYQrf/image.png)

#### 3. GX Simulator2 를 선택하고, Target Simulator 를 Simulator A 로 선택한 후 Next 를 누릅니다.
![MxComponentV4 -2](https://i.ibb.co/pjDZ3RXH/image.png)

#### 4. Finish 를 누르고 Exit 을 누릅니다.
![MxComponentV4 -3](https://i.ibb.co/qMgs2P4p/image.png)

## 4. GX Works2 시뮬레이터 실행
#### 1. GX Works2 를 실행하고 새로만들기에서 Q CPU 를 선택하고 OK 를 누릅니다.
![GX Works2 -1](https://i.ibb.co/nsz49hWH/image.png)

#### 2. 프로그램을 간단하게 작성하고(X0 ON 을 하면 Y0이 On, 단축키 F5/F7)
Debug - Start/Stop Simulation 을 선택합니다. (단축키 Alit + b + s)
![GX Works2 -2](https://i.ibb.co/CpPmPT9Q/image.png)

#### 3. when processing ends, close this window automatically 를 체크합니다.
![GX Works2 -3](https://i.ibb.co/sv1yqj3F/image.png)

## 5. WPF 와 Mitsubishi PLC 연동하기
간단한 프로그램 작성을 위하여 Winform 방식으로 작성합니다.

### 5-1. 프로젝트 준비
- WPF(.NET 6 이상) 프로젝트 생성
- MX Component 설치 후, 필요한 DLL 참조 추가
	- **ActUtlType.dll**
		- Mx Component 기본 통신 라이브러리
		- **PLC 직접 접속 (Open/Close/GetDevce/SetDevice 등)** 기능 담당

### 5-2. MainWindow.xaml 작성

```cs
<Window x:Class="MitsubishiPlcDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Mitsubishi PLC Demo" Height="250" Width="300">
    <StackPanel Margin="20">
        <Button Name="ConnectButton" Content="Connect" Click="ConnectButton_Click" Margin="0,10"/>
        <Button Name="ReadButton" Content="Read D100" Click="ReadButton_Click" Margin="0,10"/>
        <Button Name="WriteButton" Content="Write D100 = 1234" Click="WriteButton_Click" Margin="0,10"/>
        <TextBlock Name="ResultTextBlock" Margin="0,20,0,0" FontSize="16" TextAlignment="Center"/>
    </StackPanel>
</Window>
```

### 5-3. MainWindow.xaml.cs 작성

```
using ACTMULTILib;
using System.Windows;

namespace MitsubishiPlcDemo
{
    public partial class MainWindow : Window
    {
        private ActUtlType plc;

        public MainWindow()
        {
            InitializeComponent();
            plc = new ActUtlType();
        }

        private void ConnectButton_Click(object sender, RoutedEventArgs e)
        {
            plc.ActLogicalStationNumber = 1; // Logical Station No.
            int result = plc.Open();
            if (result == 0)
            {
                MessageBox.Show("PLC 연결 성공!");
            }
            else
            {
                MessageBox.Show($"PLC 연결 실패! 에러코드: {result}");
            }
        }

        private void ReadButton_Click(object sender, RoutedEventArgs e)
        {
            int value;
            int result = plc.GetDevice("Y0", out value);
            if (result == 0)
            {
                ResultTextBlock.Text = $"Y0: {value}";
            }
            else
            {
                MessageBox.Show($"읽기 실패! 에러코드: {result}");
            }
        }

        private void WriteButton_Click(object sender, RoutedEventArgs e)
        {
            int result = plc.SetDevice("X0", 1);
            if (result == 0)
            {
                MessageBox.Show("X0에 1 성공적으로 씀!");
            }
            else
            {
                MessageBox.Show($"쓰기 실패! 에러코드: {result}");
            }
        }
    }
}
```