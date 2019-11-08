---
title: 'Guida introduttiva: Inviare eventi personalizzati alla coda di archiviazione - Griglia di eventi, interfaccia della riga di comando di Azure'
description: "Guida introduttiva: Usare Griglia di eventi di Azure e l'interfaccia della riga di comando di Azure per pubblicare un argomento e sottoscrivere l'evento. Una coda di archiviazione viene usata per l'endpoint."
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 44ffa570cfda39c186966866ee5755ab090083a5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721457"
---
# <a name="quickstart-route-custom-events-to-azure-queue-storage-with-azure-cli-and-event-grid"></a>Guida introduttiva: Instradare eventi personalizzati ad Archiviazione code di Azure con l'interfaccia della riga di comando di Azure e Griglia di eventi

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. Archiviazione code di Azure è uno dei gestori eventi supportati. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un argomento personalizzato, sottoscrivere l'argomento e attivare l'evento per visualizzare il risultato. Gli eventi vengono inviati all'archivio code.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si usa l'interfaccia della riga di comando di Azure o Azure PowerShell nel computer locale invece di usare Cloud Shell nel portale di Azure, assicurarsi di avere le versioni seguenti dell'interfaccia della riga di comando di Azure e di Azure PowerShell. 

- Interfaccia della riga di comando di Azure versione 2.0.56 o successiva. Per istruzioni sull'installazione della versione più recente dell'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 
- Azure PowerShell versione 1.1.0 o successiva. Scaricare l'ultima versione di Azure PowerShell nel computer Windows da [Download di Azure - Strumenti da riga di comando](https://azure.microsoft.com/downloads/). 

Questo articolo elenca i comandi utili per l'uso dell'interfaccia della riga di comando di Azure. 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gli argomenti della griglia di eventi sono risorse di Azure e devono essere inseriti in un gruppo di risorse di Azure. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). 

L'esempio seguente crea un gruppo di risorse denominato *gridResourceGroup* nella località *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Creare un argomento personalizzato

Un argomento di Griglia di eventi fornisce un endpoint definito dall'utente in cui vengono pubblicati gli eventi. L'esempio seguente crea l'argomento personalizzato nel gruppo di risorse. Sostituire `<topic_name>` con un nome univoco per l'argomento personalizzato. Il nome dell'argomento della griglia di eventi deve essere univoco perché è rappresentato da una voce DNS.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-queue-storage"></a>Creare l'archivio code

Prima di sottoscrivere l'argomento personalizzato, creare l'endpoint per il messaggio dell'evento. Si crea un archivio code per la raccolta degli eventi.

```azurecli-interactive
storagename="<unique-storage-name>"
queuename="eventqueue"

az storage account create -n $storagename -g gridResourceGroup -l westus2 --sku Standard_LRS
az storage queue create --name $queuename --account-name $storagename
```

## <a name="subscribe-to-a-custom-topic"></a>Sottoscrivere un argomento personalizzato

Si sottoscrive un argomento personalizzato per indicare alla griglia di eventi quali sono gli eventi di cui si vuole tenere traccia. L'esempio seguente sottoscrive l'argomento personalizzato creato e passa l'ID risorsa dell'archivio code per l'endpoint. Con l'interfaccia della riga di comando di Azure, passare l'ID dell'archivio code come endpoint. L'endpoint è nel formato:

`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/queueservices/default/queues/<queue-name>`

Lo script seguente ottiene l'ID risorsa dell'account di archiviazione per la coda. Costruisce l'ID per l'archivio code e sottoscrive un argomento della griglia di eventi. Imposta il tipo di endpoint su `storagequeue` e usa l'ID coda per l'endpoint.

```azurecli-interactive
storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)
queueid="$storageid/queueservices/default/queues/$queuename"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type storagequeue \
  --endpoint $queueid \
  --expiration-date "<yyyy-mm-dd>"
```

L'account che crea la sottoscrizione dell'evento deve avere accesso in scrittura all'archivio code. Si noti che per la sottoscrizione è impostata una [data di scadenza](concepts.md#event-subscription-expiration).

Se si usa l'API REST per creare la sottoscrizione, passare l'ID dell'account di archiviazione e il nome della coda come parametro separato.

```json
"destination": {
  "endpointType": "storagequeue",
  "properties": {
    "queueName":"eventqueue",
    "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>"
  }
  ...
```

## <a name="send-an-event-to-your-custom-topic"></a>Inviare un evento all'argomento personalizzato

Attivare un evento per vedere come la Griglia di eventi distribuisce il messaggio nell'endpoint. Ottenere prima di tutto l'URL e la chiave per l'argomento personalizzato. Usare ancora una volta il nome dell'argomento personalizzato per `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Per semplificare questo articolo, si useranno dati di evento di esempio da inviare all'argomento personalizzato. In genere, i dati dell'evento vengono inviati da un'applicazione o un servizio di Azure. CURL è un'utilità che invia richieste HTTP. In questo articolo CURL viene usato per inviare l'evento all'argomento personalizzato.  L'esempio seguente invia tre eventi all'argomento della griglia di eventi:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

Passare all'archivio code nel portale e notare che Griglia di eventi ha inviato i tre eventi alla coda.

![Visualizzare i messaggi](./media/custom-event-to-queue-storage/messages.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Se si intende continuare a usare questo evento, non è necessario pulire le risorse create con questo articolo. In caso contrario, usare il comando seguente per eliminare le risorse create con questo articolo.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare argomenti e sottoscrizioni di eventi, è possibile approfondire le operazioni possibili con la griglia di eventi:

- [Informazioni sulla griglia di eventi](overview.md)
- [Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato (anteprima)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorare le modifiche alla macchina virtuale con la griglia di eventi di Azure e le app per la logica](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md)
