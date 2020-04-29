---
title: Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)
description: Informazioni su come configurare la pubblicazione automatica di Griglia di eventi di Azure per Funzioni durevoli.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 671f7bd5221a936ea9dad0f0cece895bdbe9512f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535486"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)

Questo articolo illustra come configurare Durable Functions per pubblicare eventi del ciclo di vita di orchestrazione (ad esempio "creato", "completato" e "non riuscito") in un [argomento personalizzato di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview).

Di seguito sono indicati alcuni scenari in cui questa funzionalità è utile:

* **Scenari DevOps come le distribuzioni blu/verde**: è possibile che si desideri verificare se sono in esecuzione attività prima [di implementare la strategia di distribuzione side-by-side](durable-functions-versioning.md#side-by-side-deployments).

* **Supporto avanzato per il monitoraggio e la diagnostica**: è possibile tenere traccia delle informazioni sullo stato dell'orchestrazione in un archivio esterno ottimizzato per le query, ad esempio il database SQL di Azure o Azure Cosmos DB.

* **Attività in background a esecuzione prolungata**: se si usa Funzioni durevoli per un'attività in background a esecuzione prolungata, questa funzionalità consente di stabilire lo stato corrente.

## <a name="prerequisites"></a>Prerequisiti

* Installare [Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) nel progetto Durable Functions.
* Installare l' [emulatore di archiviazione di Azure](../../storage/common/storage-use-emulator.md) (solo Windows) o usare un account di archiviazione di Azure esistente.
* Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) oppure usare [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Creare un argomento personalizzato di Griglia di eventi

Creare un argomento di Griglia di eventi per l'invio di eventi da Funzioni durevoli. Le istruzioni seguenti illustrano come creare un argomento tramite l'interfaccia della riga di comando di Azure. Questa operazione può essere eseguita anche tramite [PowerShell](../../event-grid/custom-event-quickstart-powershell.md) o tramite [il portale di Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando `az group create`. Attualmente, griglia di eventi di Azure non supporta tutte le aree. Per informazioni sulle aree supportate, vedere [Panoramica di griglia di eventi di Azure](../../event-grid/overview.md).

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

## <a name="configure-event-grid-publishing"></a>Configurare la pubblicazione di griglia di eventi

Nel progetto Funzioni durevoli trovare il file `host.json`.

### <a name="durable-functions-1x"></a>Durable Functions 1. x

Aggiungere `eventGridTopicEndpoint` e `eventGridKeySettingName` in una proprietà `durableTask`.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2. x

Aggiungere una `notifications` sezione alla `durableTask` proprietà del file, sostituendo `<topic_name>` con il nome scelto. Se le `durableTask` proprietà `extensions` o non esistono, crearle come nell'esempio seguente:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

Le possibili proprietà di configurazione di griglia di eventi di Azure sono disponibili nella [documentazione di host. JSON](../functions-host-json.md#durabletask). Dopo aver configurato il `host.json` file, l'app per le funzioni Invia gli eventi del ciclo di vita all'argomento di griglia di eventi. Questa operazione funziona quando si esegue l'app per le funzioni sia localmente che in Azure.

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

Se si usa l' [emulatore di archiviazione](../../storage/common/storage-use-emulator.md) (solo Windows), verificare che funzioni. È consigliabile usare il comando `AzureStorageEmulator.exe clear all` prima dell'esecuzione.

Se si usa un account di archiviazione di `UseDevelopmentStorage=true` `local.settings.json` Azure esistente, sostituire con la relativa stringa di connessione.

## <a name="create-functions-that-listen-for-events"></a>Creare funzioni che ascoltano gli eventi

Usando il portale di Azure, creare un'altra app per le funzioni per ascoltare gli eventi pubblicati dall'app Durable Functions. È consigliabile individuarlo nella stessa area dell'argomento griglia di eventi.

### <a name="create-an-event-grid-trigger-function"></a>Creare una funzione di trigger di Griglia di eventi

Creare una funzione per ricevere gli eventi del ciclo di vita. Selezionare **Funzione personalizzata**.

![Selezionare Funzione personalizzata.](./media/durable-functions-event-publishing/functions-portal.png)

Scegliere trigger griglia di eventi e selezionare una lingua.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

## <a name="run-durable-functions-app-to-send-the-events"></a>Eseguire Durable Functions app per inviare gli eventi

Nel progetto Durable Functions configurato in precedenza, avviare il debug nel computer locale e avviare un'orchestrazione. L'app pubblica Durable Functions eventi del ciclo di vita in griglia di eventi. Verificare che griglia di eventi inneschi la funzione listener creata controllando i relativi log nel portale di Azure.

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

* **`id`**: Identificatore univoco per l'evento di griglia di eventi.
* **`subject`**: Percorso dell'oggetto dell'evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` saranno `Running`, `Completed`, `Failed` e `Terminated`.  
* **`data`**: Durable Functions parametri specifici.
  * **`hubName`**: Nome [TaskHub](durable-functions-task-hubs.md) .
  * **`functionName`**: Nome della funzione dell'agente di orchestrazione.
  * **`instanceId`**: Durable Functions instanceId.
  * **`reason`**: Dati aggiuntivi associati all'evento di rilevamento. Per altre informazioni, vedere [Diagnostica in Funzioni durevoli (Funzioni di Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`**: Stato di runtime dell'orchestrazione. Running (In esecuzione), Completed (Completato), Failed (Non riuscito), Canceled (Annullato).
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: Ora dell'evento (UTC).
* **`dataVersion`**: Versione dello schema di eventi del ciclo di vita.
* **`metadataVersion`**: Versione dei metadati.
* **`topic`**: Risorsa dell'argomento della griglia di eventi.

## <a name="how-to-test-locally"></a>Come eseguire test in locale

Per eseguire il test in locale, leggere [trigger di griglia di eventi di griglia di eventi debug locale](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulla gestione delle istanze in Funzioni durevoli](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Informazioni sul controllo delle versioni in Funzioni durevoli](durable-functions-versioning.md)
