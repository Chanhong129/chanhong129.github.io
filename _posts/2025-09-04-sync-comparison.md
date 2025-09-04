---
title: WPF 에서의 동기화 도구 비교
date: 2025-09-04 08:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, lock, SemaphoreSlim, Mutex, Semaphore]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---

# WPF에서의 동기화 도구 비교: `lock`, `SemaphoreSlim`, `Mutex`, `Semaphore`

WPF 애플리케이션 개발에서는 **멀티스레드 환경에서 공유 자원에 대한
동기화**가 필수적입니다. 이를 적절히 처리하지 않으면 데이터 불일치, 교착
상태(Deadlock), UI 프리징 같은 문제가 발생할 수 있습니다.

이번 글에서는 자주 쓰이는 네 가지 동기화 도구를 정리합니다.

-   `lock` (Monitor)
-   `SemaphoreSlim`
-   `Mutex`
-   `Semaphore`

------------------------------------------------------------------------

## 1. `lock` (Monitor)

-   C# 언어 차원에서 제공하는 키워드.
-   내부적으로 `Monitor`를 사용해 **한 번에 하나의 스레드만 임계 구역
    진입**.
-   **프로세스 내부 전용**.

### 사용법

``` csharp
private readonly object _lockObj = new();
private int _counter;

void Increment()
{
    lock (_lockObj)
    {
        _counter++;
    }
}
```

### 장단점 및 결과

-   ✅ **빠르고 가벼움** (커널 전환 없음).
-   ✅ 코드가 단순.
-   ❌ `await` 불가 → 비동기 작업 보호에는 부적합.
-   ❌ 프로세스 간 동기화 불가.

👉 **짧고 빠른 동기화 구역**에서 최적.

------------------------------------------------------------------------

## 2. `SemaphoreSlim`

-   경량화된 세마포어.
-   동시 실행 개수를 제한 가능.
-   **`WaitAsync` 제공** → 비동기 코드에서 자연스럽게 사용 가능.

### 사용법

``` csharp
private readonly SemaphoreSlim _sem = new(1, 1); // 동시 1개만 허용

async Task SaveAsync()
{
    await _sem.WaitAsync();
    try
    {
        await Task.Delay(1000); // DB 저장 시뮬레이션
    }
    finally
    {
        _sem.Release();
    }
}
```

### 장단점 및 결과

-   ✅ **비동기 친화적** (`await` 가능).
-   ✅ 동시 실행 개수를 자유롭게 조정 가능.
-   ❌ `Release` 누락 시 교착 상태 발생 가능.
-   ❌ 프로세스 간 동기화 불가.

👉 **WPF + 비동기 코드**에서는 가장 많이 쓰이는 기본 도구.

------------------------------------------------------------------------

## 3. `Mutex`

-   **Mutual Exclusion** (상호 배제).
-   한 번에 하나만 접근 가능.
-   **Named Mutex**를 사용하면 **프로세스 간 단독 실행** 보장 가능.

### 사용법

``` csharp
bool createdNew;
using var mutex = new Mutex(true, "Global\\MyWpfApp", out createdNew);

if (!createdNew)
{
    Console.WriteLine("이미 실행 중입니다.");
    return;
}

// 단일 실행 보장
```

### 장단점 및 결과

-   ✅ **프로세스 간 동기화 가능**.
-   ✅ 앱 단일 실행 보장에 유용.
-   ❌ 커널 오브젝트 기반 → 상대적으로 무거움.
-   ❌ `await` 불가.

👉 **앱 단일 인스턴스 보장**이나 **파일 단독 접근** 같은 특수한 상황에만
사용.

------------------------------------------------------------------------

## 4. `Semaphore`

-   OS 수준 세마포어.
-   지정된 수만큼 스레드/프로세스 접근 허용.
-   **프로세스 간 동기화 가능**.

### 사용법

``` csharp
var semaphore = new Semaphore(3, 3); // 동시 3개 허용

semaphore.WaitOne();
try
{
    // 작업 처리
}
finally
{
    semaphore.Release();
}
```

### 장단점 및 결과

-   ✅ 프로세스 간 동시 접근 제어 가능.
-   ❌ 커널 오브젝트 기반이라 무거움.
-   ❌ `await` 불가.
-   ❌ 현대 WPF 앱에서는 잘 사용되지 않음.

👉 **레거시 시스템이나 특수한 멀티프로세스 제어**에서만 고려.

------------------------------------------------------------------------

## 최종 정리

|도구|특징|적합한 경우|
|--|--|--|
|**`lock`**|프로세스 내부,가볍고 빠름|짧은 임계 구역 보호|
|**`SemaphoreSlim`**|비동기 지원, 동시성 N개 제한|WPF 비동기 로직 보호|
|**`Mutex`**|단독 점유, 프로세스 간 가능|앱 단일 실행, 파일 단독 접근|
|**`Semaphore`**|프로세스 간, 동시성 N개 제한|레거시/특수 멀티프로세스 상황|
 
------------------------------------------------------------------------

## 결론

-   **동기 코드 보호** → `lock`
-   **비동기 코드 보호** → `SemaphoreSlim`
-   **앱 단일 인스턴스 보장** → `Mutex`
-   **특수한 멀티프로세스 동기화** → `Semaphore`

특히 WPF에서는 **UI 스레드 프리징을 막기 위해
`SemaphoreSlim.WaitAsync`**를 사용하는 습관이 중요합니다.
