---
title: Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)
description: Informazioni su come configurare la pubblicazione automatica di Griglia di eventi di Azure per Funzioni durevoli.
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: c0106f3754e0cdcbf1f295fbe3f1b5def8dc3ca1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83124265"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Pubblicazione di Funzioni durevoli in Griglia di eventi di Azure (anteprima)

Questo articolo illustra come configurare Durable Functions per pubblicare eventi del ciclo di vita di orchestrazione (ad esempio "creato", "completato" e "non riuscito") in un [argomento personalizzato di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/overview).

Di seguito sono indicati alcuni scenari in cui questa funzionalità è utile:

* **Scenari DevOps come le distribuzioni Blue/Green**: può essere opportuno sapere se sono presenti attività in esecuzione prima di implementare la [strategia di distribuzione side-by-side](durable-functions-versioning.md#side-by-side-deployments).

* **Supporto di monitoraggio e diagnostica avanzati**: è possibile tenere traccia delle informazioni sullo stato dell'orchestrazione in un archivio esterno ottimizzato per le query, ad esempio il database SQL di Azure o Azure Cosmos DB.

* **Attività in background a esecuzione prolungata**: se si usa Durable Functions per un'attività in background a esecuzione prolungata, questa funzionalità consente di stabilire lo stato corrente.

## <a name="prerequisites"></a>Prerequisiti

* Installare [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) nel progetto Durable Functions.
* Installare l'[emulatore di Archiviazione di Azure](../../storage/common/storage-use-emulator.md) (solo Windows) o usare un account di archiviazione di Azure esistente.
* Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) oppure usare [Azure Cloud Shell](../../cloud-shell/overview.md)

## <a name="create-a-custom-event-grid-topic"></a>Creare un argomento personalizzato di Griglia di eventi

Creare un argomento di Griglia di eventi per l'invio di eventi da Funzioni durevoli. Le istruzioni seguenti illustrano come creare un argomento tramite l'interfaccia della riga di comando di Azure. È anche possibile creare l'argomento [usando PowerShell](../../event-grid/custom-event-quickstart-powershell.md) o [il portale di Azure](../../event-grid/custom-event-quickstart-portal.md).

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando `az group create`. Griglia di eventi di Azure non supporta attualmente tutte le aree. Per informazioni sulle aree supportate, vedere la [Panoramica di Griglia di eventi di Azure](../../event-grid/overview.md).

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

### <a name="durable-functions-1x"></a>Durable Functions 1.x

Aggiungere `eventGridTopicEndpoint` e `eventGridKeySettingName` in una proprietà `durableTask`.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2.x

Aggiungere una sezione `notifications` alla proprietà `durableTask` del file, sostituendo `<topic_name>` con il nome scelto. Se le proprietà `durableTask` o `extensions` non esistono, è necessario crearle come nell'esempio seguente:

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

