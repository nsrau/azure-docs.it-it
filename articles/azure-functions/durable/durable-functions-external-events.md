---
title: Gestione di eventi esterni in Funzioni permanenti - Azure
description: Informazioni su come gestire gli eventi esterni nell'estensione Funzioni permanenti per Funzioni di Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 7d7fcc725d78b24a93b09cb9c76cf7dc0231cac2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232887"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Gestione di eventi esterni in Funzioni permanenti (Funzioni di Azure)

Le funzioni di orchestrazione possono rimanere in attesa e in ascolto di eventi esterni. Questa funzionalità di [Funzioni permanenti](durable-functions-overview.md) è spesso utile per gestire l'interazione umana o altri trigger esterni.

> [!NOTE]
> Gli eventi esterni sono operazioni asincrone unidirezionali. Non sono adatti per situazioni in cui il client che invia l'evento necessita di una risposta sincrona da parte dell'agente di orchestrazione.

## <a name="wait-for-events"></a>Attendere eventi

The `WaitForExternalEvent` (.NET) and `waitForExternalEvent` (JavaScript) methods of the [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger) allows an orchestrator function to asynchronously wait and listen for an external event. La funzione di orchestrazione in ascolto dichiara il *nome* dell'evento e la *forma dei dati* che si aspetta di ricevere.

### <a name="c"></a>C#

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
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

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

Nell'esempio precedente è in attesa di un singolo evento specifico ed esegue l'azione quando lo riceve.

È possibile rimanere in ascolto di più eventi in modo simultaneo, come nell'esempio seguente, che è in attesa di una delle tre notifiche degli eventi possibili.

#### <a name="c"></a>C#

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
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

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

Nell'esempio precedente è in ascolto di *uno qualsiasi* di più eventi. È anche possibile attendere *tutti* gli eventi.

#### <a name="c"></a>C#

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
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

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

`WaitForExternalEvent` waits indefinitely for some input.  È possibile scaricare l'app per le funzioni in modo sicuro durante l'attesa. Se e quando si riceve un evento per questa istanza di orchestrazione,l'app viene riattivata automaticamente ed elabora immediatamente l'evento.

> [!NOTE]
> Se l'app per le funzioni usa il piano a consumo, non vengono addebitati costi mentre una funzione dell'agente di orchestrazione è in attesa di un'attività da `WaitForExternalEvent` (.NET) o `waitForExternalEvent` (JavaScript), indipendentemente dal tempo di attesa.

In .NET se il payload dell'evento non può essere convertito nel tipo previsto `T`, viene generata un'eccezione.

## <a name="send-events"></a>Inviare eventi

The `RaiseEventAsync` (.NET) or `raiseEvent` (JavaScript) method of the [orchestration client binding](durable-functions-bindings.md#orchestration-client) sends the events that `WaitForExternalEvent` (.NET) or `waitForExternalEvent` (JavaScript) waits for.  Il metodo `RaiseEventAsync` accetta *eventName* e *eventData* come parametri. I dati dell'evento devono essere serializzabile in JSON.

Di seguito è riportata una funzione di esempio attivata da coda che invia un evento di "approvazione" a un'istanza della funzione dell'agente di orchestrazione. L'ID istanza di orchestrazione proviene dal corpo del messaggio in coda.

### <a name="c"></a>C#

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
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Internamente `RaiseEventAsync` (.NET) o `raiseEvent` (JavaScript) accoda un messaggio che viene prelevato dalla funzione dell'agente di orchestrazione in attesa. Se l'istanza non è in attesa del *nome dell'evento* specificato, il messaggio di evento viene aggiunto a una coda in memoria. Se l'istanza di orchestrazione in seguito inizia l'ascolto per tale *nome dell'evento*, controllerà la presenza dei messaggi di evento nella coda.

> [!NOTE]
> Se non esiste alcuna istanza di orchestrazione con l'*ID istanza* specificato, il messaggio di evento viene rimosso.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Learn how to implement error handling](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Eseguire un esempio in attesa di interazione umana](durable-functions-phone-verification.md)