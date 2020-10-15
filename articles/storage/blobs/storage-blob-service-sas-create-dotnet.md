---
title: Creare una firma di accesso condiviso del servizio per un contenitore o un BLOB con .NET
titleSuffix: Azure Storage
description: Informazioni su come creare una firma di accesso condiviso (SAS) del servizio per un contenitore o un BLOB usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/05/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: eab8993a98ce202964ade9fb5deae5c741ac7eb6
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090353"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>Creare una firma di accesso condiviso del servizio per un contenitore o un BLOB con .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare la chiave dell'account di archiviazione per creare una firma di accesso condiviso del servizio per un contenitore o un BLOB con la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage).

## <a name="create-a-service-sas-for-a-blob-container"></a>Creare una firma di accesso condiviso del servizio per un contenitore BLOB

L'esempio di codice seguente crea una firma di accesso condiviso per un contenitore. Se viene specificato il nome di un criterio di accesso archiviato esistente, tale criterio è associato alla firma di accesso condiviso. Se non viene specificato alcun criterio di accesso archiviato, il codice crea una firma di accesso condiviso ad hoc nel contenitore.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Una firma di accesso condiviso del servizio è firmata con la chiave di accesso dell'account. Utilizzare la classe [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) per creare le credenziali utilizzate per firmare la firma di accesso condiviso. Successivamente, creare un nuovo oggetto [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e chiamare il [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) per ottenere la stringa del token SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetContainerSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Per creare una firma di accesso condiviso del servizio per un contenitore, chiamare il metodo [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) .

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}

```
---

## <a name="create-a-service-sas-for-a-blob"></a>Creare una firma di accesso condiviso del servizio per un BLOB

L'esempio di codice seguente crea una firma di accesso condiviso su un BLOB. Se viene specificato il nome di un criterio di accesso archiviato esistente, tale criterio è associato alla firma di accesso condiviso. Se non viene specificato alcun criterio di accesso archiviato, il codice crea una firma di accesso condiviso ad hoc nel BLOB.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Una firma di accesso condiviso del servizio è firmata con la chiave di accesso dell'account. Utilizzare la classe [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) per creare le credenziali utilizzate per firmare la firma di accesso condiviso. Successivamente, creare un nuovo oggetto [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e chiamare il [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) per ottenere la stringa del token SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetBlobSasUri":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Per creare una firma di accesso condiviso del servizio per un BLOB, chiamare il metodo [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) .

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso (SAS)](../common/storage-sas-overview.md)
- [Creare un SAS di servizio](/rest/api/storageservices/create-service-sas)
