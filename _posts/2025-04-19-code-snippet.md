---
title: Code Snippet 작성하기
date: 2025-04-19 10:00:00 +0900
categories: [Programming, Visual Studio]
tags: [c#, code snippet, visual studio]
description: Code snippet
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---
> 몇 글자로 코드 완성하는 기능이 있다. 그걸 **"코드 조각"** 이라고 부르기로 했어요.

## Code snippet 이란?
**코드 조각**이라고 불리는 Code snippet 은 간단한 키워드로 코드를 만들어 내는 기능이다. Visual Studio 에서도 기본적 으로 제공해주는 코드조각들이 있다.
> prop, propf, propdp, try, cw 등등
![코드조각 이미지](https://i.ibb.co/zHxqcHZs/image.png)

<br>

**prop** 을 입력하고 Tab, Tab 을 누르면

```cs
public int MyProperty { get; set; }
```
이 자동 완성되고 **Type** 이나 **변수명** 을 탭을 이용하여 이동하며 변경할 수 있다.

<br>

**propf** 을 입력하고 Tab, Tab 을 누르면

```cs
   private int myVar;
   
   public int MyProperty
   {
       get { return myVar; }
       set { myVar = value; }
   }
```

이 자동 완성된다. 
코드 조각은 단순 키워드 입력으로 코드를 자동 완성하는 기능이다.

당연히 **Custom 도 가능**하다

## Code Snippet 만들기
Visual Studio 아무 버전에서 xml 파일을 하나 만들고 아래 코드를 입력합니다.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<CodeSnippets xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet">
  <CodeSnippet Format="1.0.0">
    <Header>
      <Title>Community Toolkit Partial Observable Property</Title> <!-- 스니펫 이름 -->
      <Shortcut>propno</Shortcut> <!-- 단축어 (Tab 키 두 번으로 실행) -->
      <Description>Creates a observable property</Description>
      <Author>Chanhong</Author>
      <SnippetTypes>
           <SnippetType>Expansion</SnippetType>
      </SnippetTypes>
    </Header>
    <Snippet>
      <Declarations>
		<Literal>
          <ID>type</ID>
          <Default>int</Default>
          <ToolTip>Property Type</ToolTip>
        </Literal>
        <Literal>
          <ID>name</ID>
          <Default>MyProperty</Default>
          <ToolTip>Property Name</ToolTip>
        </Literal>
      </Declarations>
      <Code Language="CSharp">
        <![CDATA[
[ObservableProperty]
public partial $type$ $name$ { get; set; }$end$
]]>
      </Code>
    </Snippet>
  </CodeSnippet>
</CodeSnippets>
```
파일을 저장하고 **도구 - 코드 조각 관리자** 메뉴를 선택합니다.
![코드조각 관리자](https://i.ibb.co/hxG0fM3M/image.png)

1. Language 를 CSharp 으로 변경하고 가져오기를 클릭합니다.
2. My Code snippets 를 선택합니다.
3. 완료 및 확인 클릭

> 코드조각 사용
![코드조각 사용1](https://i.ibb.co/HLXx1hjW/image.png)
![코드조각 사용2](https://i.ibb.co/CsJtFfLr/image.png)

> 내 손가락아, 더 많은 코드를 작성할 수 있겠지...? 응?
