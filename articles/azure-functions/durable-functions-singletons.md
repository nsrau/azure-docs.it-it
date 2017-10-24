---
title: Singleton per Funzioni permanenti - Azure
description: Informazioni su come usare i singleton nell'estensione Funzioni permanenti di Funzioni di Azure.
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
ms.openlocfilehash: e82cc53d53a6d0296aaab2c3a76ad4e2f6c12c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Agenti di orchestrazione singleton in Funzioni permanenti (Funzioni di Azure)

Per i processi in background o per orchestrazioni in stile Attore, è spesso necessario garantire che venga eseguita solo un'istanza di un particolare agente di orchestrazione alla volta. In [Funzioni permanenti](durable-functions-overview.md) è possibile assegnare uno specifico ID di istanza a un agente di orchestrazione quando lo si crea.

## <a name="singleton-example"></a>Esempio di singleton

L'esempio C# seguente illustra una funzione di attivazione HTTP che crea un'orchestrazione di processo in background singleton. Usa un ID di istanza noto per verificare che esista una sola istanza.

```cs
[FunctionName("EnsureSingletonTrigger")]
public static async Task<HttpResponseMessage> Ensure(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    // Ensure only one instance is ever running at a time
    const string OrchestratorName = "MySingletonOrchestrator";
    const string InstanceId = "MySingletonInstanceId";

    var existingInstance = await starter.GetStatusAsync(InstanceId);
    if (existingInstance == null)
    {
        log.Info($"Creating singleton instance with ID = {InstanceId}...");
        await starter.StartNewAsync(OrchestratorName, InstanceId, input: null);
    }

    return starter.CreateCheckStatusResponse(req, InstanceId);
}
```

Per impostazione predefinita, gli ID delle istanze sono GUID generati in modo casuale. Si noti in questo caso che la funzione trigger usa una variabile predefinita `InstanceId` con un valore di `MySingletonInstanceId` per pre-assegnare un ID istanza alla funzione dell'agente di orchestrazione. Ciò consente al trigger di verificare se l'istanza nota è già in esecuzione chiamando [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_).

I dettagli di implementazione della funzione dell'agente di orchestrazione non sono rilevanti. Può trattarsi di una funzione di agente di orchestrazione regolare che viene avviata e completata oppure può essere eseguita in modo permanente (si tratta di un'[orchestrazione perenne](durable-functions-eternal-orchestrations.md)). L'aspetto importante è che in esecuzione un'unica istanza alla volta.

> [!NOTE]
> Se l'istanza di orchestrazione singleton termina, ha esito negativo o viene completata, non sarà possibile ricrearla usando lo stesso ID. In questi casi, è necessario essere pronti a ricrearla usando un nuovo ID di istanza.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come chiamare le orchestrazioni secondarie](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Eseguire un singleton di esempio](durable-functions-counter.md)
