---
title: Abilitare l'accesso in lettura pubblico per contenitori e BLOB in Archiviazione BLOB di Azure | Microsoft Docs
description: Informazioni su come rendere disponibili per l'accesso anonimo contenitori e BLOB e su come accedervi a livello di programmazione.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a2cffee6-3224-4f2a-8183-66ca23b2d2d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: f52079c72be298daaa45074e516f911022780392
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gestire l'accesso in lettura anonimo a contenitori e BLOB
È possibile abilitare l'accesso in lettura pubblico anonimo a un contenitore e ai relativi BLOB in Archiviazione BLOB di Azure. Ciò permette di concedere l'accesso in sola lettura a queste risorse senza condividere la chiave dell'account e senza richiedere una firma di accesso condiviso (SAS).

L'accesso in lettura pubblico è ideale per scenari in cui si vuole che BLOB specifici siano sempre disponibili per l'accesso in lettura anonimo. Per un controllo più accurato, è possibile creare una firma di accesso condiviso. Le firme di accesso condiviso consentono di fornire accesso limitato usando autorizzazioni diverse, per un periodo di tempo specifico. Per altre informazioni sulla creazione di firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Concedere le autorizzazioni agli utenti anonimi per contenitori e BLOB
Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere a un contenitore e ai BLOB in esso contenuti. Se si desidera assegnare a utenti anonimi autorizzazioni di lettura per un contenitore e i relativi BLOB, è possibile impostare le autorizzazioni del contenitore per consentire l'accesso pubblico. Gli utenti anonimi possono leggere i BLOB presenti in un contenitore accessibile pubblicamente senza effettuare l'autenticazione della richiesta.

È possibile configurare un contenitore con le autorizzazioni seguenti:

* **Nessun accesso in lettura pubblico:** solo il proprietario dell'account di archiviazione può accedere al contenitore e ai relativi BLOB. Questa è l'impostazione predefinita per tutti i nuovi contenitori.
* **Accesso in lettura pubblico solo per i BLOB:** i dati del BLOB all'interno del contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client anonimi non possono enumerare i BLOB all'interno del contenitore.
* **Accesso in lettura pubblico completo:** i dati del BLOB e del contenitore possono essere letti tramite richiesta anonima. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

È possibile impostare le autorizzazioni per il contenitore usando gli strumenti seguenti:

* [Portale di Azure](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Interfaccia della riga di comando di Azure 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* A livello di programmazione, usando una delle librerie client di archiviazione o l'API REST.

### <a name="set-container-permissions-in-the-azure-portal"></a>Impostare le autorizzazioni per in contenitore nel portale di Azure
Per impostare le autorizzazioni per il contenitore nel [portale di Azure](https://portal.azure.com), seguire questa procedura:

1. Selezionare il pannello **Account di archiviazione** nel portale di Azure. È possibile trovare l'account di archiviazione selezionando **Account di archiviazione** nel pannello del menu principale del portale.
1. Nel pannello del menu, in **SERVIZIO BLOB** selezionare **Contenitori**.
1. Fare clic con il pulsante destro del mouse sulla riga del contenitore o selezionare i puntini di sospensione per aprire il **menu di scelta rapida** del contenitore.
1. In questo menu selezionare **Criteri di accesso**.
1. Nel menu a discesa selezionare un **Tipo di accesso**.

    ![Finestra di dialogo Modifica metadati contenitore](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Impostare le autorizzazioni per il contenitore con .NET
Per impostare le autorizzazioni per un contenitore con C# e la libreria client di archiviazione per .NET, recuperare prima di tutto le autorizzazioni esistenti per il contenitore eseguendo una chiamata al metodo **GetPermissions**. Impostare quindi la proprietà **PublicAccess** per l'oggetto **BlobContainerPermissions** restituito dal metodo **GetPermissions**. Infine, chiamare il metodo **SetPermissions** con le autorizzazioni aggiornate.

L'esempio seguente imposta le autorizzazioni per il contenitore per l'accesso in lettura pubblico completo. Per impostare le autorizzazioni per l'accesso in lettura pubblico solo per i BLOB, impostare la proprietà **PublicAccess** su **BlobContainerPublicAccessType.Blob**. Per rimuovere tutte le autorizzazioni per gli utenti anonimi, impostare la proprietà su **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Accesso anonimo a contenitori e BLOB
Un client che accede a contenitori e BLOB in modo anonimo può usare costruttori che non richiedono credenziali. L'esempio seguente mostra alcuni modi diversi per fare riferimento a risorse del servizio BLOB in modo anonimo.

### <a name="create-an-anonymous-client-object"></a>Creare un oggetto client anonimo
È possibile creare un nuovo oggetto client del servizio per l'accesso anonimo, specificando l'endpoint del servizio BLOB per l'account. Tuttavia, è necessario conoscere anche il nome di un contenitore in tale account disponibile per l'accesso anonimo.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
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
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
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
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Funzionalità disponibili per utenti anonimi
Nella tabella seguente sono riportate le operazioni che possono essere richiamate da utenti anonimi quando l'ACL di un contenitore è impostato per consentire l'accesso pubblico.

| Operazione REST | Autorizzazione con accesso in lettura pubblico completo | Autorizzazione con accesso in lettura pubblico solo per BLOB |
| --- | --- | --- |
| List Containers |Solo proprietario |Solo proprietario |
| Create Container |Solo proprietario |Solo proprietario |
| Get Container Properties |Tutti |Solo proprietario |
| Get Container Metadata |Tutti |Solo proprietario |
| Set Container Metadata |Solo proprietario |Solo proprietario |
| Get Container ACL |Solo proprietario |Solo proprietario |
| Set Container ACL |Solo proprietario |Solo proprietario |
| Delete Container |Solo proprietario |Solo proprietario |
| List Blobs |Tutti |Solo proprietario |
| Put Blob |Solo proprietario |Solo proprietario |
| Get Blob |Tutti |Tutti |
| Get Blob Properties |Tutti |Tutti |
| Set Blob Properties |Solo proprietario |Solo proprietario |
| Get Blob Metadata |Tutti |Tutti |
| Set Blob Metadata |Solo proprietario |Solo proprietario |
| Put Block |Solo proprietario |Solo proprietario |
| Get Block List (solo blocchi con commit) |Tutti |Tutti |
| Get Block List (solo blocchi senza commit o tutti i blocchi) |Solo proprietario |Solo proprietario |
| Put Block List |Solo proprietario |Solo proprietario |
| Delete Blob |Solo proprietario |Solo proprietario |
| Copy Blob |Solo proprietario |Solo proprietario |
| Snapshot Blob |Solo proprietario |Solo proprietario |
| Lease Blob |Solo proprietario |Solo proprietario |
| Put Page |Solo proprietario |Solo proprietario |
| Get Page Ranges |Tutti |Tutti |
| Append Blob |Solo proprietario |Solo proprietario |

## <a name="next-steps"></a>Passaggi successivi

* [Autenticazione per i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Uso delle firme di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delega dell'accesso con una firma di accesso condiviso](https://msdn.microsoft.com/library/azure/ee395415.aspx)
