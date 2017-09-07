---
title: Reazione a eventi di Archiviazione BLOB di Azure (anteprima) | Microsoft Docs
description: Usare Griglia di eventi di Azure per sottoscrivere eventi di Archiviazione BLOB.
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/25/2017
ms.topic: article
ms.service: storage
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b9b117bdeb62f5ebb2e4e3fbfe71572068927082
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---

# <a name="reacting-to-blob-storage-events-preview"></a>Reazione a eventi di archiviazione BLOB (anteprima)

Gli eventi di archiviazione BLOB di Azure consentono alle applicazioni di reagire alla creazione e all'eliminazione di oggetti BLOB usando moderne architetture senza server e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti.  Gli eventi vengono invece inviati attraverso [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai sottoscrittori, ad esempio [Funzioni di Azure](https://azure.microsoft.com/services/functions/), [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) o anche al listener http personalizzato in uso, e si paga solo ciò che si usa.

Tra gli scenari comuni di eventi di archiviazione BLOB sono inclusi l'elaborazione di immagini o video, l'indicizzazione delle ricerche o qualsiasi flusso di lavoro orientato ai file.  I caricamenti asincroni di file sono operazioni perfette per gli eventi.  Quando le modifiche non sono frequenti, ma lo scenario richiede tempi di risposta immediata, un'architettura basata su eventi può essere particolarmente efficiente.

![Modello di Griglia di eventi di Azure](./media/storage-blob-event-overview/event-grid-functional-model.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="join-the-preview"></a>Accedere all'anteprima
Gli eventi di archiviazione BLOB sono disponibili per l'anteprima.  Gli utenti possono richiedere di accedere all'anteprima eseguendo questi comandi in base alla sottoscrizione:
```azurecli-interactive
az provider register --namespace  Microsoft.EventGrid
az feature register --name storageEventSubscriptions --namespace Microsoft.EventGrid
```
Le sottoscrizioni vengono aggiunte al programma di anteprima in base alla disponibilità di capacità.  È possibile monitorare lo stato della richiesta usando il comando seguente:
```azurecli-interactive
az feature show --name storageEventSubscriptions --namespace Microsoft.EventGrid
```
Quando lo stato della registrazione diventa "Registered" (Registrato), si è stati ammessi al programma di anteprima ed è possibile sottoscrivere gli eventi di archiviazione BLOB per gli account nell'area *Stati Uniti centro-occidentali*.  Per un rapido esempio, vedere [Route Blob storage events to a custom web endpoint](storage-blob-event-quickstart.md) (Indirizzare gli eventi di archiviazione BLOB verso un endpoint Web personalizzato).

## <a name="blob-storage-accounts"></a>Account di archiviazione BLOB
Gli eventi di archiviazione BLOB sono disponibili negli [account di archiviazione BLOB](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) (e non negli account di archiviazione di uso generico).  Gli account di archiviazione BLOB sono account di archiviazione specializzati per l'archiviazione dei dati non strutturati come BLOB (oggetti) in Archiviazione di Azure. Gli account di archiviazione BLOB sono simili agli account di archiviazione di uso generico e includono tutte le straordinarie caratteristiche di durabilità, disponibilità, scalabilità e prestazioni che si usano già normalmente, inclusa la coerenza API al 100% per i BLOB in blocchi e i BLOB di aggiunta. Per applicazioni che richiedono solo archivi BLOB in blocchi o BLOB di aggiunta, è consigliabile usare account di archiviazione BLOB.

## <a name="available-blob-storage-events"></a>Eventi di archiviazione BLOB disponibili
Griglia di eventi usa le [sottoscrizioni di eventi](../../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori.  Le sottoscrizioni di eventi di archiviazione BLOB possono includere due tipi di eventi:  

> |Nome evento|Descrizione|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Generato quando un BLOB viene creato o sostituito tramite l'operazione `PutBlob`, `PutBlockList` o `CopyBlob`|
> |`Microsoft.Storage.BlobDeleted`|Generato quando un BLOB viene eliminato tramite un'operazione `DeleteBlob`|

## <a name="event-schema"></a>Schema di eventi
Gli eventi di archiviazione BLOB contengono tutte le informazioni necessarie per rispondere alle modifiche dei dati.  Un evento di archiviazione BLOB è riconoscibile perché la proprietà eventType inizia con "Microsoft.Storage".  
Informazioni aggiuntive sull'utilizzo delle proprietà degli eventi di Griglia di eventi sono disponibili nello [schema di eventi di Griglia di eventi](../../event-grid/event-schema.md).  

> |Proprietà|Tipo|Descrizione|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |argomento|string|ID completo di Azure Resource Manager dell'account di archiviazione che genera l'evento.|
> |subject|string|Percorso relativo della risorsa all'oggetto che è l'argomento dell'evento, con lo stesso formato esteso di Azure Resource Manager che si usa per descrivere gli account di archiviazione, i servizi e i contenitori per Controllo degli accessi in base al ruolo di Azure.  Questo formato include un nome di BLOB che conserva le maiuscole/minuscole.|
> |eventTime|string|Data e ora in cui è stato generato l'evento, nel formato ISO 8601|
> |eventType|string|"Microsoft.Storage.BlobCreated" o "Microsoft.Storage.BlobDeleted"|
> |ID|string|Identificatore univoco di questo evento|
> |data|object|Raccolta di dati dell'evento specifico dell'archiviazione BLOB|
> |data.contentType|string|Tipo di contenuto del BLOB, come verrebbe restituito nell'intestazione Content-Type dal BLOB|
> |data.contentLength|number|Dimensione del BLOB come valore intero che rappresenta un numero di byte, come verrebbe restituita nell'intestazione Content-Length dal BLOB.  Inviato con l'evento BlobCreated ma non con BlobDeleted.|
> |data.url|string|URL dell'oggetto che è l'argomento dell'evento|
> |data.eTag|string|Etag dell'oggetto quando viene generato questo evento.  Non disponibile per l'evento BlobDeleted.|
> |data.api|string|Nome dell'operazione API che ha generato questo evento.  Per gli eventi BlobCreated questo valore è "PutBlob", "PutBlockList" o "CopyBlob".  Per gli eventi BlobDeleted questo valore è "DeleteBlob".  Questi valori sono gli stessi nomi di API che sono presenti nei registri di diagnostica di Archiviazione di Azure.  Vedere [Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Operazioni e messaggi di stati registrati).|
> |data.sequencer|string|Valore stringa opaca che rappresenta la sequenza logica di eventi per qualsiasi nome di BLOB specifico.  Gli utenti possono usare il confronto tra stringhe standard per comprendere la sequenza relativa di due eventi sullo stesso nome di BLOB.|
> |data.requestId|string|ID di richiesta generato dal servizio per l'operazione API di archiviazione.  Può essere usato per la correlazione ai registri di diagnostica di Archiviazione di Azure usando il campo "request-id-header" nei registri e viene restituito dall'avvio di una chiamata API nell'intestazione 'x-ms-request-id'. Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log).|
> |data.clientRequestId|string|ID di richiesta generato dal client per l'operazione API di archiviazione.  Può essere usato per la correlazione ai registri di diagnostica di Archiviazione di Azure usando il campo "client-request-id" nei registri e può essere inserito nelle richieste client usando l'intestazione "x-ms-client-request-id". Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log).|
> |data.storageDiagnostics|object|Dati di diagnostica occasionalmente inclusi dal servizio Archiviazione di Azure.  Quando è presente, questa proprietà deve essere ignorata dai consumer di eventi.|

Di seguito è riportato un esempio di un evento BlobCreated:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  }
}]

