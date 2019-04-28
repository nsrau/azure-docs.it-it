---
title: Hub attività in Funzioni permanenti - Azure
description: Informazioni sugli hub attività nell'estensione Funzioni permanenti per Funzioni di Azure. Informazioni su come configurare gli hub attività.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2017
ms.date: 02/21/2019
ms.author: v-junlch
ms.openlocfilehash: 596eedab39ff926fcdc880c82c49ac464b7ff23b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730269"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Hub attività in Funzioni permanenti (Funzioni di Azure)

Un *hub attività* in [Funzioni permanenti](durable-functions-overview.md) è un contenitore logico per le risorse di Archiviazione di Microsoft Azure che vengono usate per le orchestrazioni. Le funzioni attività e di orchestrazione possono interagire tra loro solo quando appartengono allo stesso hub attività.

Se molteplici app per le funzioni condividono un account di archiviazione, ogni app per le funzioni *deve* essere configurata con un nome di hub attività separato. Un account di archiviazione può contenere molteplici hub attività. Nel diagramma seguente è illustrato un hub attività per app per le funzioni in account di archiviazione condivisi e dedicati.

![Diagramma che illustra gli account di archiviazione condivisi e dedicati.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Risorse di archiviazione di Azure

Un hub di attività è costituito dalle risorse di archiviazione seguenti:

- Una o più code di controllo.
- Una coda di elementi di lavoro.
- Una tabella di cronologia.
- Una tabella di istanze.
- Un contenitore di archiviazione che contiene uno o più BLOB del lease.

Tutte le risorse vengono create automaticamente nell'account di Archiviazione di Azure predefinito quando vengono eseguite o sono pianificate per l'esecuzione funzioni attività o di orchestrazione. Nell'articolo [Prestazioni e scalabilità](durable-functions-perf-and-scale.md) viene descritto l'uso di queste risorse.

## <a name="task-hub-names"></a>Nomi degli hub attività

Gli hub attività sono identificati mediante un nome dichiarato nel file *host.json* file, come illustrato nell'esempio seguente:

### <a name="hostjson-functions-1x"></a>host.json (funzioni 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (funzioni 2.x)

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

Gli hub attività possono anche essere configurati usando le impostazioni dell'app, come illustrato nel seguente file di esempio *host.json*:

### <a name="hostjson-functions-1x"></a>host.json (funzioni 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host.json (funzioni 2.x)

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

Di seguito è riportato un esempio di C# precompilato su come scrivere una funzione che usa un oggetto [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) per funzionare con un hub attività configurato come Impostazione app:

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
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

Di seguito è riportata la configurazione necessaria per JavaScript. La proprietà dell'hub attività nel file `function.json` viene configurata tramite Impostazione app:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, il nome predefinito è **DurableFunctionsHub**.

> [!NOTE]
> Il nome è ciò che distingue un hub attività da un altro quando sono presenti più hub attività in un account di archiviazione condiviso. Se si dispone di più app per le funzioni che condividono lo stesso account di archiviazione condiviso, è necessario configurare in modo esplicito nomi diversi per ogni hub attività nei file *host.json*. In caso contrario, le diverse app per le funzioni competeranno tra loro per i messaggi, causando eventualmente un comportamento indefinito.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire il controllo delle versioni](durable-functions-versioning.md)

<!-- Update_Description: wording update -->