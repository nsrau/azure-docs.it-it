<properties 
	pageTitle="Set and retrieve Properties and Metadata for Blob Storage \| Microsoft Azure" 
	description="Learn how to set and retrieve properties and metadata for Azure Storage containers and blobs." services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt\_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tamram"/>


# Impostare e recuperare proprietà e metadati #

## Panoramica

Contenitori e BLOB supportano due formati di dati associati, oltre ai dati che contengono:

*   **Proprietà di sistema.** Le proprietà di sistema sono presenti in ciascuna risorsa contenitore o BLOB. Alcune di esse possono essere lette o impostate, mentre altre sono di sola lettura. Anche se in modo non esplicito, alcune proprietà di sistema corrispondono a specifiche intestazioni HTTP standard, mantenute dalla Libreria gestita di Azure per conto dell’utente.  

*   **Metadati definiti dall’utente.** I metadati definiti dall'utente sono metadati specificati in una determinata risorsa, sotto forma di coppia nome-valore. È possibile utilizzare i metadati per archiviare valori aggiuntivi con un contenitore o BLOB. Questi valori sono destinati esclusivamente all’utente e non influiscono sul comportamento del contenitore o del BLOB.

> [AZURE.IMPORTANT]Il recupero dei valori di proprietà e dei metadati per una risorsa è un processo in due fasi. Prima di leggere questi valori, è necessario recuperarli in modo esplicito negli oggetti [CloudBlobContainer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx), [CloudBlockBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx) o [CloudPageBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.aspx). Per recuperare proprietà e metadati in modo sincrono, chiamare il metodo **FetchAttributes** sul contenitore o sul BLOB. Per recuperarli in modo asincrono, chiamare **FetchAttributesAsync**.

## Impostazione e recupero di proprietà

Un contenitore include unicamente proprietà di sola lettura, mentre un BLOB include proprietà sia di sola lettura, sia di lettura-scrittura. Per impostare le proprietà di un BLOB, specificare il valore della proprietà, quindi chiamare il metodo [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.setproperties.aspx) oppure il metodo [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.setproperties.aspx).

Per leggere le proprietà in un contenitore o BLOB, chiamare il metodo **FetchAttributes**, quindi recuperare il valore della proprietà.

Nell'esempio di codice seguente vengono creati un contenitore e un BLOB e vengono scritti i valori delle proprietà in una finestra della console. In questo esempio viene utilizzato l'emulatore di archiviazione, pertanto, affinché il codice funzioni, il servizio di archiviazione emulato deve essere in esecuzione:

	// Use the storage emulator account.
	CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

	// As an alternative, you can create the credentials from the account name and key.
	// string accountName = "myaccount";
	// string accountKey = "SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";
	// StorageCredentials credentials = new StorageCredentials(accountName, accountKey);
	// CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);

	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	CloudBlockBlob blob = container.GetBlockBlobReference(<span style="color:#A31515;">"myblob.txt");

	// Create or overwrite the "myblob.txt" blob with contents from a local file.
	<span style="color:Blue;">using (<span style="color:Blue;">var fileStream = System.IO.File.OpenRead(<span style="color:#A31515;">@"c:\test\myblob.txt"))
	{
	   blob.UploadFromStream(fileStream);
	} 

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	Uri blobUri =<span style="color:Blue;">new UriBuilder(containerUri.AbsoluteUri + <span style="color:#A31515;">"/ablob.txt").Uri;
	CloudPageBlob blob = <span style="color:Blue;">new CloudPageBlob(blobUri, credentials);
	blob.Create(1024);
				

	// Set the CacheControl property.
	blob.Properties.CacheControl = <span style="color:#A31515;">"public, max-age=31536000";
	blob.SetProperties();

	// Fetch blob attributes.
	blob.FetchAttributes();

	Console.WriteLine(<span style="color:#A31515;">"Read-only properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"BlobType: " + blob.Properties.BlobType);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + blob.Properties.ETag);
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUtc: " + blob.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"Length: " + blob.Properties.Length);
	Console.WriteLine();

	Console.WriteLine(<span style="color:#A31515;">"Read-write properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"CacheControl: " +
	   (blob.Properties.CacheControl == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.CacheControl));
	Console.WriteLine(<span style="color:#A31515;">"ContentEncoding: " +
	   (blob.Properties.ContentEncoding == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentEncoding));
	Console.WriteLine(<span style="color:#A31515;">"ContentLanguage: " +
	   (blob.Properties.ContentLanguage == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentLanguage));
	Console.WriteLine(<span style="color:#A31515;">"ContentMD5: " +
	   (blob.Properties.ContentMD5 == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentMD5));
	Console.WriteLine(<span style="color:#A31515;">"ContentType: " +
	   (blob.Properties.ContentType == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentType));

	// Clean up.
	blob.DeleteIfExists();
	container.Delete();
## Impostazione e recupero dei metadati

È possibile specificare i metadati come uno o più coppie nome-valore in una risorsa BLOB o contenitore. Per impostare i metadati, aggiungere coppie nome-valore alla raccolta **Metadati** nella risorsa, quindi chiamare il metodo **SetMetadata** per salvare i valori nel servizio.

> [AZURE.NOTE]\: il nome dei metadati deve essere conforme alle convenzioni di denominazione per gli identificatori C\#.
 
Per recuperare i metadati, chiamare il metodo **FetchAttributes** sul BLOB o sul contenitore per popolare la raccolta **Metadati**, quindi leggere i valori.

Nell’esempio di codice seguente viene creato un nuovo contenitore e vengono impostati i metadati, quindi vengono letti valori dei metadati:

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## Vedere anche  

- [Riferimento libreria client di Archiviazione di Azure](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Introduzione all’archiviazione BLOB per .NET](storage-dotnet-how-to-use-blobs.md)  

<!--HONumber=52-->
