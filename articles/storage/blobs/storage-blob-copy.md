---
title: Copiare un BLOB con .NET-archiviazione di Azure
description: Informazioni su come copiare un BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9b3dba0041b38d9d59a10eaf80592bab91f65b98
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600280"
---
# <a name="copy-a-blob-with-net"></a>Copiare un BLOB con .NET

Questo articolo illustra come copiare un BLOB con un account di archiviazione di Azure. Viene inoltre illustrato come interrompere un'operazione di copia asincrona. Il codice di esempio usa la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage/client).

## <a name="about-copying-blobs"></a>Informazioni sulla copia di BLOB

Quando si copia un BLOB nello stesso account di archiviazione, si tratta di un'operazione sincrona. Quando si esegue la copia tra gli account, si tratta di un'operazione asincrona. I metodi [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) e [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) restituiscono un valore ID di copia usato per controllare lo stato o per interrompere l'operazione di copia.

Il BLOB di origine per un'operazione di copia può essere un BLOB in blocchi, un BLOB di Accodamento, un BLOB di pagine o uno snapshot. Se il BLOB di destinazione esiste già, deve essere dello stesso tipo di BLOB del BLOB di origine. Eventuali BLOB di destinazione esistenti verranno sovrascritti. 

Non è possibile modificare il BLOB di destinazione mentre è in corso un'operazione di copia. Un BLOB di destinazione può avere una sola operazione di copia BLOB in attesa. In altre parole, un BLOB non può essere la destinazione di più operazioni di copia in sospeso.

L'intero BLOB o file di origine viene sempre copiato. La copia di un intervallo di byte o di un set di blocchi non è supportata.

Quando viene copiato un BLOB, le proprietà di sistema vengono copiate nel BLOB di destinazione con gli stessi valori.

Per tutti i tipi di BLOB, è possibile controllare la proprietà [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) nel BLOB di destinazione per ottenere lo stato dell'operazione di copia. Al termine della copia verrà eseguito il commit del BLOB finale.

Un'operazione di copia può assumere uno dei seguenti formati:

  - È possibile copiare un BLOB di origine in un BLOB di destinazione con un nome diverso. Il BLOB di destinazione può essere un BLOB esistente dello stesso tipo di BLOB (blocco, Accodamento o pagina) oppure può essere un nuovo blob creato dall'operazione di copia.
  - È possibile copiare un BLOB di origine in un BLOB di destinazione con lo stesso nome, sostituendo in modo efficace il BLOB di destinazione. Tale operazione di copia rimuove tutti i blocchi di cui non è stato eseguito il commit e sovrascrive i metadati del BLOB di destinazione.
  - È possibile copiare un file di origine nel servizio file di Azure in un BLOB di destinazione. Il BLOB di destinazione può essere un BLOB in blocchi esistente oppure può essere un nuovo BLOB in blocchi creato dall'operazione di copia. La copia da file a BLOB di pagine o BLOB di Accodamento non è supportata.
  - È possibile copiare uno snapshot sul relativo BLOB di base. Promuovendo uno snapshot alla posizione del BLOB di base, è possibile ripristinare la versione precedente di un BLOB.
  - È possibile copiare uno snapshot in un BLOB di destinazione con un nome diverso. Il BLOB di destinazione risultante è un BLOB scrivibile, non uno snapshot.

## <a name="copy-a-blob"></a>Copiare un BLOB

Per copiare un BLOB, chiamare uno dei metodi seguenti:

 - [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

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

## <a name="abort-a-blob-copy-operation"></a>Interrompi un'operazione di copia BLOB

L'interruzione di un'operazione di copia comporta un BLOB di destinazione di lunghezza zero per i BLOB in blocchi, i BLOB di Accodamento e i BLOB di pagine. Tuttavia, i metadati per il BLOB di destinazione avranno i nuovi valori copiati dal BLOB di origine o impostati in modo esplicito nella chiamata a [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) o [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) . Per preservare i metadati originali prima della copia, creare uno snapshot del BLOB di destinazione prima di chiamare `StartCopy` o `StartCopyAsync`.

Quando si interrompe un'operazione di copia BLOB in corso, [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) del BLOB di destinazione è impostato su [CopyStatus. Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

I metodi [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) e [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) annullano un'operazione di copia BLOB in corso e lasciano un BLOB di destinazione con lunghezza zero e metadati completi.

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

Gli argomenti seguenti contengono informazioni sulla copia di BLOB e l'interruzione delle operazioni di copia in corso usando le API REST di Azure.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Interrompi copia BLOB](/rest/api/storageservices/abort-copy-blob)
