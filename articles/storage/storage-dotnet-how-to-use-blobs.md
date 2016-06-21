<properties
	pageTitle="Introduzione all'archivio BLOB di Azure (archivio di oggetti) con .NET | Microsoft Azure"
	description="Archiviare i dati non strutturati nel cloud con l'archivio BLOB (archivio di oggetti) di Azure."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="06/09/2016"
	ms.author="tamram"/>


# Introduzione all'archivio BLOB di Azure con .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Panoramica

L'archiviazione BLOB di Azure è un servizio che archivia dati non strutturati nel cloud come oggetti/BLOB. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.

### Informazioni sull'esercitazione

Questa esercitazione illustra come scrivere codice .NET per alcuni scenari comuni dell'archivio BLOB di Azure. Gli scenari presentati includono caricamento, visualizzazione dell'elenco, download ed eliminazione di BLOB.

**Tempo previsto per il completamento:** 45 minuti

**Prerequisiti:**

- [Microsoft Visual Studio](https://www.visualstudio.com/it-IT/visual-studio-homepage-vs.aspx)
- [Libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestione configurazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [account di archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### Aggiungere le dichiarazioni dello spazio dei nomi

Aggiungere le istruzioni `using` seguenti all'inizio del file `program.cs`.

	using Microsoft.Azure; // Namespace for CloudConfigurationManager
	using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### Analizzare la stringa di connessione

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### Creare il client del servizio BLOB

La classe **CloudBlobClient** consente di recuperare contenitori e BLOB archiviati nell'archivio BLOB. Ecco come creare il client del servizio:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

A questo punto si è pronti a scrivere codice che legge e scrive i dati nell'archivio BLOB.

## Creare un contenitore

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

In questo esempio viene creato un contenitore, nel caso in cui non esista già:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Per impostazione predefinita, il nuovo contenitore è privato, ovvero è necessario specificare la chiave di accesso alle risorse di archiviazione per scaricare BLOB da questo contenitore. Se si desidera che i file all'interno del contenitore siano disponibili a tutti, è possibile impostare il contenitore come pubblico usando il codice seguente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess =
	    BlobContainerPublicAccessType.Blob });

I BLOB in un contenitore pubblico sono visibili a tutti gli utenti di Internet, tuttavia è possibile modificarli o eliminarli solo se è disponibile la chiave di accesso dell'account appropriata o una firma di accesso condiviso.

## Caricare un BLOB in un contenitore

Nell'archivio BLOB di Azure sono supportati BLOB in blocchi e BLOB di pagine. Nella maggior parte dei casi è consigliabile utilizzare il tipo di BLOB in blocchi.

Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB in blocchi. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStream**. Questa operazione consentirà di creare il BLOB se non già esistente o di sovrascriverlo se già esistente.

Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore, presupponendo che il contenitore sia già stato creato.

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

## Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore. Sarà quindi possibile utilizzare il metodo **ListBlobs** del contenitore per recuperare i BLOB e/o le directory in esso contenuti. Per accedere al set avanzato di proprietà e metodi per un oggetto **IListBlobItem** restituito, è necessario eseguirne il cast in un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Se il tipo è sconosciuto, è possibile usare un controllo del tipo per determinare quello in cui viene eseguito il cast. Il codice seguente illustra come recuperare e visualizzare l'URI di ogni elemento del contenitore `photos`:

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

Come illustrato in precedenza, è possibile assegnare il nome ai BLOB con le informazioni sul percorso nel nome. Consente di creare una struttura di directory virtuale che è possibile organizzare e attraversare come un file system tradizionale. Si noti che la struttura di directory è solo virtuale, le uniche risorse disponibili nell'archivio BLOB sono i contenitori e i BLOB. Tuttavia, la libreria client di archiviazione offre un oggetto **CloudBlobDirectory** per fare riferimento a una directory virtuale e semplificare il processo di utilizzo dei BLOB organizzati in questo modo.

Si consideri, ad esempio, il seguente set di BLOB in blocchi di un contenitore denominato `photos`:

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Quando si chiama **ListBlobs** sul contenitore 'photos', come nell'esempio precedente, viene restituito un elenco gerarchico. Contiene gli oggetti **CloudBlobDirectory** e **CloudBlockBlob** che rappresentano, rispettivamente, le directory e i BLOB nel contenitore. L'output restituito è simile al seguente:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Facoltativamente, è possibile impostare su **true** il parametro **UseFlatBlobListing** del metodo **ListBlobs**. In questo caso, tutti i BLOB del contenitore vengono restituiti come oggetto **CloudBlockBlob**. La chiamata a **ListBlobs** per restituire un elenco semplice è simile alla seguente:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

e i risultati sono simili ai seguenti:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## Scaricare BLOB

Per scaricare BLOB, recuperare prima un riferimento al BLOB e quindi chiamare il metodo **DownloadToStream**. Nell'esempio seguente il metodo **DownloadToStream** viene utilizzato per trasferire il contenuto del BLOB a un oggetto stream che è quindi possibile rendere persistente in un file locale.

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

È anche possibile utilizzare il metodo **DownloadToStream** per scaricare il contenuto di un BLOB come stringa di testo.

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

## Eliminare BLOB

Per eliminare un BLOB, ottenere prima un riferimento al BLOB su cui chiamare il metodo **Delete**.

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


## Elencare BLOB nelle pagine in modalità asincrona

Se si sta elencando un numero elevato di BLOB oppure si vuole controllare il numero di risultati restituiti in un'operazione di elenco, è possibile elencare i BLOB nelle pagine dei risultati. Questo esempio spiega come restituire i risultati nelle pagine in modalità asincrona, in modo che l'esecuzione non venga bloccata durante l'attesa della restituzione di un set di risultati di grandi dimensioni.

Questo esempio illustra un elenco di BLOB lineare, ma è anche possibile eseguire un elenco gerarchico impostando il parametro `useFlatBlobListing` del metodo **ListBlobsSegmentedAsync** su `false`.

Poiché il metodo di esempio chiama un metodo asincrono, deve avere come prefisso la parola chiave `async` e restituire un oggetto **Task**. La parola chiave di attesa specificata per il metodo **ListBlobsSegmentedAsync** sospende l'esecuzione del metodo di esempio fino al completamento dell'attività di elenco.

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

## Scrittura in un blob di Accodamento

Un blob di accodamento è un nuovo tipo di blob, introdotto con la versione 5. x della libreria client di archiviazione di Azure per .NET. Un blob di accodamento è ottimizzato per le operazioni di accodamento, ad esempio la registrazione. Come un blob in blocchi, un blob di accodamento è costituito da blocchi, ma quando si aggiunge un nuovo blocco in un blob di accodamento, viene aggiunto sempre alla fine del blob. È possibile aggiornare o eliminare un blocco esistente in un blob di Accodamento. L'ID di blocco per un blob di accodamento non vengono esposte come in un blob in blocchi.

Ogni blocco di un blob di accodamento può avere dimensioni diverse, con un massimo di 4 MB e un blob di accodamento può includere un massimo di 50.000 blocchi. La dimensione massima di un blob di accodamento è pertanto leggermente superiore a 195 GB (4 MB X 50.000 blocchi).

Nell'esempio seguente si crea un nuovo blob di accodamento e vi si aggiungono alcuni dati, per simulare un'operazione di registrazione semplice.

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

Vedere [Informazioni sui BLOB in blocchi, BLOB di pagine e BLOB di accodamento](https://msdn.microsoft.com/library/azure/ee691964.aspx)per ulteriori informazioni sulle differenze tra i tre tipi di BLOB.

## Gestione della sicurezza dei BLOB

Per impostazione predefinita, Archiviazione di Azure garantisce la sicurezza dei dati limitando l'accesso al proprietario dell'account, che possiede le chiavi di accesso dell'account. Quando è necessario condividere dati BLOB nell'account di archiviazione, è importante farlo senza compromettere la sicurezza delle chiavi di accesso dell'account. È anche possibile crittografare i dati BLOB per assicurarne la sicurezza durante il trasferimento in rete e in Archiviazione di Azure.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### Controllo dell'accesso ai dati BLOB

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere ai dati BLOB in tale account. L'autenticazione delle richieste per l'archiviazione BLOB richiede la chiave di accesso dell'account per impostazione predefinita. È tuttavia possibile rendere disponibile ad altri utenti determinati dati BLOB. Sono disponibili due opzioni:

- **Accesso anonimo**: è possibile rendere disponibili pubblicamente per l'accesso anonimo un contenitore o i BLOB in esso inclusi. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md).
- **Firme di accesso condiviso**: è possibile fornire ai client una firma di accesso condiviso che consente l'accesso delegato a una risorsa nell'account di archiviazione, con le autorizzazioni specificate e per un intervallo specificato. Per altre informazioni, vedere [Firme di accesso condiviso: informazioni sul modello di firma di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).

### Crittografia dei dati BLOB

Archiviazione di Azure supporta la crittografia dei dati BLOB nel client e nel server:

- **Crittografia lato client**: la libreria client di archiviazione per .NET supporta la crittografia dei dati nelle applicazioni client prima del caricamento in Archiviazione di Azure, nonché la decrittografia dei dati durante il download nel client. La libreria supporta anche l'integrazione con l'insieme di credenziali delle chiavi di Azure per la gestione delle chiavi dell'account di archiviazione. Per altre informazioni, vedere [Crittografia lato client con .NET per Archiviazione di Microsoft Azure](storage-client-side-encryption.md). Vedere anche [Esercitazione: Crittografare e decrittografare i BLOB in Archiviazione di Microsoft Azure tramite l'insieme di credenziali delle chiavi di Azure](storage-encrypt-decrypt-blobs-key-vault.md).
- **Crittografia lato server**: Archiviazione di Azure supporta ora la crittografia lato server. Vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi (anteprima)](storage-service-encryption.md). 

## Passaggi successivi

A questo punto, dopo avere appreso le nozioni di base dell'archivio BLOB, visitare i collegamenti seguenti per altre informazioni.

### Informazioni di riferimento sull'archiviazione BLOB

- [Informazioni di riferimento sulla libreria client di archiviazione per .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Informazioni di riferimento sulle API REST](http://msdn.microsoft.com/library/azure/dd179355)

### Guide concettuali

- [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
- [Come usare l'archiviazione file di Azure](storage-dotnet-how-to-use-files.md)
- [Come usare il servizio di archiviazione BLOB di Azure con WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355

<!---HONumber=AcomDC_0615_2016-->