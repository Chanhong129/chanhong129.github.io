---
title: 나만의 Siemens PLC Class 작성
date: 2025-04-29 12:00:00 +0900
categories: [Programming, Communication]
tags: [c#, wpf, siemens, plc]
description: Siemens PLC Class
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

> 바로 실무에 적용 가능한 Simens PLC Class

```cs
using S7.Net;
using System;
using System.Collections.Generic;
using System.Net.NetworkInformation;
using System.Threading;
using System.Threading.Tasks;

namespace PNEProxy.PLC
{
    public enum CpuType { S7200, Logo0BA8, S7200Smart, S7300 = 10, S7400 = 20, S71200 = 30, S71500 = 40 }
    public enum DataType { Counter = 28, Timer = 29, Input = 129, Output = 130, Memory = 131, DataBlock = 132 }
    public enum VarType { Bit, Byte, Word, DWord, Int, DInt, Real, LReal, String, S7String, S7WString, Timer, Counter, DateTime, DateTimeLong }
    public enum ErrorCode { NoError, WrongCPU_Type, ConnectionError, IPAddressNotAvailable, WrongVarFormat = 10, WrongNumberReceivedBytes = 11, SendDataError = 20, ReadDataError = 30, WriteDataError = 50 }

    public class SiemensPLC
    {
        public ErrorCode LastErrorCode { get; set; }
        public string LastErrorString { get; set; }

        private Plc mPlc;
        private S7.Net.CpuType mCpuType;
        private string mIP;
        private short mRack;
        private short mSlot;

        public SiemensPLC() { }

        /// <summary>
        /// PLC 연결을 초기화합니다.
        /// </summary>
        public bool Initialize(CpuType cpuType, string ip, short rack, short slot, int port = 102)
        {
            try
            {
                mCpuType = (S7.Net.CpuType)Enum.Parse(typeof(S7.Net.CpuType), cpuType.ToString());
                mIP = ip;
                mRack = rack;
                mSlot = slot;
                mPlc = new Plc(mCpuType, mIP, port, mRack, mSlot)
                {
                    ReadTimeout = 1000,
                    WriteTimeout = 1000
                };
                return true;
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
            return false;
        }

        /// <summary>
        /// PLC에 비동기 연결을 시도합니다.
        /// </summary>
        public async Task<ErrorCode> Open()
        {
            try
            {
                var cts = new CancellationTokenSource(TimeSpan.FromSeconds(1));
                await Task.WhenAny(mPlc?.OpenAsync(cts.Token), Task.Delay(TimeSpan.FromSeconds(1)));
                return mPlc?.IsConnected == true ? ErrorCode.NoError : ErrorCode.ConnectionError;
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
                return ErrorCode.ConnectionError;
            }
        }

        /// <summary>
        /// PLC 연결을 종료합니다.
        /// </summary>
        public bool Close()
        {
            mPlc?.Close();
            return true;
        }

        /// <summary>
        /// 현재 PLC와의 연결 상태를 반환합니다.
        /// </summary>
        public bool? IsConnected() => mPlc?.IsConnected;

        /// <summary>
        /// PLC IP로 Ping을 보내 연결 가능 여부를 확인합니다.
        /// </summary>
        public bool IsPingSuccessful()
        {
            try
            {
                Ping ping = new Ping();
                var reply = ping.Send(mIP, 500);
                if (reply?.Status == IPStatus.Success)
                    return true;
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
            if (IsConnected() == true) Close();
            return false;
        }

        /// <summary>
        /// PLC에서 변수를 읽습니다. (제네릭 지원)
        /// </summary>
        public T Read<T>(string variable)
        {
            try
            {
                var result = mPlc?.Read(variable);
                return (T)Convert.ChangeType(result, typeof(T));
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
                return default;
            }
        }

        /// <summary>
        /// PLC에서 여러 바이트를 읽습니다.
        /// </summary>
        public byte[] ReadBytes(DataType dataType, int db, int startByteAdr, int count)
        {
            try
            {
                return mPlc?.ReadBytes((S7.Net.DataType)Enum.Parse(typeof(S7.Net.DataType), dataType.ToString()), db, startByteAdr, count);
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
                return Array.Empty<byte>();
            }
        }

        /// <summary>
        /// PLC 데이터 블록을 클래스 객체로 읽습니다.
        /// </summary>
        public T ReadClass<T>(int db, int startByteAdr = 0) where T : class
        {
            try
            {
                return mPlc?.ReadClass<T>(db, startByteAdr);
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
                return null;
            }
        }

        /// <summary>
        /// PLC에 데이터를 씁니다.
        /// </summary>
        public ErrorCode Write(string variable, object value, VarType varType = VarType.String)
        {
            try
            {
                var typedValue = ConvertToTargetType((S7.Net.VarType)Enum.Parse(typeof(S7.Net.VarType), varType.ToString()), value);
                mPlc?.Write(variable, typedValue);
                return ErrorCode.NoError;
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
                return ErrorCode.WriteDataError;
            }
        }

        /// <summary>
        /// PLC에 바이트 배열을 씁니다.
        /// </summary>
        public ErrorCode WriteBytes(DataType dataType, int db, int startByteAdr, byte[] value)
        {
            try
            {
                mPlc?.WriteBytes((S7.Net.DataType)Enum.Parse(typeof(S7.Net.DataType), dataType.ToString()), db, startByteAdr, value);
                return ErrorCode.NoError;
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
                return ErrorCode.WriteDataError;
            }
        }

        /// <summary>
        /// 여러 변수(데이터아이템)를 동시에 읽습니다.
        /// </summary>
        public List<byte[]> ReadMultipleVars(List<DataItem> dataItems)
        {
            var result = new List<byte[]>();
            try
            {
                List<S7.Net.Types.DataItem> _dataItems = new List<S7.Net.Types.DataItem>();
                foreach (var item in dataItems)
                {
                    _dataItems.Add(new S7.Net.Types.DataItem
                    {
                        DataType = (S7.Net.DataType)Enum.Parse(typeof(S7.Net.DataType), item.DataType.ToString()),
                        DB = item.DB,
                        StartByteAdr = item.StartByteAdr,
                        Count = item.Count,
                        VarType = (S7.Net.VarType)Enum.Parse(typeof(S7.Net.VarType), item.VarType.ToString()),
                        BitAdr = item.BitAdr
                    });
                }
                mPlc.ReadMultipleVars(_dataItems);
                foreach (var item in _dataItems)
                {
                    result.Add(item.Value as byte[]);
                }
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
            return result;
        }

        /// <summary>
        /// 여러 변수(데이터아이템)를 동시에 씁니다.
        /// </summary>
        public ErrorCode WriteMultipleVars(List<DataItem> dataItems)
        {
            try
            {
                List<S7.Net.Types.DataItem> _dataItems = new List<S7.Net.Types.DataItem>();
                foreach (var item in dataItems)
                {
                    object converted = ConvertToTargetType((S7.Net.VarType)Enum.Parse(typeof(S7.Net.VarType), item.VarType.ToString()), item.Value);

                    _dataItems.Add(new S7.Net.Types.DataItem
                    {
                        DataType = (S7.Net.DataType)Enum.Parse(typeof(S7.Net.DataType), item.DataType.ToString()),
                        DB = item.DB,
                        StartByteAdr = item.StartByteAdr,
                        Count = item.Count,
                        VarType = (S7.Net.VarType)Enum.Parse(typeof(S7.Net.VarType), item.VarType.ToString()),
                        BitAdr = item.BitAdr,
                        Value = converted
                    });
                }
                mPlc.Write(_dataItems.ToArray());
                return ErrorCode.NoError;
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
                return ErrorCode.WriteDataError;
            }
        }

        /// <summary>
        /// 내부에서 데이터 타입에 따라 값을 변환합니다.
        /// </summary>
        private object ConvertToTargetType(S7.Net.VarType varType, object value)
        {
            switch (varType)
            {
                case S7.Net.VarType.Bit:
                    return value.ToString() == "1" ? true : value.ToString() == "0" ? false : bool.Parse(value.ToString());
                case S7.Net.VarType.Byte:
                    return byte.Parse(value.ToString());
                case S7.Net.VarType.Word:
                case S7.Net.VarType.Int:
                    return short.Parse(value.ToString());
                case S7.Net.VarType.DWord:
                case S7.Net.VarType.DInt:
                    return int.Parse(value.ToString());
                case S7.Net.VarType.Real:
                    return float.Parse(value.ToString());
                case S7.Net.VarType.LReal:
                    return double.Parse(value.ToString());
                default:
                    return value;
            }
        }
    }

    public class DataItem
    {
        public DataType DataType { get; set; }
        public VarType VarType { get; set; }
        public int DB { get; set; }
        public int StartByteAdr { get; set; }
        public int Count { get; set; }
        public byte BitAdr { get; set; }
        public object Value { get; set; }

        public DataItem() { }
    }
}
```