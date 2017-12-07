---
title: Introduzione all'archiviazione BLOB e ai servizi connessi di Visual Studio (ASP.NET Core) | Microsoft Docs
description: Informazioni su come iniziare a usare l'archiviazione BLOB di Azure in un progetto ASP.NET Core in Visual Studio dopo aver creato un account di archiviazione con i servizi connessi di Visual Studio
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: afd73bd0fd041a53fbe31aa3a5c23b3e27d7a9ec
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introduzione all'archiviazione BLOB di Azure e ai relativi servizi di Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Questo articolo descrive come iniziare a usare l'archiviazione BLOB di Azure in Visual Studio dopo aver creato o fatto riferimento a un account di archiviazione di Azure in un progetto ASP.NET Core usando la funzionalità **Servizi connessi** di Visual Studio. **Servizi connessi** consente di installare i pacchetti NuGet appropriati per accedere all'archiviazione di Azure nel progetto e di aggiungere la stringa di connessione per l'account di archiviazione ai file di configurazione del progetto. Per informazioni generali su Archiviazione di Azure, vedere la relativa [documentazione](https://azure.microsoft.com/documentation/services/storage/).

L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Un singolo BLOB può avere qualsiasi dimensione. I BLOB possono essere costituiti da immagini, file audio e video, dati non elaborati e file di documento. Questo articolo descrive come iniziare a usare l'archiviazione BLOB dopo aver creato un account di archiviazione di Azure usando **Servizi connessi** di Visual Studio in un progetto ASP.NET Core.

I BLOB di archiviazione si trovano nei contenitori esattamente come i file si trovano nelle cartelle. Dopo aver creato un BLOB, è possibile creare uno o più contenitori nel BLOB. Ad esempio, in un BLOB denominato "Raccoglitore" è possibile creare contenitori denominati "immagini" per archiviare immagini e un altro contenitore denominato "audio" per archiviare file audio. Dopo aver creato i contenitori, è possibile caricarvi singoli file. Per altre informazioni sulla modifica dei BLOB a livello di codice, vedere [Introduzione all'archiviazione BLOB di Azure con .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) .

Alcune API di Archiviazione di Azure sono asincrone e il codice riportato in questo articolo presuppone che vengano usati metodi asincroni. Per altre informazioni, vedere [Programmazione asincrona](https://docs.microsoft.com/dotnet/csharp/async).

## <a name="access-blob-containers-in-code"></a>Accesso ai contenitori BLOB nel codice

Per accedere ai BLOB a livello di codice nei progetti ASP.NET Core, è necessario aggiungere il codice seguente, se non è già presente:

1. Aggiungere le necessarie istruzioni `using`:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Ottenere un oggetto `CloudStorageAccount` che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Usare un oggetto `CloudBlobClient` per ottenere un riferimento `CloudBlobContainer` a un contenitore esistente nell'account di archiviazione:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Creazione di un contenitore nel codice

È anche possibile usare l'oggetto `CloudBlobClient` per creare un contenitore nell'account di archiviazione chiamando `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Per rendere i file all'interno del contenitore disponibili per tutti, impostare il contenitore come pubblico:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Caricare un BLOB in un contenitore

Per caricare un file BLOB in un contenitore, ottenere un riferimento a un contenitore e usarlo per ottenere un riferimento a un BLOB. Caricare quindi un flusso di dati in questo riferimento chiamando il metodo `UploadFromStreamAsync`. Questa operazione crea il BLOB, se non esiste già, oppure sovrascrive un BLOB eistente. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore, quindi chiamare il relativo metodo `ListBlobsSegmentedAsync` per recuperare i BLOB e/o le directory all'interno del contenitore. Per accedere al set completo di proprietà e metodi di un elemento restituito `IListBlobItem`, eseguire il cast a un oggetto `CloudBlockBlob`, `CloudPageBlob` o `CloudBlobDirectory`. Se non si conosce il tipo di BLOB, usare un controllo del tipo per determinare quello in cui eseguire il cast.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

Per altri modi di elencare il contenuto di un contenitore BLOB, vedere [Introduzione all'archivio BLOB di Azure con .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container).

## <a name="download-a-blob"></a>Scaricare un BLOB

Per scaricare un BLOB, ottenere prima un riferimento al BLOB, quindi chiamare il metodo `DownloadToStreamAsync`. L'esempio seguente usa il metodo `DownloadToStreamAsync` per trasferire il contenuto del BLOB in un oggetto flusso che è possibile successivamente salvare come file locale.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Per altri modi di salvare i BLOB come file, vedere [Introduzione all'archivio BLOB di Azure con .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).

## <a name="delete-a-blob"></a>Eliminare un BLOB

Per eliminare un BLOB, ottenere prima un riferimento al BLOB, quindi chiamare il metodo `DeleteAsync`:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
