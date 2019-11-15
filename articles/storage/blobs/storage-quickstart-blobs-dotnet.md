---
title: 'Guida introduttiva: Libreria di Archiviazione BLOB di Azure v12 - .NET'
description: In questa Guida introduttiva verrà illustrato come usare la libreria client di Archiviazione BLOB di Azure versione 12 per .NET per creare un contenitore e un BLOB nell'archiviazione (oggetto) BLOB. Verrà successivamente illustrato come scaricare il BLOB nel computer locale e come elencare tutti i BLOB in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 18f4053a2f8b1b5c880b46cf6eeb46c0dbf97f15
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825388"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Guida introduttiva: Libreria client di Archiviazione BLOB di Azure v12 per .NET

Iniziare a usare la libreria client di Archiviazione BLOB di Azure v12 per .NET. L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. L'archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati.

> [!NOTE]
> Per iniziare a usare la versione precedente dell'SDK, vedere [Avvio rapido: Libreria client di Archiviazione BLOB di Azure per .NET](storage-quickstart-blobs-dotnet-legacy.md).

Usare la libreria client di Archiviazione BLOB di Azure v12 per .NET per:

* Creare un contenitore
* Caricare un oggetto BLOB in Archiviazione di Azure
* Elencare tutti i BLOB in un contenitore
* Scaricare il BLOB nel computer locale
* Eliminare un contenitore

