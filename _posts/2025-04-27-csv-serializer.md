---
title: C# Csv Serializer 사용법 (with CsvHelper)
date: 2025-04-27 10:00:00 +0900
categories: [Programming, Data Serialization]
tags: [c#, csv, serializer, csvhelper]
description: Csv Serializer
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## CSV 란 ?
**CSV (Comma-Separated Values)** 는   
**쉼표(,)로 구분된 데이터 파일 포맷**입니다.   
표 형식의 데이터를 텍스트로 저장하는 가장 간단하고 널리 사용되는 방식입니다.   
주로 엑셀, 데이터베이스, 통계 툴 등에서 사용합니다.   
**예시:**

```
Name,Age,IsStudent
홍길동,30,false
김영희,25,true
```

## CSV 의 장단점
**장점**
- **단순함**: 텍스트 기반이랑 생성과 편집이 쉽다.
- **범용성**: 대부분의 프로그램(엑셀, 구글 스프레드시트 등)과 호환된다.
- **가볍다**: 별다른 구조가 없어 파일 용량이 작다.

**단점**
- **구조 제한**: 중첩된 데이터(배열, 객체) 표현이 어렵다.
- **데이터 오류**: 쉼표나 줄바꿈이 데이터에 포함되면 파싱이 복잡해진다.
- **메타테이더 없음**: 데이터 타입 정보가 포함되지 않는다.

## CsvHelper 사용법
**CsvHelper**는 C# 에서 CSV 를 쉽게 다룰 수 있게 도와주는 강력한 라이브러리 입니다.

### 1. 설치
Nuget 패키지 매니저에서 설치합니다.

```
Install-Package CsvHelper
```

또는 Visual Studio → 프로젝트 → Nuget 관리 → ```CsvHelper``` 검색 → 설치

### 2. 객체 리스트를 CSV 로 저장 (쓰기)

```cs
using CsvHelper;
using System.Globalization;
using System.IO;

var people = new List<Person>
{
    new Person { Name = "홍길동", Age = 30, IsStudent = false },
    new Person { Name = "김영희", Age = 25, IsStudent = true }
};

using (var writer = new StreamWriter("people.csv"))
using (var csv = new CsvWriter(writer, CultureInfo.InvariantCulture))
{
    csv.WriteRecords(people);
}
```

**생성되는 people.csv 파일:**

```
Name,Age,IsStudent
홍길동,30,False
김영희,25,True
```

### 3. CSV 파일을 객체 리스트로 읽기 (읽기)

```cs
using CsvHelper;
using System.Globalization;
using System.IO;

using (var reader = new StreamReader("people.csv"))
using (var csv = new CsvReader(reader, CultureInfo.InvariantCulture))
{
    var people = csv.GetRecords<Person>().ToList();
    
    foreach (var person in people)
    {
        Console.WriteLine($"{person.Name} - {person.Age} - {person.IsStudent}");
    }
}
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
- **헤더 이름과 속성명이 다를 때 매핑하기**

```cs
using CsvHelper.Configuration;

public class PersonMap : ClassMap<Person>
{
    public PersonMap()
    {
        Map(m => m.Name).Name("이름");
        Map(m => m.Age).Name("나이");
        Map(m => m.IsStudent).Name("학생여부");
    }
}
```

**사용 방법:**

```cs
using (var reader = new StreamReader("people.csv"))
using (var csv = new CsvReader(reader, CultureInfo.InvariantCulture))
{
    csv.Context.RegisterClassMap<PersonMap>();
    var people = csv.GetRecords<Person>().ToList();
}
```

- **구분자를 쉼표(,) 대신 다른 문자로 변경하기**

```cs
var config = new CsvHelper.Configuration.CsvConfiguration(CultureInfo.InvariantCulture)
{
    Delimiter = ";"
};

using (var writer = new StreamWriter("people_semicolon.csv"))
using (var csv = new CsvWriter(writer, config))
{
    csv.WriteRecords(people);
}
```

## 요약
- CSV 는 **간단하고 범용성 높은** 데이터 저장 포맷이다.
- **CsvHelper**를 이용하면 C#에서 **쉽고 빠르게 CSV를 읽고 쓸 수 있다.**
- 매핑, 구분자 변경 등 다양한 설정도 가능하다.
