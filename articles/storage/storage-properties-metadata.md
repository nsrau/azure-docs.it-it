
<properties 
  pageTitle="Impostare e recuperare proprietà e metadati per gli oggetti in Archiviazione di Azure | Microsoft Azure" 
  description="Archiviare i metadati personalizzati per oggetti di archiviazione di Azure, impostare e recuperare le proprietà di sistema." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="09/03/2015" 
  ms.author="tamram"/>


# Impostare e recuperare proprietà e metadati #

## Panoramica

Oggetti nelle proprietà di sistema di supporto di archiviazione di Azure e i metadati definiti dall'utente, oltre ai dati che contengono:

*   **Proprietà di sistema.** Proprietà di sistema su ogni risorsa di archiviazione. Alcune di esse possono essere lette o impostate, mentre altre sono di sola lettura. Anche se in modo non esplicito, alcune proprietà di sistema corrispondono a specifiche intestazioni HTTP standard. La libreria client di archiviazione di Azure gestisce tale funzionalità.  

*   **Metadati definiti dall’utente.** I metadati definiti dall'utente sono metadati specificati in una determinata risorsa, sotto forma di coppia nome-valore. È possibile utilizzare i metadati per archiviare valori aggiuntivi con una risorsa di archiviazione; questi valori sono destinati esclusivamente all’utente e non influiscono sul comportamento della risorsa.

## Impostazione e recupero di proprietà

Il recupero dei valori di proprietà e dei metadati per una risorsa è un processo in due fasi. Prima di leggere questi valori, è necessario recuperarli in modo esplicito chiamando il metodo **FetchAttributes** o **FetchAttributesAsync**.

> [AZURE.IMPORTANT]I valori di proprietà e i metadati per una risorsa di archiviazione non vengono popolati a meno che non si chiami uno dei metodi **FetchAttributes**.

Per impostare le proprietà di un BLOB, specificare il valore della proprietà, quindi chiamare il metodo **SetProperties** oppure il metodo **SetPropertiesAsync**.

L’esempio di codice seguente crea un contenitore e scrive i valori delle proprietà in una finestra della console.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	//Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## Impostazione e recupero dei metadati

È possibile specificare i metadati come uno o più coppie nome-valore in una risorsa BLOB o contenitore. Per impostare i metadati, aggiungere coppie nome-valore alla raccolta **Metadati** nella risorsa, quindi chiamare il metodo **SetMetadata** per salvare i valori nel servizio.

> [AZURE.NOTE]\: il nome dei metadati deve essere conforme alle convenzioni di denominazione per gli identificatori C#.
 
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
 

<!---HONumber=Oct15_HO3-->