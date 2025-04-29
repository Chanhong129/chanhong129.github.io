---
title: Siemens PLC 통신 기초 (S7.NetPlus + WPF)
date: 2025-04-29 10:00:00 +0900
categories: [Programming, Communication]
tags: [c#, wpf, siemens, plc]
description: TIA Portal, S7.NetPlus 소개 + 통신 예제
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## Siemens PLC 란?
- Siemens 사의 대표적인 산업용 PLC
- 주요 유럽/글로벌 시장 점유율 1위
- 주요 시리즈: **S7-1200**, **S7-1500**, **S7-300**, **S7-400** 등
- 통신 방식
	- **PROFINET** (Ethernet 기반)
	- **MPI** (Multi-Point Interface)
	- **Profibus** (Fieldbus)
	- **Modbus TCP/IP** 지원 가능 (게이트웨이 사용)

## 사전 준비 (소프트웨어 설치)
- **TIA Portal** (Totall Integrated Automation Portal)
	- Siemens PLC 프로그램 작성/다운로드/시뮬레이션 툴

- **S7 Communication Library** (통신 라이브러리)
	-	S7.NetPlus (C#용, 간편한 오픈소스)

## WPF 에서 Siemens PLC 연결하기
### Nuget 패키지 관리에서   ```S7.NetPlus``` 검색 후 설치

### **WPF 에서 기본 통신 코드**

```cs
using S7.Net;

public class SiemensPLC
{
    private Plc plc;

    public bool Connect(string ipAddress, CpuType cpuType = CpuType.S71500, short rack = 0, short slot = 1)
    {
        plc = new Plc(cpuType, ipAddress, rack, slot);
        plc.Open();
        return plc.IsConnected;
    }

    public void Disconnect()
    {
        plc.Close();
    }

    public object Read(string address)
    {
        return plc.Read(address);
    }

    public void Write(string address, object value)
    {
        plc.Write(address, value);
    }
}
```

### 사용 예시

```cs
var siemens = new SiemensPLC();
bool connected = siemens.Connect("192.168.0.10");

if (connected)
{
    var value = siemens.Read("DB1.DBW0"); // 데이터 블록1의 워드0 읽기
    siemens.Write("DB1.DBW2", (short)1234); // 데이터 블록1의 워드2에 1234 쓰기
}
```