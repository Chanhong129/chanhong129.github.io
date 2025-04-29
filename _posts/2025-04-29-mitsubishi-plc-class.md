---
title: 나만의 Misubishi PLC Class 작성
date: 2025-04-29 09:00:00 +0900
categories: [Programming, Communication]
tags: [c#, wpf, mitsubishi, plc]
description: Mitsubishi PLC Class
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---
> 나만의 클래스를 작성해 놓으면 언제든지 원하는 데로 수정하고 유용하게 사용 가능하다.

```cs
public class MitsubishiPLC
{
    private ActUtlType mx;

    /// <summary>
    /// PLC 연결 여부 상태
    /// </summary>
    public bool IsConnected { get; set; }

    private int logicalNumber = 1;

    /// <summary>
    /// PLC 로지컬 스테이션 번호 설정
    /// </summary>
    public int LogicalNumber
    {
        get { return logicalNumber; }
        set
        {
            if (mx != null)
            {
                logicalNumber = value;
                mx.ActLogicalStationNumber = logicalNumber;
            }
        }
    }

    public MitsubishiPLC() { }

    ~MitsubishiPLC()
    {
        if (mx != null)
        {
            mx.Close();
        }
    }

    /// <summary>
    /// MX Component 객체 초기화
    /// </summary>
    public bool Initialize()
    {
        try
        {
            mx = new ActUtlType();
            return true;
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        return false;
    }

    /// <summary>
    /// PLC 연결 시도
    /// </summary>
    public bool Open()
    {
        try
        {
            int resultCode = mx.Open();
            if (resultCode == 0)
            {
                Console.WriteLine($"Mitsubishi PLC open. (Logical Number: {LogicalNumber})");
                IsConnected = true;
                return true;
            }
            else
            {
                WriteErrorCodeLog(resultCode);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        IsConnected = false;
        return false;
    }

    /// <summary>
    /// PLC 연결 종료
    /// </summary>
    public bool Close()
    {
        try
        {
            int resultCode = mx.Close();
            if (resultCode == 0)
            {
                Console.WriteLine($"Mitsubishi PLC close. (Logical Number: {LogicalNumber})");
                IsConnected = false;
                return true;
            }
            else
            {
                WriteErrorCodeLog(resultCode);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        return false;
    }

    /// <summary>
    /// PLC 온라인 상태 확인 (SM400 디바이스 이용)
    /// </summary>
    public bool IsOnline()
    {
        try
        {
            int resultCode = mx.GetDevice("SM400", out int iData);
            if (resultCode == 0 && iData == 1)
            {
                IsConnected = true;
                return true;
            }
            else
            {
                WriteErrorCodeLog(resultCode);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        IsConnected = false;
        return false;
    }

    /// <summary>
    /// 단일 디바이스(short형 데이터) 쓰기
    /// </summary>
    public bool SetDevice2(string deviceCode, int address, short data)
    {
        try
        {
            if (IsConnected)
            {
                string deviceAddress = deviceCode + address.ToString();
                int resultCode = mx.SetDevice2(deviceAddress, data);
                if (resultCode == 0)
                {
                    Console.WriteLine($"SetDevice2 :: Address: {deviceAddress}, Data: {data}");
                    return true;
                }
                else
                {
                    WriteErrorCodeLog(resultCode);
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        return false;
    }

    /// <summary>
    /// 연속 디바이스 블록(short 배열) 쓰기
    /// </summary>
    public bool WriteDeviceBlock2(string deviceCode, int headAddress, short[] data)
    {
        try
        {
            if (IsConnected && data.Length > 0)
            {
                string deviceAddress = deviceCode + headAddress.ToString();
                int resultCode = mx.WriteDeviceBlock2(deviceAddress, data.Length, ref data[0]);
                if (resultCode == 0)
                {
                    Console.WriteLine($"WriteDeviceBlock2 :: Start Address: {deviceAddress}, Size: {data.Length}");
                    return true;
                }
                else
                {
                    WriteErrorCodeLog(resultCode);
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        return false;
    }

    /// <summary>
    /// 복수 디바이스 지정하여 랜덤 쓰기
    /// </summary>
    public bool WriteDeviceRandom2(string deviceList, short[] data)
    {
        try
        {
            if (IsConnected && deviceList.Contains('\n'))
            {
                string[] arrayDeviceList = deviceList.Split('\n');
                if (arrayDeviceList.Length == data.Length && data.Length > 0)
                {
                    int resultCode = mx.WriteDeviceRandom2(deviceList, data.Length, ref data[0]);
                    if (resultCode == 0)
                    {
                        Console.WriteLine($"WriteDeviceRandom2 :: Start Address: {arrayDeviceList[0]}, Size: {data.Length}");
                        return true;
                    }
                    else
                    {
                        WriteErrorCodeLog(resultCode);
                    }
                }
                else
                {
                    Console.WriteLine($"WriteDeviceRandom2 :: Size mismatch (Device List: {arrayDeviceList.Length}, Data: {data.Length})");
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        return false;
    }

    /// <summary>
    /// 단일 디바이스(short) 읽기
    /// </summary>
    public short GetDeivce2(string deviceCode, int address)
    {
        try
        {
            if (IsConnected)
            {
                string deviceAddress = deviceCode + address.ToString();
                int resultCode = mx.GetDevice2(deviceAddress, out short data);
                if (resultCode == 0)
                {
                    Console.WriteLine($"GetDevice2 :: Address: {deviceAddress}, Data: {data}");
                    return data;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        return 0;
    }

    /// <summary>
    /// 연속 디바이스 블록 읽기
    /// </summary>
    public short[] ReadDeviceBlock2(string deviceAddress, int size)
    {
        short[] data = new short[size];
        try
        {
            if (IsConnected)
            {
                int resultCode = mx.ReadDeviceBlock2(deviceAddress, size, out data[0]);
                if (resultCode == 0)
                {
                    Console.WriteLine($"Read Device Block2 :: Address: {deviceAddress}, Size: {size}");
                    return data;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        return data;
    }

    /// <summary>
    /// 복수 디바이스 지정하여 랜덤 읽기
    /// </summary>
    public short[] ReadDeviceRandom2(string deviceList, int size)
    {
        short[] data = new short[size];
        try
        {
            if (IsConnected && deviceList.Contains('\n'))
            {
                string[] arrayDeviceList = deviceList.Split('\n');
                if (arrayDeviceList.Length == size && size > 0)
                {
                    int resultCode = mx.ReadDeviceRandom2(deviceList, size, out data[0]);
                    if (resultCode == 0)
                    {
                        Console.WriteLine($"ReadDeviceRandom2 :: Start Address: {arrayDeviceList[0]}, Size: {size}");
                        return data;
                    }
                }
                else
                {
                    Console.WriteLine($"ReadDeviceRandom2 :: Size mismatch (Device List: {arrayDeviceList.Length}, Data: {size})");
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
        }
        return data;
    }

    /// <summary>
    /// 에러 코드 로깅 (16진수 포맷)
    /// </summary>
    private void WriteErrorCodeLog(int errorCode)
    {
        string errorMessage = string.Format("0x{0:x8} [HEX]", errorCode);
        Console.WriteLine(errorMessage);
    }
}
```

> 바로 실무에 사용 가능하다.