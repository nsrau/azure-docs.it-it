---
title: Gestione della concorrenza nell'archiviazione BLOB
titleSuffix: Azure Storage
description: Informazioni su come gestire più writer in un BLOB implementando la concorrenza ottimistica o pessimistica nell'applicazione. La concorrenza ottimistica verifica il valore ETag per un BLOB e lo confronta con l'ETag fornito. La concorrenza pessimistica usa un lease esclusivo per bloccare il BLOB con altri writer.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523518"
---
# <a name="managing-concurrency-in-blob-storage"></a>Gestione della concorrenza nell'archiviazione BLOB

Le applicazioni moderne spesso hanno più utenti che visualizzano e aggiornano contemporaneamente i dati. Gli sviluppatori di applicazioni devono considerare attentamente come fornire un'esperienza prevedibile agli utenti finali, in particolare per gli scenari in cui più utenti possono aggiornare gli stessi dati. Gli sviluppatori in genere prendono in considerazione tre strategie principali di concorrenza dei dati:  

- **Concorrenza ottimistica**: un'applicazione che esegue un aggiornamento, come parte dell'aggiornamento, determina se i dati sono stati modificati dall'ultima lettura dei dati da parte dell'applicazione. Ad esempio, se due utenti che visualizzano una pagina wiki effettuano un aggiornamento alla pagina, la piattaforma wiki deve garantire che il secondo aggiornamento non sovrascriva il primo aggiornamento. Deve inoltre assicurarsi che entrambi gli utenti capiscano se l'aggiornamento è stato eseguito correttamente. Questa strategia viene usata con maggiore frequenza nelle applicazioni Web.

- **Concorrenza pessimistica**: un'applicazione che cerca di eseguire un aggiornamento apporterà un blocco su un oggetto che impedisce ad altri utenti di aggiornare i dati fino a quando il blocco non viene rilasciato. In uno scenario di replica dei dati primario/secondario, ad esempio, in cui solo il database primario esegue gli aggiornamenti, il database primario in genere tiene un blocco esclusivo sui dati per un periodo di tempo prolungato, in modo da evitare che nessuno possa aggiornarlo.

- **Ultimo writer wins**: un approccio che consente alle operazioni di aggiornamento di procedere senza prima determinare se un'altra applicazione ha aggiornato i dati da quando è stato letto. Questo approccio viene in genere usato quando i dati vengono partizionati in modo che più utenti non accedano contemporaneamente agli stessi dati. Può inoltre essere utile per l'elaborazione di flussi dei dati di breve durata.

Archiviazione di Azure supporta tutte e tre le strategie, sebbene sia caratterizzata dalla capacità di fornire supporto completo per la concorrenza ottimistica e pessimistica. Archiviazione di Azure è stata progettata per adottare un modello di coerenza forte che garantisce che dopo l'esecuzione di un'operazione di inserimento o aggiornamento da parte del servizio, le operazioni di lettura successive restituiranno l'aggiornamento più recente.

Oltre a selezionare una strategia di concorrenza appropriata, gli sviluppatori devono anche conoscere il modo in cui una piattaforma di archiviazione isola le modifiche, in particolare le modifiche apportate allo stesso oggetto tra le transazioni. Archiviazione di Azure usa l'isolamento dello snapshot per consentire le operazioni di lettura contemporaneamente alle operazioni di scrittura all'interno di una singola partizione. L'isolamento dello snapshot garantisce che tutte le operazioni di lettura restituiscano uno snapshot coerente dei dati anche quando si verificano gli aggiornamenti.

È possibile scegliere di usare modelli di concorrenza ottimistica o pessimistica per gestire l'accesso a BLOB e contenitori. Se non si specifica in modo esplicito una strategia, per impostazione predefinita viene vinto l'ultimo writer.  

## <a name="optimistic-concurrency"></a>Concorrenza ottimistica

Archiviazione di Azure assegna un identificatore a ogni oggetto archiviato. Questo identificatore viene aggiornato ogni volta che viene eseguita un'operazione di scrittura su un oggetto. L'identificatore viene restituito al client come parte di una risposta HTTP GET nell'intestazione ETag definita dal protocollo HTTP.

Un client che esegue un aggiornamento può inviare l'ETag originale insieme a un'intestazione condizionale per garantire che un aggiornamento venga eseguito solo se è stata soddisfatta una determinata condizione. Se, ad esempio, si specifica l'intestazione **If-Match** , archiviazione di Azure verifica che il valore dell'ETag specificato nella richiesta di aggiornamento corrisponda all'eTag per l'oggetto da aggiornare. Per ulteriori informazioni sulle intestazioni condizionali, vedere [specifica di intestazioni condizionali per le operazioni del servizio BLOB](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).

