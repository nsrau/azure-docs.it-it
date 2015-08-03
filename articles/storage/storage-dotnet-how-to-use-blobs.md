<properties
	pageTitle="Come usare l'archiviazione BLOB da .NET | Microsoft Azure"
	description="Informazioni su archiviazione BLOB di Azure e come creare un contenitore e per caricare, scaricare, elencare ed eliminare contenuto di BLOB."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="07/06/2015"
	ms.author="tamram"/>


# Come usare l'archiviazione BLOB da .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Panoramica

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione BLOB di Azure. Negli esempi, scritti in C#, viene utilizzata la libreria del client di archiviazione di Azure per .NET. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB.

> [AZURE.NOTE]Questa guida fa riferimento alla libreria client di archiviazione di Azure per .NET 2.x e versioni successive. La versione consigliata è la libreria client di archiviazione di Azure per .NET 4.x, disponibile tramite [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o come parte di [Azure SDK per .NET](/downloads/). Per ulteriori informazioni su come ottenere la libreria client di archiviazione, vedere [Accedere all'archiviazione BLOB a livello di programmazione](#programmatically-access-blob-storage).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Accedere all'archiviazione BLOB a livello di programmazione

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Dichiarazioni dello spazio dei nomi
Aggiungere le seguenti dichiarazioni dello spazio dei nomi all'inizio del file C# in cui si desidera accedere ad Archiviazione di Azure a livello di programmazione:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Assicurarsi di fare riferimento all'assembly `Microsoft.WindowsAzure.Storage.dll`.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

Il tipo **CloudBlobClient** consente di recuperare oggetti che rappresentano contenitori e BLOB archiviati nel servizio di archiviazione BLOB. Il codice seguente consente di creare un oggetto **CloudBlobClient** utilizzando l'oggetto account di archiviazione recuperato in precedenza:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

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

Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione per scaricare BLOB da questo contenitore. Se si desidera che i file all'interno del contenitore siano disponibili a tutti, è possibile impostare il contenitore come pubblico usando il codice seguente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess =
 	    BlobContainerPublicAccessType.Blob });

I BLOB in un contenitore pubblico sono visibili a tutti gli utenti di Internet, tuttavia è possibile modificarli o eliminarli solo se si dispone della chiave di accesso appropriata.

## Caricare un BLOB in un contenitore

In Archiviazione BLOB di Azure sono supportati BLOB in blocchi e BLOB di pagine. Nella maggior parte dei casi è consigliabile utilizzare il tipo di BLOB in blocchi.

Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB in blocchi. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStream**. Questa operazione consentirà di creare il BLOB se non già esistente o di sovrascriverlo se già esistente. Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore, presupponendo che il contenitore sia già stato creato.

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

Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore. Sarà quindi possibile utilizzare il metodo **ListBlobs** del contenitore per recuperare i BLOB e/o le directory in esso contenuti. Per accedere al set avanzato di proprietà e metodi per un oggetto **IListBlobItem** restituito, è necessario eseguirne il cast in un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Se il tipo è sconosciuto, è possibile usare un controllo del tipo per determinare quello in cui viene eseguito il cast. Nel codice seguente viene illustrato come recuperare e visualizzare l'URI di ogni elemento del contenitore `photos`:

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

Come illustrato in precedenza, è possibile assegnare il nome ai BLOB con le informazioni sul percorso nel nome. Consente di creare una struttura di directory virtuale che è possibile organizzare e attraversare come un file system tradizionale. Si noti che la struttura di directory è solo virtuale, le uniche risorse disponibili nell'archiviazione BLOB sono i contenitori e i BLOB. Tuttavia, la libreria client di archiviazione offre un oggetto **CloudBlobDirectory** per fare riferimento a una directory virtuale e semplificare il processo di utilizzo dei BLOB organizzati in questo modo.

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

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse. <ul> <li>Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio BLOB: <ul> <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Riferimento per la libreria client di archiviazione per .NET</a> </li> <li><a href="http://msdn.microsoft.com/library/azure/dd179355">Riferimento per l'API REST</a></li> </ul> </li> <li>Informazioni su ulteriori attività avanzate che si possono eseguire con Archiviazione di Azure nella pagina relativa all’<a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">archiviazione e all’accesso ai dati in Azure</a>.</li> <li>Per ulteriori informazioni su come semplificare il codice scritto da usare con Archiviazione di Azure, vedere <a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK.</li> <li>Per informazioni su altre opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità. <ul> <li>Per archiviare dati strutturati, usare <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">Archiviazione tabella</a>.</li> <li>Per archiviare dati non strutturati, usare <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">Archiviazione coda</a>.</li> <li>Per archiviare dati relazionali, usare <a href="/documentation/articles/sql-database-dotnet-how-to-use/">Database SQL</a>.</li> </ul> </li> </ul>

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
 

<!---HONumber=July15_HO4-->