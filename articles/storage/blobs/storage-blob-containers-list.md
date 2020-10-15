---
title: Elencare i contenitori BLOB con .NET-archiviazione di Azure
description: Informazioni su come elencare i contenitori BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ab7749c93f39d0c7b630b63e0b0e68589b61ede2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090948"
---
# <a name="list-blob-containers-with-net"></a>Elencare i contenitori BLOB con .NET

Quando si elencano i contenitori in un account di archiviazione di Azure dal codice, è possibile specificare diverse opzioni per gestire il modo in cui i risultati vengono restituiti dall'archiviazione di Azure. Questo articolo illustra come elencare i contenitori usando la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage).  

## <a name="understand-container-listing-options"></a>Informazioni sulle opzioni relative all'elenco di contenitori

Per elencare i contenitori nell'account di archiviazione, chiamare uno dei metodi seguenti:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

Gli overload di questi metodi forniscono opzioni aggiuntive per la gestione del modo in cui i contenitori vengono restituiti dall'operazione di elenco. Le opzioni disponibili sono descritte nelle sezioni seguenti.

### <a name="manage-how-many-results-are-returned"></a>Gestire il numero di risultati restituiti

Per impostazione predefinita, un'operazione di elenco restituisce fino a 5.000 risultati alla volta. Per restituire un set di risultati più piccolo, fornire un valore diverso da zero per la dimensione della pagina di risultati da restituire.

Se l'account di archiviazione contiene più di 5000 contenitori o se è stata specificata una dimensione della pagina in modo che l'operazione di elenco restituisca un subset di contenitori nell'account di archiviazione, archiviazione di Azure restituisce un *token di continuazione* con l'elenco dei contenitori. Un token di continuazione è un valore opaco che è possibile usare per recuperare il set di risultati successivo da Archiviazione di Azure.

Nel codice, controllare il valore del token di continuazione per determinare se è vuoto (per .NET V12) o null (per .NET V11 e versioni precedenti). Se il token di continuazione è Null, il set di risultati è completo. Se il token di continuazione non è null, chiamare di nuovo il metodo Listing, passando il token di continuazione per recuperare il set di risultati successivo, fino a quando il token di continuazione non è null.

### <a name="filter-results-with-a-prefix"></a>Filtrare i risultati con un prefisso

Per filtrare l'elenco dei contenitori, specificare una stringa per il parametro `prefix`. La stringa di prefisso può includere uno o più caratteri. Archiviazione di Azure restituisce quindi solo i contenitori i cui nomi iniziano con tale prefisso.

### <a name="return-metadata"></a>Restituire i metadati

Per restituire i metadati del contenitore con i risultati, specificare il valore **dei metadati** per [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) enum (per .NET V12) o [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) enum (per .net V11 e versioni precedenti). Archiviazione di Azure include metadati con ogni contenitore restituito, quindi non è necessario recuperare anche i metadati del contenitore.

## <a name="example-list-containers"></a>Esempio: elencare i contenitori

Nell'esempio seguente vengono elencati in modo asincrono i contenitori in un account di archiviazione che iniziano con un prefisso specificato. Nell'esempio vengono elencati i contenitori che iniziano con il prefisso specificato e restituisce il numero specificato di risultati per ogni chiamata all'operazione di elenco. USA quindi il token di continuazione per ottenere il segmento successivo dei risultati. Nell'esempio vengono restituiti anche i metadati del contenitore con i risultati.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="ListContainers":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

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

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Vedi anche

- [Elencare i contenitori](/rest/api/storageservices/list-containers2)
- [Enumerazione di risorse Blob](/rest/api/storageservices/enumerating-blob-resources)
