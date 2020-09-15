---
title: Accesso anonimo a contenitori e BLOB pubblici con .NET
titleSuffix: Azure Storage
description: Usare la libreria client di archiviazione di Azure per .NET per accedere in modo anonimo a contenitori e BLOB pubblici.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088817"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>Accesso anonimo a contenitori e BLOB pubblici con .NET

Archiviazione di Azure supporta l'accesso in lettura pubblico facoltativo per contenitori e BLOB. I client possono accedere in modo anonimo a contenitori e BLOB pubblici usando le librerie client di archiviazione di Azure, nonché usando altri strumenti e utilità che supportano l'accesso ai dati ad archiviazione di Azure.

Questo articolo illustra come accedere a un contenitore o a un BLOB pubblico da .NET. Per informazioni sulla configurazione dell'accesso in lettura anonimo in un contenitore, vedere [configurare l'accesso in lettura pubblico anonimo per contenitori e BLOB](anonymous-read-access-configure.md). Per informazioni su come impedire l'accesso anonimo a un account di archiviazione, vedere [impedire l'accesso in lettura pubblico anonimo a contenitori e BLOB](anonymous-read-access-prevent.md).

Un client che accede a contenitori e BLOB in modo anonimo può usare costruttori che non richiedono credenziali. Gli esempi seguenti illustrano alcuni modi diversi per fare riferimento a contenitori e BLOB in modo anonimo.

## <a name="create-an-anonymous-client-object"></a>Creare un oggetto client anonimo

È possibile creare un nuovo oggetto client del servizio per l'accesso anonimo fornendo l'endpoint di archiviazione BLOB per l'account. Tuttavia, è necessario conoscere anche il nome di un contenitore in tale account disponibile per l'accesso anonimo.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>Fare riferimento a un contenitore in modo anonimo

Se si conosce l'URL per un contenitore disponibile in modo anonimo, è possibile usarlo per fare riferimento direttamente al contenitore.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>Fare riferimento a un BLOB in modo anonimo

Se si conosce l'URL per un BLOB disponibile per l'accesso anonimo, è possibile fare riferimento direttamente al BLOB con tale URL:

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'accesso in lettura pubblico anonimo per contenitori e BLOB](anonymous-read-access-configure.md)
- [Impedisci l'accesso in lettura pubblico anonimo a contenitori e BLOB](anonymous-read-access-prevent.md)
- [Autorizzazione dell'accesso ad Archiviazione di Azure](../common/storage-auth.md)
