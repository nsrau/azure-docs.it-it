---
title: Gestire le impostazioni di recapito per le sottoscrizioni di Griglia di eventi di Azure
description: Descrive come personalizzare le opzioni di recapito degli eventi per Griglia di eventi.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 65e79f492e96c418502e096b60992ba992868dd7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034934"
---
# <a name="manage-event-grid-delivery-settings"></a>Gestire le impostazioni di recapito di Griglia di eventi

Quando si crea una sottoscrizione di eventi, è possibile personalizzare le impostazioni per il recapito di tali eventi. È possibile impostare l'intervallo di tempo in cui Griglia di eventi prova a recapitare il messaggio. È possibile anche impostare un account di archiviazione per gli eventi che non possono essere recapitati a un endpoint.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-retry-policy"></a>Impostare criteri di ripetizione

Quando si crea una sottoscrizione di Griglia di eventi, è possibile impostare valori per l'intervallo di tempo in cui il servizio deve provare a recapitare l'evento. Per impostazione predefinita, i tentativi vengono eseguiti per 24 ore (1440 minuti) e per un massimo di 30 volte. Per la sottoscrizione di Griglia di eventi è possibile impostare uno dei valori seguenti.

Per impostare la durata TTL dell'evento su un valore diverso da 1440 minuti, usare il codice seguente:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --event-ttl 720
```

Per impostare il numero massimo di tentativi su un valore diverso da 30, usare il codice seguente:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --max-delivery-attempts 18
```

Se si impostano entrambe le opzioni `event-ttl` e `max-deliver-attempts`, Griglia di eventi usa la prima che scade per eseguire i tentativi successivi.

## <a name="set-dead-letter-location"></a>Impostare la posizione degli eventi non recapitabili

Quando Griglia di eventi non riesce a eseguire un'operazione di recapito, l'evento non recapitato può essere inviato a un account di archiviazione. Questo processo è noto come inserimento nella coda di eventi non recapitabili. Per impostazione predefinita, Griglia di eventi non attiva l'inserimento nella coda di eventi non recapitabili. Per abilitarlo, è necessario specificare che un account di archiviazione deve contenere gli eventi non recapitati durante la sottoscrizione di eventi. Per risolvere le operazioni di recapito, viene eseguito il pull degli eventi da questo account di archiviazione.

Griglia di eventi invia un evento alla posizione dei messaggi non recapitabili dopo aver esaurito tutti i tentativi di recapito oppure in caso di ricevimento di un messaggio di errore indicante l'impossibilità di recapitare l'evento. Se, ad esempio, durante il recapito di un evento Griglia di eventi riceve un errore di formato non corretto, tale evento viene immediatamente inviato alla posizione dei messaggi non recapitabili.

Prima di impostare la posizione dei messaggi non recapitabili, è necessario avere un account di archiviazione con un contenitore. L'endpoint di questo contenitore deve essere specificato durante la creazione della sottoscrizione di eventi. Di seguito è indicato il formato dell'endpoint: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Lo script seguente si serve dell'ID di risorsa di un account di archiviazione esistente e crea una sottoscrizione di eventi che usa un contenitore dell'account per l'endpoint di eventi non recapitabili.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Per usare Griglia di eventi per rispondere a eventi non recapitati, [creare una sottoscrizione di eventi](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) per l'archivio BLOB di eventi non recapitabili. Ogni volta che l'archivio BLOB riceve un evento non recapitato, Griglia di eventi invia una notifica al gestore. Il gestore risponde con le azioni da eseguire per la riconciliazione degli eventi non recapitati. 

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
