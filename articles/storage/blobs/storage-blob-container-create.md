---
title: Creare o eliminare un contenitore BLOB con .NET - Archiviazione di AzureCreate or delete a blob container with .NET - Azure Storage
description: Informazioni su come creare o eliminare un contenitore BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135939"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Creare o eliminare un contenitore in Archiviazione di Azure con .NETCreate or delete a container in Azure Storage with .NET

I BLOB in Archiviazione di Azure sono organizzati in contenitori. Prima di poter caricare un BLOB, è necessario creare un contenitore. Questo articolo illustra come creare ed eliminare contenitori con la [libreria client di Archiviazione di Azure per .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="name-a-container"></a>Assegnare un nome a un contenitoreName a container

Un nome di contenitore deve essere un nome DNS valido, in quanto fa parte dell'URI univoco usato per indirizzare il contenitore o i relativi BLOB. Seguire queste regole quando si assegna un nome a un contenitore:Follow these rules when naming a container:

- I nomi dei contenitori possono avere una lunghezza compresa tra 3 e 63 caratteri.
- I nomi dei contenitori devono iniziare con una lettera o un numero e possono contenere solo lettere minuscole, numeri e il carattere trattino (-).
- Nei nomi dei contenitori non sono consentiti due o più caratteri trattini consecutivi.

L'URI per un contenitore è nel seguente formato:The URI for a container is in this format:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Creare un contenitore

Per creare un contenitore, chiamare uno dei seguenti metodi:

- [Crea](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

I metodi **Create** e **CreateAsync** generano un'eccezione se esiste già un contenitore con lo stesso nome.

I metodi **CreateIfNotExists** e **CreateIfNotExistsAsync** restituiscono un valore booleano che indica se il contenitore è stato creato. Se esiste già un contenitore con lo stesso nome, questi metodi restituiscono **False** per indicare che non è stato creato un nuovo contenitore.

I contenitori vengono creati immediatamente sotto l'account di archiviazione. Non è possibile annidare un contenitore in un altro.

L'esempio seguente crea un contenitore in modo asincrono:The following example creates a container asynchronously:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>Creare il contenitore radiceCreate the root container

Un contenitore radice funge da contenitore predefinito per l'account di archiviazione. Ogni account di archiviazione può avere un contenitore radice, che deve essere denominato *$root.*. È necessario creare o eliminare in modo esplicito il contenitore radice.

È possibile fare riferimento a un BLOB archiviato nel contenitore radice senza includere il nome del contenitore radice. Il contenitore radice consente di fare riferimento a un BLOB al livello superiore della gerarchia di account di archiviazione. Ad esempio, è possibile fare riferimento a un BLOB che risiede nel contenitore radice nel modo seguente:

`https://myaccount.blob.core.windows.net/default.html`

L'esempio seguente crea il contenitore radice in modo sincrono:The following example creates the root container synchronously:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Eliminare un contenitore

Per eliminare un contenitore in .NET, utilizzare uno dei seguenti metodi:

- [Elimina](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Il **Delete** e **DeleteAsync** metodi generano un'eccezione se il contenitore non esiste.

I metodi **DeleteIfExists** e **DeleteIfExistsAsync** restituiscono un valore booleano che indica se il contenitore è stato eliminato. Se il contenitore specificato non esiste, questi metodi restituiscono **False** per indicare che il contenitore non è stato eliminato.

Dopo aver eliminato un contenitore, non è possibile creare un contenitore con lo stesso nome per almeno 30 secondi ed eventualmente più a lungo. Durante l'eliminazione del contenitore, un tentativo di creare un contenitore con lo stesso nome avrà esito negativo con codice di errore HTTP 409 (conflitto). Tutte le altre operazioni nel contenitore o nei BLOB in esso contenuti avranno esito negativo con codice di errore HTTP 404 (Non trovato) durante l'eliminazione del contenitore.

Nell'esempio seguente viene eliminato il contenitore specificato e viene gestita l'eccezione se il contenitore non esiste:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Nell'esempio seguente viene illustrato come eliminare tutti i contenitori che iniziano con un prefisso specificato. Nell'esempio il lease viene interrotto se è presente un lease esistente nel contenitore.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Vedere anche

- [Operazione Crea contenitore](/rest/api/storageservices/create-container)
- [Delete Container operation](/rest/api/storageservices/delete-container) (Operazione di eliminazione contenitore)
