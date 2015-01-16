<properties urlDisplayName="Blob Service" pageTitle="Come usare l'archiviazione BLOB da .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Informazioni su come usare il servizio di archiviazione BLOB di Microsoft Azure per caricare, scaricare, elencare ed eliminare contenuti BLOB. Gli esempi sono scritti in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# Come usare l'archiviazione BLOB da .NET

In questa guida verranno illustrati diversi scenari comuni di uso del
servizio di archiviazione BLOB di Azure. Gli esempi sono scritti in C\# e
usano la libreria client di archiviazione di Azure per .NET. Tra gli scenari illustrati sono inclusi
**caricamento**, **inclusione in elenco**, **download** ed **eliminazione** di BLOB. Per
altre informazioni sui BLOB, vedere la sezione [Passaggi successivi][].

> [WACOM.NOTE] Questa guida fa riferimento alla libreria client di archiviazione di Azure per .NET 2.x e versioni successive. La versione consigliata è la libreria client di archiviazione di Azure per .NET 4.x, disponibile tramite [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o come parte di [Azure SDK per .NET](/it-it/downloads/). Vedere [Procedura: Accedere all'archiviazione BLOB a livello di codice][], per informazioni su come ottenere la libreria client di archiviazione.

##Sommario

-   [Informazioni sull'archiviazione BLOB][]
-   [Concetti][]
-   [Creare un account di archiviazione di Azure][]
-   [Configurare una stringa di connessione di archiviazione][]
-   [Procedura: Accedere all'archiviazione BLOB a livello di codice][]
-   [Procedura: Creare un contenitore][]
-   [Procedura: Caricare un BLOB in un contenitore][]
-   [Procedura: Elencare i BLOB in un contenitore][]
-   [Procedura: Scaricare BLOB][]
-   [Procedura: Eliminare BLOB][]
-   [Procedura: Elencare BLOB nelle pagine in modo asincrono][]
-   [Passaggi successivi][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Creare un account di archiviazione di Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a>Configurare una stringa di connessione di archiviazione

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Procedura: Accedere all'archiviazione BLOB a livello di codice

###Recupero dell'assembly
È consigliare usare NuGet per ottenere l'assembly `Microsoft.WindowsAzure.Storage.dll`. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.  Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

Il file `Microsoft.WindowsAzure.Storage.dll` è inoltre incluso in Azure SDK per .NET, che può essere scaricato dal <a href="http://www.windowsazure.com/it-it/develop/net/#">Centro per sviluppatori .NET</a>. L'assembly viene installato nella directory `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<versione-sdk>\ref\`.

###Dichiarazioni dello spazio dei nomi
Aggiungere le seguenti dichiarazioni di spazi dei nomi all'inizio del file C\#
in cui si intende accedere ad Archiviazione di Azure a livello di codice:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Assicurarsi di fare riferimento all'assembly `Microsoft.WindowsAzure.Storage.dll`.

###Recupero della stringa di connessione
È possibile usare il tipo **CloudStorageAccount** per rappresentare 
le informazioni dell'account di archiviazione. Se si usa un 
modello di progetto di Azure e/o si dispone di un riferimento a 
Microsoft.WindowsAzure.CloudConfigurationManager, è 
possibile usare il tipo **CloudConfigurationManager**
per recuperare la stringa di connessione di archiviazione e le informazioni dell'account di archiviazione
dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se si intende creare un'applicazione senza riferimenti a Microsoft.WindowsAzure.CloudConfigurationManager e la stringa di connessione si trova nel file `web.config` o `app.config` come illustrato in precedenza, per recuperarla è possibile usare **ConfigurationManager**.  Sarà necessario aggiungere un riferimento a System.Configuration.dll al progetto e aggiungere un'altra dichiarazione dello spazio dei nomi:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Un tipo **CloudBlobClient** consente di recuperare oggetti che rappresentano
contenitori e BLOB archiviati nel servizio di archiviazione BLOB. Il
codice seguente crea un oggetto **CloudBlobClient** usando
l'oggetto account di archiviazione recuperato in precedenza.

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Dipendenze ODataLib
Le dipendenze ODataLib nella libreria client di archiviazione per .NET vengono risolte con i pacchetti ODataLib (versione 5.0.2) disponibili tramite NuGet e non WCF Data Services.  È possibile scaricare le librerie ODataLib direttamente oppure farvi riferimento nel progetto del codice tramite NuGet.  I pacchetti ODataLib specifici sono [OData], [Edm] e [Spatial].

## <a name="create-container"> </a>Procedura: Creare un contenitore

Ogni BLOB nell'archiviazione di Azure deve risiedere in un contenitore. In questo esempio viene creato un contenitore, nel caso in cui non esista già:

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

## <a name="upload-blob"> </a>Procedura: Caricare un BLOB in un contenitore

In Archiviazione BLOB di Azure sono supportati BLOB in blocchi e BLOB di pagine.  Nella maggior parte dei casi è consigliabile usare il tipo di BLOB in blocchi.

Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB in blocchi. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStream**. Questa operazione consentirà di creare il BLOB se non già esistente o di sovrascriverlo se già esistente. Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore e si presuppone che il contenitore sia già stato creato.

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

##<a name="list-blob"> </a>Procedura: Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore. Sarà quindi possibile usare il metodo **ListBlobs** del contenitore per recuperare i BLOB e/o le directory in esso contenuti. Per accedere all'insieme avanzato di proprietà e metodi per un oggetto **IListBlobItem** restituito, è necessario eseguirne il cast in un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**.  Se il tipo è sconosciuto, è possibile usare un controllo del tipo per determinare quello in cui viene eseguito il cast.  Nel codice seguente viene illustrato come recuperare e visualizzare l'URI di ogni elemento nel 
contenitore `photos`:

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

Come illustrato in precedenza, il servizio BLOB include anche il concetto di directory all'interno di contenitori. È quindi possibile organizzare i BLOB in una struttura più simile a quella delle cartelle. Si consideri, ad esempio, il seguente set di BLOB in blocchi in un contenitore denominato `photos`:

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Quando si chiama **ListBlobs** sul contenitore 'photos' (come nell'esempio precedente), la raccolta restituita conterrà gli oggetti **CloudBlobDirectory** e **CloudBlockBlob**
che rappresentano le directory e i BLOB contenuti al primo livello. L'output risultante è il seguente:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Facoltativamente, è possibile impostare su **true** il parametro **UseFlatBlobListing** del metodo **ListBlobs**. In questo modo tutti i BLOB verranno restituiti come **CloudBlockBlob**, indipendentemente dalla directory.  La chiamata a **ListBlobs** è la seguente:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

e questi sono i risultati:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Per altre informazioni, vedere [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a>Procedura: Scaricare BLOB

Per scaricare BLOB, recuperare prima un riferimento al BLOB e quindi chiamare il metodo **DownloadToStream**. L'esempio
seguente usa il metodo **DownloadToStream** per trasferire i contenuti del BLOB
a un oggetto stream che è quindi possibile salvare come file locale.

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

È anche possibile usare il metodo **DownloadToStream** per scaricare il contenuto di un BLOB come stringa di testo.

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

##<a name="delete-blobs"> </a>Procedura: Eliminare BLOB

Per eliminare un BLOB, ottenere prima un riferimento al BLOB su cui chiamare il
metodo **Delete**.

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


##<a name="list-blobs-async"> </a>Procedura: Elencare BLOB nelle pagine in modo asincrono

Se si sta elencando un numero elevato di BLOB oppure si vuole controllare il numero di risultati restituiti in un'operazione di elenco, è possibile elencare i BLOB nelle pagine dei risultati. Questo esempio spiega come restituire i risultati nelle pagine in modo asincrono, in modo che l'esecuzione non venga bloccata durante l'attesa della restituzione di un set di risultati di grandi dimensioni.

Questo esempio illustra un elenco di BLOB lineare, ma è anche possibile eseguire un elenco gerarchico semplicemente impostando il parametro `useFlatBlobListing` del metodo **ListBlobsSegmentedAsync** su `false`.

Poiché il metodo di esempio chiama un metodo asincrono, deve essere prefissato con la parola chiave `async` e restituire un oggetto **Task**. La parola chiave di attesa specificata per il metodo **ListBlobsSegmentedAsync** sospende l'esecuzione del metodo di esempio fino al completamento dell'attività di elenco.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.
<ul>
<li>Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio BLOB:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Informazioni di riferimento sulla libreria client di archiviazione per .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/it-it/library/windowsazure/dd179355">Informazioni di riferimento sulle API REST</a></li>
  </ul>
</li>
<li>Per altre informazioni sulle attività avanzate che è possibile eseguire con <a href="http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx">Archiviazione di Azure, vedere la pagina relativa all'archiviazione e all'accesso di dati in Azure</a>.</li>
<li>Per altre informazioni su come usare Archiviazione di Azure nei processi back-end per Siti Web di Azure, vedere <a href="/it-it/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Introduzione all'uso dell'SDK di Processi Web di Azure</a>.</li>
<li>Per altre opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
  <ul>
    <li>Per archiviare dati strutturati, usare <a href="/it-it/documentation/articles/storage-dotnet-how-to-use-tables/">Archiviazione tabelle</a>.</li>
    <li>Per archiviare dati non strutturati, usare <a href="/it-it/documentation/articles/storage-dotnet-how-to-use-queues/">Archiviazione di accodamento</a>.</li>
    <li>Per archiviare dati relazionali, usare <a href="/it-it/documentation/articles/sql-database-dotnet-how-to-use/">Database SQL</a>.</li>
  </ul>
</li>
</ul>

  [Passaggi successivi]: #next-steps
  [Informazioni sull'archiviazione BLOB]: #what-is
  [Concetti]: #concepts
  [Creare un account di archiviazione di Azure]: #create-account
  [Configurare una stringa di connessione di archiviazione]: #setup-connection-string
  [Procedura: Accedere all'archiviazione BLOB a livello di codice]: #configure-access
  [Procedura: Creare un contenitore]: #create-container
  [Procedura: Caricare un BLOB in un contenitore]: #upload-blob
  [Procedura: Elencare i BLOB in un contenitore]: #list-blob
  [Procedura: Scaricare BLOB]: #download-blobs
  [Procedura: Eliminare BLOB]: #delete-blobs
  [Procedura: Elencare BLOB nelle pagine in modo asincrono]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurazione delle stringhe di connessione]: http://msdn.microsoft.com/it-it/library/windowsazure/ee758697.aspx
  [Informazioni di riferimento sulla libreria client .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Informazioni di riferimento sulle API REST]: http://msdn.microsoft.com/it-it/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35.1-->