[Documentazione di riferimento dell'API](/dotnet/api/azure.storage.blobs) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs/12.0.0) | [Esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Account di archiviazione di Azure: [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Versione aggiornata di [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) per il sistema operativo in uso. Assicurarsi di ottenere l'SDK e non il runtime.

## <a name="setting-up"></a>Configurazione

Questa sezione illustra come preparare un progetto da usare con la libreria client di Archiviazione BLOB di Azure v12 per .NET.

### <a name="create-the-project"></a>Creare il progetto

Creare un'applicazione .NET Core denominata *BlobQuickstartV12*.

1. Nella finestra di una console (ad esempio cmd, PowerShell o Bash) usare il comando `dotnet new` per creare una nuova app console con il nome *BlobQuickstartV12*. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Passare alla directory *BlobQuickstartV12* creata.

   ```console
   cd BlobQuickstartV12
   ```

1. Nella directory *BlobQuickstartV12* creare un'altra directory denominata *data*. Qui verranno creati e archiviati i file di dati BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installare il pacchetto

Sempre nella directory dell'applicazione, installare la libreria client di Archiviazione BLOB di Azure per il pacchetto .NET usando il comando `dotnet add package`.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire il file *Program.cs* in un editor di testo
1. Rimuovere l'istruzione `Console.WriteLine("Hello World!");`
1. Aggiungere le direttive `using`
1. Aggiornare la dichiarazione del metodo `Main` per supportare codice asincrono

Ecco il codice:

```csharp
using Azure.Storage;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiare le credenziali dal portale di Azure

Le richieste effettuate dall'applicazione di esempio ad Archiviazione di Azure devono essere autorizzate. Per autorizzare una richiesta, aggiungere all'applicazione le credenziali dell'account di archiviazione sotto forma di stringa di connessione. Visualizzare le credenziali dell'account di archiviazione seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. Nella sezione **Impostazioni** della panoramica dell'account di archiviazione selezionare **Chiavi di accesso**. A questo punto è possibile visualizzare le chiavi di accesso dell'account e la stringa di connessione completa per ogni chiave.
4. Trovare il valore **Stringa di connessione** in **key1** e selezionare il pulsante **Copia** per copiare la stringa di connessione. Il valore della stringa di connessione verrà aggiunto a una variabile di ambiente nel passaggio successivo.

    ![Screenshot che mostra come copiare una stringa di connessione dal portale di Azure](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Dopo aver copiato la stringa di connessione, scriverla in una nuova variabile di ambiente nel computer locale che esegue l'applicazione. Per impostare la variabile di ambiente, aprire una finestra della console e seguire le istruzioni per il sistema operativo specifico. Sostituire `<yourconnectionstring>` con la stringa di connessione effettiva.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Dopo aver aggiunto la variabile di ambiente in Windows, è necessario avviare una nuova istanza della finestra di comando.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Riavviare i programmi

Dopo aver aggiunto la variabile di ambiente, riavviare gli eventuali programmi in esecuzione che dovranno leggerla. Ad esempio, riavviare l'ambiente di sviluppo o l'editor prima di continuare.

## <a name="object-model"></a>Modello a oggetti

Archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari. L’archiviazione BLOB offre tre tipi di risorse:

* L'account di archiviazione
* Un contenitore nell'account di archiviazione
* Un oggetto BLOB in un contenitore

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB](./media/storage-blob-introduction/blob1.png)

Per interagire con queste risorse, usare le classi .NET seguenti:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): la classe `BlobServiceClient` consente di modificare le risorse di Archiviazione di Azure e i contenitori BLOB.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): la classe `BlobContainerClient` consente di modificare i contenitori di Archiviazione di Azure e i relativi oggetti BLOB.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): la classe `BlobClient` consente di modificare gli oggetti BLOB di Archiviazione di Azure.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): la classe `BlobDownloadInfo` rappresenta le proprietà e il contenuto restituiti dal download di un BLOB.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire le operazioni seguenti con la libreria client di Archiviazione BLOB di Azure per .NET:

* [Ottenere la stringa di connessione](#get-the-connection-string)
* [Creare un contenitore](#create-a-container)
* [Caricare BLOB in un contenitore](#upload-blobs-to-a-container)
* [Elencare i BLOB in un contenitore](#list-the-blobs-in-a-container)
* [Scaricare BLOB](#download-blobs)
* [Eliminare un contenitore](#delete-a-container)

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione per l'account di archiviazione dalla variabile di ambiente creata nella sezione [Configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice all'interno del metodo `Main`:

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("CONNECT_STR");
```

### <a name="create-a-container"></a>Creare un contenitore

Decidere un nome per il nuovo contenitore. Il codice seguente aggiunge un valore GUID al nome del contenitore per garantirne l'univocità.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).

Creare un'istanza della classe [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient). Chiamare quindi il metodo [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) per creare il contenitore nell'account di archiviazione.

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Caricare BLOB in un contenitore

Il frammento di codice seguente consente di:

1. Creare un file di testo nella directory *data* locale.
1. Ottenere un riferimento a un oggetto [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) chiamando il metodo [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) sul contenitore dalla sezione [Creare un contenitore](#create-a-container).
1. Caricare il file di testo locale nel BLOB chiamando il metodo [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync). Questo metodo crea il BLOB se non esiste o lo sovrascrive se esiste già.

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB nel contenitore chiamando il metodo [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync). In questo caso, al contenitore è stato aggiunto un unico BLOB, quindi l'operazione di recupero dell'elenco restituisce solo tale BLOB.

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare il BLOB creato in precedenza chiamando il metodo [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync). Il codice di esempio aggiunge il suffisso "DOWNLOADED" al nome del file in modo da consentire la visualizzazione di entrambi i file nel file system locale.

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so you can see both files in MyDocuments
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using FileStream downloadFileStream = File.OpenWrite(downloadFilePath);
await download.Content.CopyToAsync(downloadFileStream);
downloadFileStream.Close();
```

### <a name="delete-a-container"></a>Eliminare un contenitore

Il codice seguente elimina le risorse create dall'app eliminando l'intero contenitore usando [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Elimina anche i file locali creati dall'app.

L'app viene sospesa per l'input dell'utente chiamando `Console.ReadLine` prima dell'eliminazione di BLOB, contenitore e file locali. Si tratta di una valida opportunità per verificare che le risorse siano state effettivamente create correttamente, prima che vengano eliminate.

Aggiungere questo codice alla fine del metodo `Main`:

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea un file di test nella cartella *MyDocuments* locale e lo carica nella risorsa di archiviazione BLOB. L'esempio elenca quindi i BLOB presenti nel contenitore e scarica il file con un nuovo nome per consentire il confronto tra i nuovi file e quelli precedenti.

Passare alla directory dell'applicazione, quindi compilarla ed eseguirla.

```console
dotnet build
```

```console
dotnet run
```

L'output dell'app è simile all'esempio seguente:

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Prima di iniziare il processo di pulizia, controllare che nella cartella *Documenti* siano presenti due file. È possibile aprirli e verificare che sono identici.

Dopo aver verificato i file, premere **INVIO** per eliminare i file di test e completare la demo.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come caricare, scaricare ed elencare i BLOB con .NET.

Per visualizzare le app di esempio di Archiviazione BLOB, procedere con:

> [!div class="nextstepaction"]
> [Esempi di Archiviazione BLOB di Azure SDK v12 - .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Per esercitazioni, esempi, guide introduttive e altra documentazione, vedere [Azure per gli sviluppatori .NET e .NET Core](/dotnet/azure/).
* Per altre informazioni su .NET Core, vedere [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Introduzione a .NET in 10 minuti).
