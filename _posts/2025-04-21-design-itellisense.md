---
title: View 에서 Intellisense 사용하기
date: 2025-04-21 17:00:00 +0900
categories: [Programming, MVVM]
tags: [wpf, mvvm]
description: CommunityToolkit.Mvvm
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> **Visual Studio** 에서 제공하는 강력한 기능, **Intellisense** 에 중독되면 없이는 살 수 없는 몸이 되어버렷...

## View 와 ViewModel 연결 후
보이지 않는 **Intellisene**...

![보이지 않는 인텔리센스](https://i.ibb.co/1tPskpdc/image.png)

> 나는 ViewModel 에 있는 속성 목록을 보고 싶다...

하지만 우린 방법을 찾을 것이다. 늘 그래왔듯이...

Window 속성에 다음 한 줄을 추가해 준다.
내용은 **MainViewModel** 을 참고하겠다는 뜻이다.
```cs
d:DataContext="{d:DesignInstance local:MainViewModel, IsDesignTimeCreatable=True}"
```

## 한 줄 추가 후...
![보이는 인텔리센스](https://i.ibb.co/XxP7pR8R/image.png)

> 이제 다시 코딩 할 수 있다...!😁