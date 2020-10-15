---
title: Hub attività in Funzioni permanenti - Azure
description: Informazioni sugli hub attività nell'estensione Funzioni permanenti per Funzioni di Azure. Informazioni su come configurare gli hub attività.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87432706"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hub attività in Funzioni permanenti (Funzioni di Azure)

Un *hub attività* in [Funzioni permanenti](durable-functions-overview.md) è un contenitore logico per le risorse di Archiviazione di Microsoft Azure che vengono usate per le orchestrazioni. Le funzioni attività e di orchestrazione possono interagire tra loro solo quando appartengono allo stesso hub attività.

Se molteplici app per le funzioni condividono un account di archiviazione, ogni app per le funzioni *deve* essere configurata con un nome di hub attività separato. Un account di archiviazione può contenere molteplici hub attività. Nel diagramma seguente è illustrato un hub attività per app per le funzioni in account di archiviazione condivisi e dedicati.

![Diagramma che illustra gli account di archiviazione condivisi e dedicati.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Risorse di archiviazione di Azure

Un hub di attività è costituito dalle risorse di archiviazione seguenti:

* Una o più code di controllo.
* Una coda di elementi di lavoro.
* Una tabella di cronologia.
* Una tabella di istanze.
* Un contenitore di archiviazione che contiene uno o più BLOB del lease.
* Contenitore di archiviazione contenente payload di messaggi di grandi dimensioni, se applicabile.

Tutte queste risorse vengono create automaticamente nell'account di archiviazione di Azure predefinito quando le funzioni di orchestrazione, entità o attività vengono eseguite o pianificate per l'esecuzione. Nell'articolo [Prestazioni e scalabilità](durable-functions-perf-and-scale.md) viene descritto l'uso di queste risorse.

## <a name="task-hub-names"></a>Nomi degli hub attività

Gli hub attività sono identificati da un nome conforme alle regole seguenti:

* Contiene solo caratteri alfanumerici
* Inizia con una lettera
* Ha una lunghezza minima di 3 caratteri, lunghezza massima di 45 caratteri

Il nome dell'hub attività viene dichiarato nella *host.jssu* file, come illustrato nell'esempio seguente:

### <a name="hostjson-functions-20"></a>host.json (funzioni 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json (funzioni 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Gli hub attività possono anche essere configurati usando le impostazioni dell'app, come illustrato nel `host.json` file di esempio seguente:

### <a name="hostjson-functions-10"></a>host.json (funzioni 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (funzioni 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

Il nome dell'hub attività verrà impostato in base al valore dell’impostazione dell’app `MyTaskHub`. La seguente impostazione `local.settings.json` spiega come configurare l’impostazione `MyTaskHub` su `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Il codice seguente illustra come scrivere una funzione che usa l' [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client) per usare un hub attività configurato come impostazione dell'app:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> L'esempio C# precedente è per Durable Functions 2. x. Per Durable Functions 1. x, è necessario usare `DurableOrchestrationContext` anziché `IDurableOrchestrationContext` . Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

La proprietà dell'hub attività nel file `function.json` viene configurata tramite Impostazione app:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

La proprietà dell'hub attività nel file `function.json` viene configurata tramite Impostazione app:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, verrà usato un nome predefinito dell'hub attività, come illustrato nella tabella seguente:

| Versione durevole dell'estensione | Nome dell'hub attività predefinito |
| - | - |
| 2.x | Quando viene distribuito in Azure, il nome dell'hub attività viene derivato dal nome dell' _app_per le funzioni. Quando viene eseguito all'esterno di Azure, il nome dell'hub attività predefinito è `TestHubName` . |
| 1.x | Il nome predefinito dell'hub attività per tutti gli ambienti è `DurableFunctionsHub` . |

Per ulteriori informazioni sulle differenze tra le versioni delle estensioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

> [!NOTE]
> Il nome è ciò che distingue un hub attività da un altro quando sono presenti più hub attività in un account di archiviazione condiviso. Se si dispone di più app per le funzioni che condividono lo stesso account di archiviazione condiviso, è necessario configurare in modo esplicito nomi diversi per ogni hub attività nei file *host.json*. In caso contrario, le app per più funzioni competono tra loro per i messaggi, che potrebbero causare un comportamento indefinito, incluse le orchestrazioni che si bloccano in modo imprevisto nello `Pending` `Running` stato o.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni delle orchestrazioni](durable-functions-versioning.md)
