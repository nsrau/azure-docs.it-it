---
title: Elencare i BLOB con .NET - Archiviazione di Azure
description: Informazioni su come elencare i BLOB in un contenitore nell'account di Archiviazione di Azure usando la libreria client .NET. Gli esempi di codice illustrano come elencare i BLOB in un elenco semplice oppure in modo gerarchico, come se fossero organizzati in directory o cartelle.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/05/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9295b1d8a54d9c3c1a138a54f4b3706bd39227fd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009543"
---
# <a name="list-blobs-with-net"></a>Elencare BLOB con .NET

Quando si elencano i BLOB tramite il codice, è possibile specificare diverse opzioni per gestire il modo in cui i risultati vengono restituiti dall'Archiviazione di Azure. È possibile specificare il numero di risultati da restituire in ogni set di risultati e quindi recuperare i set successivi. È possibile specificare un prefisso per restituire i BLOB i cui nomi iniziano con tale carattere o stringa. Ed è possibile elencare i BLOB in una struttura elenco semplice o gerarchica. Un elenco gerarchico restituisce i BLOB come se fossero organizzati in cartelle.

Questo articolo illustra come elencare i BLOB usando la [libreria client di Archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-blob-listing-options"></a>Informazioni sulle opzioni per l'elenco di BLOB

Per elencare i BLOB in un account di archiviazione, chiamare uno dei metodi seguenti:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [BlobContainerClient. GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs?view=azure-dotnet)
- [BlobContainerClient.GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync?view=azure-dotnet)
- [BlobContainerClient.GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy?view=azure-dotnet)
- [BlobContainerClient.GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync?view=azure-dotnet)

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Per elencare i BLOB in un contenitore, chiamare uno dei metodi seguenti:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Gli overload di questi metodi offrono opzioni aggiuntive per la gestione del modo in cui i BLOB vengono restituiti dall'operazione di elencazione. Le opzioni disponibili sono descritte nelle sezioni seguenti.

---

### <a name="manage-how-many-results-are-returned"></a>Gestire il numero di risultati restituiti

Per impostazione predefinita, un'operazione di elenco restituisce fino a 5000 risultati alla volta, ma è possibile specificare il numero di risultati che devono essere restituiti da ogni operazione di elenco. Gli esempi presentati in questo articolo illustrano come eseguire questa operazione.

Se un'operazione di elenco restituisce più di 5000 BLOB o se il numero di BLOB disponibili supera il numero specificato, archiviazione di Azure restituisce un *token di continuazione* con l'elenco di BLOB. Un token di continuazione è un valore opaco che è possibile usare per recuperare il set di risultati successivo da Archiviazione di Azure.

Nel codice, controllare il valore del token di continuazione per determinare se è di Null. Se il token di continuazione è Null, il set di risultati è completo. Se il token di continuazione non è Null, chiamare di nuovo l'operazione di elenco, passando il token di continuazione per recuperare il set di risultati successivo, fino a quando il token di continuazione non diventa Null.

### <a name="filter-results-with-a-prefix"></a>Filtrare i risultati con un prefisso

Per filtrare l'elenco dei contenitori, specificare una stringa per il parametro `prefix`. La stringa di prefisso può includere uno o più caratteri. Archiviazione di Azure restituisce solo i BLOB i cui nomi iniziano con tale prefisso.

### <a name="return-metadata"></a>Restituire i metadati

È possibile restituire i metadati dei BLOB con i risultati. 

- Se si usa .NET V12 SDK, specificare il valore **dei metadati** per l'enumerazione [BlobTraits](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.models.blobtraits?view=azure-dotnet) .

- Se si usa .NET V11 SDK, specificare il valore **dei metadati** per l'enumerazione [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) . Archiviazione di Azure include i metadati con ogni BLOB restituito, pertanto non è necessario chiamare uno dei metodi **FetchAttributes** in questo contesto per recuperare i metadati dei BLOB.

### <a name="flat-listing-versus-hierarchical-listing"></a>Confronto tra elenco semplice e gerarchico

I BLOB in Archiviazione di Azure sono organizzati secondo un paradigma semplice, anziché gerarchico (come un file system classico). Tuttavia, è possibile organizzare i BLOB in *directory virtuali* per simulare una struttura di cartelle. Una directory virtuale costituisce parte del nome del BLOB ed è indicata dal carattere di delimitazione.

Per organizzare i BLOB in directory virtuali, usare un carattere di delimitazione nel nome del BLOB. Il delimitatore predefinito è la barra (/), ma è possibile specificare qualsiasi carattere.

Se i BLOB vengono denominati usando un delimitatore, è possibile scegliere un elenco gerarchico. In un'operazione di elenco gerarchico, Archiviazione di Azure restituisce le directory virtuali e i BLOB sotto l'oggetto padre. È possibile chiamare l'operazione di elenco in modo ricorsivo per attraversare la gerarchia in modo analogo a come si attraverserebbe un file system classico a livello di codice.

## <a name="use-a-flat-listing"></a>Usare un elenco semplice

Per impostazione predefinita, un'operazione di elenco restituisce i BLOB in un elenco semplice. In un elenco semplice, i BLOB non sono organizzati in base alla directory virtuale.

Nell'esempio seguente vengono elencati i BLOB nel contenitore specificato utilizzando un elenco semplice, con una dimensione di segmento facoltativa specificata. Il nome del BLOB viene scritto in una finestra della console.

Se è stata abilitata la funzionalità spazio dei nomi gerarchico nell'account, le directory non sono virtuali. Sono invece oggetti concreti e indipendenti. Pertanto, le directory vengono visualizzate nell'elenco come BLOB di lunghezza zero.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

L'output è simile al seguente:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Usare un elenco gerarchico

Quando si chiama un'operazione di elenco gerarchico, Archiviazione di Azure restituisce le directory virtuali e i BLOB del primo livello della gerarchia. Il [prefisso](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) proprietà di ogni directory virtuale è impostato in modo che sia possibile passare il prefisso in una chiamata ricorsiva per recuperare la directory successiva.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Per elencare i BLOB in modo gerarchico, chiamare il metodo [BlobContainerClient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy?view=azure-dotnet)o [BlobContainerClient. GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync?view=azure-dotnet) .

L'esempio seguente elenca i BLOB nel contenitore specificato usando un elenco gerarchico, con una dimensione di segmento facoltativa specificata, e scrive il nome del BLOB nella finestra della console.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Per elencare i BLOB in modo gerarchico, impostare il parametro `useFlatBlobListing` del metodo di elenco su **false**.

Nell'esempio seguente vengono elencati i BLOB nel contenitore specificato utilizzando un elenco semplice, con una dimensione di segmento facoltativa specificata. Il nome del BLOB viene scritto nella finestra della console.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

L'output è simile al seguente:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> In un'operazione di elenco gerarchico non è possibile elencare gli snapshot BLOB.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Elencare i BLOB](/rest/api/storageservices/list-blobs)
- [Enumerazione di risorse Blob](/rest/api/storageservices/enumerating-blob-resources)
