---
title: Gestione degli errori in Funzioni permanenti - Azure
description: Informazioni su come gestire gli errori nell'estensione Funzioni permanenti per le Funzioni di Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0900e3f3b76f4a82e06fe3c0e6d9bbe63b545f56
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231413"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Gestione degli errori in Funzioni permanenti (Funzioni di Azure)

Durable Function orchestrations are implemented in code and can use the programming language's built-in error-handling features. There really aren't any new concepts you need to learn to add error handling and compensation into your orchestrations. È tuttavia opportuno conoscere alcuni comportamenti.

## <a name="errors-in-activity-functions"></a>Errori nelle funzioni di attività

Any exception that is thrown in an activity function is marshaled back to the orchestrator function and thrown as a `FunctionFailedException`. È possibile scrivere il codice di compensazione e gestione degli errori adatto alle esigenze nella funzione dell'agente di orchestrazione.

Ad esempio, si consideri la seguente funzione dell'agente di orchestrazione che consente di trasferire fondi da un account a un altro:

### <a name="precompiled-c"></a>C# precompilato

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
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

public static async Task Run(IDurableOrchestrationContext context)
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

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

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

If the first **CreditAccount** function call fails, the orchestrator function compensates by crediting the funds back to the source account.

## <a name="automatic-retry-on-failure"></a>Ripetizione automatica in caso di errore

Quando si chiamano le funzioni di attività o di orchestrazione secondaria, è possibile specificare un criterio di ripetizione automatica. Nell'esempio seguente si tenta di chiamare una funzione fino a tre volte e si attende 5 secondi tra un tentativo e l'altro:

### <a name="precompiled-c"></a>C# precompilato

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
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
public static async Task Run(IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

L'API `CallActivityWithRetryAsync` (.NET) o `callActivityWithRetry` (JavaScript) accetta un parametro `RetryOptions`. Sub-orchestration calls using the `CallSubOrchestratorWithRetryAsync` (.NET) or `callSubOrchestratorWithRetry` (JavaScript) API can use these same retry policies.

There are several options for customizing the automatic retry policy:

* **Max number of attempts** (Numero massimo di tentativi): il numero massimo di tentativi di ripetizione.
* **First retry interval** (Intervallo primo tentativo): il tempo di attesa prima del primo tentativo.
* **Backoff coefficient** (Coefficiente di backoff): il coefficiente usato per determinare la frequenza di aumento del backoff. Assume il valore predefinito 1.
* **Max retry interval** (Intervallo massimo tra tentativi): il tempo di attesa massimo tra i tentativi di ripetizione.
* **Retry timeout** (Timeout tentativi): il tempo massimo a disposizione per i tentativi. Il comportamento predefinito è la ripetizione per un periodo illimitato.
* **Handle**: A user-defined callback can be specified to determine whether a function should be retried.

## <a name="function-timeouts"></a>Timeout delle funzioni

You might want to abandon a function call within an orchestrator function if it's taking too long to complete. A tale scopo il metodo migliore è creare un [timer permanente](durable-functions-timers.md) usando `context.CreateTimer` (.NET) o `context.df.createTimer` (JavaScript), insieme a `Task.WhenAny` (.NET) o `context.df.Task.any` (JavaScript) come nell'esempio seguente:

### <a name="precompiled-c"></a>C# precompilato

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
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
public static async Task<bool> Run(IDurableOrchestrationContext context)
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

> [!NOTE]
> The previous C# examples are for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

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
> [Learn about eternal orchestrations](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Informazioni su come eseguire la diagnostica dei problemi](durable-functions-diagnostics.md)
