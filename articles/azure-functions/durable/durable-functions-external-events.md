---
title: Gestione di eventi esterni in Funzioni permanenti - Azure
description: Informazioni su come gestire gli eventi esterni nell'estensione Funzioni permanenti per Funzioni di Azure.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 3cd04c93d508bd06c4ddd2e05074084202b9fc60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87014940"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Gestione di eventi esterni in Funzioni permanenti (Funzioni di Azure)

Le funzioni di orchestrazione possono rimanere in attesa e in ascolto di eventi esterni. Questa funzionalità di [Funzioni permanenti](durable-functions-overview.md) è spesso utile per gestire l'interazione umana o altri trigger esterni.

> [!NOTE]
> Gli eventi esterni sono operazioni asincrone unidirezionali. Non sono adatti per situazioni in cui il client che invia l'evento necessita di una risposta sincrona da parte dell'agente di orchestrazione.

## <a name="wait-for-events"></a>Attendere eventi

I metodi [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET), `waitForExternalEvent` (JavaScript) e `wait_for_external_event` (Python) dell'associazione del [trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger) consentono a una funzione dell'agente di orchestrazione di attendere in modo asincrono e restare in ascolto di un evento esterno. La funzione di orchestrazione in ascolto dichiara il *nome* dell'evento e la *forma dei dati* che si aspetta di ricevere.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

Nell'esempio precedente è in attesa di un singolo evento specifico ed esegue l'azione quando lo riceve.

È possibile rimanere in ascolto di più eventi in modo simultaneo, come nell'esempio seguente, che è in attesa di una delle tre notifiche degli eventi possibili.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

Nell'esempio precedente è in ascolto di *uno qualsiasi* di più eventi. È anche possibile attendere *tutti* gli eventi.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> Il codice precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

In .NET se il payload dell'evento non può essere convertito nel tipo previsto `T`, viene generata un'eccezione.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` attende indefinitamente un input.  È possibile scaricare l'app per le funzioni in modo sicuro durante l'attesa. Se e quando si riceve un evento per questa istanza di orchestrazione,l'app viene riattivata automaticamente ed elabora immediatamente l'evento.

> [!NOTE]
> Se l'app per le funzioni usa il piano a consumo, non vengono addebitati costi di fatturazione mentre una funzione dell'agente di orchestrazione è in attesa di un'attività da `WaitForExternalEvent` (.NET), `waitForExternalEvent` (JavaScript) o `wait_for_external_event` (Python), indipendentemente dal tempo di attesa.

## <a name="send-events"></a>Inviare eventi

È possibile utilizzare i metodi [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) o `raiseEventAsync` (JavaScript) per inviare un evento esterno a un'orchestrazione. Questi metodi sono esposti dall'associazione del [client di orchestrazione](durable-functions-bindings.md#orchestration-client) . Per inviare un evento esterno a un'orchestrazione, è inoltre possibile utilizzare l' [API HTTP di generazione eventi](durable-functions-http-api.md#raise-event) incorporata.

Un evento generato include un *ID istanza*, un *EventName*e *EventData* come parametri. Le funzioni dell'agente di orchestrazione gestiscono questi eventi usando le `WaitForExternalEvent` API (.NET) o `waitForExternalEvent` (JavaScript). Per poter elaborare l'evento, il *gestore eventi* deve corrispondere sia alle estremità di invio che alla ricezione. Anche i dati dell'evento devono essere serializzabili in JSON.

Internamente, i meccanismi "genera evento" accodano un messaggio che viene prelevato dalla funzione dell'agente di orchestrazione in attesa. Se l'istanza non è in attesa del *nome dell'evento* specificato, il messaggio di evento viene aggiunto a una coda in memoria. Se l'istanza di orchestrazione in seguito inizia l'ascolto per tale *nome dell'evento*, controllerà la presenza dei messaggi di evento nella coda.

> [!NOTE]
> Se non esiste alcuna istanza di orchestrazione con l'*ID istanza* specificato, il messaggio di evento viene rimosso.

Di seguito è riportata una funzione di esempio attivata da coda che invia un evento di "approvazione" a un'istanza della funzione dell'agente di orchestrazione. L'ID istanza di orchestrazione proviene dal corpo del messaggio in coda.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

Internamente, `RaiseEventAsync` (.NET), `raiseEvent` (JavaScript) o `raise_event` (Python) accoda un messaggio che viene prelevato dalla funzione dell'agente di orchestrazione in attesa. Se l'istanza non è in attesa del *nome dell'evento* specificato, il messaggio di evento viene aggiunto a una coda in memoria. Se l'istanza di orchestrazione in seguito inizia l'ascolto per tale *nome dell'evento*, controllerà la presenza dei messaggi di evento nella coda.

> [!NOTE]
> Se non esiste alcuna istanza di orchestrazione con l'*ID istanza* specificato, il messaggio di evento viene rimosso.

### <a name="http"></a>HTTP

Di seguito è riportato un esempio di una richiesta HTTP che genera un evento di "approvazione" per un'istanza di orchestrazione. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

In questo caso, l'ID istanza è hardcoded come *InstanceID*.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come implementare la gestione degli errori](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Eseguire un esempio in attesa di interazione umana](durable-functions-phone-verification.md)