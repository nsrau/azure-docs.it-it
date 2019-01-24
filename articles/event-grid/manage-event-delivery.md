---
title: Messaggi non recapitabili e ripetizione dei tentativi per le sottoscrizioni di Griglia di eventi di Azure
description: Descrive come personalizzare le opzioni di recapito degli eventi per Griglia di eventi. Impostare una destinazione per i messaggi non recapitabili e specificare il numero di tentativi di recapito.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 8660f09c41cf6226f2ffb173508d37c260522b80
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474248"
---
# <a name="dead-letter-and-retry-policies"></a>Messaggi non recapitabili e criteri di ripetizione dei tentativi

Quando si crea una sottoscrizione di eventi, è possibile personalizzare le impostazioni per il recapito di tali eventi. Questo articolo illustra come configurare una posizione per gli eventi non recapitabili e personalizzare le impostazioni di ripetizione dei tentativi. Per informazioni su queste funzionalità, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).

## <a name="install-preview-feature"></a>Installare la funzionalità di anteprima

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Impostare la posizione degli eventi non recapitabili

Per impostare una posizione per gli eventi non recapitabili, è necessario un account di archiviazione per contenere gli eventi che non possono essere recapitati a un endpoint. Gli esempi ricevono l'ID di risorsa da un account di archiviazione esistente. Creano una sottoscrizione evento che usa un contenitore in tale account di archiviazione per l'endpoint di messaggi non recapitabili.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Per disabilitare gli eventi non recapitabili, rieseguire il comando per creare la sottoscrizione di eventi ma non specificare un valore per `deadletter-endpoint`. Non è necessario eliminare la sottoscrizione di eventi.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$containername = "testcontainer"

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Per disabilitare gli eventi non recapitabili, rieseguire il comando per creare la sottoscrizione di eventi ma non specificare un valore per `DeadLetterEndpoint`. Non è necessario eliminare la sottoscrizione di eventi.

## <a name="set-retry-policy"></a>Impostare criteri di ripetizione

Quando si crea una sottoscrizione di Griglia di eventi, è possibile impostare valori per l'intervallo di tempo in cui il servizio deve provare a recapitare l'evento. Per impostazione predefinita, la Griglia di eventi effettua tentativi per 24 ore (1.440 minuti) o 30 volte. Per la sottoscrizione di Griglia di eventi è possibile impostare uno dei valori seguenti. Il valore di time-to-live degli eventi deve essere un numero intero compreso tra 1 e 1440. Il valore relativo al numero massimo di tentativi deve essere un numero intero compreso tra 1 e 30.

Non è possibile configurare la [pianificazione della ripetizione](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

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

Per impostare il numero massimo di tentativi su un valore diverso da 30, usare:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Se si impostano entrambi `event-ttl` e `max-deliver-attempts`, la Griglia di eventi usa quello che scade per primo per stabilire quando interrompere la consegna dell'evento.

### <a name="powershell"></a>PowerShell

Per impostare la durata TTL dell'evento su un valore diverso da 1440 minuti, usare il codice seguente:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Per impostare il numero massimo di tentativi su un valore diverso da 30, usare:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Se si impostano entrambi `EventTtl` e `MaxDeliveryAttempt`, la Griglia di eventi usa quello che scade per primo per stabilire quando interrompere la consegna dell'evento.

## <a name="next-steps"></a>Passaggi successivi

* Per un'applicazione di esempio che usa un'app per le funzioni di Azure per elaborare eventi di messaggi non recapitabili, vedere [Azure Event Grid Dead Letter Samples for .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/) (Esempi di messaggi non recapitabili per Griglia di eventi di Azure).
* Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
