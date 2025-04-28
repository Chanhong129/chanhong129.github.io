---
title: C# XML Serializer 사용법
date: 2025-04-25 10:00:00 +0900
categories: [Programming, Data Serialization]
tags: [c#, xml, serializer]
description: XML Serializer
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## XmlSerializer 란 ?

- C# 에서 XML 형식으로 **객체를 저장하거나 복원**하는 기능 제공
- ```System.Xml.Serialization.XmlSerializer``` 클래스 사용
- 기본적으로 **기본 생성자 필요**
- ```[Serializable]``` 필요 없음 (BinaryFormatter와 다름)

## 기본 예제
- **예제 클래스**

```cs
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```
- **직렬화 코드 (객체 → XML)**

```cs
var person = new Person { Name = "Alice", Age = 30 };
var serializer = new XmlSerializer(typeof(Person));

using var writer = new StringWriter();
serializer.Serialize(writer, person);
string xml = writer.ToString();

Console.WriteLine(xml);
```
- **역직렬화 코드 (XML → 객체)**

```cs
var xml = @"<Person><Name>Alice</Name><Age>30</Age></Person>";
var serializer = new XmlSerializer(typeof(Person));

using var reader = new StringReader(xml);
Person? result = serializer.Deserialize(reader) as Person;
```

## 속성제어

```cs
public class Person
{
    [XmlAttribute]
    public string Name { get; set; }

    [XmlElement("UserAge")]
    public int Age { get; set; }

    [XmlIgnore]
    public string Secret { get; set; }
}
```
- ```[XmlAttribute]```: XML 속성으로 변환
- ```[XmlElement("UserAge")]```: 커스텀 태그 이름
- ```[XmlIgnore]```: 직렬화에서 제외

그리고 객체가 다음과 같다고 가정

```cs
var person = new Person
{
    Name = "Alice",
    Age = 30,
    Secret = "비밀이에요"
};
```

### 생성되는 XML 결과

```xml
<?xml version="1.0" encoding="utf-16"?>
<Person Name="Alice">
  <UserAge>30</UserAge>
</Person>
```

## 파일 저장/불러오기

```cs
using var fs = new FileStream("person.xml", FileMode.Create);
serializer.Serialize(fs, person);

// 불러오기
using var fsRead = new FileStream("person.xml", FileMode.Open);
var loaded = serializer.Deserialize(fsRead) as Person;
```

## 주의점

- **기본 생성자 반드시 필요**
- **상속은 지원하지 않음** (추상 클래스 직렬화 불가)
- Dictionary, Tuple 등 복잡 타입 지원 X
- Nullable 처리 제한적

## 실무 예시 & 정리

|장점|단점|
|--|--|
|XML 표준 포맷|문법이 무겁고 파일 크기 큼|
|가독성 있음 (태그 구조)|중첩 구조가 복잡하면 유지 보수 어려움|
|외부 시스템 연동에 적합|속도 느림, 제한된 타입 지원|

> 노드에 속성을 여러 개 입력할 수 있고 보기가 편한 것이 가장 큰 장점 !
