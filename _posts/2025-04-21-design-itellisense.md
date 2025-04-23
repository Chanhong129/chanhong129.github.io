---
title: View 에서 Intellisense 사용하기
date: 2025-04-21 17:00:00 +0900
categories: [Programming, MVVM]
tags: [c#, wpf, mvvm]
description: CommunityToolkit.Mvvm
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> **Visual Studio** 에서 제공하는 강력한 기능 **Intellisense** 는 코딩을 할 때, 많은 도움을 주는 도구이다.

## View 와 ViewModel 연결 후
보이지 않는 **Intellisene**...

![보이지 않는 인텔리센스](https://i.ibb.co/1tPskpdc/image.png)

> ViewModel 에 있는 속성 목록이 인텔리센스로 나오길 원하지만 나오지 않는 모습...
> 하지만 언제 나 그랬듯이 방법은 있다.

Window 속성에 다음 한 줄을 추가해 준다.
내용은 **MainViewModel** 을 참고하겠다는 뜻이다.
```cs
d:DataContext="{d:DesignInstance local:MainViewModel, IsDesignTimeCreatable=True}"
```

## 한 줄 추가 후...
![보이는 인텔리센스](https://i.ibb.co/XxP7pR8R/image.png)

> 이제 다시 편하게 코딩 할 수 있다...!😁