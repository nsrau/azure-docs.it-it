---
title: 'Guida introduttiva di Azure: Caricare, scaricare ed elencare BLOB in Archiviazione di Azure con .NET | Microsoft Docs'
description: In questa guida introduttiva viene creato un account di archiviazione e un contenitore. Si usa quindi la libreria client di archiviazione per .NET per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/01/2018
ms.author: tamram
ms.openlocfilehash: 8d1f09a39e865500aa8e4d093473d4989f134c3d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con .NET

In questa guida introduttiva si apprenderà a usare la libreria client .NET per consentire ad Archiviazione di Azure di caricare, scaricare ed elencare BLOB in blocchi in un contenitore.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, per prima cosa creare un account di archiviazione di Azure nel [portale di Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Per informazioni sulla creazione dell'account, vedere [Creare un account di archiviazione](../common/storage-quickstart-create-account.md).

Successivamente, scaricare e installare .NET Core 2.0 per il sistema operativo in uso. Si può anche scegliere di installare un editor da usare con il sistema operativo.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- Installare [.NET Core per Windows](https://www.microsoft.com/net/download/windows/build) 
- Facoltativamente, installare [Visual Studio per Windows](https://www.visualstudio.com/) 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Installare [.NET Core per Linux](https://www.microsoft.com/net/download/linux/build)
- Facoltativamente, installare [Visual Studio Code](https://www.visualstudio.com/) e l'[estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Installare [.NET Core per macOS](https://www.microsoft.com/net/download/macos/build)
- Facoltativamente, installare [Visual Studio per Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

---

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'applicazione di esempio usata in questa guida rapida è un'applicazione console di base. È possibile esplorare l'applicazione di esempio in [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire la soluzione di Visual Studio, cercare la cartella storage-blobs-dotnet-quickstart, aprirla e fare doppio clic su storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Per eseguire l'applicazione, è necessario specificare la stringa di connessione per l'account di archiviazione. È possibile archiviare questa stringa di connessione in una variabile di ambiente nel computer locale che esegue l'applicazione. Creare la variabile di ambiente con uno dei comandi riportati di seguito, in base al sistema operativo in uso. Sostituire `<yourconnectionstring>` con la stringa di connessione effettiva.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Modificare il file con estensione bash_profile e aggiungere la variabile di ambiente:

```
export STORAGE_CONNECTION_STRING=
```

Dopo aver aggiunto la variabile di ambiente, disconnettersi e accedere di nuovo per rendere effettive le modifiche. In alternativa, è possibile digitare "source .bash_profile" nel terminale.

---

## <a name="run-the-sample"></a>Eseguire l'esempio

Questo esempio crea un file di test nella cartella **MyDocuments** locale e lo carica nell'archivio BLOB. L'esempio elenca quindi i BLOB nel contenitore e scarica il file con un nuovo nome per consentire il confronto tra i nuovi file e quelli precedenti. 

Passare alla directory dell'applicazione e quindi eseguire l'applicazione con il comando `dotnet run`.

```
dotnet run
```

L'output mostrato è simile all'esempio seguente:

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

Quando si preme **INVIO**, l'applicazione elimina il contenitore di archiviazione e i file. Prima dell'eliminazione, controllare che nella cartella **MyDocuments** siano presenti i due file. È possibile aprirli e verificare che sono identici. Copiare l'URL del BLOB nella finestra della console e incollarlo in un browser per visualizzare il contenuto del BLOB.

Dopo aver verificato i file, premere un tasto qualsiasi per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file Program.cs per esaminare il codice. 

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Esplorare quindi il codice di esempio per poterne comprendere il funzionamento.

### <a name="try-parsing-the-connection-string"></a>Provare ad analizzare la stringa di connessione

Per prima cosa, l'esempio controlla che la variabile di ambiente contenga una stringa di connessione analizzabile per creare un oggetto [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) che punti all'account di archiviazione. Per verificare la validità della stringa di connessione, usare il metodo [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Se **TryParse** ha esito positivo, inizializza la variabile *storageAccount* e restituisce **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring", EnvironmentVariableTarget.User);

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>Creare il contenitore e impostare le autorizzazioni

Successivamente, l'esempio crea un contenitore e ne imposta le autorizzazioni in modo che tutti i BLOB nel contenitore siano pubblici. Un BLOB pubblico è accessibile in modo anonimo da qualsiasi client.

Per creare il contenitore, per prima cosa creare un'istanza dell'oggetto [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient) che punti all'archivio BLOB nell'account di archiviazione. Creare quindi un'istanza dell'oggetto [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) e infine il contenitore. 

In questo caso, l'esempio chiama il metodo [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) per creare il contenitore. Al nome del contenitore viene aggiunto un valore GUID per garantirne l'univocità. In un ambiente di produzione è spesso preferibile usare il metodo [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) per creare un contenitore solo se non esiste già ed evitare conflitti di nomi.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

Successivamente, l'esempio carica un file locale in un BLOB in blocchi. L'esempio di codice ottiene un riferimento a un oggetto **CloudBlockBlob** chiamando il metodo [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) sul contenitore creato nella sezione precedente. Carica quindi il file selezionato nel BLOB chiamando il metodo [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Questo metodo crea il BLOB se non esiste o lo sovrascrive se esiste già. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

L'esempio elenca i BLOB nel contenitore usando il metodo [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). Nel caso dell'esempio, al contenitore è stato aggiunto un unico BLOB, quindi l'operazione di recupero dell'elenco restituisce solo tale BLOB.

Se il numero di BLOB da restituire in una chiamata è eccessivo (per impostazione predefinita, superiore a 5000), il metodo **ListBlobsSegmentedAsync** restituisce un segmento del set di risultati totale e un token di continuazione. Per recuperare il successivo segmento di BLOB, si passa il token di continuazione restituito dalla chiamata precedente e così via finché il token non è Null. Un token di continuazione Null indica che sono stati recuperati tutti i BLOB. Il codice di esempio mostra come usare il token di continuazione per illustrare le procedure consigliate.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Scaricare BLOB

Successivamente, l'esempio scarica il BLOB creato in precedenza nel file system locale con il metodo [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync). Il codice di esempio aggiunge il suffisso "_DOWNLOADED" al nome del BLOB in modo da consentire la visualizzazione di entrambi i file nel file system locale.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Pulire le risorse

L'esempio esegue la pulizia delle risorse create eliminando l'intero contenitore con [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). È anche possibile eliminare i file locali, se si vuole.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="resources-for-developing-net-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni .NET con BLOB

Per lo sviluppo .NET con archivi BLOB, vedere le risorse aggiuntive seguenti:

### <a name="binaries-and-source-code"></a>File binari e codice sorgente

- Scaricare il pacchetto NuGet per l'ultima versione della [libreria client di archiviazione per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). 
- Visualizzare il [codice sorgente della libreria client di archiviazione per .NET](https://github.com/Azure/azure-storage-net) in GitHub.

### <a name="client-library-reference-and-samples"></a>Informazioni di riferimento ed esempi relativi alla libreria client

- Per altre informazioni sulla libreria client, vedere le [informazioni di riferimento sulle API di archiviazione per .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Esplorare gli [esempi per l'archivio BLOB](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob) scritti con la libreria client di archiviazione per .NET.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come caricare, scaricare ed elencare i BLOB con .NET. 

Per informazioni su come creare un'app Web che carica un'immagine in un archivio BLOB, vedere [Upload image data in the cloud with Azure Storage](storage-upload-process-images.md) (Caricare dati di immagine nel cloud con Archiviazione di Azure).

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](storage-dotnet-how-to-use-blobs.md)

- Per altre informazioni su .NET Core, vedere [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Introduzione a .NET in 10 minuti).
- Per esplorare un'applicazione di esempio distribuibile da Visual Studio per Windows, vedere [.NET Photo Gallery Web Application Sample with Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/) (Esempio di applicazione Web .NET di raccolta foto con archivio BLOB di Azure).
 