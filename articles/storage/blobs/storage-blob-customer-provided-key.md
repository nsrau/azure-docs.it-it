---
title: Specificare una chiave fornita dal cliente per una richiesta all'archivio BLOB con .NET-archiviazione di Azure
description: Informazioni su come specificare una chiave fornita dal cliente per una richiesta di archiviazione BLOB con .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807001"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Specificare una chiave fornita dal cliente per una richiesta all'archivio BLOB con .NET

I client che effettuano richieste nell'archivio BLOB di Azure hanno la possibilità di fornire una chiave di crittografia per una singola richiesta. L'inclusione della chiave di crittografia nella richiesta offre un controllo granulare sulle impostazioni di crittografia per le operazioni di archiviazione BLOB. Le chiavi fornite dal cliente (anteprima) possono essere archiviate in Azure Key Vault o in un altro archivio chiavi.

Questo articolo illustra come specificare una chiave fornita dal cliente per una richiesta con .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Per altre informazioni su come eseguire l'autenticazione con la libreria client di identità di Azure da archiviazione di Azure, vedere la sezione **eseguire l'autenticazione con la libreria di identità di Azure** in [autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Esempio: usare una chiave fornita dal cliente per caricare un BLOB

Nell'esempio seguente viene creata una chiave fornita dal cliente e tale chiave viene utilizzata per caricare un BLOB. Il codice consente di caricare un blocco, quindi di eseguire il commit dell'elenco di blocchi per scrivere il BLOB in archiviazione di Azure.

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

- [Crittografia di archiviazione di Azure per dati inattivi](../common/storage-service-encryption.md)
- [Autorizzare l'accesso a BLOB e code con Azure Active Directory e identità gestite per le risorse di Azure](../common/storage-auth-aad-msi.md)
