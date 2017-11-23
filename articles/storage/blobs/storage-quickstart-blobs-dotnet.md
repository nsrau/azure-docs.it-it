---
title: Guida rapida di Azure - Trasferire oggetti da e verso Archiviazione BLOB di Azure con .NET | Microsoft Docs
description: Apprendere rapidamente a trasferire oggetti da e verso Archiviazione BLOB di Azure con .NET
services: storage
documentationcenter: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: tamram
ms.openlocfilehash: 1eac4165c35cb116a359c074bd629c918b58097c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Trasferire oggetti da e verso Archiviazione BLOB di Azure con .NET

In questa guida introduttiva si apprenderà a usare la libreria client .NET per consentire ad Archiviazione di Azure di caricare, scaricare ed elencare BLOB in blocchi in un contenitore.

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa guida introduttiva, installare [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) con i carichi di lavoro seguenti:
    
    - **Sviluppo di Azure**

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'applicazione di esempio usata in questa guida rapida è un'applicazione console di base. 

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire la soluzione di Visual Studio, cercare la cartella storage-blobs-dotnet-quickstart, aprirla e fare doppio clic su storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Nell'applicazione è necessario inserire la stringa di connessione per l'account di archiviazione. Aprire il file `app.config` da Esplora soluzioni in Visual Studio. Cercare la voce `StorageConnectionString`. Per **value** sostituire l'intero valore della stringa di connessione con quello salvato dal portale di Azure. `storageConnectionString` dovrebbe essere simile al seguente:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=<NameHere>;
    AccountKey=<KeyHere>" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>Eseguire l'esempio

Questo esempio consente di creare un file di test in Documenti, caricarlo nell'archiviazione BLOB, elencare i BLOB nel contenitore, quindi scaricare il file con un nuovo nome, così da poter confrontare i file nuovi e quelli vecchi. 

Eseguire l'esempio premendo F5. Viene mostrato un output nella finestra della console simile al seguente: 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

Quando si preme un tasto qualsiasi per continuare, si elimina il contenitore di archiviazione e i file. Prima di continuare, controllare che nella cartella "Documenti" siano presenti i due file. È possibile aprirli e vedere che sono identici. Copiare l'URL per il BLOB nella finestra della console e incollarlo in un browser per visualizzare il contenuto del file nell'archiviazione BLOB.

È anche possibile usare uno strumento come [Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per visualizzare i file nell'archiviazione BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 

Dopo aver verificato i file, premere un tasto qualsiasi per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file Program.cs per esaminare il codice. 

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Viene quindi descritto in dettaglio il codice di esempio, per consentire di comprenderne il funzionamento.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione

La prima cosa da fare è creare i riferimenti agli oggetti usati per accedere e gestire l'archiviazione BLOB. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto **CloudStorageAccount** che punti all'account di archiviazione. 

* Creare un'istanza dell'oggetto **CloudBlobClient** che punti al servizio BLOB nell'account di archiviazione. 

* Creare un'istanza dell'oggetto **CloudBlobContainer** che rappresenti il contenitore a cui si accede. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.

Dopo aver creato **CloudBlobContainer**, è possibile creare un'istanza dell'oggetto **CloudBlockBlob** che punti al BLOB specifico a cui si è interessati ed esegua operazioni di caricamento, download, copia e così via.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione dei contenitori e dei BLOB vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

In questa sezione si creano istanze degli oggetti, si crea un nuovo contenitore e quindi si impostano le autorizzazioni nel contenitore in modo che i BLOB siano pubblici e accessibili solo con un URL. Il contenitore è denominato **quickstartblobs**. 

Questo esempio usa **CreateIfNotExists** perché si desidera creare un nuovo contenitore ogni volta che si esegue l'esempio. In un ambiente di produzione in cui si usa lo stesso contenitore per tutta l'applicazione, è consigliabile chiamare **CreateIfNotExists** solo una volta. In alternativa, creare il contenitore in anticipo in modo che non sia necessario crearlo nel codice.

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di accodamento e BLOB di pagine. I BLOB in blocchi sono quelli usati più di frequentemente e vengono usati in questa guida rapida. 

Per caricare un file in un BLOB, ottenere un riferimento al BLOB nel contenitore di destinazione. Dopo aver creato il riferimento al BLOB, è possibile caricarvi i dati con [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Questa operazione consente di creare il BLOB se non esiste o di sovrascriverlo se esiste già.

Il codice di esempio crea un file locale da usare per il caricamento e il download, per archiviare il file da caricare come **fileAndPath** e il nome del BLOB nel **localFileName**. Nell'esempio seguente il file viene caricato in un contenitore denominato **quickstartblobs**.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

Esistono diversi metodi di caricamento che è possibile usare con l'archiviazione BLOB. Ad esempio, se si dispone di un flusso di memoria, è possibile usare il metodo UploadFromStreamAsync anziché UploadFromFileAsync. 

I BLOB in blocchi possono essere qualsiasi tipo di file di testo o binario. I BLOB di pagine vengono usati principalmente per i file VHD usati per tornare alle macchine virtuali IaaS. I BLOB di accodamento sono usati per la registrazione, ad esempio quando si desidera scrivere in un file e poi continuare ad aggiungere altre informazioni. La maggior parte degli oggetti presenti nell'archiviazione BLOB è costituita da BLOB in blocchi.

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

È possibile ottenere un elenco di file nel contenitore usando [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). Il codice seguente recupera l'elenco di BLOB, quindi esegue il ciclo per tutti loro mostrando gli URI dei BLOB trovati. Per visualizzare il file è possibile copiare l'URI dalla finestra di comando e incollarlo in un browser.

Se si dispone di un massimo di 5.000 BLOB nel contenitore, tutti i nomi di BLOB vengono recuperati in una chiamata a ListBlobsSegmentedAsync. Se si dispone di più di 5.000 BLOB nel contenitore, il servizio recupera l'elenco in set di 5.000 fino a quando tutti i nomi di BLOB vengono recuperati. Pertanto la prima volta che questa API viene chiamata, restituisce i primi 5.000 nomi di BLOB e un token di continuazione. La seconda volta, l'utente presenta il token e il servizio recupera il set successivo di nomi di BLOB e così via, fino a quando il token di continuazione è null, il che vuol dire che sono stati recuperati tutti i nomi di BLOB. 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare i BLOB nel disco locale mediante [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

Il codice seguente consente di scaricare il BLOB caricato nella sezione precedente, aggiungendo un suffisso "_DOWNLOADED" al nome del BLOB in modo da visualizzare entrambi i file sul disco locale. 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

### <a name="clean-up-resources"></a>Pulire le risorse

Se i BLOB caricati in questa guida rapida non sono più necessari, è possibile eliminare l'intero contenitore usando [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Eliminare anche i file creati se non sono più necessari.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida rapida è stato descritto il trasferimento di file tra il disco locale e un Archiviazione BLOB di Azure con .NET. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](storage-dotnet-how-to-use-blobs.md)

Per altri esempi di codice di Archiviazione di Azure da scaricare ed eseguire, vedere l'elenco di [esempi di Archiviazione di Azure che usano .NET](../common/storage-samples-dotnet.md).

Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
