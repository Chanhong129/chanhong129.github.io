---
title: 그 외 데이터 포맷 (yaml, protobuf)
date: 2025-04-28 14:00:00 +0900
categories: [Programming, Data Serialization]
tags: [c#, yaml, protobuf, serializer]
description: yaml, protobuf Serializer
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## 그 외 데이터 포맷 (YAML, Protobuf)
우리가 흔히 다루는 데이터 포맷은 **XML, JSON, CSV** 가 대표적입니다.   
하지만 현대 개발 환경에서는 이 외에도 다양한 포맷이 쓰이고 있으며,   
특히 **YAML**과 **Protobuf**는 점점 더 중요해지고 있습니다.   

이번 포스팅에서는 두 포맷을 중심으로 살펴보겠습니다.

## YAML이란 ?
**YAML**은 "YAML Aint' Markup Language"의 약자로,   
**사람이 읽고 쓰기 쉬운 데이터 직렬화 포맷** 입니다.   

**들여쓰기**를 통해 데이터 구조를 표현하며,   
JSON에 비해 훨씬 가볍고 직관적인 문법을 가지고 있습니다.

## YAML 예시

```yaml
person:
  name: 홍길동
  age: 30
  isStudent: false
  hobbies:
    - soccer
    - painting
    - reading
```

**특징**:
- 들여쓰기로 구조 표현 (괄호나 중괄호 없음)
- 배열은 ```-``` 로 표현
- 주로 설정 파일에서 많이 사용됨 (ex. Kubernetes, GitHub Actions)

## YAML의 장단점

|**장점**|**단점|
|--|--|
|사람이 읽기 쉽다|들여쓰기 오류가 발생하기 쉽다|
|JSON 보다 간결하다|파싱 과정에서 실수가 생기기 쉽다|
|설정 파일에 최적화되어 있다|복잡한 데이터에는 부적합할 수 있다|

**대표 사용처:**
- Docker Compose(```docker-compose.yaml```)
- Kubernetes Deployment 설정
- GitHub Actions Workflow (```.github/workflows/*.yaml```)
- CI/CD 설정 파일 등

## Protobuf 란 ?
**Protocol Buffers (Protobuf)** 는   
**Google** 이 개발한 **고성능 바이너리 데이터 직렬화 포맷** 입니다.   
텍스트 기반인 JSON, XML 과 달리,   
Protobuf는 데이터를 바이너리로 변환해 **빠르고 용량을 작게** 전송합니다.    
**Protobuf 메시지 정의 예시**

```proto
syntax = "proto3";

message Person {
  string name = 1;
  int32 age = 2;
  bool is_student = 3;
  repeated string hobbies = 4;
}
```

여기서 ```.proto``` 파일을 작성한 후, 컴파일러를 이용해 코드로 변환하여 사용합니다.   
변환된 코드는 네트워크 통신, 저장소 저장 등에 사용됩니다.   

**Protobuf**의 장단점

|**장점**|**단점**|
|--|--|
|매우 빠르고 용량이 작다|사람이 읽을 수 없다 (바이너리)|
|스키마(구조) 정의로 안정성 확보|구조 변경 시 관리가 필요하다|
|다양한 언어 지원 (C#, Java, Python 등)|초기 학습이 필요하다|

**대표 사용처:**
- gRPC 기반 서버 통신
- 대규모 서비스 간 데이터 교환 (ex. Google 내부 시스템)
- IoT, 모바일 앱 고성능 통신

## YAML vs Protobuf 요약 비교

|**항목**|**YAML**|**Protobuf**|
|--|--|--|
|목적|사람이 읽기 쉬운 데이터 표현|기계간 빠른 데이터 전송|
|형식|텍스트 기반|바이너리 기반|
|스키마 필요 여부|없음|필요함 (.proto 파일 작성)|
|주요 사용처|설정 파일, 인프라 설정|통신, 대용량 데이터 처리|
|읽기/수정 용이성|매우 쉬움|불가(툴 필요)|
|성능|적당히 빠름|매우 빠름|

## 마치며
XML, JSON, CSV는 여전히 강력한 데이터 포맷입니다.   
하지만 현대 개발 환경에서는 **YAML과 Protobuf** 같은 포맷이 더 자주 쓰이기도 합니다.

- **YAML**은 사람이 쉽게 읽고 관리해야 하는 설정 파일**에,
- **Protobuf**는 **빠르고 효율적인 시스템 간 통신**에 최적화 되어 있습니다.

각 포맷의 특징을 이해하고,   
상황에 맞는 포맷을 선택할 수 있는 것이 중요합니다.   

앞으로 YAML과 Protobuf를 다룰 기회가 많아질 테니,   
미리 친숙해져두면 큰 도움이 될 것입니다.  