Le possibili proprietà di configurazione di Griglia di eventi di Azure sono disponibili nella [documentazione host.json](../functions-host-json.md#durabletask). Dopo aver configurato il file `host.json`, l'app per le funzioni invia gli eventi del ciclo di vita all'argomento di Griglia di eventi. Questa azione si avvia quando si esegue l'app per le funzioni sia localmente che in Azure.

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

Se si usa l'[emulatore di Archiviazione](../../storage/common/storage-use-emulator.md) (solo Windows), verificarne il funzionamento. È consigliabile usare il comando `AzureStorageEmulator.exe clear all` prima dell'esecuzione.

Se si usa un account di Archiviazione di Azure esistente, sostituire `UseDevelopmentStorage=true` in `local.settings.json` con la relativa stringa di connessione.

## <a name="create-functions-that-listen-for-events"></a>Creare funzioni che ascoltano gli eventi

Usando il portale di Azure, creare un'altra app per le funzioni per rimanere in ascolto degli eventi pubblicati dall'app Durable Functions. È consigliabile crearla nella stessa area dell'argomento di Griglia di eventi.

### <a name="create-an-event-grid-trigger-function"></a>Creare una funzione di trigger di Griglia di eventi

1. Selezionare **Funzioni** nell'app per le funzioni, quindi selezionare **+ Aggiungi** 

   :::image type="content" source="./media/durable-functions-event-publishing/function-add-function.png" alt-text="Aggiungere una funzione nel portale di Azure" border="true":::.

1. Cercare **Griglia di eventi**, quindi selezionare il modello del **trigger di Griglia di eventi di Azure**. 

    :::image type="content" source="./media/durable-functions-event-publishing/function-select-event-grid-trigger.png" alt-text="Selezionare il modello di trigger di Griglia di eventi nel portale di Azure." border="true":::

1. Assegnare un nome al nuovo trigger, quindi selezionare **Crea funzione**.

    :::image type="content" source="./media/durable-functions-event-publishing/function-name-event-grid-trigger.png" alt-text="Assegnare un nome al trigger di Griglia di eventi nel portale di Azure." border="true":::


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

### <a name="add-an-event-grid-subscription"></a>Aggiungere una sottoscrizione di Griglia di eventi

È ora possibile aggiungere una sottoscrizione di Griglia di eventi per l'argomento di Griglia di eventi creato. Per altre informazioni, vedere [Concetti di Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/concepts).

1. Selezionare **Integrazione** nella nuova funzione, quindi selezionare **Trigger di Griglia di eventi (eventGridEvent)** . 

    :::image type="content" source="./media/durable-functions-event-publishing/eventgrid-trigger-link.png" alt-text="Selezionare il collegamento del trigger di Griglia di eventi." border="true":::

1. Selezionare **Crea descrizione di Griglia di eventi**.

    :::image type="content" source="./media/durable-functions-event-publishing/create-event-grid-subscription.png" alt-text="Creare la sottoscrizione di Griglia di eventi." border="true":::

1. Assegnare un nome alla sottoscrizione di eventi e selezionare il tipo di argomento **Argomenti di Griglia di eventi**. 

1. Selezionare la sottoscrizione. Dopodiché, selezionare il gruppo di risorse e la risorsa creati per l'argomento di Griglia di eventi. 

1. Selezionare **Create** (Crea).

    :::image type="content" source="./media/durable-functions-event-publishing/event-grid-subscription-details.png" alt-text="Creare una sottoscrizione di Griglia di eventi." border="true":::

Ora si è pronti a ricevere gli eventi del ciclo di vita.

## <a name="run-durable-functions-app-to-send-the-events"></a>Eseguire l'app Durable Functions per inviare gli eventi

Avviare il debug nel computer locale, quindi avviare un'orchestrazione nel progetto Durable Functions configurato in precedenza. L'app pubblica eventi del ciclo di vita di Durable Functions in Griglia di eventi. Verificare che Griglia di eventi attivi la funzione di listener creata controllandone i log nel portale di Azure.

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

* **`id`** : identificatore univoco dell'evento di Griglia di eventi.
* **`subject`** : percorso dell'oggetto dell'evento. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` saranno `Running`, `Completed`, `Failed` e `Terminated`.  
* **`data`** : parametri specifici di Durable Functions.
  * **`hubName`** : nome [TaskHub](durable-functions-task-hubs.md).
  * **`functionName`** : Nome della funzione dell'agente di orchestrazione.
  * **`instanceId`** : ID istanza di Durable Functions.
  * **`reason`** : dati aggiuntivi associati all'evento di rilevamento. Per altre informazioni, vedere [Diagnostica in Funzioni durevoli (Funzioni di Azure)](durable-functions-diagnostics.md)
  * **`runtimeStatus`** : stato di runtime dell'orchestrazione. Running (In esecuzione), Completed (Completato), Failed (Non riuscito), Canceled (Annullato).
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Ora evento (UTC).
* **`dataVersion`** : versione dello schema di eventi del ciclo di vita.
* **`metadataVersion`** :  Versione dei metadati.
* **`topic`** : Risorsa argomento di Griglia di eventi.

## <a name="how-to-test-locally"></a>Come eseguire test in locale

Per eseguire il test a livello locale, leggere [Debug locale del trigger di Griglia di eventi di Funzioni di Azure](../functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulla gestione delle istanze in Funzioni durevoli](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Informazioni sul controllo delle versioni in Funzioni durevoli](durable-functions-versioning.md)
