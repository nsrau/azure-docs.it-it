---
title: Singleton per Funzioni permanenti - Azure
description: Informazioni su come usare i singleton nell'estensione Funzioni permanenti di Funzioni di Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 8c12e0ab854bb2b5764dd326e3f1649202f6f16b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232810"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Agenti di orchestrazione singleton in Funzioni permanenti (Funzioni di Azure)

Per i processi in background, è spesso necessario assicurarsi che venga eseguita una sola istanza di un determinato agente di orchestrazione alla volta. È possibile garantire questo tipo di comportamento singleton in [Durable Functions](durable-functions-overview.md) assegnando un ID istanza specifico a un agente di orchestrazione durante la creazione.

## <a name="singleton-example"></a>Esempio di singleton

Nell'esempio seguente viene illustrata una funzione trigger HTTP che crea un'orchestrazione di processi in background singleton. Il codice garantisce l'esistenza di una sola istanza per un ID istanza specificato.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
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

> [!NOTE]
> Il codice C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `OrchestrationClient` attributo anziché l'attributo `DurableClient` ed è necessario usare il tipo di parametro `DurableOrchestrationClient` anziché `IDurableOrchestrationClient`. Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

### <a name="javascript-functions-20-only"></a>JavaScript (solo Funzioni 2.0)

Ecco il file function.json:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Ecco il codice JavaScript:
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Per impostazione predefinita, gli ID delle istanze sono GUID generati in modo casuale. Nell'esempio precedente, tuttavia, l'ID istanza viene passato nei dati della route dall'URL. Il codice chiama `GetStatusAsync`(C#) o `getStatus` (JavaScript) per verificare se un'istanza con l'ID specificato è già in esecuzione. Se tale istanza non è in esecuzione, viene creata una nuova istanza con tale ID.

> [!NOTE]
> In questo esempio c'è una possibile race condition. Se due istanze di **HttpStartSingle** vengono eseguite contemporaneamente, entrambe le chiamate di funzione segnaleranno l'esito positivo, ma verrà effettivamente avviata solo un'istanza dell'orchestrazione. A seconda dei requisiti, questo potrebbe avere effetti indesiderati. Per questo motivo è importante assicurarsi che due richieste non possano eseguire questa funzione trigger contemporaneamente.

I dettagli di implementazione della funzione dell'agente di orchestrazione non sono realmente importanti. Può trattarsi di una funzione di agente di orchestrazione regolare che viene avviata e completata oppure può essere eseguita in modo permanente (si tratta di un'[orchestrazione perenne](durable-functions-eternal-orchestrations.md)). L'aspetto importante è che in esecuzione un'unica istanza alla volta.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle funzionalità HTTP native delle orchestrazioni](durable-functions-http-features.md)
