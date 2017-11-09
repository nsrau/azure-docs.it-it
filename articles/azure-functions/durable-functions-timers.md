---
title: Timer in Funzioni permanenti - Azure
description: Informazioni su come implementare timer permanenti nell'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 27312846caf4f51e708b48655578846a52cbd1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timer in Funzioni permanenti (Funzioni di Azure)

[Funzioni permanenti](durable-functions-overview.md) fornisce *timer permanenti* da usare nelle funzioni di orchestrazione per implementare ritardi o per impostare timeout nelle azioni asincrone. I timer permanenti andrebbero usati nelle funzioni di orchestrazione al posto di `Thread.Sleep` o `Task.Delay`.

Si crea un timer permanente chiamando [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_). Il metodo restituisce un'attività che riprende a una data e ora specificata.

## <a name="timer-limitations"></a>Limitazioni dei timer

Quando si crea un timer che scade alle 16:30, il Durable Task Framework sottostante accoda un messaggio che diventa visibile solo alle 16:30. Quando è in uso il piano a consumo di Funzioni di Azure, il nuovo messaggio del timer visibile garantisce che l'app per le funzioni sia attivata in una macchina virtuale appropriata.

> [!WARNING]
> * I timer permanenti non possono durare più di 7 giorni a causa dei limiti di Archiviazione di Azure. Microsoft sta lavorando su una [richiesta di funzionalità per estendere i timer oltre i 7 giorni](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Usare sempre [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) anziché `DateTime.UtcNow` come illustrato negli esempi riportati di seguito durante il calcolo di una scadenza relativa di un timer permanente.

## <a name="usage-for-delay"></a>Utilizzo per il ritardo

L'esempio seguente illustra come usare i timer permanenti per ritardare l'esecuzione. L'esempio emette una notifica di fatturazione ogni giorno per dieci giorni.

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallFunctionAsync("SendBillingEvent");
    }
}
```

> [!WARNING]
> Evitare i cicli infiniti nelle funzioni di orchestrazione. Per informazioni su come implementare scenari di ciclo infinito in modo sicuro ed efficiente, vedere [Orchestrazioni perenni](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Utilizzo per i timeout

Questo esempio illustra come usare i timer permanenti per implementare timeout.

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallFunctionAsync("GetQuote");
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

> [!WARNING]
> Usare un `CancellationTokenSource` per annullare un timer permanente se il codice non ne attenderà il completamento. Durable Task Framework non imposterà come completato lo stato di un'orchestrazione finché tutte le attività in sospeso non sono state completate o annullate.

Questo meccanismo non termina effettivamente l'esecuzione della funzione di attività in corso, ma consente semplicemente alla funzione di orchestrazione di ignorare il risultato e continuare. Se l'app per le funzioni usa il piano a consumo, verranno comunque addebitati il tempo e la memoria utilizzati dalla funzione di attività abbandonata. Per impostazione predefinita, le funzioni in esecuzione nel piano a consumo hanno un timeout di cinque minuti. Se questo limite viene superato, l'host di Funzioni di Azure viene riciclato per interrompere ogni esecuzione e impedire una fatturazione eccessiva. Il [timeout delle funzioni è configurabile](functions-host-json.md#functiontimeout).

Per un esempio dettagliato su come implementare i timeout nelle funzioni di orchestrazione, vedere la procedura dettagliata [Interazione umana e timeout - Verifica telefonica](durable-functions-phone-verification.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come generare e gestire eventi esterni](durable-functions-external-events.md)

