---
title: Abilitare l'accesso in lettura pubblico per contenitori e BLOB in Archiviazione BLOB di Azure | Microsoft Docs
description: Informazioni su come rendere disponibili per l'accesso anonimo contenitori e BLOB e su come accedervi a livello di programmazione.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: e0f93b0a95a228b26fae266129aea4b595b05e0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148376"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gestire l'accesso in lettura anonimo a contenitori e BLOB

È possibile abilitare l'accesso in lettura pubblico anonimo a un contenitore e ai relativi BLOB in Archiviazione BLOB di Azure. Ciò permette di concedere l'accesso in sola lettura a queste risorse senza condividere la chiave dell'account e senza richiedere una firma di accesso condiviso (SAS).

L'accesso in lettura pubblico è ideale per scenari in cui si vuole che BLOB specifici siano sempre disponibili per l'accesso in lettura anonimo. Per un controllo più accurato, è possibile creare una firma di accesso condiviso. Le firme di accesso condiviso consentono di fornire accesso limitato usando autorizzazioni diverse, per un periodo di tempo specifico. Per altre informazioni sulla creazione di firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Concedere le autorizzazioni agli utenti anonimi per contenitori e BLOB

Per impostazione predefinita, un contenitore e ai BLOB in esso contenuti sono accessibili solo da un utente che dispone delle autorizzazioni appropriate. Per concedere l'accesso in lettura agli utenti anonimi per un contenitore e ai relativi BLOB, è possibile impostare il livello di accesso pubblico del contenitore. Quando si concede l'accesso pubblico a un contenitore, gli utenti anonimi possono leggere i BLOB all'interno di un contenitore accessibile pubblicamente senza autorizzare la richiesta.

È possibile configurare un contenitore con le autorizzazioni seguenti:

* **Nessun accesso in lettura pubblico:** Il contenitore e ai relativi BLOB sono accessibili solo dal proprietario dell'account di archiviazione. Questa è l'impostazione predefinita per tutti i nuovi contenitori.
* **Accesso in lettura pubblico solo per i BLOB:** I BLOB all'interno del contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client anonimi non possono enumerare i BLOB all'interno del contenitore.
* **Accesso per contenitori e ai relativi BLOB in lettura pubblico:** Tutti i dati blob e contenitore possono essere letti tramite richiesta anonima. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

È possibile impostare le autorizzazioni per il contenitore usando gli strumenti seguenti:

* [Portale di Azure](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Interfaccia della riga di comando di Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* A livello di programmazione, usando una delle librerie client di archiviazione o l'API REST.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Impostare livello di accesso pubblico del contenitore nel portale di Azure

Dal [portale di Azure](https://portal.azure.com), è possibile aggiornare il livello di accesso pubblico per uno o più contenitori:

1. Passare all'account di archiviazione nel portale di Azure.
1. Sotto **servizio Blob** nel pannello del menu, selezionare **BLOB**.
1. Selezionare i contenitori per il quale si desidera impostare il livello di accesso pubblico.
1. Usare la **livello di accesso di modifica** pulsante per visualizzare le impostazioni di accesso pubblico.
1. Selezionare il livello di accesso pubblico da usare la **livello di accesso pubblico** elenco a discesa e fare clic su OK per applicare la modifica per i contenitori selezionati.

Lo screenshot seguente mostra come modificare il livello di accesso pubblico per i contenitori selezionati.

![Screenshot che illustra come impostare a livello di accesso pubblico nel portale](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Non è possibile modificare il livello di accesso pubblico per un singolo blob. Livello di accesso pubblico è impostato solo a livello di contenitore.

### <a name="set-container-public-access-level-with-net"></a>Impostare il livello di accesso pubblico di contenitori con .NET

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

Un client che accede a contenitori e BLOB in modo anonimo può usare costruttori che non richiedono credenziali. Gli esempi seguenti illustrano alcuni modi per fare riferimento in modo anonimo a contenitori e BLOB.

### <a name="create-an-anonymous-client-object"></a>Creare un oggetto client anonimo

È possibile creare un nuovo oggetto client del servizio per l'accesso anonimo, fornendo l'endpoint di archiviazione Blob per l'account. Tuttavia, è necessario conoscere anche il nome di un contenitore in tale account disponibile per l'accesso anonimo.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
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

La tabella seguente illustra le operazioni che possono essere chiamate in modo anonimo quando un contenitore è configurato per l'accesso pubblico.

| Operazione REST | Accesso in lettura pubblico al contenitore | Accesso in lettura pubblico solo per i BLOB |
| --- | --- | --- |
| List Containers | Solo richieste autorizzate | Solo richieste autorizzate |
| Create Container | Solo richieste autorizzate | Solo richieste autorizzate |
| Get Container Properties | Richieste anonime consentite | Solo richieste autorizzate |
| Get Container Metadata | Richieste anonime consentite | Solo richieste autorizzate |
| Set Container Metadata | Solo richieste autorizzate | Solo richieste autorizzate |
| Get Container ACL | Solo richieste autorizzate | Solo richieste autorizzate |
| Set Container ACL | Solo richieste autorizzate | Solo richieste autorizzate |
| Delete Container | Solo richieste autorizzate | Solo richieste autorizzate |
| List Blobs | Richieste anonime consentite | Solo richieste autorizzate |
| Put Blob | Solo richieste autorizzate | Solo richieste autorizzate |
| Get Blob | Richieste anonime consentite | Richieste anonime consentite |
| Get Blob Properties | Richieste anonime consentite | Richieste anonime consentite |
| Set Blob Properties | Solo richieste autorizzate | Solo richieste autorizzate |
| Get Blob Metadata | Richieste anonime consentite | Richieste anonime consentite |
| Set Blob Metadata | Solo richieste autorizzate | Solo richieste autorizzate |
| Put Block | Solo richieste autorizzate | Solo richieste autorizzate |
| Get Block List (solo blocchi con commit) | Richieste anonime consentite | Richieste anonime consentite |
| Get Block List (solo blocchi senza commit o tutti i blocchi) | Solo richieste autorizzate | Solo richieste autorizzate |
| Put Block List | Solo richieste autorizzate | Solo richieste autorizzate |
| Delete Blob | Solo richieste autorizzate | Solo richieste autorizzate |
| Copy Blob | Solo richieste autorizzate | Solo richieste autorizzate |
| Snapshot Blob | Solo richieste autorizzate | Solo richieste autorizzate |
| Lease Blob | Solo richieste autorizzate | Solo richieste autorizzate |
| Put Page | Solo richieste autorizzate | Solo richieste autorizzate |
| Get Page Ranges | Richieste anonime consentite | Richieste anonime consentite |
| Append Blob | Solo richieste autorizzate | Solo richieste autorizzate |

## <a name="next-steps"></a>Passaggi successivi

* [Autorizzazione per i servizi di archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Uso delle firme di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)