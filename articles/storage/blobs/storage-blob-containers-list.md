---
title: Elencare i contenitori BLOB con .NET-archiviazione di Azure
description: Informazioni su come elencare i contenitori BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f443cd5603e6ca0f60dc0e69b734bfa46138d476
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018944"
---
# <a name="list-blob-containers-with-net"></a>Elencare i contenitori BLOB con .NET

Quando si elencano i contenitori in un account di archiviazione di Azure dal codice, è possibile specificare diverse opzioni per gestire il modo in cui i risultati vengono restituiti dall'archiviazione di Azure. Questo articolo illustra come elencare i contenitori usando la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-container-listing-options"></a>Informazioni sulle opzioni relative all'elenco di contenitori

Per elencare i contenitori nell'account di archiviazione, chiamare uno dei metodi seguenti:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Gli overload di questi metodi forniscono opzioni aggiuntive per la gestione del modo in cui i contenitori vengono restituiti dall'operazione di elenco. Le opzioni disponibili sono descritte nelle sezioni seguenti.

### <a name="manage-how-many-results-are-returned"></a>Gestire il numero di risultati restituiti

Per impostazione predefinita, un'operazione di elenco restituisce fino a 5.000 risultati alla volta. Per restituire un set di risultati più piccolo, fornire un valore diverso da zero per il `maxresults` parametro quando si chiama uno dei metodi **ListContainerSegmented** .

Se l'account di archiviazione contiene più di 5000 contenitori o se è stato specificato un valore `maxresults` in modo che l'operazione di elenco restituisca un subset di contenitori nell'account di archiviazione, archiviazione di Azure restituisce un *token di continuazione* con l'elenco dei contenitori. Un token di continuazione è un valore opaco che è possibile usare per recuperare il set di risultati successivo da Archiviazione di Azure.

Nel codice, controllare il valore del token di continuazione per determinare se è di Null. Se il token di continuazione è Null, il set di risultati è completo. Se il token di continuazione non è null, chiamare di nuovo **ListContainersSegmented** o **ListContainersSegmentedAsync** , passando il token di continuazione per recuperare il set di risultati successivo, fino a quando il token di continuazione non è null.

### <a name="filter-results-with-a-prefix"></a>Filtrare i risultati con un prefisso

Per filtrare l'elenco dei contenitori, specificare una stringa per il parametro `prefix`. La stringa di prefisso può includere uno o più caratteri. Archiviazione di Azure restituisce quindi solo i contenitori i cui nomi iniziano con tale prefisso.

### <a name="return-metadata"></a>Restituire i metadati

Per restituire i metadati del contenitore con i risultati, specificare il valore **dei metadati** per l'enumerazione [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) . Archiviazione di Azure include metadati con ogni contenitore restituito, pertanto non è necessario chiamare anche uno dei metodi **FetchAttributes** per recuperare i metadati del contenitore.

## <a name="example-list-containers"></a>Esempio: elencare i contenitori

Nell'esempio seguente vengono elencati in modo asincrono i contenitori in un account di archiviazione che iniziano con un prefisso specificato. Nell'esempio vengono elencati i contenitori con incrementi di 5 risultati alla volta e viene utilizzato il token di continuazione per ottenere il segmento di risultati successivo. Nell'esempio vengono restituiti anche i metadati del contenitore con i risultati.

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
 [Enumerazione di risorse BLOB](/rest/api/storageservices/enumerating-blob-resources)
