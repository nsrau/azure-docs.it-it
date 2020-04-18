---
title: Hub attività in Funzioni permanenti - Azure
description: Informazioni sugli hub attività nell'estensione Funzioni permanenti per Funzioni di Azure. Informazioni su come configurare gli hub attività.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 427ab6c4e0e769ab881af0af3023d514c1b092c6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604603"
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

Tutte queste risorse vengono create automaticamente nell'account di archiviazione di Azure predefinito quando vengono eseguite o pianificate l'esecuzione di funzioni dell'agente di orchestrazione, entità o attività. Nell'articolo [Prestazioni e scalabilità](durable-functions-perf-and-scale.md) viene descritto l'uso di queste risorse.

## <a name="task-hub-names"></a>Nomi degli hub attività

Gli hub attività sono identificati da un nome conforme a queste regole:Task hubs are identified by a name that conforms to these rules:

* Contiene solo caratteri alfanumerici
* Inizia con una lettera
* Ha una lunghezza minima di 3 caratteri, una lunghezza massima di 45 caratteri

Il nome dell'hub attività viene dichiarato nel file *host.json,* come illustrato nell'esempio seguente:The task hub name is declared in the host.json file, as shown in the following example:

### <a name="hostjson-functions-20"></a>host.json (Funzioni 2.0)

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

Gli hub attività possono anche essere configurati usando `host.json` le impostazioni dell'app, come illustrato nel file di esempio seguente:Task hubs can also be configured using app settings, as shown in the following example file:

### <a name="hostjson-functions-10"></a>host.json (Funzioni 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Funzioni 2.0)

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

Il codice seguente illustra come scrivere una funzione che usa [l'associazione client dell'orchestrazione](durable-functions-bindings.md#orchestration-client) per usare un hub attività configurato come impostazione dell'app:The following code demonstrates how to write a function that uses the orchestration client binding to work with a task hub that is configured as an App Setting:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> L'esempio precedente in Cè è per Funzioni durevoli 2.x. Per funzioni durevoli 1.x, è necessario utilizzare `DurableOrchestrationContext` al posto di `IDurableOrchestrationContext`. Per altre informazioni sulle differenze tra le versioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

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

---

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, verrà utilizzato un nome hub attività predefinito, come illustrato nella tabella seguente:If not specified, a default task hub name will be used as shown in the following table:

| Versione di estensione durevole | Nome hub attività predefinito |
| - | - |
| 2.x | Quando viene distribuito in Azure, il nome dell'hub attività deriva dal nome dell'app per le _funzioni._ Quando si esegue all'esterno di `TestHubName`Azure, il nome dell'hub attività predefinito è . |
| 1.x | Il nome dell'hub attività `DurableFunctionsHub`predefinito per tutti gli ambienti è . |

Per altre informazioni sulle differenze tra le versioni delle estensioni, vedere l'articolo Versioni di [Funzioni permanenti.](durable-functions-versions.md)

> [!NOTE]
> Il nome è ciò che distingue un hub attività da un altro quando sono presenti più hub attività in un account di archiviazione condiviso. Se si dispone di più app per le funzioni che condividono lo stesso account di archiviazione condiviso, è necessario configurare in modo esplicito nomi diversi per ogni hub attività nei file *host.json*. In caso contrario, le app con funzioni multiple competeranno tra loro per i messaggi, `Pending` `Running` il che potrebbe causare un comportamento indefinito, incluse le orchestrazioni che vengono inaspettatamente "bloccate" nello stato o .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni di orchestrazioneLearn how to handle orchestration versioning](durable-functions-versioning.md)
