<properties
	pageTitle="Impostare e recuperare proprietà e metadati per gli oggetti in Archiviazione di Azure | Microsoft Azure"
	description="Archiviare i metadati personalizzati per oggetti di archiviazione di Azure, impostare e recuperare le proprietà di sistema."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/07/2016"
	ms.author="dineshm;tamram"/>

# Impostare e recuperare proprietà e metadati #

## Overview

Oggetti nelle proprietà di sistema di supporto di archiviazione di Azure e i metadati definiti dall'utente, oltre ai dati che contengono:

*   **Proprietà di sistema.** Proprietà di sistema su ogni risorsa di archiviazione. Alcune di esse possono essere lette o impostate, mentre altre sono di sola lettura. Anche se in modo non esplicito, alcune proprietà di sistema corrispondono a specifiche intestazioni HTTP standard. La libreria client di archiviazione di Azure gestisce tale funzionalità.

*   **Metadati definiti dall’utente.** I metadati definiti dall'utente sono metadati specificati in una determinata risorsa, sotto forma di coppia nome-valore. È possibile utilizzare i metadati per archiviare valori aggiuntivi con una risorsa di archiviazione; questi valori sono destinati esclusivamente all’utente e non influiscono sul comportamento della risorsa.

Il recupero dei valori di proprietà e dei metadati per una risorsa è un processo in due fasi. Prima di leggere questi valori, è necessario recuperarli in modo esplicito chiamando il metodo **FetchAttributes**.

> [AZURE.IMPORTANT] I valori di proprietà e i metadati per una risorsa di archiviazione non vengono popolati a meno che non si chiami uno dei metodi **FetchAttributes**.

## Impostazione e recupero di proprietà

Per recuperare i valori della proprietà, chiamare il metodo **FetchAttributes** sul BLOB o sul contenitore per popolare le proprietà, quindi leggere i valori.

Per impostare le proprietà di un oggetto, specificare il valore della proprietà, quindi chiamare il metodo **SetProperties**.

L’esempio di codice seguente crea un contenitore e scrive alcuni dei valori delle proprietà in una finestra della console.

    //Parse the connection string for the storage account.
	const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
	
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

> [AZURE.NOTE] Il nome dei metadati deve essere conforme alle convenzioni di denominazione degli identificatori C#.
 
Il seguente codice di esempio imposta i metadati in un contenitore. Un valore è impostato mediante l'utilizzo del metodo di raccolta **Aggiungi**. L'altro valore è impostato utilizzando la sintassi implicita chiave/valore. Entrambi sono validi.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Per recuperare i metadati, chiamare il metodo **FetchAttributes** sul BLOB o sul contenitore per popolare la raccolta **Metadati**, quindi leggere i valori, come nell’esempio mostrato di seguito.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## Vedere anche  

- [Informazioni di riferimento sulla libreria client di archiviazione di Azure per .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Pacchetto sulla libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)

<!---HONumber=AcomDC_0928_2016-->