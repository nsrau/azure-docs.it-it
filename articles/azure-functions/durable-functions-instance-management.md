---
title: Gestire le istanze in Funzioni permanenti - Azure
description: Informazioni su come gestire le istanze nell'estensione Funzioni permanenti per le Funzioni di Azure.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 9cea9b18cd7434a34138d5cecad8a8fd7f10d2e5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Gestire le istanze in Funzioni permanenti (Funzioni di Azure)

Le istanze di orchestrazione di [Funzioni permanenti](durable-functions-overview.md) possono essere avviate e terminate, è possibile eseguire query su esse e inviare a esse eventi di notifica. La gestione delle istanza viene eseguita usando l'[associazione del client di orchestrazione](durable-functions-bindings.md). Questo articolo riporta informazioni dettagliate su ogni operazione di gestione delle istanze.

## <a name="starting-instances"></a>Avvio di istanze

Il metodo [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) di [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) avvia una nuova istanza di una funzione dell'agente di orchestrazione. Le istanze di questa classe possono essere acquisite tramite l'associazione `orchestrationClient`. Internamente, questo metodo accoda un messaggio nella coda di controllo, che poi attiva l'avvio di una funzione con il nome specificato che usa l'associazione del trigger `orchestrationTrigger`. 

L'attività viene completata quando viene avviato il processo di orchestrazione. Il processo di orchestrazione dovrebbe essere avviato entro 30 secondi. Se l'avvio richiede più tempo, viene generata un'eccezione`TimeoutException`. 

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

Il metodo [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) della classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) esegue una query sullo stato di un'istanza di orchestrazione. Accetta `instanceId` (obbligatorio), `showHistory` (facoltativo) e `showHistoryOutput` (facoltativo) come parametri. Se `showHistory` è impostato su `true`, la risposta conterrà la cronologia dell'esecuzione. Se anche `showHistoryOutput` è impostato su `true`, la cronologia dell'esecuzione conterrà gli output delle attività. Il metodo restituisce un oggetto con le proprietà seguenti:

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
* **History**: cronologia di esecuzione dell'orchestrazione. Questo campo viene popolato solo se `showHistory` è impostato su `true`.
    
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

## <a name="wait-for-orchestration-completion"></a>Attendere il completamento dell'orchestrazione

La classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) espone un'API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) che può essere usata per ottenere in modo sincrono l'output effettivo da un'istanza di orchestrazione. Il metodo usa il valore predefinito di 10 secondi per `timeout` e di 1 secondo per `retryInterval` quando non sono impostati.  

Di seguito è riportato un esempio di funzione trigger HTTP che illustra come usare questa API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

La funzione può essere chiamata con la riga seguente usando un timeout di 2 secondi e un intervallo tra tentativi di 0,5 secondi:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

A seconda del tempo necessario per ottenere la risposta dall'istanza di orchestrazione, si verificano due casi:

1. Le istanze di orchestrazione vengono completate entro il timeout definito (in questo caso, 2 secondi) e la risposta è l'output effettivo delle istanze di orchestrazione recapitato in modo sincrono:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. Le istanze di orchestrazione non possono essere completate entro il timeout definito (in questo caso, 2 secondi) e la risposta è quella predefinita illustrata in **Rilevamento dell'URL di API HTTP**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Il formato degli URL webhook può variare in base alla versione dell'host di Funzioni di Azure in esecuzione. L'esempio precedente è per l'host di Funzioni di Azure 2.0.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare le API HTTP per la gestione delle istanze](durable-functions-http-api.md)
