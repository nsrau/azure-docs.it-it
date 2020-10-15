---
title: Copiare un BLOB con le API di archiviazione di Azure
description: Informazioni su come copiare un BLOB usando le librerie client di archiviazione di Azure.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 2c474ed4d4158356075f861c3c0d5ace69173255
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90014651"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Copiare un BLOB con le librerie client di archiviazione di Azure

Questo articolo illustra come copiare un BLOB in un account di archiviazione di Azure. Viene inoltre illustrato come interrompere un'operazione di copia asincrona. Il codice di esempio usa le librerie client di archiviazione di Azure.

## <a name="about-copying-blobs"></a>Informazioni sulla copia di BLOB

Quando si copia un BLOB nello stesso account di archiviazione, si tratta di un'operazione sincrona. Quando si esegue la copia tra gli account, si tratta di un'operazione asincrona.

Il BLOB di origine per un'operazione di copia può essere un BLOB in blocchi, un BLOB di Accodamento, un BLOB di pagine o uno snapshot. Se il Blob di destinazione esiste già, deve essere dello stesso tipo del Blob di origine. Un BLOB di destinazione esistente verrà sovrascritto.

Non è possibile modificare il BLOB di destinazione mentre è in corso un'operazione di copia. Un BLOB di destinazione può disporre di una sola operazione di copia in attesa. In altre parole, un BLOB non può essere la destinazione di più operazioni di copia in sospeso.

L'intero BLOB o file di origine viene sempre copiato. La copia di un intervallo di byte o di un set di blocchi non è supportata.

Quando viene copiato un BLOB, le proprietà di sistema vengono copiate nel BLOB di destinazione con gli stessi valori.

Un'operazione di copia può assumere uno dei seguenti formati:

- Copiare un BLOB di origine in un BLOB di destinazione con un nome diverso. Il BLOB di destinazione può essere un BLOB esistente dello stesso tipo di BLOB (blocco, Accodamento o pagina) oppure può essere un nuovo blob creato dall'operazione di copia.
- Copiare un BLOB di origine in un BLOB di destinazione con lo stesso nome, sostituendo in modo efficace il BLOB di destinazione. Tale operazione di copia rimuove tutti i blocchi di cui non è stato eseguito il commit e sovrascrive i metadati del BLOB di destinazione.
- Copiare un file di origine nel servizio file di Azure in un BLOB di destinazione. Il BLOB di destinazione può essere un BLOB in blocchi esistente oppure può essere un nuovo BLOB in blocchi creato dall'operazione di copia. La copia da file a BLOB di pagine o BLOB di Accodamento non è supportata.
- Copiare uno snapshot sul relativo BLOB di base. Promuovendo uno snapshot alla posizione del BLOB di base, è possibile ripristinare la versione precedente di un BLOB.
- Copiare uno snapshot in un BLOB di destinazione con un nome diverso. Il BLOB di destinazione risultante è un BLOB scrivibile, non uno snapshot.

## <a name="copy-a-blob"></a>Copiare un BLOB

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Per copiare un BLOB, chiamare uno dei metodi seguenti:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

I `StartCopyFromUri` `StartCopyFromUriAsync` metodi e restituiscono un oggetto [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) che contiene informazioni sull'operazione di copia.

L'esempio di codice seguente ottiene un [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) che rappresenta un blob creato in precedenza e lo copia in un nuovo BLOB nello stesso contenitore:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Per copiare un BLOB, chiamare uno dei metodi seguenti:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

I `StartCopy` `StartCopyAsync` metodi e restituiscono un valore ID di copia usato per controllare lo stato o per interrompere l'operazione di copia.

L'esempio di codice seguente ottiene un riferimento a un blob creato in precedenza e lo copia in un nuovo BLOB nello stesso contenitore:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

# <a name="python-v12"></a>[Python V12](#tab/python)

Per copiare un BLOB, chiamare il metodo [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) . Il `start_copy_from_url` metodo restituisce un dizionario che contiene informazioni sull'operazione di copia.

L'esempio di codice seguente ottiene un [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) che rappresenta un blob creato in precedenza e lo copia in un nuovo BLOB nello stesso contenitore:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Interrompi un'operazione di copia

L'interruzione di un'operazione di copia comporta un BLOB di destinazione di lunghezza zero. Tuttavia, i metadati per il BLOB di destinazione avranno i nuovi valori copiati dal BLOB di origine o impostati in modo esplicito durante l'operazione di copia. Per preservare i metadati originali da prima della copia, creare uno snapshot del BLOB di destinazione prima di chiamare uno dei metodi di copia.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Controllare la proprietà [BlobProperties. CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) nel BLOB di destinazione per ottenere lo stato dell'operazione di copia. Al termine della copia, verrà eseguito il commit del Blob finale.

Quando si interrompe un'operazione di copia, lo stato di copia del BLOB di destinazione viene impostato su [CopyStatus. interrotta](/dotnet/api/microsoft.azure.storage.blob.copystatus).

I metodi [AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) e [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) annullano un'operazione di copia in corso.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Controllare la proprietà [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) nel BLOB di destinazione per ottenere lo stato dell'operazione di copia. Al termine della copia, verrà eseguito il commit del Blob finale.

Quando si interrompe un'operazione di copia, lo stato di copia del BLOB di destinazione viene impostato su [CopyStatus. interrotta](/dotnet/api/microsoft.azure.storage.blob.copystatus).

I metodi [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) e [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) annullano un'operazione di copia in corso.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

# <a name="python-v12"></a>[Python V12](#tab/python)

Controllare la voce "status" nel dizionario [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) restituito dal metodo [get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) per ottenere lo stato dell'operazione di copia. Al termine della copia, verrà eseguito il commit del Blob finale.

Quando si interrompe un'operazione di copia, lo [stato](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) viene impostato su "interrotto".

Il metodo [abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) Annulla un'operazione di copia in corso.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Azure SDK

Ottenere altre informazioni sugli SDK di Azure:

 - [Azure SDK per .NET](https://github.com/azure/azure-sdk-for-net)
 - [Azure SDK per Java](https://github.com/azure/azure-sdk-for-java)
 - [Azure SDK per Python](https://github.com/azure/azure-sdk-for-python)
 - [Azure SDK per JavaScript](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Passaggi successivi

Gli argomenti seguenti contengono informazioni sulla copia di BLOB e l'interruzione delle operazioni di copia in corso usando le API REST di Azure.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Abort Copy Blob](/rest/api/storageservices/abort-copy-blob)
