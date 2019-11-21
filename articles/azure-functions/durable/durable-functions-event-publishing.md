---
title: Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)
description: Informazioni su come configurare la pubblicazione automatica di Griglia di eventi di Azure per Funzioni durevoli.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: f0fbb46320b896008b6a1343357f016a9f57b0fe
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231439"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)

Questo articolo illustra come configurare Durable Functions per pubblicare eventi del ciclo di vita di orchestrazione (ad esempio "creato", "completato" e "non riuscito") in un [argomento personalizzato di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview).

Di seguito sono indicati alcuni scenari in cui questa funzionalità è utile:

* **DevOps scenarios like blue/green deployments**: You might want to know if any tasks are running before implementing the [side-by-side deployment strategy](durable-functions-versioning.md#side-by-side-deployments).

* **Supporto di monitoraggio e diagnostica avanzati**: è possibile tenere traccia delle informazioni sullo stato dell'orchestrazione in un archivio esterno ottimizzato per le query, ad esempio database SQL o CosmosDB.

* **Attività in background a esecuzione prolungata**: se si usa Funzioni durevoli per un'attività in background a esecuzione prolungata, questa funzionalità consente di stabilire lo stato corrente.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Install [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) in your Durable Functions project.
* Installare l'[emulatore di archiviazione di Azure](../../storage/common/storage-use-emulator.md).
* Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) oppure usare [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Create a custom event grid topic

Create an event grid topic for sending events from Durable Functions. Le istruzioni seguenti illustrano come creare un argomento tramite l'interfaccia della riga di comando di Azure. Per informazioni su come eseguire questa operazione tramite PowerShell o il portale di Azure, vedere gli articoli seguenti:

* [Guide introduttive di Griglia di eventi: Creare un evento personalizzato - PowerShell](../../event-grid/custom-event-quickstart-powershell.md)
* [Guide introduttive di Griglia di eventi: Creare un evento personalizzato - Portale di Azure](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando `az group create`. Currently, Azure Event Grid doesn't support all regions. For information about which regions are supported, see the [Azure Event Grid overview](../../event-grid/overview.md).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Creare un argomento personalizzato

An event grid topic provides a user-defined endpoint that you post your event to. Sostituire `<topic_name>` con un nome univoco per l'argomento. Il nome dell'argomento deve essere univoco perché diventa una voce DNS.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Ottenere l'endpoint e la chiave

Ottenere l'endpoint dell'argomento. Sostituire `<topic_name>` con il nome scelto.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Ottenere la chiave dell'argomento. Sostituire `<topic_name>` con il nome scelto.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Ora è possibile inviare eventi all'argomento.

## <a name="configure-azure-event-grid-publishing"></a>Configurare la pubblicazione di Griglia di eventi di Azure

Nel progetto Funzioni durevoli trovare il file `host.json`.

Aggiungere `eventGridTopicEndpoint` e `eventGridKeySettingName` in una proprietà `durableTask`.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

The possible Azure Event Grid configuration properties can be found in the [host.json documentation](../functions-host-json.md#durabletask). After you configure the `host.json` file, your function app sends lifecycle events to the event grid topic. This works when you run your function app both locally and in Azure.```

Definire l'impostazione dell'app per la chiave dell'argomento nell'app per le funzioni e in `local.setting.json`. Il codice JSON seguente è un esempio di `local.settings.json` per il debug locale. Sostituire `<topic_key>` con la chiave dell'argomento.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Assicurarsi che l'[emulatore di archiviazione](../../storage/common/storage-use-emulator.md) sia in esecuzione. È consigliabile usare il comando `AzureStorageEmulator.exe clear all` prima dell'esecuzione.

## <a name="create-functions-that-listen-for-events"></a>Creare funzioni che ascoltano gli eventi

Creare un'app per le funzioni. It's best to locate it in the same region as the event grid topic.

### <a name="create-an-event-grid-trigger-function"></a>Create an event grid trigger function

Creare una funzione per ricevere gli eventi del ciclo di vita. Selezionare **Funzione personalizzata**.

![Selezionare Funzione personalizzata.](./media/durable-functions-event-publishing/functions-portal.png)

Scegliere Trigger griglia di eventi e selezionare `C#`.

![Selezionare il trigger di Griglia di eventi.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Immettere il nome della funzione e quindi selezionare `Create`.

![Creare il trigger di Griglia di eventi.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Viene creata una funzione con il codice seguente:

#### <a name="precompiled-c"></a>C# precompilato
```csharp
public static void Run([HttpTrigger] JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script"></a>Script C#

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Selezionare `Add Event Grid Subscription`. This operation adds an event grid subscription for the event grid topic that you created. Per altre informazioni, vedere [Concetti di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/concepts).

![Selezionare il collegamento del trigger di Griglia di eventi.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selezionare `Event Grid Topics` per il **tipo di argomento**. Select the resource group that you created for the event grid topic. Then select the instance of the event grid topic. Fare clic su `Create`.

![Creare una sottoscrizione di Griglia di eventi.](./media/durable-functions-event-publishing/eventsubscription.png)

Ora si è pronti a ricevere gli eventi del ciclo di vita.

## <a name="create-durable-functions-to-send-the-events"></a>Creare Durable Functions per inviare gli eventi

Nel progetto Funzioni durevoli avviare il debug nel computer locale.  Il codice seguente è uguale al codice del modello per Funzioni durevoli. Sono già stati configurati `host.json` e `local.settings.json` nel computer locale.

### <a name="precompiled-c"></a>C# precompilato

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] IDurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestMessage req,
            [DurableClient] IDurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`, `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

Se si chiama `Sample_HttpStart` con Postman o il browser, la funzione durevole inizia a inviare eventi del ciclo di vita. L'endpoint è in genere `http://localhost:7071/api/Sample_HttpStart` per il debug locale.

Vedere i log della funzione creata nel portale di Azure.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schema di eventi

L'elenco seguente spiega lo schema degli eventi del ciclo di vita:

* **`id`** : Unique identifier for the event grid event.
* **`subject`** : Path to the event subject. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` saranno `Running`, `Completed`, `Failed` e `Terminated`.  
* **`data`** : Durable Functions Specific Parameters.
  * **`hubName`** : [TaskHub](durable-functions-task-hubs.md) name.
  * **`functionName`** : Orchestrator function name.
  * **`instanceId`** : Durable Functions instanceId.
  * **`reason`** : Additional data associated with the tracking event. Per altre informazioni, vedere [Diagnostica in Funzioni durevoli (Funzioni di Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : Orchestration Runtime Status. Running (In esecuzione), Completed (Completato), Failed (Non riuscito), Canceled (Annullato).
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Event time (UTC).
* **`dataVersion`** : Version of the lifecycle event schema.
* **`metadataVersion`** :  Version of the metadata.
* **`topic`** : Event grid topic resource.

## <a name="how-to-test-locally"></a>Come eseguire test in locale

Per eseguire test in locale, usare [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulla gestione delle istanze in Funzioni durevoli](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Informazioni sul controllo delle versioni in Funzioni durevoli](durable-functions-versioning.md)