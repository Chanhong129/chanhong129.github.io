---
title: Socket 비동기 채팅 프로그램 with Console
date: 2025-04-30 13:00:00 +0900
categories: [Programming, Communication]
tags: [c#, wpf, socket, async, chat]
description: Async Socket Chat
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

## 구성 요약
- **SocketAsyncEventArgs 없이 간단한** ```async/await``` **패턴 사용**
- 서버: 다중 클라이언트 수용, 메시지 브로드캐스트
- 클라이언트: 메시지 입력, 서버로 전송 + 서버 메시지 수신
- 모두 ```UTF-8``` 인코딩 사용

## 비동기 채팅 서버 (Socket + async)

```cs
// AsyncSocketChatServer.cs
using System.Net;
using System.Net.Sockets;
using System.Text;

class AsyncSocketChatServer
{
    static List<Socket> clients = new();
    static object locker = new();

    static async Task Main()
    {
        var serverSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
        serverSocket.Bind(new IPEndPoint(IPAddress.Any, 9000));
        serverSocket.Listen(100);

        Console.WriteLine("서버 시작됨.");

        while (true)
        {
            Socket clientSocket = await serverSocket.AcceptAsync();
            Console.WriteLine($"클라이언트 연결됨: {clientSocket.RemoteEndPoint}");

            lock (locker) clients.Add(clientSocket);
            _ = Task.Run(() => HandleClientAsync(clientSocket));
        }
    }

    static async Task HandleClientAsync(Socket client)
    {
        byte[] buffer = new byte[1024];

        try
        {
            while (true)
            {
                int received = await client.ReceiveAsync(buffer, SocketFlags.None);
                if (received == 0) break;

                string msg = Encoding.UTF8.GetString(buffer, 0, received);
                Console.WriteLine($"수신: {msg}");

                await BroadcastAsync(client, msg);
            }
        }
        catch (SocketException ex)
        {
            Console.WriteLine($"소켓 예외: {ex.Message}");
        }
        finally
        {
            Console.WriteLine($"클라이언트 연결 종료: {client.RemoteEndPoint}");
            lock (locker) clients.Remove(client);
            client.Close();
        }
    }

    static async Task BroadcastAsync(Socket sender, string message)
    {
        byte[] data = Encoding.UTF8.GetBytes(message);
        List<Task> tasks = new();

        lock (locker)
        {
            foreach (var client in clients)
            {
                if (client != sender)
                    tasks.Add(client.SendAsync(data, SocketFlags.None));
            }
        }

        await Task.WhenAll(tasks);
    }
}
```

## 비동기 채팅 클라이언트 (Socket + async)

```cs
// AsyncSocketChatClient.cs
using System.Net.Sockets;
using System.Text;

class AsyncSocketChatClient
{
    static async Task Main()
    {
        var socket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
        await socket.ConnectAsync("127.0.0.1", 9000);
        Console.WriteLine("서버에 연결됨.");

        _ = Task.Run(async () =>
        {
            byte[] buffer = new byte[1024];
            while (true)
            {
                int received = await socket.ReceiveAsync(buffer, SocketFlags.None);
                if (received == 0) break;

                string msg = Encoding.UTF8.GetString(buffer, 0, received);
                Console.WriteLine(msg);
            }
        });

        while (true)
        {
            string? input = Console.ReadLine();
            if (string.IsNullOrWhiteSpace(input)) continue;

            byte[] data = Encoding.UTF8.GetBytes(input);
            await socket.SendAsync(data, SocketFlags.None);
        }
    }
}
```

## 장점 요약
- ```Socket``` 클래스 그대로 사용하면서도 ```async/await``` 기반이라 **UI 스레드랑도 잘 맞음**
- ```AcceptAsync```, ```ReceiveAsync```, ```SendAsync```로 확장성 확보
- ```SocketAsyncEventArgs``` 보다 간단해서 유지보수 편함