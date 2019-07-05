---
title: Reazione a eventi di Archiviazione BLOB di Azure | Microsoft Docs
description: Usare la Griglia di eventi di Azure per sottoscrivere eventi di archiviazione BLOB.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444688"
---
# <a name="reacting-to-blob-storage-events"></a>Reazione a eventi di Archiviazione BLOB di Azure

Eventi di archiviazione di Azure consentono alle applicazioni di reagire agli eventi, ad esempio la creazione e l'eliminazione del BLOB usando moderne architetture senza server. e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti.

Al contrario, gli eventi vengono inviati [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai sottoscrittori, ad esempio funzioni di Azure, App per la logica di Azure, o persino di personalizzati listener http e si paga solo per ciò che usi.

Eventi di archiviazione BLOB vengono inviati in modo affidabile per il servizio griglia di eventi che fornisce servizi di distribuzione affidabile per le applicazioni tramite criteri di ripetizione dei tentativi avanzati e il recapito non recapitabilità.

Tra gli scenari comuni di eventi di Archiviazione BLOB sono inclusi l'elaborazione di immagini o video, l'indicizzazione delle ricerche o qualsiasi flusso di lavoro orientato ai file. I caricamenti asincroni di file sono operazioni perfette per gli eventi. Quando le modifiche non sono frequenti, ma lo scenario richiede tempi di risposta immediata, un'architettura basata su eventi può essere particolarmente efficiente.

Se si desidera provare questa procedura, vedere questi articoli di Guida introduttiva:

|Se si vuole usare questo strumento:    |Vedere questo articolo: |
|--|-|
|Portale di Azure    |[Avvio rapido: Indirizzare eventi di archiviazione Blob a endpoint web nel portale di Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Interfaccia della riga di comando di Azure    |[Avvio rapido: Indirizzare gli eventi di archiviazione a endpoint web con PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Avvio rapido: Indirizzare eventi di archiviazione a endpoint web con Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>Il modello di eventi

Griglia di eventi Usa [le sottoscrizioni di eventi](../../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Questa immagine illustra la relazione tra gli autori di eventi, le sottoscrizioni di eventi e gestori di eventi.

![Modello di Griglia di eventi di Azure](./media/storage-blob-event-overview/event-grid-functional-model.png)

Eseguire la sottoscrizione prima di tutto un endpoint a un evento. Quindi, quando si verifica un evento, il servizio griglia di eventi invierà i dati sull'evento all'endpoint.

Vedere le [schema di eventi di archiviazione Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) articolo da visualizzare:

> [!div class="checklist"]
> * Un elenco completo di eventi di archiviazione Blob e modo in cui ogni evento viene attivato.
> * Un esempio dei dati di griglia di eventi invierebbe per ciascuno di questi eventi.
> * Lo scopo di ogni coppia chiave / valore che viene visualizzato nei dati.

## <a name="filtering-events"></a>Filtro degli eventi

Le sottoscrizioni di eventi BLOB possono essere filtrate in base al tipo di evento e in base al nome del contenitore e al nome del BLOB dell'oggetto che è stato creato o eliminato.  È possibile applicare filtri alle sottoscrizioni di eventi durante la [creazione](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) della sottoscrizione stessa o [in un secondo momento](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). I filtri dell'oggetto in Griglia di eventi si basano sulle corrispondenze con "inizia con" e "termina con". In questo modo gli eventi con un oggetto corrispondente vengono recapitati al sottoscrittore.

Per altre informazioni su come applicare filtri, vedere [filtrare gli eventi di griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

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
> * Dal momento che i messaggi non arrivano in ordine e giungono con un po' di ritardo, usare i campi etag per verificare se le informazioni disponibili sugli oggetti sono ancora aggiornate.  È possibile usare anche i campi del sequencer per comprendere l'ordine degli eventi relativi a un oggetto specifico.
> * Usare il campo blobType per capire il tipo di operazioni consentite sul BLOB e quali tipi di librerie client è consigliabile usare per accedere al BLOB. I valori validi sono `BlockBlob` o `PageBlob`. 
> * Usare il campo URL con i costruttori `CloudBlockBlob` e `CloudAppendBlob` per accedere al BLOB.
> * Ignorare i campi che non si conoscono. Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
> * Se si desidera assicurarsi che il **Microsoft.Storage.BlobCreated** evento viene attivato solo quando un Blob in blocchi viene completamente eseguito il commit, filtrare l'evento per il `CopyBlob`, `PutBlob`, `PutBlockList` o `FlushWithClose` REST Chiamate API. Questi trigger di chiamate API di **Microsoft.Storage.BlobCreated** evento solo dopo che i dati si impegnerà a un Blob in blocchi. Per informazioni su come creare un filtro, vedere [filtrare gli eventi di griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni su Griglia di eventi e la possibilità di provare a usare gli eventi di archiviazione BLOB:

- [Informazioni sulla griglia di eventi](../../event-grid/overview.md)
- [Route Blob storage Events to a custom web endpoint](storage-blob-event-quickstart.md) (Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato)