```

Per altre informazioni, vedere lo [schema di eventi di archiviazione BLOB](../../event-grid/event-schema.md#azure-blob-storage).

## <a name="filtering-events"></a>Filtro degli eventi
Le sottoscrizioni di eventi BLOB possono essere filtrate in base al tipo di evento e in base al nome del contenitore e al nome del BLOB dell'oggetto che è stato creato o eliminato.  I filtri dell'oggetto in Griglia di eventi si basano sulle corrispondenze con "inizia con" e "termina con". In questo modo gli eventi con un argomento corrispondente vengono recapitati al sottoscrittore.
L'oggetto di eventi di archiviazione BLOB usa il formato:
```json
/blobServices/default/containers/<containername>/blobs/<blobname>
```
Per trovare la corrispondenza di tutti gli eventi per un account di archiviazione, è possibile non impostare alcun filtro dell'oggetto.

Per trovare la corrispondenza di eventi da BLOB creati in un set di contenitori che condividono un prefisso, usare un filtro `subjectBeginsWith` come:
```json
/blobServices/default/containers/containerprefix
```
Per trovare la corrispondenza di eventi da BLOB creati in un contenitore specifico, usare un filtro `subjectBeginsWith` come:
```json
/blobServices/default/containers/containername/
```
Per trovare la corrispondenza di eventi da BLOB creati in un contenitore specifico che condividono un prefisso di nome di BLOB, usare un filtro `subjectBeginsWith` come:
```json
/blobServices/default/containers/containername/blobs/blobprefix
```

Per trovare la corrispondenza di eventi da BLOB creati in un contenitore specifico che condividono un suffisso di BLOB, usare un filtro `subjectEndsWith`, ad esempio ".log" o ".jpg".

Per altre informazioni, vedere [Concetti di Griglia di eventi di Azure](../../event-grid/concepts.md#filters).

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi
Per le applicazioni che gestiscono gli eventi di archiviazione BLOB è consigliabile seguire alcune procedure:
> [!div class="checklist"]
> * Dal momento che vi possono essere più sottoscrizioni configurate per instradare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi derivino da un'origine specifica, ma controllare l'argomento del messaggio per assicurarsi che provengano dall'account di archiviazione previsto.
> * Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
> * Dal momento che i messaggi non arrivano in ordine e giungono con un po' di ritardo, usare i campi etag per verificare se le informazioni disponibili sugli oggetti sono ancora aggiornate.  È possibile usare anche i campi del sequencer per comprendere l'ordine degli eventi relativi a un oggetto specifico.
> * Usare il campo blobType per capire il tipo di operazioni consentite sul BLOB e quali tipi di librerie client è consigliabile usare per accedere al BLOB.
> * Usare il campo URL con i costruttori `CloudBlockBlob` e `CloudAppendBlob` per accedere al BLOB.
> * Ignorare i campi che non si conoscono.  Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.


## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altre informazioni su Griglia di eventi e la possibilità di provare a usare gli eventi di archiviazione BLOB:

- [Informazioni sulla griglia di eventi](../../event-grid/overview.md)
- [Route Blob storage Events to a custom web endpoint](storage-blob-event-quickstart.md) (Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato)
