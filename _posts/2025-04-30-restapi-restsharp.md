---
title: NugetPackage, RestSharp
date: 2025-04-30 16:00:00 +0900
categories: [Programming, Communication]
tags: [c#, restapi, restsharp]
description: RestSharp 으로 간편하게 REST API 호출하기 (C#)
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> C# 에서 Rest API 호출할 땐 ```HttpClient`` 도 많이 사용하지만,   
> 더 간결하고 유연한 방식으로 API 를 호출하고 싶다면 **RestSharp** 라이브러리가 좋은 선택이 될 수 있습니다.

---

## RestSharp 이란 ?

> RestSharp 은 .NET 에서 Restful API 호출을 쉽게 할 수 있도록 도와주는 오픈소스 라이브러리 입니다.

|**특징**|**설명**|
|직관적인 문법|URL, 파라미터, Body 구성 등이 간단|
|다양한 HTTP 메서드 지원|GET, POST, PUT, DELETE, PATCH 등|
|JSON 자동 직렬화/역직렬화|Newtonsoft.Json 또는 System.Text.Json 사용 가능|
|비동기 지원|```ExecuteAsync```, ```GetAsync<T>``` 등 지원|

## 설치 방법
**NuGet 패키지 매니저에서 설치:**

```mathematica
Install-Package RestSharp
```

또는

```bash
dotnet add package RestSharp
```

## 간단한 사용 예 (GET)

```cs
using RestSharp;

var client = new RestClient("https://jsonplaceholder.typicode.com");
var request = new RestRequest("posts/1", Method.Get);

var response = await client.ExecuteAsync(request);
Console.WriteLine(response.Content);
```

## POST 예제 (Body 포함)

```cs
var client = new RestClient("https://jsonplaceholder.typicode.com");
var request = new RestRequest("posts", Method.Post);
request.AddJsonBody(new
{
    title = "foo",
    body = "bar",
    userId = 1
});

var response = await client.ExecuteAsync(request);
Console.WriteLine(response.Content);
```

## 자동 직렬화 (Generic)

```cs
var request = new RestRequest("posts/1", Method.Get);
var response = await client.GetAsync<Post>(request);  // 자동 JSON → 객체 변환

Console.WriteLine(response.Title);
```

```cs
public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Body { get; set; }
    public int UserId { get; set; }
}
```

## 헤더 추가, 인증 처리

```cs
request.AddHeader("Authorization", "Bearer your_token");
```

또는 기본 인증:

```cs
client.Authenticator = new HttpBasicAuthenticator("username", "password");
```

## 주의 사항
- 최신 버전 (107.x 이상)에서는 ```Method.Get```,  ```Method.Post``` 같은 방식으로 API 가 변경됨
- ```AddJsonBody()``` 는 내부적으로 JSON 직렬화를 수행하므로 복잡한 객체도 쉽게 전송 가능
- ```response.IsSuccessful``` 로 HTTP 상태 체크 가능

## 마무리
RestSharp은 C# 에서 REST API 를 사용할 때 **가독성이 좋고 빠르게 구현**할 수 있는 도구입니다.   
HttpClient 보다 복잡한 코드 없이, **API 호출 + 파라미터 + 바디 + 응답 처리까지 한번에 처리**할 수 있어 개발 속도도 빠릅니다.