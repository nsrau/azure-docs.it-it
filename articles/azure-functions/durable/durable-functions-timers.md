---
title: Timer in Funzioni permanenti - Azure
description: Informazioni su come implementare timer permanenti nell'estensione Funzioni permanenti per Funzioni di Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261484"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timer in Funzioni permanenti (Funzioni di Azure)

[Funzioni permanenti](durable-functions-overview.md) fornisce *timer permanenti* da usare nelle funzioni di orchestrazione per implementare ritardi o per impostare timeout nelle azioni asincrone. I timer permanenti devono essere usati nelle funzioni dell'agente di orchestrazione al posto di `Thread.Sleep` e `Task.Delay` (C#) oppure di `setTimeout()` e `setInterval()` (JavaScript).

Per creare un timer `CreateTimer` durevole, chiamare il `createTimer` metodo (.NET) o il metodo (JavaScript) dell'associazione del trigger di [orchestrazione](durable-functions-bindings.md#orchestration-trigger). Il metodo restituisce un'attività che viene completata in una data e un'ora specificate.

## <a name="timer-limitations"></a>Limitazioni dei timer

Quando si crea un timer che scade alle 16:30, il framework di attività durevole sottostante accoda un messaggio che diventa visibile solo alle 16:30. Quando viene eseguita nel piano consumo di Funzioni di Azure, il messaggio timer appena visibile garantirà che l'app per le funzioni venga attivata in una macchina virtuale appropriata.

> [!NOTE]
> * I timer durevoli sono attualmente limitati a 7 giorni. Se sono necessari ritardi più lunghi, è possibile simularli `while` usando le API del timer in un ciclo.
> * Utilizzare `CurrentUtcDateTime` sempre `DateTime.UtcNow` al posto `currentUtcDateTime` di `Date.now` .NET o anziché o `Date.UTC` in JavaScript quando si calcola il tempo di attivazione per i timer durevoli. Per altre informazioni, vedere l'articolo Vincoli di [codice della funzione dell'agente di orchestrazione.](durable-functions-code-constraints.md)

## <a name="usage-for-delay"></a>Utilizzo per il ritardo

L'esempio seguente illustra come usare i timer permanenti per ritardare l'esecuzione. L'esempio è l'emissione di una notifica di fatturazione ogni giorno per 10 giorni.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Nell'esempio precedente di Cè destinato a Funzioni durevoli 2.x. Per funzioni durevoli 1.x, è necessario utilizzare `DurableOrchestrationContext` al posto di `IDurableOrchestrationContext`. Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

---

> [!WARNING]
> Evitare i cicli infiniti nelle funzioni di orchestrazione. Per informazioni su come implementare scenari di ciclo infinito in modo sicuro ed efficiente, vedere [Orchestrazioni perenni](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Utilizzo per i timeout

Questo esempio illustra come usare i timer permanenti per implementare timeout.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> Nell'esempio precedente di Cè destinato a Funzioni durevoli 2.x. Per funzioni durevoli 1.x, è necessario utilizzare `DurableOrchestrationContext` al posto di `IDurableOrchestrationContext`. Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

---

> [!WARNING]
> Utilizzare `CancellationTokenSource` un (.NET) `cancel()` o chiamare `TimerTask` il restituito (JavaScript) per annullare un timer durevole se il codice non attenderà il completamento. Il framework delle attività permanenti non modificherà lo stato di un'orchestrazione in "completato" fino a quando tutte le attività in sospeso non vengono completate o annullate.

Questo meccanismo di annullamento non termina le esecuzioni di funzione di attività o sottoorchestrazione in corso. ma consente semplicemente alla funzione di orchestrazione di ignorare il risultato e continuare. Se l'app per le funzioni usa il piano Consumo, ti verrà comunque addebitato il tempo e la memoria utilizzati dalla funzione di attività abbandonata. Per impostazione predefinita, le funzioni in esecuzione nel piano a consumo hanno un timeout di cinque minuti. Se questo limite viene superato, l'host di Funzioni di Azure viene riciclato per interrompere ogni esecuzione e impedire una fatturazione eccessiva. Il [timeout delle funzioni è configurabile](../functions-host-json.md#functiontimeout).

Per un esempio più approfondito di come implementare i timeout nelle funzioni dell'agente di orchestrazione, vedere l'articolo [Interazione umana & Timeout - Verifica telefono.](durable-functions-phone-verification.md)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come generare e gestire eventi esterni](durable-functions-external-events.md)
