---
title: Messaggi non recapitabili e ripetizione dei tentativi per le sottoscrizioni di Griglia di eventi di Azure
description: Descrive come personalizzare le opzioni di recapito degli eventi per Griglia di eventi. Impostare una destinazione per i messaggi non recapitabili e specificare il numero di tentativi di recapito.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: fcf3ecaff6e8ba1421496a96d01428946cf8ab8e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077783"
---
# <a name="dead-letter-and-retry-policies"></a>Messaggi non recapitabili e criteri di ripetizione dei tentativi

Quando si crea una sottoscrizione di eventi, è possibile personalizzare le impostazioni per il recapito di tali eventi. Questo articolo illustra come configurare una posizione per gli eventi non recapitabili e personalizzare le impostazioni di ripetizione dei tentativi. Per informazioni su queste funzionalità, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Impostare la posizione degli eventi non recapitabili

Per impostare una posizione per gli eventi non recapitabili, è necessario un account di archiviazione per contenere gli eventi che non possono essere recapitati a un endpoint. Lo script seguente si serve dell'ID di risorsa di un account di archiviazione esistente e crea una sottoscrizione di eventi che usa un contenitore dell'account per l'endpoint di eventi non recapitabili.

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
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Per disabilitare gli eventi non recapitabili, rieseguire il comando per creare la sottoscrizione di eventi ma non specificare un valore per `deadletter-endpoint`. Non è necessario eliminare la sottoscrizione di eventi.

## <a name="set-retry-policy"></a>Impostare criteri di ripetizione

Quando si crea una sottoscrizione di Griglia di eventi, è possibile impostare valori per l'intervallo di tempo in cui il servizio deve provare a recapitare l'evento. Per impostazione predefinita, i tentativi vengono eseguiti per 24 ore (1440 minuti) e per un massimo di 30 volte. Per la sottoscrizione di Griglia di eventi è possibile impostare uno dei valori seguenti. Il valore di time-to-live degli eventi deve essere un numero intero compreso tra 1 e 1440. Il numero massimo di tentativi di recapito deve essere un numero intero compreso tra 1 e 30.

Non è possibile configurare la [pianificazione della ripetizione](delivery-and-retry.md#retry-schedule-and-duration).

Per impostare la durata TTL dell'evento su un valore diverso da 1440 minuti, usare il codice seguente:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Per impostare il numero massimo di tentativi su un valore diverso da 30, usare il codice seguente:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Se si impostano entrambe le opzioni `event-ttl` e `max-deliver-attempts`, Griglia di eventi usa la prima che scade per eseguire i tentativi successivi.

## <a name="next-steps"></a>Passaggi successivi

* Per un'applicazione di esempio che usa un'app per le funzioni di Azure per elaborare eventi di messaggi non recapitabili, vedere [Azure Event Grid Dead Letter Samples for .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/) (Esempi di messaggi non recapitabili per Griglia di eventi di Azure).
* Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
