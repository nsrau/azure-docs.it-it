---
title: Elencare i contenitori BLOB con .NET-archiviazione di Azure
description: Informazioni su come elencare i contenitori BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 06454798deb4a5bc5064e28535a837f73c083e1c
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671292"
---
# <a name="list-blob-containers-with-net"></a>Elencare i contenitori BLOB con .NET

Quando si elencano i contenitori in un account di archiviazione di Azure dal codice, è possibile specificare diverse opzioni per gestire il modo in cui i risultati vengono restituiti dall'archiviazione di Azure. Questo articolo illustra come elencare i contenitori usando la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage/client).  

## <a name="understand-container-listing-options"></a>Informazioni sulle opzioni relative all'elenco di contenitori

Per elencare i contenitori nell'account di archiviazione, chiamare uno dei metodi seguenti:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Gli overload di questi metodi forniscono opzioni aggiuntive per la gestione del modo in cui i contenitori vengono restituiti dall'operazione di elenco. Queste opzioni sono descritte nelle sezioni riportate di seguito.

### <a name="manage-how-many-results-are-returned"></a>Gestire il numero di risultati restituiti

Per impostazione predefinita, un'operazione di elenco restituisce fino a 5000 risultati alla volta. Per restituire un set di risultati più piccolo, fornire un valore diverso da zero `maxresults` per il parametro quando si chiama uno dei metodi **ListContainerSegmented** .

Se l'account di archiviazione contiene più di 5000 contenitori o se è stato specificato un valore `maxresults` in modo che l'operazione di elenco restituisca un subset di contenitori nell'account di archiviazione, archiviazione di Azure restituisce un token di *continuazione* con il elenco di contenitori. Un token di continuazione è un valore opaco che è possibile usare per recuperare il set successivo di risultati dall'archiviazione di Azure.

Nel codice, controllare il valore del token di continuazione per determinare se è null. Quando il token di continuazione è null, il set di risultati è completo. Se il token di continuazione non è null, chiamare di nuovo **ListContainersSegmented** o **ListContainersSegmentedAsync** , passando il token di continuazione per recuperare il set di risultati successivo, fino a quando il token di continuazione non è null.

### <a name="filter-results-with-a-prefix"></a>Filtrare i risultati con un prefisso

Per filtrare l'elenco dei contenitori, specificare una stringa per il `prefix` parametro. La stringa di prefisso può includere uno o più caratteri. Archiviazione di Azure restituisce quindi solo i contenitori i cui nomi iniziano con tale prefisso.

### <a name="return-container-metadata"></a>Restituisci metadati del contenitore

Per restituire i metadati del contenitore con i risultati, specificare il valore **dei metadati** per l'enumerazione [ContainerListDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) . Archiviazione di Azure include metadati con ogni contenitore restituito, pertanto non è necessario chiamare anche uno dei metodi **FetchAttributes** per recuperare i metadati del contenitore.

## <a name="example-list-containers"></a>Esempio: Elenca contenitori

Nell'esempio seguente vengono elencati in modo asincrono i contenitori in un account di archiviazione che iniziano con un prefisso specificato. Nell'esempio vengono elencati i contenitori con incrementi di 5 risultati alla volta e viene utilizzato il token di continuazione per ottenere il segmento di risultati successivo. Nell'esempio vengono restituiti anche i metadati del contenitore con i risultati.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
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
[enumerazione delle risorse BLOB](/rest/api/storageservices/enumerating-blob-resources)
