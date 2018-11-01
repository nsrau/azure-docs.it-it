---
title: Singleton per Funzioni permanenti - Azure
description: Informazioni su come usare i singleton nell'estensione Funzioni permanenti di Funzioni di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 58e5b06d613ee3e3311b58af64abd2411c637449
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50084442"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Agenti di orchestrazione singleton in Funzioni permanenti (Funzioni di Azure)

Per i processi in background o per orchestrazioni in stile Attore, è spesso necessario garantire che venga eseguita solo un'istanza di un particolare agente di orchestrazione alla volta. In [Funzioni permanenti](durable-functions-overview.md) è possibile assegnare uno specifico ID di istanza a un agente di orchestrazione quando lo si crea.

## <a name="singleton-example"></a>Esempio di singleton

L'esempio C# seguente illustra una funzione di attivazione HTTP che crea un'orchestrazione di processo in background singleton. Il codice garantisce l'esistenza di una sola istanza per un ID istanza specificato.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

Per impostazione predefinita, gli ID delle istanze sono GUID generati in modo casuale. In questo caso, l'ID istanza viene passato ai dati della route dall'URL. Il codice chiama [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) per verificare se un'istanza con l'ID specificato è già in esecuzione. In caso contrario, viene creata un'istanza con tale ID.

> [!NOTE]
> In questo esempio c'è una possibile race condition. Se due istanze di **HttpStartSingle** sono eseguite contemporaneamente, il risultato potrebbe essere la creazione di due istanze diverse del singleton, una che sovrascrive l'altra. A seconda dei requisiti, questo potrebbe avere effetti indesiderati. Per questo motivo è importante assicurarsi che due richieste non possano eseguire questa funzione trigger contemporaneamente.

I dettagli di implementazione della funzione dell'agente di orchestrazione non sono rilevanti. Può trattarsi di una funzione di agente di orchestrazione regolare che viene avviata e completata oppure può essere eseguita in modo permanente (si tratta di un'[orchestrazione perenne](durable-functions-eternal-orchestrations.md)). L'aspetto importante è che in esecuzione un'unica istanza alla volta.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come chiamare le orchestrazioni secondarie](durable-functions-sub-orchestrations.md)
