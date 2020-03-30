---
title: Gestire l'accesso in lettura pubblico per contenitori e BLOBManage public read access for containers and blobs
titleSuffix: Azure Storage
description: Informazioni su come rendere disponibili per l'accesso anonimo contenitori e BLOB e su come accedervi a livello di programmazione.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255483"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gestire l'accesso in lettura anonimo a contenitori e BLOB

È possibile abilitare l'accesso in lettura pubblico anonimo a un contenitore e ai relativi BLOB in Archiviazione BLOB di Azure. Ciò permette di concedere l'accesso in sola lettura a queste risorse senza condividere la chiave dell'account e senza richiedere una firma di accesso condiviso (SAS).

L'accesso in lettura pubblico è ideale per scenari in cui si vuole che BLOB specifici siano sempre disponibili per l'accesso in lettura anonimo. Per un controllo più accurato, è possibile creare una firma di accesso condiviso. Le firme di accesso condiviso consentono di fornire accesso limitato usando autorizzazioni diverse, per un periodo di tempo specifico. Per altre informazioni sulla creazione di firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Concedere le autorizzazioni agli utenti anonimi per contenitori e BLOB

Per impostazione predefinita, un contenitore e i BLOB al suo interno sono accessibili solo a un utente a cui sono state concesse le autorizzazioni appropriate. Per assegnare a utenti anonimi l'accesso in lettura a un contenitore e ai relativi BLOB, è possibile impostare il livello di accesso pubblico del contenitore. Quando si concede l'accesso pubblico a un contenitore, gli utenti anonimi possono leggere i BLOB presenti in un contenitore accessibile pubblicamente senza effettuare l'autorizzazione della richiesta.

È possibile configurare un contenitore con le autorizzazioni seguenti:

- **Nessun accesso in lettura pubblico:** solo il proprietario dell'account di archiviazione può accedere al contenitore e ai relativi BLOB. Questa è l'impostazione predefinita per tutti i nuovi contenitori.
- **Accesso in lettura pubblico solo per i BLOB:** i dati del BLOB all'interno del contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client anonimi non possono enumerare i BLOB all'interno del contenitore.
- Accesso in lettura pubblico per il **contenitore e i relativi BLOB:Public read access for container and its blobs:** Tutti i dati del contenitore e del BLOB possono essere letti tramite richiesta anonima. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Impostare il livello di accesso pubblico del contenitore nel portale di AzureSet container public access level in the Azure portal

Dal [portale di Azure](https://portal.azure.com)è possibile aggiornare il livello di accesso pubblico per uno o più contenitori:From the Azure portal , you can update the public access level for one or more containers:

1. Passare alla panoramica dell'account di archiviazione nel portale di Azure.Navigate to your storage account overview in the Azure portal.
1. In **Servizio BLOB** nel pannello del menu selezionare **BLOB**.
1. Selezionare i contenitori per i quali si desidera impostare il livello di accesso pubblico.
1. Utilizzare il pulsante **Modifica livello** di accesso per visualizzare le impostazioni di accesso pubblico.
1. Selezionare il livello di accesso pubblico desiderato dall'elenco a discesa **Livello di accesso pubblico** e fare clic sul pulsante OK per applicare la modifica ai contenitori selezionati.

Nella schermata seguente viene illustrato come modificare il livello di accesso pubblico per i contenitori selezionati.

![Screenshot che mostra come impostare il livello di accesso pubblico nel portale](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Non è possibile modificare il livello di accesso pubblico per un singolo BLOB. Il livello di accesso pubblico viene impostato solo a livello di contenitore.

### <a name="set-container-public-access-level-with-net"></a>Impostare il livello di accesso pubblico del contenitore con .NETSet container public access level with .NET

Per impostare le autorizzazioni per un contenitore usando la libreria client di Archiviazione di Azure per .NET, recuperare innanzitutto le autorizzazioni esistenti del contenitore chiamando uno dei metodi seguenti:To set permissions for a container using the Azure Storage client library for .NET, first retrieve the container existing permissions by calling one of the following methods:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsyncGetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Impostare quindi la proprietà PublicAccess sull'oggetto BlobContainerPermissions restituito dal metodo GetPermissions.Next, set the **PublicAccess** property on the [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) object that is returned by the **GetPermissions** method.

Infine, chiamare uno dei seguenti metodi per aggiornare le autorizzazioni del contenitore:

- [Setpermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

L'esempio seguente imposta le autorizzazioni per il contenitore per l'accesso in lettura pubblico completo. Per impostare le autorizzazioni per l'accesso in lettura pubblico solo per i BLOB, impostare la proprietà **PublicAccess** su **BlobContainerPublicAccessType.Blob**. Per rimuovere tutte le autorizzazioni per gli utenti anonimi, impostare la proprietà su **BlobContainerPublicAccessType.Off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Accesso anonimo a contenitori e BLOB

Un client che accede a contenitori e BLOB in modo anonimo può usare costruttori che non richiedono credenziali. Gli esempi seguenti illustrano alcuni modi diversi per fare riferimento a contenitori e BLOB in modo anonimo.

### <a name="create-an-anonymous-client-object"></a>Creare un oggetto client anonimo

È possibile creare un nuovo oggetto client del servizio per l'accesso anonimo specificando l'endpoint di archiviazione BLOB per l'account. Tuttavia, è necessario conoscere anche il nome di un contenitore in tale account disponibile per l'accesso anonimo.

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

### <a name="reference-a-container-anonymously"></a>Fare riferimento a un contenitore in modo anonimo

Se si conosce l'URL per un contenitore disponibile in modo anonimo, è possibile usarlo per fare riferimento direttamente al contenitore.

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

### <a name="reference-a-blob-anonymously"></a>Fare riferimento a un BLOB in modo anonimo

Se si conosce l'URL per un BLOB disponibile per l'accesso anonimo, è possibile fare riferimento direttamente al BLOB con tale URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Autorizzazione dell'accesso ad Archiviazione di Azure](../common/storage-auth.md)
- [Concedere l'accesso limitato alle risorse di Archiviazione di Azure usando le firme di accesso condivisoGrant limited access to Azure Storage resources using shared access signatures (SAS)](../common/storage-sas-overview.md)
- [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api) (API REST del servizio BLOB)
