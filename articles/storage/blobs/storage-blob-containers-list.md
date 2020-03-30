---
title: Elencare i contenitori BLOB con .NET - Archiviazione di AzureList blob containers with .NET - Azure Storage
description: Informazioni su come elencare i contenitori BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135905"
---
# <a name="list-blob-containers-with-net"></a>Elencare i contenitori BLOB con .NETList blob containers with .NET

Quando si elencano i contenitori in un account di archiviazione di Azure dal codice, è possibile specificare una serie di opzioni per gestire la modalità di restituzione dei risultati da Archiviazione di Azure.When you list the containers in an Azure Storage account from your code, you can specify a number of options to manage how results are returned from Azure Storage. Questo articolo illustra come elencare i contenitori usando la [libreria client di Archiviazione di Azure per .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)  

## <a name="understand-container-listing-options"></a>Informazioni sulle opzioni di elenco dei contenitori

Per elencare i contenitori nell'account di archiviazione, chiamare uno dei metodi seguenti:To list containers in your storage account, call one of the following methods:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Gli overload per questi metodi forniscono opzioni aggiuntive per la gestione della modalità di restituzione dei contenitori dall'operazione di elenco. Queste opzioni sono descritte nelle sezioni seguenti.

### <a name="manage-how-many-results-are-returned"></a>Gestire il numero di risultati restituiti

Per impostazione predefinita, un'operazione di elenco restituisce fino a 5000 risultati alla volta. Per restituire un set di risultati più piccolo, fornire un valore diverso da zero per il `maxresults` parametro quando si chiama uno dei **ListContainerSegmented** metodi.

Se l'account di archiviazione contiene più di 5000 `maxresults` contenitori o se è stato specificato un valore per tale che l'operazione di elenco restituisce un subset di contenitori nell'account di archiviazione, Archiviazione di Azure restituisce un token di *continuazione* con l'elenco dei contenitori. Un token di continuazione è un valore opaco che è possibile usare per recuperare il set di risultati successivo da Archiviazione di Azure.A continuation token is an opaque value that you can use to retrieve the next set of results from Azure Storage.

Nel codice, controllare il valore del token di continuazione per determinare se è null. Quando il token di continuazione è null, il set di risultati è completo. Se il token di continuazione non è null, chiamare nuovamente **ListContainersSegmented** o **ListContainersSegmentedAsync,** passando il token di continuazione per recuperare il set di risultati successivo, fino a quando il token di continuazione non è null.

### <a name="filter-results-with-a-prefix"></a>Filtrare i risultati con un prefisso

Per filtrare l'elenco di contenitori, specificare una stringa per il `prefix` parametro. La stringa del prefisso può includere uno o più caratteri. Archiviazione di Azure restituisce quindi solo i contenitori i cui nomi iniziano con tale prefisso.

### <a name="return-metadata"></a>Restituire i metadati

Per restituire i metadati del contenitore con i risultati, specificare **il** metadata valore per il [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) enumerazione. Archiviazione di Azure include metadati con ogni contenitore restituito, pertanto non è necessario chiamare anche uno dei metodi **FetchAttributes** per recuperare i metadati del contenitore.

## <a name="example-list-containers"></a>Esempio: elencare i contenitoriExample: List containers

Nell'esempio seguente vengono elencati in modo asincrono i contenitori in un account di archiviazione che iniziano con un prefisso specificato. L'esempio elenca i contenitori con incrementi di 5 risultati alla volta e usa il token di continuazione per ottenere il segmento successivo dei risultati. L'esempio restituisce anche i metadati del contenitore con i risultati.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Vedere anche

[Elencare i contenitori](/rest/api/storageservices/list-containers2)
[che enumerano le risorse BLOBList](/rest/api/storageservices/enumerating-blob-resources) Containers Enumerating Blob Resources
