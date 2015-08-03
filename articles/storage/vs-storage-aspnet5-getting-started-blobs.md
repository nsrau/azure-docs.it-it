<properties 
	pageTitle="Introduzione all'Archiviazione di Azure" 
	description="Informazioni su come iniziare a usare l'archiviazione BLOB di Azure in un progetto ASP.NET 5 in Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Introduzione all'Archiviazione di Azure (progetti ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Un singolo BLOB può avere qualsiasi dimensione. I BLOB possono essere costituiti da immagini, file audio e video, dati non elaborati e file di documento. In questo articolo viene descritto come iniziare a utilizzare l'archiviazione BLOB dopo la creazione di una risorsa di archiviazione Azure usando la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio.

I BLOB di archiviazione si trovano nei contenitori esattamente come i file si trovano nelle cartelle. Dopo aver creato una risorsa di archiviazione, è possibile creare uno o più contenitori nello spazio di archiviazione. Ad esempio, in una risorsa di archiviazione denominata "Raccoglitore", è possibile creare contenitori denominati "immagini" per archiviare immagini e un altro contenitore denominato "audio" per archiviare file audio. Dopo la creazione dei contenitori, sarà possibile caricarvi singoli file BLOB. Per altre informazioni sulla modifica dei BLOB a livello di codice, vedere [Come usare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md "Come usare l'archiviazione BLOB da .NET").

##Creazione di contenitori BLOB in Esplora server di Visual Studio

[AZURE.INCLUDE [vs-create-blob-container-in-server-explorer](../../includes/vs-create-blob-container-in-server-explorer.md)]



##Accesso ai contenitori BLOB nel codice 

Per accedere ai BLOB a livello di codice in progetti ASP.NET 5, è necessario aggiungere gli elementi seguenti, se non sono già presenti.

1. Aggiungere le dichiarazioni dello spazio dei nomi del codice seguenti all'inizio del file C# in cui si desidera accedere ad Archiviazione di Azure a livello di codice:

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

    **NOTA:** utilizzare tutto il codice riportato in precedenza prima del codice indicato nelle sezioni seguenti.


3. Utilizzare un oggetto **CloudBlobClient** per ottenere un riferimento **CloudBlobContainer** a un contenitore esistente nell'account di archiviazione.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##Creazione di un contenitore nel codice

È inoltre possibile utilizzare **CloudBlobClient** per creare un contenitore nell'account di archiviazione. È sufficiente aggiungere un richiamo a `CreateIfNotExistsAsync()` come nel codice seguente:

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();    


**Nota:** le API che eseguono chiamate ad Archiviazione di Azure in ASP.NET 5 sono asincrone. Per ulteriori informazioni, vedere [Programmazione asincrona con Async e Await](http://msdn.microsoft.com/library/hh191443.aspx). Nel codice riportato di seguito si presuppone vengano utilizzati i metodi di programmazione asincrona.

Per rendere i file all'interno del contenitore disponibili a tutti, è possibile impostare il contenitore come pubblico utilizzando il codice seguente.

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##Caricare un BLOB in un contenitore

Per caricare un file BLOB in un contenitore, ottenere un riferimento a un contenitore e usarlo per ottenere un riferimento a un BLOB. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStreamAsync()**. Questa operazione permetterà di creare il BLOB, se non esiste già, oppure di sovrascriverlo se esiste già. Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore e si presuppone che il contenitore sia già stato creato.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##Elencare i BLOB in un contenitore
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

È possibile elencare i contenuti di un contenitore BLOB in altri modi. Per altre informazioni, vedere [Come utilizzare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container).

##Scaricare un BLOB
Per scaricare un BLOB, ottenere prima di tutto un riferimento al BLOB, quindi chiamare il metodo **DownloadToStreamAsync()**. Nell'esempio seguente viene utilizzato il metodo **DownloadToStreamAsync()** per trasferire i contenuti del BLOB a un oggetto stream che è quindi possibile salvare come file locale.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

È possibile salvare i BLOB come file in altri modi. Per altre informazioni, vedere [Come utilizzare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md/#download-blobs).

##Eliminare un BLOB
Per eliminare un BLOB, ottenere prima di tutto un riferimento al BLOB, quindi chiamare il metodo **DeleteAsync()**.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

## Passaggi successivi

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]



 
[Image1]: ./media/vs-storage-aspnet5-getting-started-blobs/vs-storage-create-blob-containers-in-Server-Explorer.png

<!---HONumber=July15_HO4-->