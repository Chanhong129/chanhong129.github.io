---
title: Xml, Json, Csv Serializer 비교
date: 2025-04-28 10:00:00 +0900
categories: [Programming, Data Serialization]
tags: [c#, xml, json, csv, serializer]
description: Xml, Json, Csv Serializer
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## XML / JSON / CSV 직렬화 비교

|항목|XML Serializer|JSON Serializer|CSV Serializer|
|--|--|--|--|
|구조 표현|계층적 (Nested) 구조 표현 강력|계층적 구조 표현 가능|단순 행/열 구조 (Nested 불가)|
|가독성|읽기 쉬우나 태그가 많아 복잡할 수 있음|가장 읽기 간결함|단순하지만 복잡한 데이터 표현 어려움|
|파일 크기|가장 큼(태그 포함)|중간(텍스트 기반이지만 효율적)|가장 작음|
|표준화|강력한 표준 (XSD 등)|유연하지만 표준 스키마 없음|스키마 없음|
|호환성|거의 모든 시스템과 호환|거의 모든 시스템과 호환|거의 모든 시스템에서 읽을 수 있음|
|사용 목적|복잡한 데이터, 설정파일, 통신|웹 API, 서버-클라이언트 통신|간단한 데이터 저장 및 교환|
|코드 작성 난이도|복잡 (Attribute 설정 필요)|쉬움 (Newtonsoft 등 라이브러리 지원)|쉬움 (CsvHelper 등 라이브러리 지원)|
|예시|```<Person><Name>홍길동</Name></Person>```|```{"Name":"홍길동"}```|```홍길동,30,false```|

## 추가 간단 설명
**XML Serializer**
- .NET 기본 ```XmlSerializer``` 사용
- 복잡한 데이터 구조와 메타정보(스키마 등) 함께 다룰 때 적합
- SOAP, 설정 파일(config) 등에 주로 사용

**JSON Serializer**
- 대표적으로 ```Newtonsoft.Json**``` (또는 .NET ```System.Text.Json```)
- API, 모바일 앱, 서버 통신에서 가장 널리 쓰임
- 빠르고 가볍고 현대적인 데이터 포맷

**CSV Serializer**
- 대표적으로 ```CsvHelper``` 사용
- 테이블 데이터, 엑셀과의 데이터 교환 등에 유리
- 복잡한 계층형 구조는 표현할 수 없지만, 빠르고 효율적

## 요약 한줄
- **XML**: 복잡하고 계층적인 데이터에 강하다.
- **JSON**: 빠르고 가볍게 계층형 데이터를 주고받을 때 최고.
- **CSV**: 단순한 목록형 데이터 저장에 최적.
