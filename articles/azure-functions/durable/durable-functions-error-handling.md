---
title: Gestione degli errori in Funzioni permanenti - Azure
description: Informazioni su come gestire gli errori nell'estensione Funzioni permanenti per le Funzioni di Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 5a3cfb78fe97b52abb1406dff64132fc1b3fb985
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933421"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Gestione degli errori in Funzioni permanenti (Funzioni di Azure)

Le orchestrazioni di funzioni permanenti vengono implementate nel codice e possono utilizzare le funzionalità di gestione degli errori predefinite del linguaggio di programmazione. In realtà non sono presenti nuovi concetti necessari per aggiungere la gestione degli errori e la compensazione alle orchestrazioni. È tuttavia opportuno conoscere alcuni comportamenti.

## <a name="errors-in-activity-functions"></a>Errori nelle funzioni di attività

Qualsiasi eccezione generata in una funzione di attività viene sottoposta a marshalling di nuovo nella funzione dell'agente `FunctionFailedException`di orchestrazione e generata come. È possibile scrivere il codice di compensazione e gestione degli errori adatto alle esigenze nella funzione dell'agente di orchestrazione.

Ad esempio, si consideri la seguente funzione dell'agente di orchestrazione che consente di trasferire fondi da un account a un altro:

### <a name="precompiled-c"></a>C# precompilato

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="c-script"></a>Script C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

Se la prima chiamata di funzione **CreditAccount** ha esito negativo, la funzione dell'agente di orchestrazione compensa il credito dei fondi all'account di origine.

## <a name="automatic-retry-on-failure"></a>Ripetizione automatica in caso di errore

Quando si chiamano le funzioni di attività o di orchestrazione secondaria, è possibile specificare un criterio di ripetizione automatica. Nell'esempio seguente si tenta di chiamare una funzione fino a tre volte e si attende 5 secondi tra un tentativo e l'altro:

### <a name="precompiled-c"></a>C# precompilato

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

L'API `CallActivityWithRetryAsync` (.NET) o `callActivityWithRetry` (JavaScript) accetta un parametro `RetryOptions`. Le chiamate di orchestrazione secondarie che usano l'API `CallSubOrchestratorWithRetryAsync` (.NET) o `callSubOrchestratorWithRetry` (JavaScript) possono usare gli stessi criteri di ripetizione dei tentativi.

Per personalizzare i criteri di ripetizione automatica sono disponibili diverse opzioni:

* **Numero massimo di tentativi**: Il massimi numero di tentativi.
* **Primo intervallo tra tentativi**: il tempo di attesa prima del primo tentativo.
* **Coefficiente di backoff**: il coefficiente usato per determinare la frequenza di aumento del backoff. Assume il valore predefinito 1.
* **Intervallo massimo tra tentativi**: il tempo di attesa massimo tra i tentativi di ripetizione.
* **Timeout tentativi**: il tempo massimo a disposizione per i tentativi. Il comportamento predefinito è la ripetizione per un periodo illimitato.
* **Punto di controllo**: È possibile specificare un callback definito dall'utente per determinare se una funzione deve essere ritentata.

## <a name="function-timeouts"></a>Timeout delle funzioni

Potrebbe essere necessario abbandonare una chiamata di funzione all'interno di una funzione dell'agente di orchestrazione se il completamento richiede troppo tempo. A tale scopo il metodo migliore è creare un [timer permanente](durable-functions-timers.md) usando `context.CreateTimer` (.NET) o `context.df.createTimer` (JavaScript), insieme a `Task.WhenAny` (.NET) o `context.df.Task.any` (JavaScript) come nell'esempio seguente:

### <a name="precompiled-c"></a>C# precompilato

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="c-script"></a>Script C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> Questo meccanismo non termina effettivamente l'esecuzione della funzione di attività in corso, ma consente semplicemente alla funzione di orchestrazione di ignorare il risultato e continuare. Per altre informazioni, vedere la documentazione sui [Timer](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Eccezioni non gestite

Se una funzione dell'agente di orchestrazione ha esito negativo generando un'eccezione non gestita, i dettagli dell'eccezione vengono registrati e l'istanza viene completata con uno stato `Failed`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle orchestrazioni eterne](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Informazioni su come eseguire la diagnostica dei problemi](durable-functions-diagnostics.md)
