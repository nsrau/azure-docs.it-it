---
title: Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)
description: Informazioni su come configurare la pubblicazione automatica di Griglia di eventi di Azure per Funzioni durevoli.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 52ffcd4eb81936ffcfa61580288c60bd59ffb744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249751"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)

Questo articolo illustra come configurare Durable Functions per pubblicare eventi del ciclo di vita di orchestrazione (ad esempio "creato", "completato" e "non riuscito") in un [argomento personalizzato di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview).

Di seguito sono indicati alcuni scenari in cui questa funzionalità è utile:

* **Scenari DevOps come le distribuzioni blu/verde:** è possibile sapere se sono in esecuzione attività prima di implementare la strategia di [distribuzione side-by-side](durable-functions-versioning.md#side-by-side-deployments).

* **Supporto avanzato**per il monitoraggio e la diagnostica: è possibile tenere traccia delle informazioni sullo stato dell'orchestrazione in un archivio esterno ottimizzato per le query, ad esempio il database SQL di Azure o il database Cosmos di Azure.Advanced monitoring and diagnostics support : You can keep track of orchestration status information in an external store optimized for queries, such as Azure SQL Database or Azure Cosmos DB.

* **Attività in background a esecuzione prolungata**: se si usa Funzioni durevoli per un'attività in background a esecuzione prolungata, questa funzionalità consente di stabilire lo stato corrente.

## <a name="prerequisites"></a>Prerequisiti

* Installare [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) nel progetto Funzioni permanenti.
* Installare [l'emulatore](../../storage/common/storage-use-emulator.md) di archiviazione di Azure (solo Windows) o usare un account di archiviazione di Azure esistente.
* Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) oppure usare [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Creare un argomento personalizzato di Griglia di eventi

Creare un argomento di Griglia di eventi per l'invio di eventi da Funzioni durevoli. Le istruzioni seguenti illustrano come creare un argomento tramite l'interfaccia della riga di comando di Azure. È anche possibile eseguire questa operazione [usando PowerShell](../../event-grid/custom-event-quickstart-powershell.md) o il portale di [Azure.](../../event-grid/custom-event-quickstart-portal.md)

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando `az group create`. Attualmente, Griglia di eventi di Azure non supporta tutte le aree. Per informazioni sulle aree supportate, vedere Panoramica di Griglia di eventi di [Azure.](../../event-grid/overview.md)

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Creare un argomento personalizzato

Un argomento di Griglia di eventi fornisce un endpoint definito dall'utente in cui vengono pubblicati gli eventi. Sostituire `<topic_name>` con un nome univoco per l'argomento. Il nome dell'argomento deve essere univoco perché diventa una voce DNS.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Ottenere l'endpoint e la chiave

Ottenere l'endpoint dell'argomento. Sostituire `<topic_name>` con il nome scelto.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Ottenere la chiave dell'argomento. Sostituire `<topic_name>` con il nome scelto.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Ora è possibile inviare eventi all'argomento.

## <a name="configure-event-grid-publishing"></a>Configurare la pubblicazione di Griglia di eventi

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

Le possibili proprietà di configurazione di Griglia di eventi di Azure sono disponibili nella [documentazione di host.json.](../functions-host-json.md#durabletask) Dopo aver `host.json` configurato il file, l'app per le funzioni invia gli eventi del ciclo di vita all'argomento Griglia di eventi. Questo funziona quando si esegue l'app per le funzioni sia in locale che in Azure.This works when you run your function app both locally and in Azure.

Definire l'impostazione dell'app per la chiave dell'argomento nell'app per le funzioni e in `local.settings.json`. Il codice JSON seguente è un esempio di `local.settings.json` per il debug locale. Sostituire `<topic_key>` con la chiave dell'argomento.  

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

Se si utilizza [l'emulatore](../../storage/common/storage-use-emulator.md) di archiviazione (solo Windows), assicurarsi che funzioni. È consigliabile usare il comando `AzureStorageEmulator.exe clear all` prima dell'esecuzione.

Se si usa un account di `UseDevelopmentStorage=true` `local.settings.json` archiviazione di Azure esistente, sostituire con la relativa stringa di connessione.

## <a name="create-functions-that-listen-for-events"></a>Creare funzioni che ascoltano gli eventi

Usando il portale di Azure, creare un'altra app per le funzioni per l'ascolto degli eventi pubblicati dall'app Funzioni permanenti. È consigliabile individuarlo nella stessa area dell'argomento Griglia di eventi.

### <a name="create-an-event-grid-trigger-function"></a>Creare una funzione di trigger di Griglia di eventi

Creare una funzione per ricevere gli eventi del ciclo di vita. Selezionare **Funzione personalizzata**.

![Selezionare Funzione personalizzata.](./media/durable-functions-event-publishing/functions-portal.png)

Scegliere Trigger griglia di eventi e selezionare una lingua.

![Selezionare il trigger di Griglia di eventi.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Immettere il nome della funzione e quindi selezionare `Create`.

![Creare il trigger di Griglia di eventi.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Viene creata una funzione con il codice seguente:

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

Selezionare `Add Event Grid Subscription`. Questa operazione aggiunge una sottoscrizione di Griglia di eventi per l'argomento di Griglia di eventi creato. Per altre informazioni, vedere [Concetti di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/concepts).

![Selezionare il collegamento del trigger di Griglia di eventi.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Selezionare `Event Grid Topics` per il **tipo di argomento**. Selezionare il gruppo di risorse creato per l'argomento di Griglia di eventi. Selezionare quindi l'istanza dell'argomento di Griglia di eventi. Fare clic su `Create`.

![Creare una sottoscrizione di Griglia di eventi.](./media/durable-functions-event-publishing/eventsubscription.png)

Ora si è pronti a ricevere gli eventi del ciclo di vita.

## <a name="run-durable-functions-app-to-send-the-events"></a>Eseguire l'app Funzioni durevoli per inviare gli eventi

Nel progetto Funzioni permanenti configurato in precedenza avviare il debug nel computer locale e avviare un'orchestrazione. L'app pubblica gli eventi del ciclo di vita di Funzioni permanenti in Griglia di eventi. Verificare che Griglia di eventi attivi la funzione listener creata controllando i relativi log nel portale di Azure.Verify that Event Grid triggers the listener function you created by checking its logs in the Azure portal.

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

* **`id`**: identificatore univoco per l'evento Griglia di eventi.
* **`subject`**: percorso dell'oggetto dell'evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` saranno `Running`, `Completed`, `Failed` e `Terminated`.  
* **`data`**: parametri specifici delle funzioni durevoli.
  * **`hubName`**: nome [TaskHub.](durable-functions-task-hubs.md)
  * **`functionName`**: nome della funzione dell'agente di orchestrazione.
  * **`instanceId`**: instanceId delle funzioni durevoli.
  * **`reason`**: dati aggiuntivi associati all'evento di rilevamento. Per altre informazioni, vedere [Diagnostica in Funzioni durevoli (Funzioni di Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: stato del runtime dell'orchestrazione. Running (In esecuzione), Completed (Completato), Failed (Non riuscito), Canceled (Annullato).
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: ora dell'evento (UTC).
* **`dataVersion`**: versione dello schema degli eventi del ciclo di vita.
* **`metadataVersion`**: versione dei metadati.
* **`topic`**: risorsa dell'argomento griglia di eventi.

## <a name="how-to-test-locally"></a>Come eseguire test in locale

Per eseguire il test in locale, leggere Griglia di eventi delle funzioni di [Azure Attivare il debug locale](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulla gestione delle istanze in Funzioni durevoli](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Informazioni sul controllo delle versioni in Funzioni durevoli](durable-functions-versioning.md)
