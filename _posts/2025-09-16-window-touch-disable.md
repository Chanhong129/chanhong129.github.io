---
title: 윈도우 환경 사용하는 터치 모니터의 제스처 끄기 + WPF 고무줄(바운스) 제거
date: 2025-09-16 08:00:00 +0900
categories: [Windows, Etc]
tags: [windows, touch, wpf, manipulation, boundary]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

현장 HMI/키오스크처럼 **사용자가 OS UI를 의도치 않게 불러오지 못하게** 하고, **WPF 스크롤의 '끝에서 흔들림'(고무줄)** 도 제거 하는 방법을 소개합니다.

## 1. Windows 11 에서 화면 가장자리 스와이프(Edge Swipe) 전부 끄기 (레지스트리)
 가장자리 스와이프(좌/우/상/하)로 **위젯, 알림센터, 시작 메뉴 등**이 튀어나오는걸 OS 수준에서 막습니다.
 
### 레지스트리 (모든 사용자/컴퓨터 전체)

```bat
:: Edge Swipe 전체 차단 (모든 화면 가장자리)
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\EdgeUI" /v AllowEdgeSwipe /t REG_DWORD /d 0 /f
```

 - `AllowEdgeSwipe = 0` → 모든 엣지 스와이프 비활성화 (좌/우/상/하). 적용 후 로그오프 또는 재부팅 권장
 
 ### (선택) 현재 사용자만
 
 ```bat
reg add "HKCU\SOFTWARE\Policies\Microsoft\Windows\EdgeUI" /v AllowEdgeSwipe /t REG_DWORD /d 0 /f
```

 - 여러 계정이 있는 장비에서는 보통 HKLM 쪽(전체적용)을 권장.
 
 ### (주의)
  - **정밀 터치패드 3/4 손가락 제스처**는 장치/드라이버(Precision Touchpad) 영역이라 **단일 표준 레스트리**가 없습니다.
  조직 배포는 보통 **설정(GUI) 비활성** 또는 OEM 유틸/그룹정책/MDM 으로 처리합니다. (예: 설정 → 블루투스 및 장치 → 터치패드/터치 → 3:4손가락 제스처 끄기)
  - 모든 제스처를 절대적으로 막아야 한다면, 최후 수단으로 **HID-compliant Touch Screen 비활성**(장치 관리자)도 고려할 수 있으나, 이는 터치 자체를 끄므로 권장하지 않습니다.
  
  ## 2. WPF 에서 스크롤 고무줄(끝에서 창 들썩임, 흔들림) 제거
  WPF 의 터치 스크롤은 끝에 닿을 떄 `ManipulationBoudnaryFeedback` 이벤트로 바운스 피드백을 발생시킵니다. 이를 **앱 전역에서 최사우이로 가로채서** 막으면 깔끔히 해결됩니다.
  
  ### App.xaml.cs (전역 한 방 처리 - 권장)
  
```csharp
  using System.Windows;
using System.Windows.Input;

public partial class App : Application
{
    protected override void OnStartup(StartupEventArgs e)
    {
        base.OnStartup(e);

        // 모든 UIElement에서 발생하는 ManipulationBoundaryFeedback을
        // "handledEventsToo = true"로 최상위에서 가로채어 종료
        EventManager.RegisterClassHandler(
            typeof(UIElement),
            UIElement.ManipulationBoundaryFeedbackEvent,
            new EventHandler<ManipulationBoundaryFeedbackEventArgs>((s, args) => args.Handled = true),
            handledEventsToo: true);
    }
}
```

## 요약

[x] **레지스트리**: `HKLM\SOFTWARE\Policies\Microsoft\Windows\EdgeUI\AllowEdgeSwipe = 0` (가장자리 스와이프 전부 차단)
[x] **WPF 전역 바운스 제거**: `EventManager.RegisterClassHandler(... ManipulationBoundaryFeedback ..., handledEventsToo:true)`