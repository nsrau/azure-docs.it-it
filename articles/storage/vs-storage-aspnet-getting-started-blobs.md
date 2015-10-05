<properties
	pageTitle="Introduzione all'archiviazione BLOB di Azure e ai relativi servizi di Visual Studio (ASP.NET) | Microsoft Azure"
	description="Informazioni su come iniziare a usare il servizio di archiviazione BLOB in un progetto ASP.NET in Visual Studio dopo aver eseguito la connessione a un account di archiviazione con i servizi connessi di Visual Studio."
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
	ms.date="09/03/2015"
	ms.author="patshea"/>

# Introduzione all'archiviazione BLOB di Azure e ai relativi servizi di Visual Studio (ASP.NET)

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-aspnet-getting-started-blobs.md)
> - [What happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

## Panoramica

In questo articolo viene descritto come iniziare a usare l'archivio BLOB di Azure dopo aver creato o fatto riferimento a un account di archiviazione di Azure in un'app ASP.NET usando la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio. L'articolo illustra come creare i contenitori BLOB ed eseguire altre attività comuni come caricare, elencare, scaricare ed eliminare BLOB. Negli esempi, scritti in C#, viene usata la [libreria del client di archiviazione di Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

 - Per altre informazioni generali sull'uso dell'archivio BLOB di Azure, vedere [Come usare l'archivio BLOB da .NET](storage-dotnet-how-to-use-blobs.md).
 - Per altre informazioni sui progetti ASP.NET, vedere [ASP.NET](http://www.asp.net).


L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Un singolo BLOB può avere qualsiasi dimensione. I BLOB possono essere costituiti da immagini, file audio e video, dati non elaborati e file di documento.

I BLOB di archiviazione si trovano nei contenitori esattamente come i file si trovano nelle cartelle. Dopo aver creato un account di archiviazione, è possibile creare uno o più contenitori nello spazio di archiviazione. Ad esempio, in una risorsa di archiviazione denominata "Raccoglitore", è possibile creare contenitori BLOB denominati "immagini" per archiviare immagini e un altro contenitore denominato "audio" per archiviare file audio. Dopo la creazione dei contenitori, sarà possibile caricarvi singoli file BLOB.




## Accesso ai contenitori BLOB nel codice

Per accedere ai BLOB a livello di codice nei progetti ASP.NET, è necessario aggiungere gli elementi seguenti, se non sono già presenti.

1. Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di codice:

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;


2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    > [AZURE.NOTE]Usare tutto questo codice prima del codice nelle sezioni seguenti.

3. Ottenere un oggetto **CloudBlobClient** per fare riferimento a un contenitore esistente nell'account di archiviazione.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE]Alcune API che eseguono chiamate ad Archiviazione di Azure in ASP.NET 5 sono asincrone. Per altre informazioni, vedere [Programmazione asincrona con Async e Await](http://msdn.microsoft.com/library/hh191443.aspx).


## Creazione di un contenitore BLOB in codice

È inoltre possibile utilizzare l’oggetto **CloudBlobClient** per creare un contenitore nell'account di archiviazione. È necessario aggiungere una chiamata a **CreateIfNotExistsAsync** al codice precedente come illustrato nell'esempio seguente.

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();

## Caricare un BLOB in un contenitore

In Archivio BLOB di Azure sono supportati BLOB in blocchi e BLOB di pagine. Nella maggior parte dei casi è consigliabile utilizzare il tipo di BLOB in blocchi.

Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB in blocchi. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStream**. Questa operazione consentirà di creare il BLOB se non già esistente o di sovrascriverlo se già esistente. Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore, presupponendo che il contenitore sia già stato creato.

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code."

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, usare il metodo **ListBlobs** per recuperare i BLOB e/o le directory in esso contenuti. Per accedere all'insieme avanzato di proprietà e metodi per un oggetto **IListBlobItem** restituito, è necessario eseguirne il cast in un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Se il tipo è sconosciuto, è possibile usare un controllo del tipo per determinare quello in cui viene eseguito il cast. Nel codice seguente viene illustrato come recuperare e visualizzare l'URI di ogni elemento nel contenitore **photos**.

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code."

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

Come illustrato nell'esempio precedente, il servizio BLOB include anche il concetto di directory all'interno di contenitori. È quindi possibile organizzare i BLOB in una struttura più simile a quella delle cartelle. Si consideri, ad esempio, il seguente set di BLOB in blocchi in un contenitore denominato **photos**.

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Quando si chiama **ListBlobs** sul contenitore 'photos' (come nell'esempio precedente), la raccolta restituita conterrà gli oggetti **CloudBlobDirectory** e **CloudBlockBlob** che rappresentano le directory e i BLOB contenuti al primo livello. La figura seguente mostra l'output risultante.

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Facoltativamente, è possibile impostare su **true** il parametro **UseFlatBlobListing** del metodo **ListBlobs**. In questo modo tutti i BLOB verranno restituiti come **CloudBlockBlob**, indipendentemente dalla directory. L'esempio seguente illustra la chiamata a **ListBlobs**.

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

L'esempio successivo invece mostra i risultati.

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg



## Scaricare BLOB

Per scaricare i BLOB, utilizzare il metodo **DownloadToStream**. Nell'esempio seguente il metodo **DownloadToStream** viene utilizzato per trasferire il contenuto del BLOB a un oggetto stream che è quindi possibile rendere persistente in un file locale.

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code"

    // Retrieve a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

È anche possibile utilizzare il metodo **DownloadToStream** per scaricare il contenuto di un BLOB come stringa di testo.

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code"

	// Retrieve a reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Eliminare BLOB

Per eliminare un BLOB, usare il metodo **Elimina**.

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code"

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## Elencare BLOB nelle pagine in modalità asincrona

Se si sta elencando un numero elevato di BLOB oppure si vuole controllare il numero di risultati restituiti in un'operazione di elenco, è possibile elencare i BLOB nelle pagine dei risultati. L'esempio seguente illustra come restituire i risultati nelle pagine in modalità asincrona, in modo che l'esecuzione non venga bloccata durante l'attesa della restituzione di un set di risultati di grandi dimensioni.

Questo esempio illustra un elenco di BLOB lineare, ma è anche possibile eseguire un elenco gerarchico semplicemente impostando il parametro **useFlatBlobListing** del metodo **ListBlobsSegmentedAsync** su **false**.

Poiché il metodo di esempio chiama un metodo asincrono, deve essere prefissato con la parola chiave **async** e restituire un oggetto **Attività**. La parola chiave di attesa specificata per il metodo **ListBlobsSegmentedAsync** sospende l'esecuzione del metodo di esempio fino al completamento dell'attività di elenco.

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

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=Sept15_HO4-->