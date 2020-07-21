---
title: Orchestrazioni perenni in Funzioni permanenti - Azure
description: Informazioni su come implementare le orchestrazioni perenni usando l'estensione Funzioni permanenti per Funzioni di Azure.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 34c70f4305ebb2c45757d982ab558aea6450003f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506367"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orchestrazioni perenni in Funzioni permanenti (Funzioni di Azure)

Le *orchestrazioni perenni* sono funzioni di orchestrazione che non terminano mai. Sono utili quando si vuole usare [Funzioni permanenti](durable-functions-overview.md) per gli aggregatori e per qualsiasi scenario che richiede un ciclo infinito.

## <a name="orchestration-history"></a>Cronologia di orchestrazione

Come illustrato nell'argomento relativo alla [cronologia dell'orchestrazione](durable-functions-orchestrations.md#orchestration-history) , il Framework di attività permanenti tiene traccia della cronologia di ogni orchestrazione di funzioni. Questa cronologia aumenta in modo costante finché la funzione dell'agente di orchestrazione continua a pianificare nuovo lavoro. Se la funzione dell'agente di orchestrazione entra in un ciclo infinito e pianifica lavoro in modo continuo, le dimensioni della cronologia potrebbero diventare eccessive con notevoli problemi a livello di prestazioni. Il concetto di *orchestrazione perenne* è stato ideato per ridurre questa tipologia di problemi per le applicazioni che necessitano di un ciclo infinito.

## <a name="resetting-and-restarting"></a>Reimpostazione e riavvio

Anziché utilizzare cicli infiniti, le funzioni dell'agente di orchestrazione reimpostano il proprio stato chiamando il `ContinueAsNew` Metodo (.NET), `continueAsNew` (JavaScript) o `continue_as_new` (Python) dell' [associazione del trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger). Questo metodo accetta un singolo parametro serializzabile in JSON, che diventa il nuovo input per la generazione delle funzioni di orchestrazione successive.

Quando viene chiamato il metodo `ContinueAsNew`, l'istanza accoda un messaggio a se stessa prima della chiusura. Il messaggio riavvia l'istanza con il nuovo valore di input. Viene mantenuto lo stesso ID istanza, ma la cronologia della funzione dell'agente di orchestrazione viene di fatto troncata.

> [!NOTE]
> Durable Task Framework mantiene lo stesso ID istanza, ma crea internamente un nuovo *ID esecuzione* per la funzione dell'agente di orchestrazione reimpostata da `ContinueAsNew`. Questo ID di esecuzione in genere non viene esposto esternamente, ma può essere utile da conoscere durante il debug dell'esecuzione dell'orchestrazione.

## <a name="periodic-work-example"></a>Esempio di operazioni periodiche

Un caso d'uso di orchestrazioni perenni è il codice che deve eseguire operazioni periodiche per un periodo illimitato.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> L'esempio C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("DoCleanup")

    # sleep for one hour between cleanups
    next_cleanup = context.current_utc_datetime + timedelta(hours = 1)
    yield context.create_timer(next_cleanup)

    context.continue_as_new(None)

main = df.Orchestrator.create(orchestrator_function)
```

---

La differenza tra questo esempio e una funzione attivata da timer è che i tempi del trigger di pulizia non sono basati su una pianificazione. Ad esempio, una pianificazione CRON che esegue una funzione ogni ora verrà eseguita all'1:00, alle 2:00, alle 3:00 e così via e potrebbe potenzialmente generare problemi di sovrapposizione. In questo esempio, tuttavia, se la pulizia richiede 30 minuti, verrà pianificata all'1:00, alle 2:30, alle 4:00 e così via senza possibilità di sovrapposizione.

## <a name="starting-an-eternal-orchestration"></a>Avvio di un'orchestrazione eterna

Usare `StartNewAsync` (.NET), il `startNew` Metodo (JavaScript), `start_new` (Python) per avviare un'orchestrazione eterna, proprio come qualsiasi altra funzione di orchestrazione.  

> [!NOTE]
> Se è necessario assicurarsi che l'orchestrazione di un singleton eterna sia in esecuzione, è importante mantenere la stessa istanza `id` quando si avvia l'orchestrazione. Per altre informazioni, vedere [Gestione delle istanze](durable-functions-instance-management.md).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Il codice precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario utilizzare `OrchestrationClient` l'attributo anziché l' `DurableClient` attributo ed è necessario utilizzare il `DurableOrchestrationClient` tipo di parametro anziché `IDurableOrchestrationClient` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = 'StaticId'

    await client.start_new('Periodic_Cleanup_Loop', instance_id, None)

    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)

```

---

## <a name="exit-from-an-eternal-orchestration"></a>Uscire da un'orchestrazione perenne

Se una funzione dell'agente di orchestrazione deve essere completata, è sufficiente *non* chiamare `ContinueAsNew` e consentire l'uscita della funzione.

Se una funzione dell'agente di orchestrazione si trova in un ciclo infinito e deve essere arrestata, utilizzare il `TerminateAsync` Metodo (.NET), `terminate` (JavaScript) o `terminate` (Python) dell' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client) per arrestarla. Per altre informazioni, vedere [Gestione delle istanze](durable-functions-instance-management.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come implementare le orchestrazioni singleton](durable-functions-singletons.md)
