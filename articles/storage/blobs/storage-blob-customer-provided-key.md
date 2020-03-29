---
title: Specificare una chiave fornita dal cliente in una richiesta all'archiviazione BLOB con .NET - Archiviazione di AzureSpecify a customer-provided key on a request to Blob storage with .NET - Azure Storage
description: Informazioni su come specificare una chiave fornita dal cliente in una richiesta all'archiviazione BLOB tramite .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807001"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Specificare una chiave fornita dal cliente in una richiesta all'archiviazione BLOB con .NETSpecify a customer-provided key on a request to Blob storage with .NET

I client che effettuano richieste per l'archiviazione BLOB di Azure hanno la possibilità di fornire una chiave di crittografia in una singola richiesta. L'inclusione della chiave di crittografia nella richiesta offre un controllo granulare sulle impostazioni di crittografia per le operazioni di archiviazione BLOB. Le chiavi fornite dal cliente (anteprima) possono essere archiviate in Archiviazione delle chiavi di Azure o in un altro archivio chiavi.

In questo articolo viene illustrato come specificare una chiave fornita dal cliente in una richiesta con .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Per altre informazioni su come eseguire l'autenticazione con la libreria client di Azure Identity da Archiviazione di Azure, vedere la sezione **Autenticazione con la libreria di identità** di Azure in [Autorizzare l'accesso a BLOB e code con Azure Active Directory e le identità gestite per](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)le risorse di Azure.To learn more about how to authenticate with the Azure Identity client library from Azure Storage, see the section titled Authenticate with the Azure Identity library in Authorize access to blobs and queues with Azure Active Directory and managed identities for Azure Resources.

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Esempio: usare una chiave fornita dal cliente per caricare un BLOBExample: Use a customer-provided key to upload a blob

L'esempio seguente crea una chiave fornita dal cliente e la usa per caricare un BLOB. The code uploads a block, then commits the block list to write the blob to Azure Storage.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](../common/storage-service-encryption.md)
- [Autorizzare l'accesso a BLOB e code con Azure Active Directory e le identità gestite per le risorse di AzureAuthorize access to blobs and queues with Azure Active Directory and managed identities for Azure Resources](../common/storage-auth-aad-msi.md)
