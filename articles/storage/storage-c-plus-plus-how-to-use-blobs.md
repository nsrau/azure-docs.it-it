<properties 
    pageTitle="Come usare il servizio di archiviazione BLOB (C++) | Microsoft Azure" 
    description="Informazioni su come usare il servizio di archiviazione BLOB in Azure. Gli esempi sono scritti in C++." 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="09/23/2015" 
    ms.author="tamram"/>

# Come usare l'archiviazione BLOB da C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Panoramica
In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione BLOB di Azure. Gli esempi sono scritti in C++ e utilizzano la [libreria client di Archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp/blob/v1.0.0/README.md). Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB.

>[AZURE.NOTE]Questa guida fa riferimento alla libreria client di Archiviazione di Azure per C++ versione 1.0.0 e successive. La versione consigliata è Storage Client Library 1.0.0, disponibile tramite [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creazione di un’applicazione C++
In questa guida verranno utilizzate le funzionalità di archiviazione che è possibile eseguire all’interno di un’applicazione C++.

A tal fine, sarà necessario installare la libreria client di Archiviazione di Azure per C++ e creare un account di archiviazione Azure nella propria sottoscrizione Azure.

Per installare la libreria client di Archiviazione di Azure per C++, è possibile utilizzare i metodi seguenti:

-	**Linux:** seguire le istruzioni fornite nella pagina [README della libreria client di Archiviazione di Azure per C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
-	**Windows:** in Visual Studio, fare clic su **Strumenti > Gestione pacchetti NuGet > Console di gestione pacchetti**. Digitare il seguente comando nella [console Gestione pacchetti NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e premere **INVIO**.  

		Install-Package wastorage

## Configurazione dell'applicazione per l'accesso all'archiviazione BLOB  
Aggiungere le istruzioni include seguenti all'inizio del file C++ in cui si desidera utilizzare le API di archiviazione di Azure per accedere ai BLOB:

	#include "was/storage_account.h"
	#include "was/blob.h"

## Configurazione di una stringa di connessione di archiviazione di Azure
I client di archiviazione di Azure usano le stringhe di connessione di archiviazione per archiviare endpoint e credenziali per l'accesso ai servizi di gestione dati. Quando si esegue un'applicazione client, è necessario specificare la stringa di connessione di archiviazione nel formato seguente, utilizzando il nome dell'account di archiviazione e la chiave di accesso alle risorse di archiviazione relativa all'account di archiviazione riportata nel [portale di Azure](portal.azure.com) per i valori di *AccountName* e *AccountKey*. Per informazioni sugli account di archiviazione e sulle chiavi di accesso, vedere [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md). In questo esempio viene illustrato come dichiarare un campo statico per memorizzare la stringa di connessione:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Per eseguire il test dell’applicazione sul proprio computer Windows locale, è possibile utilizzare [l’emulatore di archiviazione](https://msdn.microsoft.com/library/azure/hh403989.aspx) di Microsoft Azure che viene installato con [Azure SDK](http://azure.microsoft.com/downloads/). L'emulatore di archiviazione è un'utilità che simula i servizi BLOB, tabelle e di accodamento, disponibile in Azure nel computer di sviluppo locale. Nell’esempio seguente viene illustrato come dichiarare un campo statico per memorizzare la stringa di connessione all’emulatore di archiviazione locale:

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Per avviare l'emulatore di archiviazione di Azure, selezionare il pulsante **Start** o premere il tasto **Windows**. Iniziare a digitare **Emulatore di archiviazione di Azure** e selezionare **Emulatore di archiviazione di Microsoft Azure** dall'elenco delle applicazioni.

Gli esempi seguenti presumono che sia stato usato uno di questi due metodi per ottenere la stringa di connessione di archiviazione.

## Recuperare la stringa di connessione
Per rappresentare le informazioni dell'account di archiviazione, è possibile utilizzare la classe **cloud\_storage\_account**. Per recuperare le informazioni sull'account di archiviazione dalla stringa di connessione alla risorsa di archiviazione, è possibile utilizzare il metodo **parse**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Successivamente, ottenere un riferimento alla classe **cloud\_blob\_client**, in quanto consente di recuperare oggetti che rappresentano contenitori e BLOB archiviati nel servizio di archiviazione BLOB. Il codice seguente consente di creare un oggetto **cloud\_blob\_client** utilizzando l'oggetto account di archiviazione recuperato in precedenza:

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## Procedura: creare un contenitore

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

In questo esempio viene creato un contenitore, nel caso in cui non esista già:

	try 
	{
		// Retrieve storage account from connection string.
		azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

		// Create the blob client.
		azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

		// Retrieve a reference to a container.
		azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

		// Create the container if it doesn't already exist.
		container.create_if_not_exists();
	}
	catch (const std::exception& e)
	{
		std::wcout << U("Error: ") << e.what() << std::endl;
	}  

Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione per scaricare BLOB da questo contenitore. Se si desidera rendere i file all'interno del contenitore disponibili per tutti, è possibile impostare il contenitore come pubblico utilizzando il codice seguente:

	// Make the blob container publicly accessible.
	azure::storage::blob_container_permissions permissions;
	permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
	container.upload_permissions(permissions);  

I BLOB in un contenitore pubblico sono visibili a tutti gli utenti di Internet, tuttavia è possibile modificarli o eliminarli solo se si dispone della chiave di accesso appropriata.

## Procedura: caricare un BLOB in un contenitore
In Archiviazione BLOB di Azure sono supportati BLOB in blocchi e BLOB di pagine. Nella maggior parte dei casi è consigliabile utilizzare il tipo di BLOB in blocchi.

Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB in blocchi. Dopo aver ottenuto un riferimento al BLOB, è possibile caricarvi qualsiasi flusso di dati chiamando il metodo **upload\_from\_stream**. Questa operazione consentirà di creare il BLOB se non già esistente o di sovrascriverlo se già esistente. Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore e si presuppone che il contenitore sia già stato creato.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Create or overwrite the "my-blob-1" blob with contents from a local file.
	concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
	blockBlob.upload_from_stream(input_stream);
	input_stream.close().wait();

	// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
	// Retrieve a reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
	blob2.upload_text(U("more text"));

	// Retrieve a reference to a blob named "my-blob-3".
	azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
	blob3.upload_text(U("other text"));  

In alternativa, è possibile utilizzare il metodo **upload\_from\_file** per caricare un file in un BLOB in blocchi.

## Procedura: elencare i BLOB in un contenitore
Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore. Sarà quindi possibile utilizzare il metodo **list\_blobs** del contenitore per recuperare i BLOB e/o le directory in esso contenuti. Per accedere al set completo di proprietà e metodi per un **list\_blob\_item** restituito, è necessario richiamare il metodo **list\_blob\_item.as\_blob** per ottenere un oggetto **cloud\_blob** o il metodo **list\_blob.as\_directory** per ottenere un oggetto cloud\_blob\_directory. Nel codice seguente viene illustrato come recuperare e visualizzare l'URI di ciascun elemento del contenitore **my-sample-container**:

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Output URI of each item.
	azure::storage::list_blob_item_iterator end_of_results;
	for (auto it = container.list_blobs(); it != end_of_results; ++it)
	{
		if (it->is_blob())
		{
			std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
		}
		else
		{
			std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
		}
	}

Per ulteriori informazioni sull'elenco di operazioni, vedere[elenco risorse di archiviazione Azure in C++](storage-c-plus-plus-enumeration.md).

## Procedura: scaricare i BLOB
Per scaricare i BLOB, recuperare innanzitutto un riferimento al BLOB, quindi chiamare il metodo **download\_to\_stream**. Nell'esempio seguente viene utilizzato il metodo **download\_to\_stream** per trasferire il contenuto del BLOB in un oggetto stream che è quindi possibile rendere persistente in un file locale.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Save blob contents to a file.
	concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
	concurrency::streams::ostream output_stream(buffer);
	blockBlob.download_to_stream(output_stream);

	std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
	std::vector<unsigned char>& data = buffer.collection();
		
	outfile.write((char *)&data[0], buffer.size());
	outfile.close();  

In alternativa, è possibile utilizzare il metodo **download\_to\_file** per scaricare il contenuto di un BLOB in un file. Inoltre, è possibile utilizzare il metodo **download\_text** per scaricare il contenuto di un BLOB come stringa di testo.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

	// Download the contents of a blog as a text string.
	utility::string_t text = text_blob.download_text();

## Procedura: eliminare i BLOB
Per eliminare un BLOB, ottenere innanzitutto un riferimento al BLOB, quindi chiamare il metodo **delete\_blob** sul riferimento.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Delete the blob.
	blockBlob.delete_blob();

## Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per ulteriori informazioni su Archiviazione di Azure.

-	[Come usare l’archiviazione delle code da C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Come usare l’archiviazione tabelle da C++](storage-c-plus-plus-how-to-use-tables.md)
-	[Elenco delle risorse di archiviazione di Azure in C++](storage-c-plus-plus-enumeration.md)
-	[Informazioni di riferimento sulla libreria client di archiviazione per C++](http://azure.github.io/azure-storage-cpp)
-	[Documentazione di Archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/)
- [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy)



 

<!---HONumber=AcomDC_1217_2015-->