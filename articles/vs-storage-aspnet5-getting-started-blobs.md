<properties title="Getting Started with Azure Storage" pageTitle="Introduzione all'Archiviazione di Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Per iniziare](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Risultati](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Introduzione all'Archiviazione di Azure (progetti ASP.NET 5)

> [AZURE.SELECTOR]
> - [BLOB](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Code](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [Tabelle](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Un singolo BLOB può avere qualsiasi dimensione. I BLOB possono essere costituiti da immagini, file audio e video, dati non elaborati e file di documento.

Per iniziare, è necessario creare un account di archiviazione di Azure e quindi creare uno o più contenitori nell'archiviazione. Ad esempio, è possibile creare una risorsa di archiviazione denominata "Scrapbook", quindi creare contenitori nella risorsa di archiviazione denominati "images" per archiviare immagini e un altro contenitore denominato "audio" per archiviare file audio. Dopo la creazione dei contenitori, sarà possibile caricarvi singoli file BLOB. Per altre informazioni sulla modifica dei BLOB a livello di codice, vedere [Come usare l'archiviazione BLOB da .NET](http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET").

Per accedere ai BLOB a livello di codice in progetti ASP.NET 5, è necessario aggiungere gli elementi seguenti, se non sono già presenti.

1. Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di codice:

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;

2. Usare il codice seguente per ottenere l'impostazione di configurazione.

		 Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Ottenere la stringa di connessione di archiviazione
Prima di eseguire operazioni relative a un BLOB, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui risiederà il BLOB. È possibile usare il tipo **CloudStorageAccount** per rappresentare le informazioni sull'account di archiviazione. Se si usa un progetto ASP.NET 5, sarà possibile chiamare il metodo Get dell'oggetto Configuration per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Creare un contenitore
I BLOB di archiviazione si trovano nei contenitori esattamente come i file si trovano nelle cartelle. È possibile usare un oggetto **CloudBlobClient** per fare riferimento a un contenitore esistente, oppure è possibile chiamare il metodo CreateCloudBlobClient() per crearne uno nuovo.

Il codice seguente illustra come creare un nuovo contenitore di archiviazione BLOB. Il codice crea innanzitutto un oggetto **BlobClient** per consentire l'accesso alle relative funzioni, ad esempio quella per la creazione di un contenitore di archiviazione. Il codice tenta quindi di fare riferimento a un contenitore di archiviazione denominato "mycontainer". Se non è in grado di trovare un contenitore con quel nome, ne crea uno.

**NOTA:** le API che effettuano chiamate all'Archiviazione di Azure in ASP.NET 5 sono asincrone. Per altre informazioni, vedere [Programmazione asincrona con Async e Await](http://msdn.microsoft.com/library/hh191443.aspx). Nel codice seguente si presuppone l'uso di metodi di programmazione asincrona.

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();    

Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione per scaricare BLOB da questo contenitore. Se si desidera che i file all'interno del contenitore siano disponibili a tutti, è possibile impostare il contenitore come pubblico usando il codice riportato di seguito:

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**NOTA:** Usare tutto questo codice prima del codice nelle sezioni seguenti.

#####Caricare un BLOB in un contenitore
Per caricare un file BLOB in un contenitore, ottenere un riferimento a un contenitore e usarlo per ottenere un riferimento a un BLOB. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStreamAsync()**. Questa operazione permetterà di creare il BLOB, se non esiste già, oppure di sovrascriverlo se esiste già. Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore e si presuppone che il contenitore sia già stato creato.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named "myfile".
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

#####Elencare i BLOB in un contenitore
Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore. Sarà quindi possibile chiamare il metodo **ListBlobsSegmentedAsync()** del contenitore per recuperare i BLOB e/o le directory disponibili. Per accedere all'insieme avanzato di proprietà e metodi per un oggetto **IListBlobItem** restituito, è necessario eseguirne il cast in un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Se il tipo del BLOB non è noto, è possibile usare un controllo del tipo per determinare quello in cui viene eseguito il cast. Nel codice seguente viene illustrato come recuperare e visualizzare l'URI di ogni elemento in un contenitore.

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

È possibile elencare i contenuti di un contenitore BLOB in altri modi. Per altre informazioni, vedere [Come usare l'archiviazione BLOB da .NET](http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob).

#####Scaricare un BLOB
Per scaricare un BLOB, ottenere prima di tutto un riferimento al BLOB, quindi chiamare il metodo **DownloadToStreamAsync()**. L'esempio seguente usa il metodo **DownloadToStreamAsync()** per trasferire i contenuti del BLOB a un oggetto stream che è quindi possibile salvare come file locale.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named "myfile".
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

È possibile salvare i BLOB come file in altri modi. Per altre informazioni, vedere [Come usare l'archiviazione BLOB da .NET](http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs).

#####Eliminare un BLOB
Per eliminare un BLOB, ottenere prima di tutto un riferimento al BLOB, quindi chiamare il metodo **DeleteAsync()**.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[Altre informazioni sull'Archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/)
Vedere anche [Esplorazione delle risorse di archiviazione con Esplora server](http://msdn.microsoft.com/en-us/library/azure/ff683677.aspx) e [ASP.NET 5](http://www.asp.net/vnext).
