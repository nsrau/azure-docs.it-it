---
title: Orchestrazioni perenni in Funzioni permanenti - Azure
description: Informazioni su come implementare le orchestrazioni perenni usando l'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d96229bb5e3d288915b64e5a7ce29a8651f2a181
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177387"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orchestrazioni perenni in Funzioni permanenti (Funzioni di Azure)

Le *orchestrazioni perenni* sono funzioni di orchestrazione che non terminano mai. Sono utili quando si vuole usare [Funzioni permanenti](durable-functions-overview.md) per gli aggregatori e per qualsiasi scenario che richiede un ciclo infinito.

## <a name="orchestration-history"></a>Cronologia di orchestrazione

Come illustrato nell'argomento relativo alla [cronologia dell'orchestrazione](durable-functions-orchestrations.md#orchestration-history) , il Framework di attività permanenti tiene traccia della cronologia di ogni orchestrazione di funzioni. Questa cronologia aumenta in modo costante finché la funzione dell'agente di orchestrazione continua a pianificare nuovo lavoro. Se la funzione dell'agente di orchestrazione entra in un ciclo infinito e pianifica lavoro in modo continuo, le dimensioni della cronologia potrebbero diventare eccessive con notevoli problemi a livello di prestazioni. Il concetto di *orchestrazione perenne* è stato ideato per ridurre questa tipologia di problemi per le applicazioni che necessitano di un ciclo infinito.

## <a name="resetting-and-restarting"></a>Reimpostazione e riavvio

Anziché usare cicli infiniti, le funzioni di orchestrazione reimpostano lo stato chiamando il metodo [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_). Questo metodo accetta un singolo parametro serializzabile in JSON, che diventa il nuovo input per la generazione delle funzioni di orchestrazione successive.

Quando viene chiamato il metodo `ContinueAsNew`, l'istanza accoda un messaggio a se stessa prima della chiusura. Il messaggio riavvia l'istanza con il nuovo valore di input. Viene mantenuto lo stesso ID istanza, ma la cronologia della funzione dell'agente di orchestrazione viene di fatto troncata.

> [!NOTE]
> Durable Task Framework mantiene lo stesso ID istanza, ma crea internamente un nuovo *ID esecuzione* per la funzione dell'agente di orchestrazione reimpostata da `ContinueAsNew`. Questo ID di esecuzione in genere non viene esposto esternamente, ma può essere utile da conoscere durante il debug dell'esecuzione dell'orchestrazione.

## <a name="periodic-work-example"></a>Esempio di operazioni periodiche

Un caso d'uso di orchestrazioni perenni è il codice che deve eseguire operazioni periodiche per un periodo illimitato.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Funzioni 2.x)

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

La differenza tra questo esempio e una funzione attivata da timer è che i tempi del trigger di pulizia non sono basati su una pianificazione. Ad esempio, una pianificazione CRON che esegue una funzione ogni ora verrà eseguita all'1:00, alle 2:00, alle 3:00 e così via e potrebbe potenzialmente generare problemi di sovrapposizione. In questo esempio, tuttavia, se la pulizia richiede 30 minuti, verrà pianificata all'1:00, alle 2:30, alle 4:00 e così via senza possibilità di sovrapposizione.

## <a name="starting-an-eternal-orchestration"></a>Avvio di un'orchestrazione eterna
Usare il metodo [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) per avviare un'orchestrazione eterna. Questa operazione non è diversa rispetto all'attivazione di qualsiasi altra funzione di orchestrazione.  

> [!NOTE]
> Se è necessario assicurarsi che sia in esecuzione un singleton eterna orchestrazione, è importante mantenere la stessa istanza `id` quando si avvia l'orchestrazione. Per altre informazioni, vedere [Gestione delle istanze](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [OrchestrationClient] DurableOrchestrationClientBase client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Uscire da un'orchestrazione perenne

Se una funzione dell'agente di orchestrazione deve essere completata, è sufficiente *non* chiamare `ContinueAsNew` e consentire l'uscita della funzione.

Se una funzione dell'agente di orchestrazione è in un ciclo infinito e deve essere arrestata, usare il metodo [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) a tale scopo. Per altre informazioni, vedere [Gestione delle istanze](durable-functions-instance-management.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come implementare le orchestrazioni singleton](durable-functions-singletons.md)
