---
title: WPF Dispatcher - Invoke vs InvokeAsync
date: 2025-09-04 09:00:00 +0900
categories: [Programming, WPF]
tags: [c#, wpf, Dispatcher, invoke, invokeAsync]

#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---
# WPF Dispatcher: `Invoke` vs `InvokeAsync` 완전정복

WPF에선 **UI 요소를 만든 스레드(UI 스레드)**만 그 요소를 만질 수 있습니다.  
백그라운드 스레드에서 UI를 건드리려면 **Dispatcher**를 통해 **UI 스레드 큐에 작업을 등록(마샬링)**해야 해요.  
이 글은 **`Invoke`와 `InvokeAsync`**를 중심으로, *실행 시점*과 *우선순위*, *패턴/함정*까지 한 번에 정리합니다.

---

## TL;DR

- **`Invoke`**: 동기 호출. *“지금 당장 처리해, 난 여기서 기다릴게.”* → 호출 스레드 **블록**. UI가 바쁘면 **기다림**.  
- **`InvokeAsync`**: 비동기 호출. *“메모 남겼어, 시간 날 때 처리해.”* → **즉시 반환**, 필요하면 `await`.  
- **실행 시점**: UI 스레드가 **메시지 펌프(Dispatcher 루프)로 돌아왔을 때** 우선순위에 따라 처리. UI가 긴 작업 중이면 **아무것도 못 함**.

---

## Dispatcher는 무엇? (초간단)

- 스레드마다 0~1개의 Dispatcher가 있으며, **UI 스레드의 Dispatcher**가 화면 작업을 처리합니다.
- 외부(백그라운드)에서 UI를 만지려면 **작업을 Dispatcher 큐에 넣고**, UI 스레드가 한가해질 때 **꺼내 실행**합니다.
- 실행 순서는 **`DispatcherPriority`**로 조절합니다.

> 기억: **단일 UI 스레드**이므로 선점 없음. *“빈틈(펌프 복귀)”*이 있어야 실행됨.

---

## 실행 시점, 정말 쉽게

### `InvokeAsync` (권장)
1. 백그라운드에서 `InvokeAsync` 호출 → **큐에 등록**되고 **즉시 반환**.  
2. UI 스레드가 현재 일을 마치고 펌프로 복귀하면, **우선순위**대로 작업 실행.  
3. `DispatcherOperation.Task`를 `await`하면 **예외/결과/취소**를 관찰 가능.

### `Invoke` (신중)
1. 백그라운드에서 `Invoke` 호출 → **자신(호출자)이 블록**.  
2. UI 스레드가 펌프로 복귀해야 실행됨. UI가 바쁘면 **계속 대기**.  
3. 실행이 끝나야 호출자가 풀림 → **교착/프리징 위험**.

---

## `Invoke` : 언제, 어떻게

### 시그니처(대표)
```csharp
void Dispatcher.Invoke(Action callback);
T    Dispatcher.Invoke<T>(Func<T> callback);
void Dispatcher.Invoke(Action, DispatcherPriority, TimeSpan timeout);
```

### 특징
- 호출 스레드를 **블록**. “지금 결과가 꼭 필요”할 때만.
- **UI 스레드에서 `Invoke`를 호출**하면 **즉시 인라인 실행**(큐에 안 넣고 바로 실행).
- **예외**는 호출 스레드로 **직접 전파**.

### 예시
```csharp
Application.Current.Dispatcher.Invoke(() =>
{
    Title = "즉시 업데이트"; // 호출한 스레드는 여기서 대기
});
```

> ⚠️ **주의**: UI 스레드가 긴 작업으로 막혀 있으면 `Invoke`도 끝나지 않습니다. 데드락의 단골입니다.

---

## `InvokeAsync` : 표준 패턴

### 시그니처(대표)
```csharp
DispatcherOperation InvokeAsync(Action callback);
DispatcherOperation InvokeAsync(Action, DispatcherPriority, CancellationToken);
DispatcherOperation<TResult> InvokeAsync<TResult>(Func<TResult> callback);
```

### 특징
- **즉시 반환**. UI 큐에 넣고 끝.
- 반환값은 `DispatcherOperation` → `await op.Task`로 **예외/결과/취소** 확인 가능.
- **취소 토큰**을 받아 아직 실행 전이면 취소 가능.

### 예시 (값 반환/예외 처리)
```csharp
try
{
    int len = await Application.Current.Dispatcher.InvokeAsync(() =>
    {
        return MyTextBox.Text.Length;
    });
    // len 사용
}
catch (Exception ex)
{
    // UI에서 던진 예외도 여기서 잡힘
}
```

### 예시 (취소)
```csharp
var cts = new CancellationTokenSource();
var op = Application.Current.Dispatcher.InvokeAsync(() => DoUiWork(),
                                                   DispatcherPriority.Background,
                                                   cts.Token);
// 아직 실행 전이라면
cts.Cancel();
try { await op.Task; }
catch (TaskCanceledException) { /* 취소됨 */ }
```

---

## DispatcherPriority, 무엇을 쓰나?

대략 높은 → 낮은 (핵심만):

| Priority | 언제 쓰나 | 메모 |
|---|---|---|
| `Send` | 거의 사용 금지 | 최상위. 재진입/교착 위험 ↑ |
| `Render` | **그리기 직전** 배치/정리 | 레이아웃/시각 반영 직전에 몰아서 적용 |
| `Input` | 입력 반응 우선 | 키/마우스 등 |
| `Normal` | **기본값** | 90% 상황 |
| `Background` | 입력/렌더 먼저, 내 작업 뒤로 | 체감 반응성 ↑ |
| `ApplicationIdle`/`ContextIdle` | 정말 한가할 때 | 후순위 처리 |

> 기본은 **`Normal`**, 렌더링 직전에 묶어 반영하고 싶다면 **`Render`**, 사용자 입력을 우선하고 싶으면 **`Background`**.

---

## 실전 패턴

### 1) 무거운 일은 백그라운드, UI는 마지막에만
```csharp
var result = await Task.Run(DoHeavyWork); // CPU/IO 무거움은 백그라운드

await Application.Current.Dispatcher.InvokeAsync(() =>
{
    // 최소한의 UI 변경만
    ViewModel.Items.Add(result);
});
```

### 2) 렌더 직전에 배치(배칭)하고 재진입 막기
```csharp
await Application.Current.Dispatcher.InvokeAsync(() =>
{
    using (Dispatcher.DisableProcessing()) // 재진입/중간 실행 방지
    {
        UpdateA();
        UpdateB();
        UpdateC();
    }
}, DispatcherPriority.Render);
```

### 3) Fire-and-Forget (필요 시)
```csharp
_ = Application.Current.Dispatcher.InvokeAsync(() =>
{
    // 예: 가벼운 상태표시
    StatusText = "완료";
}, DispatcherPriority.Background);
```

---

## 흔한 함정 & 회피법

- **UI 스레드에서 `.Wait()`/`.Result`**: 메시지 펌프가 멈춰 **데드락** 위험. → **항상 `await`** 사용.
- **`Invoke` 남용**: 호출 스레드를 블록. **정말 짧고 반드시 동기여야 할 때만**.
- **UI에서 긴 작업 실행**: 렌더/입력 끊김. → `Task.Run`으로 떼어내기.
- **`CurrentDispatcher`를 백그라운드에서 호출**: 새 Dispatcher가 생기지만 **펌프를 돌리지 않으면 영원히 실행 안 됨**.  
  → **항상 `Application.Current.Dispatcher`** 명시.
- **모달 대화상자/`DispatcherFrame`**: 내부적으로 **중첩 펌프**를 돌려 **재진입**이 발생할 수 있음. `DisableProcessing()`으로 보호.

---

## 미니 FAQ

**Q. `InvokeAsync`를 넣었는데 왜 실행이 안 되죠?**  
A. UI 스레드가 **긴 작업으로 막혀** 메시지 펌프로 복귀하지 못하고 있을 확률이 큽니다. 긴 작업을 백그라운드로 옮기세요.

**Q. UI 스레드에서 `Invoke`/`InvokeAsync`를 부르면?**  
A. `Invoke`는 **즉시 인라인 실행**. `InvokeAsync`는 **현재 핸들러가 끝난 뒤** 다음 펌프 사이클에 실행.

**Q. `BeginInvoke`와 `InvokeAsync` 차이는?**  
A. 둘 다 비동기. **`InvokeAsync`는 `DispatcherOperation(Task)`로 예외/취소/결과 처리**가 깔끔해 **요즘 권장**.

**Q. 우선순위는 뭘로?**  
A. 기본은 **`Normal`** → 필요 시 `Render`(그리기 직전), `Background`(내 작업 늦추기)만 기억해도 충분.

---

## 체크리스트

- [ ] 결과를 **지금 당장** 받아야 할 때만 `Invoke` (아주 짧게)  
- [ ] 그 외엔 **기본 `InvokeAsync` + `await`**  
- [ ] **긴 작업은 UI에서 금지** → `Task.Run`  
- [ ] 우선순위는 `Normal` 기본, 필요 시 `Render`/`Background`  
- [ ] 재진입/교착 주의: `DisableProcessing()`, 모달·중첩 프레임 주의

---

## 참고용 스니펫 모음

```csharp
// 1) 값 반환
int length = await Application.Current.Dispatcher.InvokeAsync(() => MyTextBox.Text.Length);

// 2) 취소 가능 호출
var cts = new CancellationTokenSource();
var op = Application.Current.Dispatcher.InvokeAsync(UpdateUi, DispatcherPriority.Background, cts.Token);
cts.Cancel();
try { await op.Task; } catch (TaskCanceledException) { /* 취소됨 */ }

// 3) 안전한 백그라운드 → UI
var data = await Task.Run(LoadAsync);
await Application.Current.Dispatcher.InvokeAsync(() => Bind(data));

// 4) 렌더 직전 배칭 + 재진입 방지
await Application.Current.Dispatcher.InvokeAsync(() =>
{
    using (Dispatcher.DisableProcessing())
    {
        ApplyBulkChanges();
    }
}, DispatcherPriority.Render);
```

---

필요하시면 이 글을 **PDF / DOCX**로도 변환해 드립니다. 또한, 프로젝트 구조(MVVM, CommunityToolkit.Mvvm)에 맞춘 **템플릿 코드** 버전도 제공 가능해요.
