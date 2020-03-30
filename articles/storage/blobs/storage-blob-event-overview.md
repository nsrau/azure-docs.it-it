---
title: Reazione a eventi di Archiviazione BLOB di Azure | Microsoft Docs
description: Usare la Griglia di eventi di Azure per sottoscrivere eventi di archiviazione BLOB.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 5281dab8fd42326d88964614fd20a81621b5e9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268496"
---
# <a name="reacting-to-blob-storage-events"></a>Reazione a eventi di Archiviazione BLOB di Azure

Gli eventi di Archiviazione di Azure consentono alle applicazioni di reagire agli eventi, ad esempio la creazione e l'eliminazione di BLOB. e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti.

Gli eventi vengono inseriti usando [Griglia di eventi](https://azure.microsoft.com/services/event-grid/) di Azure per sottoscrittori come Funzioni di Azure, App per la logica di Azure o anche nel listener http. La parte migliore è che si paga solo per quello che si utilizza.

L'archiviazione BLOB invia eventi a Griglia di eventi, che fornisce un recapito affidabile degli eventi alle applicazioni tramite criteri di ripetizione dei tentativi e caratteri non recapitabili.

Tra gli scenari comuni di eventi di Archiviazione BLOB sono inclusi l'elaborazione di immagini o video, l'indicizzazione delle ricerche o qualsiasi flusso di lavoro orientato ai file. I caricamenti asincroni di file sono operazioni perfette per gli eventi. Quando le modifiche non sono frequenti, ma lo scenario richiede tempi di risposta immediata, un'architettura basata su eventi può essere particolarmente efficiente.

Se vuoi provarlo ora, consulta uno di questi articoli della Guida introduttiva:

|Se si desidera utilizzare questo strumento:    |Vedere questo articolo:See this article: |
|--|-|
|Portale di Azure    |[Guida introduttiva: Instradare gli eventi di archiviazione BLOB all'endpoint Web con il portale di AzureQuickstart: Route Blob storage events to web endpoint with the Azure portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Guida introduttiva: Instradare gli eventi di archiviazione all'endpoint Web con PowerShellQuickstart: Route storage events to web endpoint with PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Interfaccia della riga di comando di Azure    |[Guida introduttiva: Instradare gli eventi di archiviazione all'endpoint Web con l'interfaccia della riga di comando di AzureQuickstart: Route storage events to web endpoint with Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Per visualizzare esempi approfonditi di reazione agli eventi di archiviazione BLOB usando le funzioni di Azure, vedere gli articoli seguenti:To view in-depth examples of reacting to Blob storage events by using Azure functions, see these articles:

- Esercitazione: Usare gli eventi Gen2 di Archiviazione dati di Azure per aggiornare una tabella Delta di [Databricks.](data-lake-storage-events.md)
- [Esercitazione: Automatizzare il ridimensionamento delle immagini caricate utilizzando Griglia di eventiTutorial: Automate resizing uploaded images using Event Grid](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Solo gli account di archiviazione di tipo **archiviazione V2 (utilizzo generico v2)** e **BlobStorage** supportano l'integrazione degli eventi. Il tipo **Archiviazione (utilizzo generico V1)***non* supporta l'integrazione con Griglia di eventi.

## <a name="the-event-model"></a>Il modello di evento

Griglia di eventi utilizza [le sottoscrizioni](../../event-grid/concepts.md#event-subscriptions) di eventi per instradare i messaggi di evento ai sottoscrittori. Questa immagine illustra la relazione tra editori di eventi, sottoscrizioni di eventi e gestori eventi.

![Modello di Griglia di eventi di Azure](./media/storage-blob-event-overview/event-grid-functional-model.png)

In primo luogo, sottoscrivere un endpoint a un evento. Quindi, quando viene attivato un evento, il servizio Griglia di eventi invierà i dati relativi a tale evento all'endpoint.

Vedere l'articolo dello schema degli eventi di archiviazione BLOB per visualizzare:See the [Blob storage events schema](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) article to view:

> [!div class="checklist"]
> * Un elenco completo degli eventi di archiviazione BLOB e delle modalità di attivazione di ogni evento.
> * Un esempio dei dati che la griglia di eventi invierebbe per ognuno di questi eventi.
> * Scopo di ogni coppia di valori chiave visualizzata nei dati.

## <a name="filtering-events"></a>Filtro degli eventi

Gli eventi BLOB [possono essere filtrati](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) in base al tipo di evento, al nome del contenitore o al nome dell'oggetto creato/eliminato. I filtri in Griglia di eventi corrispondono all'inizio o alla fine dell'oggetto in modo che gli eventi con un oggetto corrispondente vadano al sottoscrittore.

Per ulteriori informazioni su come applicare filtri, vedere [Filtrare gli eventi per Griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

L'oggetto di eventi di archiviazione BLOB usa il formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Per trovare la corrispondenza di tutti gli eventi per un account di archiviazione, è possibile non impostare alcun filtro dell'oggetto.

Per trovare la corrispondenza di eventi da BLOB creati in un set di contenitori che condividono un prefisso, usare un filtro `subjectBeginsWith` come:

```
/blobServices/default/containers/containerprefix
```

Per trovare la corrispondenza di eventi da BLOB creati in un contenitore specifico, usare un filtro `subjectBeginsWith` come:

```
/blobServices/default/containers/containername/
```

Per trovare la corrispondenza di eventi da BLOB creati in un contenitore specifico che condividono un prefisso di nome di BLOB, usare un filtro `subjectBeginsWith` come:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Per trovare la corrispondenza di eventi da BLOB creati in un contenitore specifico che condividono un suffisso di BLOB, usare un filtro `subjectEndsWith`, ad esempio ".log" o ".jpg". Per altre informazioni, vedere [Concetti di Griglia di eventi di Azure](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi

Per le applicazioni che gestiscono gli eventi di archiviazione BLOB è consigliabile seguire alcune procedure:
> [!div class="checklist"]
> * Dal momento che vi possono essere più sottoscrizioni configurate per instradare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi derivino da un'origine specifica, ma controllare l'argomento del messaggio per assicurarsi che provengano dall'account di archiviazione previsto.
> * Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
> * Poiché i messaggi possono arrivare dopo un certo ritardo, utilizzare i campi etag per capire se le informazioni sugli oggetti sono ancora aggiornate. Per informazioni su come usare il campo etag, vedere [Gestione della concorrenza nell'archiviazione BLOB](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * Poiché i messaggi possono arrivare in ordine, utilizzare i campi del sequencer per comprendere l'ordine degli eventi su un oggetto specifico. Il campo sequencer è un valore stringa che rappresenta la sequenza logica di eventi per un particolare nome BLOB. È possibile usare il confronto tra stringhe standard per comprendere la sequenza relativa di due eventi sullo stesso nome BLOB.
> * Usare il campo blobType per capire il tipo di operazioni consentite sul BLOB e quali tipi di librerie client è consigliabile usare per accedere al BLOB. I valori validi sono `BlockBlob` o `PageBlob`. 
> * Usare il campo URL con i costruttori `CloudBlockBlob` e `CloudAppendBlob` per accedere al BLOB.
> * Ignorare i campi che non si conoscono. Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
> * Se si desidera assicurarsi che l'evento **Microsoft.Storage.BlobCreated** venga attivato solo quando viene `CopyBlob` `PutBlob`eseguito il commit completo di un BLOB di blocchi, filtrare l'evento per le chiamate all'API REST o , `PutBlockList` . `FlushWithClose` Queste chiamate API attivano l'evento **Microsoft.Storage.BlobCreated** solo dopo il commit completo dei dati in un BLOB di blocchi. Per informazioni su come creare un filtro, vedere [Filtrare gli eventi per Griglia di eventi.](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)


## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni su Griglia di eventi e la possibilità di provare a usare gli eventi di archiviazione BLOB:

- [Informazioni sulla griglia di eventi](../../event-grid/overview.md)
- [Route Blob storage Events to a custom web endpoint](storage-blob-event-quickstart.md) (Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato)
