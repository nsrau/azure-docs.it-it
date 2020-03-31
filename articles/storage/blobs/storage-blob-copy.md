---
title: Copiare un BLOB con .NET - Archiviazione di AzureCopy a blob with .NET - Azure Storage
description: Informazioni su come copiare un BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135888"
---
# <a name="copy-a-blob-with-net"></a>Copiare un BLOB con .NETCopy a blob with .NET

Questo articolo illustra come copiare un BLOB con un account di archiviazione di Azure.This article demonstrates how to copy a blob with an Azure Storage account. Viene inoltre illustrato come interrompere un'operazione di copia asincrona. Il codice di esempio usa la [libreria client di Archiviazione di Azure per .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-copying-blobs"></a>Informazioni sulla copia di BLOB

Quando si copia un BLOB all'interno dello stesso account di archiviazione, si tratta di un'operazione sincrona. Quando si copiano tra account è un'operazione asincrona. I metodi [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) e [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) restituiscono un valore ID di copia utilizzato per controllare lo stato o interrompere l'operazione di copia.

Il BLOB di origine per un'operazione di copia può essere un BLOB in blocchi, un BLOB di accodamento, un BLOB di pagine o uno snapshot. Se il Blob di destinazione esiste già, deve essere dello stesso tipo del Blob di origine. Eventuali Blob di destinazione esistenti verranno sovrascritti. 

Il BLOB di destinazione non può essere modificato mentre è in corso un'operazione di copia. A un Blob di destinazione può corrispondere una sola operazione di copia Blob in attesa. In altre parole, un BLOB non può essere la destinazione per più operazioni di copia in sospeso.

Viene sempre copiato l'intero BLOB o file di origine. La copia di un intervallo di byte o di un set di blocchi non è supportata.

Quando un BLOB viene copiato, le proprietà di sistema vengono copiate nel BLOB di destinazione con gli stessi valori.

Per tutti i tipi di BLOB, è possibile controllare la proprietà [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) nel BLOB di destinazione per ottenere lo stato dell'operazione di copia. Al termine della copia, verrà eseguito il commit del Blob finale.

Un'operazione di copia può assumere uno dei seguenti formati:

  - È possibile copiare un Blob di origine in un Blob di destinazione con un nome diverso. Il BLOB di destinazione può essere un BLOB esistente dello stesso tipo di BLOB (blocco, aggiunta o pagina) o può essere un nuovo BLOB creato dall'operazione di copia.
  - È possibile copiare un BLOB di origine in un BLOB di destinazione con lo stesso nome, sostituendo in modo efficace il BLOB di destinazione. Un'operazione di copia di questo tipo rimuove tutti i blocchi di cui non è stato eseguito il commit e sovrascrive i metadati del BLOB di destinazione.
  - È possibile copiare un file di origine nel servizio File di Azure in un BLOB di destinazione. Il BLOB di destinazione può essere un BLOB in blocchi esistente o un nuovo BLOB in blocchi creato dall'operazione di copia. La copia da file ai BLOB di pagine o all'aggiunta di BLOB non è supportata.
  - È possibile copiare uno snapshot sul relativo BLOB di base. Promuovendo uno snapshot alla posizione del BLOB di base, è possibile ripristinare la versione precedente di un BLOB.
  - È possibile copiare uno snapshot in un BLOB di destinazione con un nome diverso. Il BLOB di destinazione risultante è un BLOB scrivibile, non uno snapshot.

## <a name="copy-a-blob"></a>Copiare un BLOBCopy a blob

Per copiare un BLOB, chiamare uno dei metodi seguenti:To copy a blob, call one of the following methods:

 - [StartCopy (Copia inizio)](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

L'esempio di codice seguente ottiene un riferimento a un BLOB creato in precedenza e lo copia in un nuovo BLOB nello stesso contenitore:The following code example gets a reference to a blob created previously and copies it to a new blob in the same container:

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

        // Lease the source blob for the copy operation to prevent another client from modifying it.
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
            Console.WriteLine();
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

## <a name="abort-a-blob-copy-operation"></a>Interrompere un'operazione di copia BLOBAbort a blob copy operation

L'interruzione di un'operazione di copia genera un BLOB di destinazione di lunghezza zero per i BLOB di blocchi, l'aggiunta di BLOB e i BLOB di pagine. Tuttavia, i metadati per il BLOB di destinazione avranno i nuovi valori copiati dal BLOB di origine o impostati in modo esplicito nella chiamata StartCopy o StartCopyAsync.However, the metadata for the destination blob will have the new values copied from the source blob or set explicitly in the [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) or [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) call. Per mantenere i metadati originali precedenti alla copia, creare `StartCopy` `StartCopyAsync`uno snapshot del BLOB di destinazione prima di chiamare o .

Quando si interrompe un'operazione di copia BLOB in corso, [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) del BLOB di destinazione viene impostato su [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

I metodi [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) e [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) annullano un'operazione di copia del BLOB in corso e lasciano un BLOB di destinazione con lunghezza zero e metadati completi.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passaggi successivi

The following topics contain information about copying blobs and aborting ongoing copy operations by using the Azure REST APIs.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Abort Copy Blob](/rest/api/storageservices/abort-copy-blob)
