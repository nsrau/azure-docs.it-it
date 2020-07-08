---
title: Abilitare e gestire il controllo delle versioni dei BLOB (anteprima)
titleSuffix: Azure Storage
description: Informazioni su come abilitare il controllo delle versioni dei BLOB nel portale di Azure o usando un modello di Azure Resource Manager.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 0e24bcb54fd26d4a3d983681b3348ef736b277cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82884345"
---
# <a name="enable-and-manage-blob-versioning"></a>Abilitare e gestire il controllo delle versioni dei BLOB

È possibile abilitare o disabilitare il controllo delle versioni dei BLOB (anteprima) per l'account di archiviazione in qualsiasi momento usando il portale di Azure o un modello di Azure Resource Manager.

## <a name="enable-blob-versioning"></a>Abilitare il controllo delle versioni dei BLOB

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per abilitare il controllo delle versioni dei BLOB nel portale di Azure:

1. Passare all'account di archiviazione nel portale.
1. In **servizio BLOB**scegliere **protezione dati**.
1. Nella sezione **controllo delle versioni** selezionare **abilitato**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Screenshot che illustra come abilitare il controllo delle versioni dei BLOB in portale di Azure":::

# <a name="template"></a>[Modello](#tab/template)

Per abilitare il controllo delle versioni dei BLOB con un modello, creare un modello con la proprietà **IsVersioningEnabled** su **true**. Nei passaggi seguenti viene descritto come creare un modello nel portale di Azure.

1. Nella portale di Azure scegliere **Crea una risorsa**.
1. In **Cerca nel Marketplace**Digitare **distribuzione modello**, quindi premere **invio**.
1. Scegliere **distribuzione modelli**, fare clic su **Crea**e quindi scegliere **Compila modello personalizzato nell'editor**.
1. Nell'editor dei modelli incollare il codice JSON seguente. Sostituire il segnaposto `<accountName>` con il nome del proprio account di archiviazione.
1. Salvare il modello.
1. Specificare il gruppo di risorse dell'account, quindi scegliere il pulsante **Acquista** per distribuire il modello e abilitare il controllo delle versioni dei BLOB.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Per altre informazioni sulla distribuzione di risorse con i modelli nel portale di Azure, vedere [distribuire le risorse con portale di Azure](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Modificare un BLOB per attivare una nuova versione

L'esempio di codice seguente illustra come attivare la creazione di una nuova versione con la libreria client di archiviazione di Azure per .NET versione 12. Prima di eseguire questo esempio, verificare di aver abilitato il controllo delle versioni per l'account di archiviazione.

Nell'esempio viene creato un BLOB in blocchi, quindi vengono aggiornati i metadati del BLOB. Se si aggiornano i metadati del BLOB, viene attivata la creazione di una nuova versione. Nell'esempio vengono recuperate la versione iniziale e la versione corrente e viene indicato che solo la versione corrente include i metadati.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Controllo delle versioni dei BLOB (anteprima)](versioning-overview.md)
- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](soft-delete-overview.md)
