---
title: Inviare eventi personalizzati a una connessione ibrida - Griglia di eventi, interfaccia della riga di comando di Azure
description: Usare Griglia di eventi di Azure e l'interfaccia della riga di comando di Azure per pubblicare un argomento e sottoscrivere l'evento. Per l'endpoint viene usata una connessione.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 02/02/2019
ms.topic: tutorial
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 2a050f8bc5d2437c7cdbadcf3296c2ef6d4659dc
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728640"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Esercitazione: Instradare eventi personalizzati a Connessioni ibride di Inoltro di Azure con l'interfaccia della riga di comando di Azure e Griglia di eventi

La griglia di eventi di Azure è un servizio di gestione degli eventi per il cloud. Connessioni ibride di Inoltro di Azure è uno gestore dell'evento supportato. Usare le connessioni ibride come gestore dell'evento quando è necessario elaborare gli eventi da applicazioni che non hanno un endpoint pubblico. Queste applicazioni potrebbero trovarsi all'interno della rete aziendale dell'organizzazione. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un argomento personalizzato, sottoscrivere l'argomento e attivare l'evento per visualizzare il risultato. Inviare gli eventi alla connessione ibrida.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente disponga già di una connessione ibrida e di un'applicazione listener. Per iniziare a usare le connessioni ibride, vedere [Introduzione alle connessioni ibride di inoltro - .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) oppure [Introduzione alle connessioni ibride di inoltro - Node](../service-bus-relay/relay-hybrid-connections-node-get-started.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!NOTE]
> Se si usa l'interfaccia della riga di comando di Azure nel computer locale, usare l'interfaccia della riga di comando di Azure versione 2.0.56 o successive. Per istruzioni sull'installazione della versione più recente dell'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Gli argomenti della griglia di eventi sono risorse di Azure e devono essere inseriti in un gruppo di risorse di Azure. Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). 

L'esempio seguente crea un gruppo di risorse denominato *gridResourceGroup* nella località *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Creare un argomento personalizzato

Un argomento di Griglia di eventi fornisce un endpoint definito dall'utente in cui vengono pubblicati gli eventi. L'esempio seguente crea l'argomento personalizzato nel gruppo di risorse. Sostituire `<topic_name>` con un nome univoco per l'argomento personalizzato. Il nome dell'argomento della griglia di eventi deve essere univoco perché è rappresentato da una voce DNS.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Sottoscrivere un argomento personalizzato

Si sottoscrive un argomento della Griglia di eventi per indicare alla griglia di eventi quali sono gli eventi di cui si vuole tenere traccia. Nell'esempio seguente viene effettuata la sottoscrizione dell'argomento personalizzato creato e viene passato l'ID risorsa della connessione ibrida per l'endpoint. L'ID della connessione ibrida ha il formato:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

Lo script seguente ottiene l'ID della risorsa dello spazio dei nomi di inoltro. Costruisce l'ID per la connessione ibrida e sottoscrive un argomento della griglia di eventi. Imposta il tipo di endpoint su `hybridconnection` e usa l'ID della connessione ibrida per l'endpoint.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

Si noti che per la sottoscrizione è impostata una [data di scadenza](concepts.md#event-subscription-expiration).

## <a name="create-application-to-process-events"></a>Creare un'applicazione per elaborare gli eventi

È necessaria un'applicazione in grado di recuperare gli eventi dalla connessione ibrida. L'[esempio di consumer della connessione ibrida di Griglia di eventi di Microsoft Azure per C#](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination) esegue tale operazione. I passaggi preliminari sono stati completati.

1. Assicurarsi di disporre di Visual Studio 2017, versione 15.5 o successiva.

1. Clonare il repository nel computer locale.

1. Caricare il progetto HybridConnectionConsumer in Visual Studio.

1. In Program.cs, sostituire `<relayConnectionString>` e `<hybridConnectionName>` con la stringa di connessione di inoltro e il nome della connessione ibrida creata.

1. Compilare ed eseguire l'applicazione da Visual Studio.

## <a name="send-an-event-to-your-topic"></a>Inviare un evento all'argomento

Attivare un evento per vedere come la Griglia di eventi distribuisce il messaggio nell'endpoint. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per attivare l'evento. In alternativa, è possibile usare [l'applicazione per la pubblicazione della Griglia di eventi](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher).

Ottenere prima di tutto l'URL e la chiave per l'argomento personalizzato. Usare ancora una volta il nome dell'argomento personalizzato per `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Per semplificare questo articolo, si useranno dati di evento di esempio da inviare all'argomento personalizzato. In genere, i dati dell'evento vengono inviati da un'applicazione o un servizio di Azure. CURL è un'utilità che invia richieste HTTP. In questo articolo CURL viene usato per inviare l'evento all'argomento personalizzato.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

L'applicazione listener deve ricevere il messaggio dell'evento.

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
