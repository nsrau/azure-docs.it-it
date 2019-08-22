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
ms.openlocfilehash: b813ef89bb1a55f769d0ea2391855ba5d671c140
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648801"
---
# <a name="reacting-to-blob-storage-events"></a>Reazione a eventi di Archiviazione BLOB di Azure

Gli eventi di archiviazione di Azure consentono alle applicazioni di reagire agli eventi, ad esempio la creazione e l'eliminazione di BLOB, usando architetture senza server moderne. e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti.

Al contrario, gli eventi vengono inviati tramite [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai sottoscrittori, ad esempio funzioni di Azure, app per la logica di Azure o anche al listener HTTP personalizzato, pagando solo per le risorse usate.

Gli eventi di archiviazione BLOB vengono inviati in modo affidabile al servizio griglia di eventi, che fornisce servizi di recapito affidabili alle applicazioni tramite criteri avanzati per i tentativi e la consegna dei messaggi non recapitabili.

Tra gli scenari comuni di eventi di Archiviazione BLOB sono inclusi l'elaborazione di immagini o video, l'indicizzazione delle ricerche o qualsiasi flusso di lavoro orientato ai file. I caricamenti asincroni di file sono operazioni perfette per gli eventi. Quando le modifiche non sono frequenti, ma lo scenario richiede tempi di risposta immediata, un'architettura basata su eventi può essere particolarmente efficiente.

Per provare a eseguire questa operazione, vedere gli articoli introduttivi seguenti:

|Se si desidera utilizzare questo strumento:    |Vedere questo articolo: |
|--|-|
|Portale di Azure    |[Avvio rapido: Indirizzare gli eventi di archiviazione BLOB all'endpoint Web con la portale di Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Avvio rapido: Indirizzare gli eventi di archiviazione all'endpoint Web con PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Interfaccia della riga di comando di Azure    |[Avvio rapido: Indirizzare gli eventi di archiviazione all'endpoint Web con l'interfaccia della riga di comando](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Se il proprio account dispone di uno spazio dei nomi gerarchico, in questa esercitazione verrà illustrato come connettere una sottoscrizione di griglia di eventi, una funzione di Azure e un [processo](https://docs.azuredatabricks.net/user-guide/jobs.html) in Azure Databricks: [Esercitazione: Usare eventi Azure Data Lake Storage Gen2 per aggiornare una tabella](data-lake-storage-events.md)Delta di databricks.

## <a name="the-event-model"></a>Modello di eventi

Griglia di eventi usa le [sottoscrizioni di eventi](../../event-grid/concepts.md#event-subscriptions) per indirizzare i messaggi di evento ai sottoscrittori. Questa immagine illustra la relazione tra autori di eventi, sottoscrizioni di eventi e gestori eventi.

![Modello di Griglia di eventi di Azure](./media/storage-blob-event-overview/event-grid-functional-model.png)

Prima di tutto, sottoscrivere un endpoint per un evento. Quindi, quando viene attivato un evento, il servizio griglia di eventi invierà i dati relativi all'evento all'endpoint.

Vedere l'articolo [dello schema degli eventi di archiviazione BLOB](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) da visualizzare:

> [!div class="checklist"]
> * Elenco completo degli eventi di archiviazione BLOB e modalità di attivazione di ogni evento.
> * Un esempio dei dati che la griglia di eventi invierà per ognuno di questi eventi.
> * Scopo di ogni coppia chiave-valore visualizzata nei dati.

## <a name="filtering-events"></a>Filtro degli eventi

Le sottoscrizioni di eventi BLOB possono essere filtrate in base al tipo di evento e in base al nome del contenitore e al nome del BLOB dell'oggetto che è stato creato o eliminato.  È possibile applicare filtri alle sottoscrizioni di eventi durante la [creazione](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) della sottoscrizione stessa o [in un secondo momento](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). I filtri dell'oggetto in Griglia di eventi si basano sulle corrispondenze con "inizia con" e "termina con". In questo modo gli eventi con un oggetto corrispondente vengono recapitati al sottoscrittore.

Per altre informazioni su come applicare i filtri, vedere [filtrare gli eventi per griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

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
> * Per assicurarsi che l'evento **Microsoft. storage. BlobCreated** venga attivato solo quando viene eseguito il commit completo di un BLOB in blocchi, filtrare l'evento per le `CopyBlob`chiamate all' `PutBlockList` API `FlushWithClose` Rest, `PutBlob`o. Queste chiamate API attivano l'evento **Microsoft. storage. BlobCreated** solo dopo che è stato eseguito il commit completo dei dati in un BLOB in blocchi. Per informazioni su come creare un filtro, vedere [filtrare gli eventi per griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni su Griglia di eventi e la possibilità di provare a usare gli eventi di archiviazione BLOB:

- [Informazioni sulla griglia di eventi](../../event-grid/overview.md)
- [Route Blob storage Events to a custom web endpoint](storage-blob-event-quickstart.md) (Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato)
