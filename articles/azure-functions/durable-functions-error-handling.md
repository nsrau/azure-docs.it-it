---
title: Gestione degli errori in Funzioni permanenti - Azure
description: Informazioni su come gestire gli errori nell'estensione Funzioni permanenti per le Funzioni di Azure.
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
ms.openlocfilehash: ee5362d33bb9dadadb4194457cfd7726f4825f56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Gestione degli errori in Funzioni permanenti (Funzioni di Azure)

Le orchestrazioni di Funzioni permanenti vengono implementate nel codice e possono usare le funzionalità di gestione degli errori del linguaggio di programmazione. Tenendo presente questa premessa non è necessario apprendere nuovi concetti quando si incorpora la compensazione e gestione degli errori nelle orchestrazioni. È tuttavia opportuno conoscere alcuni comportamenti.

## <a name="errors-in-activity-functions"></a>Errori nelle funzioni di attività

Qualsiasi eccezione generata in una funzione di attività viene sottoposta a marshalling nella funzione dell'agente di orchestrazione e generata come `TaskFailedException`. È possibile scrivere il codice di compensazione e gestione degli errori adatto alle esigenze nella funzione dell'agente di orchestrazione.

Ad esempio, si consideri la seguente funzione dell'agente di orchestrazione che consente di trasferire fondi da un account a un altro:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        { 
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

Se la chiamata alla funzione **CreditAccount** ha esito negativo per il conto di destinazione, la funzione dell'agente di orchestrazione esegue la compensazione tramite l'accredito di fondi al conto di origine.

## <a name="automatic-retry-on-failure"></a>Ripetizione automatica in caso di errore

Quando si chiamano le funzioni di attività o di orchestrazione secondaria, è possibile specificare un criterio di ripetizione automatica. Nell'esempio seguente si tenta di chiamare una funzione fino a 3 volte e si attende 5 secondi tra un tentativo e l'altro:

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions);
    
    // ...
}
```

L'API `CallActivityWithRetryAsync` accetta un parametro `RetryOptions`. Le chiamate di orchestrazione secondarie che usano l'API `CallSubOrchestratorWithRetryAsync` possono usare gli stessi criteri di ripetizione.

Per personalizzare i criteri di ripetizione automatica, sono disponibili diverse opzioni, incluse le seguenti:

* **Max number of attempts** (Numero massimo di tentativi): il numero massimo di tentativi di ripetizione.
* **First retry interval** (Intervallo primo tentativo): il tempo di attesa prima del primo tentativo.
* **Backoff coefficient** (Coefficiente di backoff): il coefficiente usato per determinare la frequenza di aumento del backoff. Assume il valore predefinito 1.
* **Max retry interval** (Intervallo massimo tra tentativi): il tempo di attesa massimo tra i tentativi di ripetizione.
* **Retry timeout** (Timeout tentativi): il tempo massimo a disposizione per i tentativi. Il comportamento predefinito è la ripetizione per un periodo illimitato.
* **Custom** (Personalizzato): è possibile specificare un callback definito dall'utente che determina se deve essere eseguito un nuovo tentativo di chiamata di funzione.

## <a name="function-timeouts"></a>Timeout delle funzioni

È possibile abbandonare una chiamata di funzione nell'ambito di una funzione dell'agente di orchestrazione, se il completamento richiede troppo tempo. A tale scopo il metodo migliore è creare un [timer permanente](durable-functions-timers.md) usando `context.CreateTimer` in combinazione con `Task.WhenAny`, come nell'esempio seguente:

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

## <a name="unhandled-exceptions"></a>Eccezioni non gestite

Se una funzione dell'agente di orchestrazione ha esito negativo generando un'eccezione non gestita, i dettagli dell'eccezione vengono registrati e l'istanza viene completata con uno stato `Failed`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come eseguire la diagnostica dei problemi](durable-functions-diagnostics.md)
