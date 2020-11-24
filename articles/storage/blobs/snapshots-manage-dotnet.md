---
title: Creare e gestire snapshot BLOB in .NET
titleSuffix: Azure Storage
description: Informazioni su come usare la libreria client .NET per creare uno snapshot di sola lettura di un BLOB per eseguire il backup dei dati BLOB in un determinato momento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543375"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Creare e gestire snapshot BLOB in .NET

Uno snapshot è una versione di sola lettura di un BLOB eseguito in un determinato momento. Questo articolo illustra come creare e gestire snapshot BLOB usando la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage).

Per altre informazioni sugli snapshot BLOB in archiviazione di Azure, vedere [snapshot BLOB](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Creare uno snapshot

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Per creare uno snapshot di un BLOB in blocchi usando la versione 12. x della libreria client di archiviazione di Azure per .NET, usare uno dei metodi seguenti:

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

Nell'esempio di codice seguente viene illustrato come creare uno snapshot con la versione 12. x. Includere un riferimento alla libreria [Azure. Identity](https://www.nuget.org/packages/azure.identity) per usare le credenziali Azure ad per autorizzare le richieste al servizio.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Per creare uno snapshot di un BLOB in blocchi usando la versione 11. x della libreria client di archiviazione di Azure per .NET, usare uno dei metodi seguenti:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

Nell'esempio di codice seguente viene illustrato come creare uno snapshot con la versione 11. x. Questo esempio specifica metadati aggiuntivi per lo snapshot al momento della creazione.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
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

## <a name="delete-snapshots"></a>Eliminare gli snapshot

Per eliminare un BLOB, è necessario innanzitutto eliminare gli snapshot del BLOB. È possibile eliminare uno snapshot singolarmente o specificare di eliminare tutti gli snapshot quando si elimina il BLOB di origine. Se si tenta di eliminare un BLOB per il quale esistono ancora degli snapshot, viene restituito un errore.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Per eliminare un BLOB e i relativi snapshot usando la versione 12. x della libreria client di archiviazione di Azure per .NET, usare uno dei metodi seguenti e includere l'enumerazione [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) :

- [Elimina](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

Nell'esempio di codice seguente viene illustrato come eliminare un BLOB e i relativi snapshot in .NET, dove `blobClient` è un oggetto di tipo [BlobClient](/dotnet/api/azure.storage.blobs.blobclient):

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Per eliminare un BLOB e i relativi snapshot usando la versione 11. x della libreria client di archiviazione di Azure per .NET, usare uno dei metodi di eliminazione BLOB seguenti e includere l'enumerazione [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) :

- [Elimina](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Nell'esempio di codice seguente viene illustrato come eliminare un BLOB e i relativi snapshot in .NET, dove `blockBlob` è un oggetto di tipo [CloudBlockBlob] [dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Snapshot BLOB](snapshots-overview.md)
- [Versioni BLOB](versioning-overview.md)
- [Eliminazione temporanea per i BLOB](./soft-delete-blob-overview.md)