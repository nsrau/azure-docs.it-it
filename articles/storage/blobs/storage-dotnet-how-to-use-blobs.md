---
title: Introduzione all'archivio BLOB di Azure (archivio di oggetti) con .NET | Documentazione Microsoft
description: Archiviare i dati non strutturati nel cloud con l'archivio BLOB (archivio di oggetti) di Azure.
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: d18a8fc8-97cb-4d37-a408-a6f8107ea8b3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 70c7d6a5e1b9aa9a13481893e0baa56538be097c
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="get-started-with-azure-blob-storage-using-net"></a>Introduzione all'archivio BLOB di Azure con .NET

[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

L'archiviazione BLOB di Azure è un servizio che archivia dati non strutturati nel cloud come oggetti/BLOB. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.

### <a name="about-this-tutorial"></a>Informazioni sull'esercitazione
Questa esercitazione illustra come scrivere codice .NET per alcuni scenari comuni dell'archivio BLOB di Azure. Gli scenari presentati includono caricamento, visualizzazione dell'elenco, download ed eliminazione di BLOB.

**Prerequisiti:**

* [Microsoft Visual Studio](https://www.visualstudio.com/)
* [Libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Gestione configurazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Un [account di archiviazione di Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Altri esempi
Per altri esempi di uso dell'archivio BLOB, vedere [Getting Started with Azure Blob Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)(Introduzione all'archiviazione BLOB di Azure in .NET). È possibile scaricare l'applicazione di esempio ed eseguirla oppure esaminare il codice in GitHub.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Aggiungere le direttive using
Aggiungere le direttive **using** seguenti all'inizio del file `Program.cs`:

```csharp
using Microsoft.WindowsAzure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types
```

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Creare il client del servizio BLOB
La classe **CloudBlobClient** consente di recuperare contenitori e BLOB archiviati nell'archivio BLOB. Ecco come creare il client del servizio:

```csharp
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```
A questo punto si è pronti a scrivere codice che legge e scrive i dati nell'archivio BLOB.

## <a name="create-a-container"></a>Creare un contenitore
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

In questo esempio viene creato un contenitore, nel caso in cui non esista già:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();
```

Per impostazione predefinita, il nuovo contenitore è privato, ovvero è necessario specificare la chiave di accesso alle risorse di archiviazione per scaricare BLOB da questo contenitore. Se si desidera che i file all'interno del contenitore siano disponibili a tutti, è possibile impostare il contenitore come pubblico usando il codice seguente:

```csharp
container.SetPermissions(
    new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });
```

Chiunque acceda a Internet può visualizzare i BLOB di un contenitore pubblico. È tuttavia possibile modificarli o eliminarli solo se è disponibile la chiave di accesso dell'account appropriata o una firma di accesso condiviso.

## <a name="upload-a-blob-into-a-container"></a>Caricare un BLOB in un contenitore
Nell'archivio BLOB di Azure sono supportati BLOB in blocchi e BLOB di pagine.  Nella maggior parte dei casi è consigliabile usare il tipo di BLOB in blocchi.

Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB in blocchi. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStream** . Questa operazione crea il BLOB, se non già esistente, o lo sovrascrive, se già esistente.

Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore e si presuppone che il contenitore sia già stato creato.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore
Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore. Sarà quindi possibile utilizzare il metodo **ListBlobs** del contenitore per recuperare i BLOB e/o le directory in esso contenuti. Per accedere all'insieme avanzato di proprietà e metodi per un oggetto **IListBlobItem** restituito, è necessario eseguirne il cast in un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Se il tipo è sconosciuto, è possibile usare un controllo del tipo per determinare quello in cui viene eseguito il cast. Nel codice seguente viene illustrato come recuperare e visualizzare l'URI di ogni elemento nel contenitore _photos_:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("photos");

// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
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
```

Includendo le informazioni sul percorso nei nomi di BLOB, è possibile creare una struttura di directory virtuale che è possibile organizzare e attraversare come un file system tradizionale. La struttura di directory è solo virtuale, le uniche risorse disponibili nell'archivio BLOB sono i contenitori e i BLOB. Tuttavia, la libreria client di archiviazione offre un oggetto **CloudBlobDirectory** per fare riferimento a una directory virtuale e semplificare il processo di utilizzo dei BLOB organizzati in questo modo.

Si consideri ad esempio il seguente set di BLOB in blocchi in un contenitore denominato *photos*:

```
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

Quando si chiama **ListBlobs** nel contenitore *photos*, come nel frammento di codice precedente, viene restituito un elenco gerarchico. Contiene gli oggetti **CloudBlobDirectory** e **CloudBlockBlob**, che rappresentano, rispettivamente, le directory e i BLOB nel contenitore. L'output restituito è simile al seguente:

```
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Facoltativamente, è possibile impostare il parametro **UseFlatBlobListing** del metodo **ListBlobs**su **true**. In questo caso, tutti i BLOB del contenitore vengono restituiti come oggetto **CloudBlockBlob** . La chiamata a **ListBlobs** per restituire un elenco semplice è simile alla seguente:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

e i risultati sono simili ai seguenti:

```
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

## <a name="download-blobs"></a>Scaricare BLOB
Per scaricare BLOB, recuperare prima un riferimento al BLOB e quindi chiamare il metodo **DownloadToStream** . Nell'esempio seguente il metodo **DownloadToStream** viene utilizzato per trasferire il contenuto del BLOB a un oggetto stream che è quindi possibile rendere persistente in un file locale.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

È anche possibile utilizzare il metodo **DownloadToStream** per scaricare il contenuto di un BLOB come stringa di testo.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Eliminare BLOB
Per eliminare un BLOB, ottenere prima un riferimento al BLOB su cui chiamare il metodo **Delete** .

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```

## <a name="list-blobs-in-pages-asynchronously"></a>Elencare BLOB nelle pagine in modalità asincrona
Se si sta elencando un numero elevato di BLOB oppure si vuole controllare il numero di risultati restituiti in un'operazione di elenco, è possibile elencare i BLOB nelle pagine dei risultati. Questo esempio spiega come restituire i risultati nelle pagine in modalità asincrona, in modo che l'esecuzione non venga bloccata durante l'attesa della restituzione di un set di risultati di grandi dimensioni.

Questo esempio illustra un elenco di BLOB lineare, ma è anche possibile eseguire un elenco gerarchico semplicemente impostando il parametro _useFlatBlobListing_ del metodo **ListBlobsSegmentedAsync** su _false_.

Poiché il metodo di esempio chiama un metodo asincrono, deve essere prefissato con la parola chiave _async_ e restituire un oggetto **Attività**. La parola chiave di attesa specificata per il metodo **ListBlobsSegmentedAsync** sospende l'esecuzione del metodo di esempio fino al completamento dell'attività di elenco.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    //List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="writing-to-an-append-blob"></a>Scrittura in un blob di Accodamento
Un blob di accodamento è ottimizzato per le operazioni di accodamento, ad esempio la registrazione. Come un blob in blocchi, un blob di accodamento è costituito da blocchi, ma quando si aggiunge un nuovo blocco in un blob di accodamento, viene aggiunto sempre alla fine del blob. È possibile aggiornare o eliminare un blocco esistente in un blob di Accodamento. L'ID di blocco per un blob di accodamento non vengono esposte come in un blob in blocchi.

Ogni blocco di un blob di accodamento può avere dimensioni diverse, con un massimo di 4 MB e un blob di accodamento può includere un massimo di 50.000 blocchi. La dimensione massima di un blob di accodamento è pertanto leggermente superiore a 195 GB (4 MB X 50.000 blocchi).

Nell'esempio seguente si crea un nuovo blob di accodamento e vi si aggiungono alcuni dati, per simulare un'operazione di registrazione semplice.

```csharp
//Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

//Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

//Create the container if it does not already exist.
container.CreateIfNotExists();

//Get a reference to an append blob.
CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

//Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
//You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
appendBlob.CreateOrReplace();

int numBlocks = 10;

//Generate an array of random bytes.
Random rnd = new Random();
byte[] bytes = new byte[numBlocks];
rnd.NextBytes(bytes);

//Simulate a logging operation by writing text data and byte data to the end of the append blob.
for (int i = 0; i < numBlocks; i++)
{
    appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
        DateTime.UtcNow, bytes[i], Environment.NewLine));
}

//Read the append blob to the console window.
Console.WriteLine(appendBlob.DownloadText());
```

Vedere [Informazioni sui BLOB in blocchi, BLOB di pagine e BLOB di accodamento](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) per ulteriori informazioni sulle differenze tra i tre tipi di BLOB.

## <a name="managing-security-for-blobs"></a>Gestione della sicurezza dei BLOB
Per impostazione predefinita, Archiviazione di Azure garantisce la sicurezza dei dati limitando l'accesso al proprietario dell'account, che possiede le chiavi di accesso dell'account. Quando è necessario condividere dati BLOB nell'account di archiviazione, è importante farlo senza compromettere la sicurezza delle chiavi di accesso dell'account. È anche possibile crittografare i dati BLOB per assicurarne la sicurezza durante il trasferimento in rete e in Archiviazione di Azure.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Controllo dell'accesso ai dati BLOB
Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere ai dati BLOB in tale account. L'autenticazione delle richieste per l'archiviazione BLOB richiede la chiave di accesso dell'account per impostazione predefinita. È tuttavia possibile rendere disponibile ad altri utenti determinati dati BLOB. Sono disponibili due opzioni:

* **Accesso anonimo** : è possibile rendere disponibili pubblicamente per l'accesso anonimo un contenitore o i BLOB in esso inclusi. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md) .
* **Firme di accesso condiviso** : è possibile fornire ai client una firma di accesso condiviso che consente l'accesso delegato a una risorsa nell'account di archiviazione, con le autorizzazioni specificate e per un intervallo specificato. Vedere [Using Shared Access Signatures (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Uso di firme di accesso condiviso) per altre informazioni.

### <a name="encrypting-blob-data"></a>Crittografia dei dati BLOB
Archiviazione di Azure supporta la crittografia dei dati BLOB nel client e nel server:

* **Crittografia lato client** : la libreria client di Archiviazione per .NET supporta la crittografia dei dati nelle applicazioni client prima del caricamento in Archiviazione di Azure, nonché la decrittografia dei dati durante il download nel client. La libreria supporta anche l'integrazione con l'insieme di credenziali delle chiavi di Azure per la gestione delle chiavi dell'account di archiviazione. Per altre informazioni, vedere [Crittografia lato client con .NET per Archiviazione di Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) . Vedere anche [Esercitazione: Crittografare e decrittografare i BLOB in Archiviazione di Microsoft Azure tramite l'insieme di credenziali delle chiavi di Azure](storage-encrypt-decrypt-blobs-key-vault.md).
* **Crittografia lato server**: Archiviazione di Azure supporta ora la crittografia lato server. Vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi (anteprima)](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo avere appreso le nozioni di base dell'archivio BLOB, visitare i collegamenti seguenti per altre informazioni.

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.

### <a name="blob-storage-samples"></a>Esempi di archiviazione BLOB
* [Getting Started with Azure Blob Storage in .NET (Introduzione all'archiviazione BLOB di Azure in .NET)](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Informazioni di riferimento sull'archiviazione BLOB
* [Informazioni di riferimento sulla libreria client di archiviazione per .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx)
* [Informazioni di riferimento sulle API REST](/rest/api/storageservices/azure-storage-services-rest-api-reference)

### <a name="conceptual-guides"></a>Guide concettuali
* [Trasferire dati con l'utilità della riga di comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Come usare l'archiviazione file di Azure](../files/storage-dotnet-how-to-use-files.md)
* [Come usare il servizio di archiviazione BLOB di Azure con WebJobs SDK](../../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

