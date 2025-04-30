---
title: UI 멈춤 없이 TCP 통신하는 방법 (TcpClient 비동기 처리)
date: 2025-04-30 11:00:00 +0900
categories: [Programming, Communication]
tags: [c#, wpf, tcp, tcpclient, tcplistener, async]
description: Async Tcp
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> 보통 C#에서 ```TcpClient```를 사용할 때 ```Read()``` 나 ```Write()``` 같은 동기 함수는    
> **UI 스레드를 블로킹**시키기 때문에 버튼 클릭, 리스트 갱신 등이 멈춰버릴 수 있습니다.   
> 이를 방지하려면 반드시 **비동기 통신 (**```async/awiat```**)**을 사용해야 합니다.

## UI가 멈추는 잘못된 예시

```cs
// WinForms / WPF 버튼 클릭 이벤트 안에서
var stream = client.GetStream();
byte[] buffer = new byte[1024];
int length = stream.Read(buffer, 0, buffer.Length);  // ❌ 이 줄에서 UI 멈춤 발생
```

## 해결방법: 비동기 방식 사용

```cs
private async Task ReceiveAsync(TcpClient client)
{
    var stream = client.GetStream();
    byte[] buffer = new byte[1024];

    while (client.Connected)
    {
        int byteCount = await stream.ReadAsync(buffer, 0, buffer.Length);
        if (byteCount > 0)
        {
            string msg = Encoding.UTF8.GetString(buffer, 0, byteCount);
            Console.WriteLine("서버 메시지: " + msg);
            // UI 업데이트는 Dispatcher.Invoke(...) 또는 InvokeRequired 사용
        }
    }
}
```


## WPF에서 UI 안전하게 업데이트 하는 방법

```cs
Application.Current.Dispatcher.Invoke(() =>
{
    MyTextBox.Text += "수신: " + msg + "\n";
});
```

또는 MVVM 구조라면 ObservableCollection + INotifyPropertyChanged 로 바인딩 처리 가능.

## 메세지 보내기도 비동기로

```cs
private async Task SendAsync(TcpClient client, string message)
{
    var stream = client.GetStream();
    byte[] data = Encoding.UTF8.GetBytes(message);
    await stream.WriteAsync(data, 0, data.Length);
}
```

## 정리

|**항목**|**동기 방식**|**비동기 방식**|
|--|--|--|
|UI 멈춤|발생함|없음|
|코드 길이|짧음|조금 더 길지만 안전|
|추천 용도|콘솔, 테스트|WPF, Winforms 등 UI 앱|

## 마무리
TCP 통신을 UI 와 함께 사용하는 경우   
반드시 ```ReadAsync```, ```WriteAsync``` 같은 비동기 방식으로 구현하고,   
UI 업데이트는 ```Dispatcher``` 를 통해 안전하게 처리해야 합니다.