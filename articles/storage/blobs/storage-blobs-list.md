---
title: Elenca BLOB con .NET - Archiviazione di AzureList blobs with .NET - Azure Storage
description: Informazioni su come elencare i BLOB in un contenitore nell'account di archiviazione di Azure usando la libreria client .NET. Esempi di codice mostrano come elencare i BLOB in un elenco semplice o come elencare i BLOB in modo gerarchico, come se fossero organizzati in directory o cartelle.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137921"
---
# <a name="list-blobs-with-net"></a>Elencare i BLOB con .NETList blobs with .NET

Quando si elencano i BLOB dal codice, è possibile specificare una serie di opzioni per gestire la modalità di restituzione dei risultati da Archiviazione di Azure.When you list blobs from your code, you can specify a number of options to manage how results are returned from Azure Storage. È possibile specificare il numero di risultati da restituire in ogni set di risultati e quindi recuperare i set successivi. È possibile specificare un prefisso per restituire i BLOB i cui nomi iniziano con tale carattere o stringa. È inoltre possibile elencare i BLOB in una struttura di elenco semplice o gerarchicamente. Un elenco gerarchico restituisce i BLOB come se fossero organizzati in cartelle. 

Questo articolo illustra come elencare i BLOB usando la [libreria client di Archiviazione di Azure per .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)  

## <a name="understand-blob-listing-options"></a>Informazioni sulle opzioni di elenco BLOB

Per elencare i BLOB in un account di archiviazione, chiamare uno dei metodi seguenti:To list the blobs in a storage account, call one of these methods:

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Per elencare i BLOB in un contenitore, chiamare uno di questi metodi:To list the blobs in a container, call one of these methods:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

Gli overload per questi metodi forniscono opzioni aggiuntive per la gestione del modo in cui i BLOB vengono restituiti dall'operazione di elenco. Queste opzioni sono descritte nelle sezioni seguenti.

### <a name="manage-how-many-results-are-returned"></a>Gestire il numero di risultati restituiti

Per impostazione predefinita, un'operazione di elenco restituisce fino a 5000 risultati alla volta. Per restituire un set di risultati più piccolo, fornire un valore diverso da zero per il `maxresults` parametro quando si chiama uno dei **listBlobs** metodi.

Se un'operazione di elenco restituisce più di 5000 `maxresults` BLOB o se è stato specificato un valore per tale che l'operazione di elenco restituisce un subset di contenitori nell'account di archiviazione, Archiviazione di Azure restituisce un token di *continuazione* con l'elenco di BLOB. Un token di continuazione è un valore opaco che è possibile usare per recuperare il set di risultati successivo da Archiviazione di Azure.A continuation token is an opaque value that you can use to retrieve the next set of results from Azure Storage.

Nel codice, controllare il valore del token di continuazione per determinare se è null. Quando il token di continuazione è null, il set di risultati è completo. Se il token di continuazione non è null, chiamare nuovamente l'operazione di elenco, passando il token di continuazione per recuperare il set di risultati successivo, fino a quando il token di continuazione non è null.

### <a name="filter-results-with-a-prefix"></a>Filtrare i risultati con un prefisso

Per filtrare l'elenco di contenitori, specificare una stringa per il `prefix` parametro. La stringa del prefisso può includere uno o più caratteri. Archiviazione di Azure restituisce quindi solo i BLOB i cui nomi iniziano con tale prefisso.

### <a name="return-metadata"></a>Restituire i metadati

Per restituire i metadati BLOB con i risultati, specificare il valore **Dei metadati** per l'enumerazione [BlobListingDetails.To](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) return blob metadata with the results, specify the Metadata value for the BlobListingDetails enumeration. Archiviazione di Azure include metadati con ogni BLOB restituito, pertanto non è necessario chiamare uno dei metodi **FetchAttributes** in questo contesto per recuperare i metadati del BLOB.

### <a name="flat-listing-versus-hierarchical-listing"></a>Inserzione semplice e elenco gerarchico

I BLOB in Archiviazione di Azure sono organizzati in un paradigma semplice, anziché in un paradigma gerarchico, ad esempio un file system classico. Tuttavia, è possibile organizzare i BLOB in *directory virtuali* per simulare una struttura di cartelle. Una directory virtuale fa parte del nome del BLOB ed è indicata dal carattere delimitatore.

Per organizzare i BLOB in directory virtuali, usare un carattere delimitatore nel nome del BLOB. Il carattere delimitatore predefinito è una barra (/), ma è possibile specificare qualsiasi carattere come delimitatore.

Se si assegna un nome ai BLOB usando un delimitatore, è possibile scegliere di elencare i BLOB in modo gerarchico. Per un'operazione di elenco gerarchico, Archiviazione di Azure restituisce tutte le directory virtuali e i BLOB sotto l'oggetto padre. È possibile chiamare l'operazione di elenco in modo ricorsivo per attraversare la gerarchia, in modo simile a come si attraversa un file system classico a livello di codice.

## <a name="use-a-flat-listing"></a>Utilizzare un elenco semplice

Per impostazione predefinita, un'operazione di elenco restituisce BLOB in un elenco semplice. In un elenco semplice, i BLOB non sono organizzati in base alla directory virtuale.

L'esempio seguente elenca i BLOB nel contenitore specificato usando un elenco semplice, con una dimensione di segmento facoltativa specificata, e scrive il nome del BLOB in una finestra della console.

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

L'output di esempio è simile al seguente:The sample output is similar to:

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

## <a name="use-a-hierarchical-listing"></a>Utilizzare un elenco gerarchico

Quando si chiama un'operazione di elenco in modo gerarchico, Archiviazione di Azure restituisce le directory virtuali e i BLOB al primo livello della gerarchia. La proprietà [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) di ogni directory virtuale viene impostata in modo che sia possibile passare il prefisso in una chiamata ricorsiva per recuperare la directory successiva.

Per elencare i BLOB `useFlatBlobListing` in modo gerarchico, impostare il parametro del metodo listing su **false**.

L'esempio seguente elenca i BLOB nel contenitore specificato usando un elenco semplice, con una dimensione di segmento facoltativa specificata, e scrive il nome del BLOB nella finestra della console.

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

L'output di esempio è simile al seguente:The sample output is similar to:

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
> Gli snapshot BLOB non possono essere elencati in un'operazione di elenco gerarchico.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- [List Blobs](/rest/api/storageservices/list-blobs)
- [Enumerazione di risorse Blob](/rest/api/storageservices/enumerating-blob-resources)
