---
title: Gestione degli errori in Funzioni permanenti - Azure
description: Informazioni su come gestire gli errori nell'estensione Funzioni permanenti per le Funzioni di Azure.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 6650322834d491d78470e2d8dbd24e2c6750ae39
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081696"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Gestione degli errori in Funzioni permanenti (Funzioni di Azure)

Le orchestrazioni di funzioni permanenti vengono implementate nel codice e possono utilizzare le funzionalità di gestione degli errori predefinite del linguaggio di programmazione. In realtà non sono presenti nuovi concetti necessari per aggiungere la gestione degli errori e la compensazione alle orchestrazioni. È tuttavia opportuno conoscere alcuni comportamenti.

## <a name="errors-in-activity-functions"></a>Errori nelle funzioni di attività

Qualsiasi eccezione generata in una funzione di attività viene sottoposta a marshalling di nuovo nella funzione dell'agente di orchestrazione e generata come `FunctionFailedException` . È possibile scrivere il codice di compensazione e gestione degli errori adatto alle esigenze nella funzione dell'agente di orchestrazione.

Ad esempio, si consideri la seguente funzione dell'agente di orchestrazione che consente di trasferire fondi da un account a un altro:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

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
> Gli esempi di C# precedenti sono per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```

---

Se la prima chiamata di funzione **CreditAccount** ha esito negativo, la funzione dell'agente di orchestrazione compensa il credito dei fondi all'account di origine.

## <a name="automatic-retry-on-failure"></a>Ripetizione automatica in caso di errore

Quando si chiamano le funzioni di attività o di orchestrazione secondaria, è possibile specificare un criterio di ripetizione automatica. Nell'esempio seguente si tenta di chiamare una funzione fino a tre volte e si attende 5 secondi tra un tentativo e l'altro:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Gli esempi di C# precedenti sono per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

La chiamata della funzione Activity nell'esempio precedente accetta un parametro per la configurazione di un criterio di ripetizione automatica. Per personalizzare i criteri di ripetizione automatica sono disponibili diverse opzioni:

* **Max number of attempts** (Numero massimo di tentativi): il numero massimo di tentativi di ripetizione.
* **First retry interval** (Intervallo primo tentativo): il tempo di attesa prima del primo tentativo.
* **Backoff coefficient** (Coefficiente di backoff): il coefficiente usato per determinare la frequenza di aumento del backoff. Assume il valore predefinito 1.
* **Max retry interval** (Intervallo massimo tra tentativi): il tempo di attesa massimo tra i tentativi di ripetizione.
* **Retry timeout** (Timeout tentativi): il tempo massimo a disposizione per i tentativi. Il comportamento predefinito è la ripetizione per un periodo illimitato.
* **Handle**: è possibile specificare un callback definito dall'utente per determinare se una funzione deve essere ritentata.

## <a name="function-timeouts"></a>Timeout delle funzioni

Potrebbe essere necessario abbandonare una chiamata di funzione all'interno di una funzione dell'agente di orchestrazione se il completamento richiede troppo tempo. Il modo giusto per eseguire questa operazione oggi è creare un [timer durevole](durable-functions-timers.md) usando `context.CreateTimer` (.NET), `context.df.createTimer` (JavaScript) o `context.create_timer` (Python) in combinazione con `Task.WhenAny` (.NET), `context.df.Task.any` (JavaScript) o `context.task_any` (Python), come nell'esempio seguente:

# <a name="c"></a>[C#](#tab/csharp)

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

> [!NOTE]
> Gli esempi di C# precedenti sono per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!NOTE]
> Questo meccanismo non termina effettivamente l'esecuzione della funzione di attività in corso, ma consente semplicemente alla funzione di orchestrazione di ignorare il risultato e continuare. Per altre informazioni, vedere la documentazione sui [Timer](durable-functions-timers.md#usage-for-timeout).

## <a name="unhandled-exceptions"></a>Eccezioni non gestite

Se una funzione dell'agente di orchestrazione ha esito negativo generando un'eccezione non gestita, i dettagli dell'eccezione vengono registrati e l'istanza viene completata con uno stato `Failed`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle orchestrazioni eterne](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Informazioni su come eseguire la diagnostica dei problemi](durable-functions-diagnostics.md)
