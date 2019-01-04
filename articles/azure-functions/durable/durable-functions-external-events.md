---
title: Gestione di eventi esterni in Funzioni permanenti - Azure
description: Informazioni su come gestire gli eventi esterni nell'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 977123459bcf9bb10c6b7ecf5d7a364f60564c48
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437079"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Gestione di eventi esterni in Funzioni permanenti (Funzioni di Azure)

Le funzioni di orchestrazione possono rimanere in attesa e in ascolto di eventi esterni. Questa funzionalità di [Funzioni permanenti](durable-functions-overview.md) è spesso utile per gestire l'interazione umana o altri trigger esterni.

> [!NOTE]
> Gli eventi esterni sono operazioni asincrone unidirezionali. Non sono adatti per situazioni in cui il client che invia l'evento necessita di una risposta sincrona da parte dell'agente di orchestrazione.

## <a name="wait-for-events"></a>Attendere eventi

Il metodo [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) consente a una funzione dell'agente di orchestrazione di rimanere in attesa e in ascolto di un evento esterno in modalità asincrona. La funzione di orchestrazione in ascolto dichiara il *nome* dell'evento e la *forma dei dati* che si aspetta di ricevere.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

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
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

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
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

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

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) attende l'input per un tempo indefinito.  È possibile scaricare l'app per le funzioni in modo sicuro durante l'attesa. Se e quando si riceve un evento per questa istanza di orchestrazione,l'app viene riattivata automaticamente ed elabora immediatamente l'evento.

> [!NOTE]
> Se l'app per le funzioni usa il piano a consumo, non vengono addebitati costi mentre una funzione dell'agente di orchestrazione è in attesa di un'attività da `WaitForExternalEvent` (.NET) o `waitForExternalEvent` (JavaScript), indipendentemente dal tempo di attesa.

In .NET se il payload dell'evento non può essere convertito nel tipo previsto `T`, viene generata un'eccezione.

## <a name="send-events"></a>Inviare eventi

Il metodo [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) invia gli eventi attesi da `WaitForExternalEvent` (.NET) o `waitForExternalEvent` (JavaScript).  Il metodo `RaiseEventAsync` accetta *eventName* e *eventData* come parametri. I dati dell'evento devono essere serializzabile in JSON.

Di seguito è riportata una funzione di esempio attivata da coda che invia un evento di "approvazione" a un'istanza della funzione dell'agente di orchestrazione. L'ID istanza di orchestrazione proviene dal corpo del messaggio in coda.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Internamente `RaiseEventAsync` (.NET) o `raiseEvent` (JavaScript) accoda un messaggio che viene prelevato dalla funzione dell'agente di orchestrazione in attesa.

> [!WARNING]
> Se non è presente un'istanza di orchestrazione con l'*ID istanza* specificato o se l'istanza non è in attesa del *nome evento* specificato, il messaggio dell'evento viene eliminato. Per altre informazioni su questo comportamento, vedere il [problema GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

> [!WARNING]
> Quando si eseguono attività di sviluppo in locale con JavaScript, è necessario impostare la variabile di ambiente `WEBSITE_HOSTNAME` su `localhost:<port>`, ad esempio `localhost:7071` per usare i metodi su `DurableOrchestrationClient`. Per altre informazioni su questo requisito, vedere il [problema descritto in GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come configurare le orchestrazioni perenni](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Eseguire un esempio in attesa di eventi esterni](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Eseguire un esempio in attesa di interazione umana](durable-functions-phone-verification.md)