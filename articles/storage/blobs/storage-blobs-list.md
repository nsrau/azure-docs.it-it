---
title: Elencare BLOB con .NET-archiviazione di Azure
description: Informazioni su come elencare i BLOB in un contenitore nell'account di archiviazione di Azure usando la libreria client .NET. Gli esempi di codice illustrano come elencare i BLOB in un elenco semplice o come elencare i BLOB in modo gerarchico, come se fossero organizzati in directory o cartelle.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/08/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4b6dc9d80cfe96e501e575d265b9fa383b1c4d2c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902009"
---
# <a name="list-blobs-with-net"></a>Elencare BLOB con .NET

Quando si elencano i BLOB dal codice, è possibile specificare diverse opzioni per gestire il modo in cui i risultati vengono restituiti dall'archiviazione di Azure. È possibile specificare il numero di risultati da restituire in ogni set di risultati, quindi recuperare i set successivi. È possibile specificare un prefisso per restituire i BLOB i cui nomi iniziano con tale carattere o stringa. Ed è possibile elencare i BLOB in una struttura di elenco semplice o gerarchicamente. Un elenco gerarchico restituisce i BLOB come se fossero organizzati in cartelle. 

Questo articolo illustra come elencare i BLOB usando la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-blob-listing-options"></a>Informazioni sulle opzioni per l'elenco di BLOB

Per elencare i BLOB in un account di archiviazione, chiamare uno dei metodi seguenti:

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Per elencare i BLOB in un contenitore, chiamare uno dei metodi seguenti:

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Gli overload di questi metodi forniscono opzioni aggiuntive per la gestione del modo in cui i BLOB vengono restituiti dall'operazione di elenco. Queste opzioni sono descritte nelle sezioni riportate di seguito.

### <a name="manage-how-many-results-are-returned"></a>Gestire il numero di risultati restituiti

Per impostazione predefinita, un'operazione di elenco restituisce fino a 5000 risultati alla volta. Per restituire un set di risultati più piccolo, fornire un valore diverso da zero per il parametro `maxresults` quando si chiama uno dei metodi **ListBlobs** .

Se un'operazione di elenco restituisce più di 5000 BLOB o se è stato specificato un valore per `maxresults` in modo che l'operazione di elenco restituisca un subset di contenitori nell'account di archiviazione, archiviazione di Azure restituisce un *token di continuazione* con l'elenco di BLOB. Un token di continuazione è un valore opaco che è possibile usare per recuperare il set successivo di risultati dall'archiviazione di Azure.

Nel codice, controllare il valore del token di continuazione per determinare se è null. Quando il token di continuazione è null, il set di risultati è completo. Se il token di continuazione non è null, chiamare di nuovo l'operazione di listato, passando il token di continuazione per recuperare il set di risultati successivo, fino a quando il token di continuazione non è null.

### <a name="filter-results-with-a-prefix"></a>Filtrare i risultati con un prefisso

Per filtrare l'elenco dei contenitori, specificare una stringa per il parametro `prefix`. La stringa di prefisso può includere uno o più caratteri. Archiviazione di Azure restituisce quindi solo i BLOB i cui nomi iniziano con tale prefisso.

### <a name="return-metadata"></a>Restituisci metadati

Per restituire i metadati dei BLOB con i risultati, specificare il valore **dei metadati** per l'enumerazione [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) . Archiviazione di Azure include metadati con ogni BLOB restituito, pertanto non è necessario chiamare uno dei metodi **FetchAttributes** in questo contesto per recuperare i metadati del BLOB.

### <a name="flat-listing-versus-hierarchical-listing"></a>Elenco semplice rispetto alla lista gerarchica

I BLOB in archiviazione di Azure sono organizzati in un paradigma semplice, anziché in un paradigma gerarchico (ad esempio un file system classico). Tuttavia, è possibile organizzare i BLOB in *directory virtuali* per simulare una struttura di cartelle. Una directory virtuale fa parte del nome del BLOB ed è indicata dal carattere delimitatore.

Per organizzare i BLOB in directory virtuali, usare un carattere delimitatore nel nome del BLOB. Il carattere delimitatore predefinito è una barra (/), ma è possibile specificare qualsiasi carattere come delimitatore.

Se i BLOB vengono denominati usando un delimitatore, è possibile scegliere di elencare i BLOB in modo gerarchico. Per un'operazione di elenco gerarchico, archiviazione di Azure restituisce le directory virtuali e i BLOB sotto l'oggetto padre. È possibile chiamare l'operazione di elenco in modo ricorsivo per attraversare la gerarchia, in modo analogo a come si attraversa un file system classico a livello di codice.

## <a name="use-a-flat-listing"></a>Usare un elenco semplice

Per impostazione predefinita, un'operazione di elenco restituisce BLOB in un elenco semplice. In un elenco semplice, i BLOB non sono organizzati in base alla directory virtuale.

Nell'esempio seguente vengono elencati i BLOB nel contenitore specificato utilizzando un elenco semplice, con una dimensione di segmento facoltativa specificata, e il nome del BLOB viene scritto in una finestra della console.

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
                // A flat listing operation returns only blobs, not virtual directories.
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

L'output di esempio è simile al seguente:

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

## <a name="use-a-hierarchical-listing"></a>Usa un elenco gerarchico

Quando si chiama un'operazione di elenco gerarchicamente, archiviazione di Azure restituisce le directory virtuali e i BLOB al primo livello della gerarchia. La proprietà [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) di ogni directory virtuale è impostata in modo che sia possibile passare il prefisso in una chiamata ricorsiva per recuperare la directory successiva.

Per elencare i BLOB in modo gerarchico, impostare il parametro `useFlatBlobListing` del metodo Listing su **false**.

Nell'esempio seguente vengono elencati i BLOB nel contenitore specificato utilizzando un elenco semplice, con una dimensione di segmento facoltativa specificata, e il nome del BLOB viene scritto nella finestra della console.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and 
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, null, null, null);
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

L'output di esempio è simile al seguente:

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
> Non è possibile elencare gli snapshot BLOB in un'operazione di elenco gerarchico.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Elencare i BLOB](/rest/api/storageservices/list-blobs)
- [Enumerazione di risorse BLOB](/rest/api/storageservices/enumerating-blob-resources)
