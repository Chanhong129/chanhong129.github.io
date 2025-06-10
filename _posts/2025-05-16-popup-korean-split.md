---
title: WPF Popup 내 TextBox 에서 한글 입력 시 자음·모음 분리 현상 해결 방법
date: 2025-05-16 10:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, popup, textbox]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

WPF 앱에서 ```Popup``` 컨트롤 안에 ```TextBox```를 배치하고 한글을 입력할 때,  자음과 모음이 분리되어 입력되는 현상을 경험하셨나요? 예를 들어, '집에가고싶어'를 입력하면 '집ㅇㅔㄱㅏㄱㅗㅅㅣㅍ어'처럼 문제가 발생할 수 있습니다.

## 문제 원인
이 현상은 ```Popup``` 이 별도의 윈도우로 생성되기 때문에 IME(입력기)가 제대로 연동되지 않아 발생합니다. 일반적인 포커스 설정이나 IME 설정으로는 이 문제를 해결하기 어렵습니다.

## 해결방법
```Popup``` 이 열릴 때 해당 ```Popup```의 윈도우 핸들에 직접포커스를 설정하여 IME 가 정상적으로 작동하도록 해야 합니다. 이를 위해 Win32 API의 ```SetpFocus``` 함수를 사용합니다.

### 구현 방법
 1. **Win32 API 함수 선언**
먼저, Win32 API의 ```SetFocus``` 함수를 선언합니다.

```cs
 [DllImport("user32.dll")]
public static extern IntPtr SetFocus(IntPtr hWnd);
```

2. **Popup의 윈도우 핸들 가져오기**
```Popup```의 자식 요소로부터 ```HwndSource```를 얻어 윈도우 핸들을 가져옵니다.

```cs
private IntPtr GetPopupHandle(Popup popup)
{
    var source = (HwndSource)PresentationSource.FromVisual(popup.Child);
    return source.Handle;
}
```

3. **Popup 열릴 때 포커스 설정**
```Popup```의 ```Opened``` 이벤트에서 윈도우 핸들에 포커스를 설정합니다.

```cs
private void Popup_Opened(object sender, EventArgs e)
{
    IntPtr handle = GetPopupHandle(myPopup);
    SetFocus(handle);
}
```

4. **XAML 에서 Popup 구성**
```Popup``` 컨트롤을 정의하고 ```Opened``` 이벤트를 연결합니다.

```cs
<Popup x:Name="myPopup" Opened="Popup_Opened">
    <TextBox x:Name="myTextBox" Width="200" Height="30"/>
</Popup>
```

## 결과
위와 같이 구현되면 ```Popup``` 내 ```TextBox``` 에서 한글 입력 시 자음과 모음이 정상적으로 조합되어 입력됩니다.

## 참고사항
- ```Popup```이 별도의 윈도우로 생성되기 때문에 IME와의 연동 문제가 발생할 수 있습니다.
- ```SetFocus```를 사용하여 명시적으로 포커스를 설정하면 이러한 문제를 해결할 수 있습니다.
- 이 방법은 ```Popup``` 내에서 한글 입력이 필요한 경우에 유용하게 사용할 수 있습니다.