---
title: Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)
description: Informazioni su come configurare la pubblicazione automatica di Griglia di eventi di Azure per Funzioni durevoli.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: glenga
ms.openlocfilehash: 00735293d8fa8c6056f1ecf89fd312fe4b90bcac
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637576"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)

Questo articolo illustra come configurare Funzioni durevoli di Azure per pubblicare eventi del ciclo di vita di orchestrazione (ad esempio "creato", "completato" e "non riuscito") in un [argomento personalizzato di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview). 

Di seguito sono indicati alcuni scenari in cui questa funzionalità è utile:

* **Scenari DevOps come le distribuzioni Blue/Green**: può essere opportuno sapere se sono presenti attività in esecuzione prima di implementare la [strategia di distribuzione side-by-side](https://docs.microsoft.com/azure/azure-functions/durable-functions-versioning#side-by-side-deployments).

* **Supporto di monitoraggio e diagnostica avanzati**: è possibile tenere traccia delle informazioni sullo stato dell'orchestrazione in un archivio esterno ottimizzato per le query, ad esempio database SQL o CosmosDB.

* **Attività in background a esecuzione prolungata**: se si usa Funzioni durevoli per un'attività in background a esecuzione prolungata, questa funzionalità consente di stabilire lo stato corrente.

## <a name="prerequisites"></a>Prerequisiti

* Installare [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc o versione successiva nel progetto Funzioni durevoli.
* Installare l'[emulatore di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) oppure usare [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

## <a name="create-a-custom-event-grid-topic"></a>Creare un argomento personalizzato di Griglia di eventi

Creare un argomento di Griglia di eventi per l'invio di eventi da Funzioni durevoli. Le istruzioni seguenti illustrano come creare un argomento tramite l'interfaccia della riga di comando di Azure. Per informazioni su come eseguire questa operazione tramite PowerShell o il portale di Azure, vedere gli articoli seguenti:

* [Guide introduttive di Griglia di eventi: Creare un evento personalizzato - PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [Guide introduttive di Griglia di eventi: Creare un evento personalizzato - Portale di Azure](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando `az group create`. Griglia di eventi non supporta attualmente tutte le aree. Per informazioni sulle aree supportate, vedere la [Panoramica di Griglia di eventi](https://docs.microsoft.com/azure/event-grid/overview). 

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Creare un argomento personalizzato

Un argomento di Griglia di eventi fornisce un endpoint definito dall'utente in cui vengono pubblicati gli eventi. Sostituire `<topic_name>` con un nome univoco per l'argomento. Il nome dell'argomento deve essere univoco perché diventa una voce DNS.

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

Aggiungere `EventGridTopicEndpoint` e `EventGridKeySettingName` in una proprietà `durableTask`.

```json
{
    "durableTask": {
        "EventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "EventGridKeySettingName": "EventGridKey"
    }
}
```

Le proprietà di configurazione di Griglia di eventi di Azure sono le seguenti:

* **EventGridTopicEndpoint**: endpoint dell'argomento di Griglia di eventi. La sintassi *%AppSettingName%* può essere usata per risolvere questo valore dalle impostazioni dell'applicazione o dalle variabili di ambiente.
* **EventGridKeySettingName**: chiave dell'impostazione applicazione nella funzione di Azure. Funzioni durevoli otterrà la chiave dell'argomento di Griglia di eventi dal valore.
* **EventGridPublishRetryCount**: [facoltativo] numero di tentativi se la pubblicazione nell'argomento di Griglia di eventi ha esito negativo.
* **EventGridPublishRetryInterval**: [facoltativo] intervallo di tentativi di pubblicazione in Griglia di eventi di Azure nel formato *hh:mm:ss*. Se non specificato, l'intervallo di ripetizione dei tentativi predefinito è 5 minuti.

Dopo aver configurato il file `host.json`, il progetto Funzioni durevoli inizia a inviare gli eventi del ciclo di vita all'argomento di Griglia di eventi. Questa operazione funziona nell'esecuzione nell'app per le funzioni e in locale.

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

Assicurarsi che l'[emulatore di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) sia in esecuzione. È consigliabile usare il comando `AzureStorageEmulator.exe clear all` prima dell'esecuzione.

## <a name="create-functions-that-listen-for-events"></a>Creare funzioni che ascoltano gli eventi

Creare un'app per le funzioni. È consigliabile crearla nella stessa area dell'argomento di Griglia di eventi.

### <a name="create-an-event-grid-trigger-function"></a>Creare una funzione di trigger di Griglia di eventi

Creare una funzione per ricevere gli eventi del ciclo di vita. Selezionare **Funzione personalizzata**. 

![Selezionare Funzione personalizzata.](./media/durable-functions-event-publishing/functions-portal.png)

Scegliere Trigger griglia di eventi e selezionare `C#`.

![Selezionare il trigger di Griglia di eventi.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Immettere il nome della funzione e quindi selezionare `Create`.

![Creare il trigger di Griglia di eventi.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Viene creata una funzione con il codice seguente: 

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

Selezionare `Add Event Grid Subscription`. Questa operazione aggiunge una sottoscrizione di Griglia di eventi per l'argomento di Griglia di eventi creato. Per altre informazioni, vedere [Concetti di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/concepts).

![Selezionare il collegamento del trigger di Griglia di eventi.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selezionare `Event Grid Topics` per il **tipo di argomento**. Selezionare il gruppo di risorse creato per l'argomento di Griglia di eventi. Selezionare quindi l'istanza dell'argomento di Griglia di eventi. Fare clic su `Create`.

![Creare una sottoscrizione di Griglia di eventi.](./media/durable-functions-event-publishing/eventsubscription.png)

Ora si è pronti a ricevere gli eventi del ciclo di vita. 

## <a name="create-durable-functions-to-send-the-events"></a>Creare funzioni durevoli per inviare gli eventi.

Nel progetto Funzioni durevoli avviare il debug nel computer locale.  Il codice seguente è uguale al codice del modello per Funzioni durevoli. Sono già stati configurati `host.json` e `local.settings.json` nel computer locale. 

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
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
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
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

Se si chiama `Sample_HttpStart` con Postman o il browser, la funzione durevole inizia a inviare eventi del ciclo di vita. L'endpoint è in genere `http://localhost:7071/api/Sample_HttpStart` per il debug locale.

Vedere i registri della funzione creata nel portale di Azure.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
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
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
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
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Schema di eventi

L'elenco seguente spiega lo schema degli eventi del ciclo di vita:

* **id**: identificatore univoco dell'evento di Griglia di eventi.
* **subject**: percorso dell'oggetto dell'evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` saranno `Running`, `Completed`, `Failed` e `Terminated`.  
* **data**: parametri specifici di Funzioni durevoli.
    * **hubName**: nome [TaskHub](https://docs.microsoft.com/azure/azure-functions/durable-functions-task-hubs).
    * **functionName**: nome della funzione dell'agente di orchestrazione.
    * **instanceId**: ID istanza di Funzioni durevoli.
    * **reason**: dati aggiuntivi associati all'evento di rilevamento. Per altre informazioni, vedere [Diagnostica in Funzioni durevoli (Funzioni di Azure)](https://docs.microsoft.com/azure/azure-functions/durable-functions-diagnostics)
    * **runtimeStatus**: stato di runtime dell'orchestrazione. Running (In esecuzione), Completed (Completato), Failed (Non riuscito), Canceled (Annullato). 
* **eventType**: "orchestratorEvent"
* **eventTime**: ora dell'evento (UTC).
* **dataVersion**: versione dello schema di eventi del ciclo di vita.
* **metadataVersion**: versione dei metadati.
* **topic**: risorsa dell'argomento di Griglia di eventi.

## <a name="how-to-test-locally"></a>Come eseguire test in locale

Per eseguire test in locale, usare [ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulla gestione delle istanze in Funzioni durevoli](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Informazioni sul controllo delle versioni in Funzioni durevoli](durable-functions-versioning.md)
