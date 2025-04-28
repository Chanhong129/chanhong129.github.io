---
title: C# Json Serializer 사용법 (with NewtonSoft.Json)
date: 2025-04-26 10:00:00 +0900
categories: [Programming, Data Serialization]
tags: [c#, json, serializer, newtonsoft]
description: Json Serializer
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## JSON 이란 ?
**JSON (JavaScript Objet Notation)** 은   
데이터를 구조화하여 저장하고 전송하기 위한 **가볍고 읽기 쉬운 포맷** 입니다.   
주로 서버와 클라이언트 간의 데이터 교환에 사용되며,   
텍스트 기반이라 사람과 기계 모두 쉽게 이해할 수 있습니다.   
**예시:**

```json
{
  "name": "홍길동",
  "age": 30,
  "isStudent": false
}
```

## JSON의 장단점
**장점**
- **가볍다**: XML 보다 훨씬 데이터 양이 작아 빠르게 전송할 수 있다.
- **가독성**: 사랍도 쉽게 읽고 편집할 수 있다.
- **언어 독립성**: 거의 모든 프로그래밍 언어가 JSON 을 지원 한다.
- **구조화**: 배열, 객체 등을 자연스럽게 표현할 수 있다.   

**단점**
- **데이터 타입 한계**: 날짜, 바이너리 데이터 등은 별도 처리해야 한다.
- **스키마 없음**: 데이터 구조가 자유로워서 검증이 필요할 수 있다.
- **보안 문제**: 서버에서 입력값을 겁증하지 않으면 취약점이 생길 수 있다.

## Newtonsoft.Json 사용법

**Newtonsoft.Json** 은 C# 에서 가장 많이 쓰이는 JSON 라이브러리 입니다.   
설치 방법부터 기본 사용법까지 소개할게요.
### 1. 설치
Nuget 패키지 매니저에서 설치합니다.

```
Install-Package Newtonsoft.Json
```
또는 Visual Studio → 프로젝트 → Nuget 관리 → ```Newtonsoft.Json``` 검색 → 설치

### 2. 객체를 JSON 문자열로 변환 (직렬화)

```cs
using Newtonsoft.Json;

var person = new Person
{
    Name = "홍길동",
    Age = 30,
    IsStudent = false
};

string json = JsonConvert.SerializeObject(person);
Console.WriteLine(json);
```

**출력 결과:**

```
{"Name":"홍길동","Age":30,"IsStudent":false}
```

### 3. JSON 문자열을 객체로 변환 (역직렬화)

```cs
using Newtonsoft.Json;

string json = @"{ ""Name"": ""홍길동"", ""Age"": 30, ""IsStudent"": false }";

var person = JsonConvert.DeserializeObject<Person>(json);

Console.WriteLine(person.Name); // 홍길동
```

### 4. 클래스 예시

```cs
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public bool IsStudent { get; set; }
}
```

## 추가 팁
- JSON 포맷 이쁘게 출력하기 (Pretty Print)

```cs
string prettyJson = JsonConvert.SerializeObject(person, Formatting.Indented);
Console.WriteLine(prettyJson);
```

출력:

```json
{
  "Name": "홍길동",
  "Age": 30,
  "IsStudent": false
}
```

- JSON 특정 필드만 직렬화하거나 제외하고 싶을 때 ```JsonProperty```, ```JsonIgnore``` 특성(Attribute) 사용

```cs
public class Person
{
    [JsonProperty("full_name")]
    public string Name { get; set; }
    
    public int Age { get; set; }
    
    [JsonIgnore]
    public bool IsStudent { get; set; }
}
```

## 요약
- JSON은 **가볍고 가독성 좋은 데이터 포맷**이다.
- **Newtonsoft.Json**을 이용해 쉽게 JSON <-> 객체 변환이 가능하다.
- 다양한 옵션을 통해 **출력형식 제어**와 **속성 제어**도 가능하다.