Il processo è il seguente:  

1. Recuperare un BLOB da archiviazione di Azure. La risposta include un valore di intestazione HTTP ETag che identifica la versione corrente dell'oggetto.
1. Quando si aggiorna il BLOB, includere il valore ETag ricevuto al passaggio 1 nell'intestazione condizionale **If-Match** della richiesta di scrittura. Archiviazione di Azure confronta il valore ETag nella richiesta con il valore ETag corrente del BLOB.
1. Se il valore ETag corrente del BLOB è diverso da quello specificato nell'intestazione condizionale **If-Match** fornita nella richiesta, archiviazione di Azure restituisce il codice di stato HTTP 412 (condizione preliminare non riuscita). Questo errore indica al client che un altro processo ha aggiornato il BLOB dal momento in cui il client lo ha recuperato.
1. Se il valore ETag corrente del BLOB è la stessa versione dell'ETag nell'intestazione condizionale **If-Match** nella richiesta, archiviazione di Azure esegue l'operazione richiesta e aggiorna il valore ETag corrente del BLOB.  

Gli esempi di codice seguenti illustrano come costruire una condizione **If-Match** nella richiesta di scrittura che controlla il valore eTag per un BLOB. Archiviazione di Azure valuta se l'ETag corrente del BLOB è uguale al valore ETag specificato nella richiesta ed esegue l'operazione di scrittura solo se i due valori ETag corrispondono. Se un altro processo ha aggiornato il BLOB nel frattempo, archiviazione di Azure restituisce un messaggio di stato HTTP 412 (condizione preliminare non riuscita).  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Archiviazione di Azure supporta anche altre intestazioni condizionali, tra cui if **-Modified-Since**, **If-Unmodified-Since** e **If-None-Match**. Per altre informazioni, vedere [Specifica di intestazioni condizionali per le operazioni del servizio BLOB](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

## <a name="pessimistic-concurrency-for-blobs"></a>Concorrenza pessimistica per i BLOB

Per bloccare un BLOB al fine di usarlo in modo esclusivo, è possibile acquisire un lease su di esso. Quando si acquisisce il lease, si specifica la durata del lease. Un lease finito può essere valido da 15 a 60 secondi. Un lease può anche essere infinito, che equivale a un blocco esclusivo. È possibile rinnovare un lease finito per estenderlo ed è possibile rilasciare il lease al termine dell'operazione. Archiviazione di Azure rilascia automaticamente i lease finiti alla scadenza.  

I lease consentono di supportare diverse strategie di sincronizzazione, tra cui operazioni esclusive di lettura/scrittura condivise, operazioni di lettura esclusive e operazioni di lettura esclusive e operazioni di lettura/scrittura condivise. Quando esiste un lease, archiviazione di Azure impone l'accesso esclusivo alle operazioni di scrittura per il titolare del lease. Tuttavia, garantire l'esclusività per le operazioni di lettura richiede che lo sviluppatore assicuri che tutte le applicazioni client usino un ID lease e che un solo client alla volta disponga di un ID lease valido. Le operazioni di lettura che non includono un ID lease determinano letture condivise.  

Gli esempi di codice seguenti illustrano come acquisire un lease esclusivo su un BLOB, aggiornare il contenuto del BLOB fornendo l'ID lease, quindi rilasciare il lease. Se il lease è attivo e l'ID lease non viene specificato in una richiesta di scrittura, l'operazione di scrittura avrà esito negativo e verrà restituito il codice di errore 412 (condizione preliminare non riuscita).  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>Concorrenza pessimistica per i contenitori

I lease sui contenitori abilitano le stesse strategie di sincronizzazione supportate per i BLOB, tra cui la scrittura esclusiva/lettura condivisa, la scrittura esclusiva/lettura esclusiva e la scrittura condivisa/lettura esclusiva. Per i contenitori, tuttavia, il blocco esclusivo viene applicato solo alle operazioni DELETE. Per eliminare un contenitore con un lease attivo, un client deve includere l'ID lease attivo con la richiesta di eliminazione. Tutte le altre operazioni del contenitore riusciranno in un contenitore con lease senza l'ID lease.  

## <a name="next-steps"></a>Passaggi successivi

* [Specifica di intestazioni condizionali per le operazioni del servizio BLOB](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Lease Container](/rest/api/storageservices/lease-container)
* [Lease del BLOB](/rest/api/storageservices/lease-blob)
