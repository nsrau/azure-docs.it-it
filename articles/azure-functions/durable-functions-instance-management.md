---
title: Gestire le istanze in Funzioni permanenti - Azure
description: Informazioni su come gestire le istanze nell'estensione Funzioni permanenti per le Funzioni di Azure.
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
<<<<<<< HEAD
ms.openlocfilehash: cbf7731c0faa82ebd3e662eb6d2a8fb0acd65c97
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
=======
ms.openlocfilehash: a938e5949896ad3bfa91903106d56ccdf827c725
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
>>>>>>> 31972e77aa0784c49b3de1fde20ec0a5e3007025
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Gestire le istanze in Funzioni permanenti (Funzioni di Azure)

Le istanze di orchestrazione di [Funzioni permanenti](durable-functions-overview.md) possono essere avviate e terminate, è possibile eseguire query su esse e inviare a esse eventi di notifica. La gestione delle istanza viene eseguita usando l'[associazione del client di orchestrazione](durable-functions-bindings.md). Questo articolo riporta informazioni dettagliate su ogni operazione di gestione delle istanze.

## <a name="starting-instances"></a>Avvio di istanze

Il metodo [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) di [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) avvia una nuova istanza di una funzione dell'agente di orchestrazione. Le istanze di questa classe possono essere acquisite tramite l'associazione `orchestrationClient`. Internamente, questo metodo accoda un messaggio nella coda di controllo, che poi attiva l'avvio di una funzione con il nome specificato che usa l'associazione del trigger `orchestrationTrigger`.

I parametri per [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) sono i seguenti:

* **Nome**: il nome della funzione dell'agente di orchestrazione da pianificare.
* **Input**: dati serializzabili in JSON che devono essere passati come input alla funzione dell'agente di orchestrazione.
* **InstanceId**: (facoltativo) l'ID univoco dell'istanza. Se non è specificato, viene generato un ID istanza casuale.

Di seguito è riportato un semplice esempio in C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

Per i linguaggi non .NET, l'associazione dell'output della funzione può essere utilizzata anche per avviare nuove istanze. In questo caso può essere usato qualsiasi oggetto serializzabile in JSON che presenta i tre parametri indicati sopra come campi. Ad esempio, si consideri la seguente funzione Node.js:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> È consigliabile usare un identificatore casuale per l'ID istanza. Questo contribuisce a garantire una distribuzione del carico equa quando si ridimensionano le funzioni dell'agente di orchestrazione tra più VM. Il momento opportuno per usare ID istanza non casuali è quando l'ID deve provenire da un'origine esterna o quando si implementa il modello [agente di orchestrazione singleton](durable-functions-singletons.md).

## <a name="querying-instances"></a>Esecuzione di query sulle istanze

Il metodo [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) esegue una query sullo stato di un'istanza di orchestrazione. Accetta `instanceId` come parametro e restituisce un oggetto con le proprietà seguenti:

* **Name**: il nome della funzione dell'agente di orchestrazione.
* **InstanceId**: l'ID istanza dell'orchestrazione (deve essere lo stesso dell'input `instanceId`).
* **CreatedTime**: l'ora in cui la funzione dell'agente di orchestrazione ha iniziato l'esecuzione.
* **LastUpdatedTime**: l'ora in cui l'orchestrazione ha eseguito l'ultimo checkpoint.
* **Input**: l'input della funzione come valore JSON.
* **Output**: l'output della funzione come valore JSON (se la funzione è stata completata). Se l'esecuzione della funzione dell'agente di orchestrazione non è riuscita, questa proprietà include i dettagli dell'errore. Se la funzione dell'agente di orchestrazione è stata terminata, questa proprietà include il motivo dell'interruzione (se presente).
* **RuntimeStatus**: uno dei valori seguenti:
    * **Running**: l'istanza ha iniziato l'esecuzione.
    * **Completed**: l'istanza è stata completata normalmente.
    * **ContinuedAsNew**: l'istanza si è riavviata con una nuova cronologia. Si tratta di uno stato temporaneo.
    * **Failed**: l'esecuzione dell'istanza non è riuscita e ha generato un errore.
    * **Terminated**: l'istanza è stata terminata in modo anomalo.
    
Questo metodo restituisce `null` se l'istanza non esiste o non è ancora iniziata la sua esecuzione.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Le query di istanza sono supportate attualmente solo per le funzioni dell'agente di orchestrazione di C#.

## <a name="terminating-instances"></a>Terminazione delle istanze

Un'istanza in esecuzione può essere terminata usando il metodo [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). I due parametri sono un `instanceId` e una stringa `reason` che saranno scritte nei log e nello stato dell'istanza. Un'istanza terminata termina la sua esecuzione non appena raggiunge il successivo punto `await` oppure termina immediatamente se è già in un `await`.

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> La terminazione delle istanze è supportata attualmente solo per le funzioni dell'agente di orchestrazione di C#.

## <a name="sending-events-to-instances"></a>Invio di eventi alle istanze

È possibile inviare notifiche degli eventi alle istanze in esecuzione usando il metodo [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Le istanze che possono gestire questi eventi sono quelle in attesa di una chiamata di [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

I parametri per [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) sono i seguenti:

* **InstanceId**: l'ID univoco dell'istanza.
* **EventName**: il nome dell'evento da inviare.
* **EventData**: un payload serializzabile in JSON da inviare all'istanza.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> La generazione di eventi è supportata attualmente solo per le funzioni dell'agente di orchestrazione di C#.

> [!WARNING]
> Se non è presente un'istanza di orchestrazione con l'*ID istanza* specificato o se l'istanza non è in attesa del *nome evento* specificato, il messaggio dell'evento viene eliminato. Per altre informazioni su questo comportamento, vedere il [problema GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare le API HTTP per la gestione delle istanze](durable-functions-http-api.md)
