<properties title="How to use blob storage (PHP) - Azure feature guide" pageTitle="Come usare il servizio di archiviazione BLOB (PHP) | Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs. Code samples are written in PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="robmcm" manager="adinah" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

#Come usare il servizio BLOB da PHP

Questa guida illustra diversi scenari di utilizzo comuni del servizio BLOB di Azure. Gli esempi sono scritti in PHP e usano [Azure SDK per PHP] [download]. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per altre informazioni sui BLOB, vedere la sezione [Passaggi successivi](#NextSteps).

##Sommario

* [Informazioni sull'archiviazione BLOB](#what-is)
* [Concetti](#concepts)
* [Creare un account di archiviazione di Azure](#CreateAccount)
* [Creare un'applicazione PHP](#CreateApplication)
* [Configurare l'applicazione per l'accesso al servizio BLOB](#ConfigureStorage)
* [Configurazione di una connessione di Archiviazione di Azure](#ConnectionString)
* [Procedura: Creare un contenitore](#CreateContainer)
* [Procedura: Caricare un BLOB in un contenitore](#UploadBlob)
* [Procedura: Elencare i BLOB in un contenitore](#ListBlobs)
* [Procedura: Scaricare un BLOB](#DownloadBlob)
* [Procedura: Eliminare un BLOB](#DeleteBlob)
* [Procedura: Eliminare un contenitore BLOB](#DeleteContainer)
* [Passaggi successivi](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Creare un account di archiviazione di Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="CreateApplication"></a>Creare un'applicazione PHP</h2>

Per creare un'applicazione PHP che accede al servizio BLOB di Azure, è sufficiente fare riferimento alle classi in Azure SDK per PHP dall'interno del codice. Per creare l'applicazione, è possibile usare qualsiasi strumento di sviluppo, incluso il Blocco note.

In questa guida si useranno le funzionalità del servizio che possono essere chiamate in un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

<h2><a id="GetClientLibrary"></a>Acquisire le librerie client di Azure</h2>

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="ConfigureStorage"></a>Configurare l'applicazione per l'accesso al servizio BLOB</h2>

Per usare le API del servizio BLOB di Azure, è necessario:

1. Fare riferimento al file autoloader mediante l'istruzione [require_once][require_once].
2. Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe **ServicesBuilder**.

> [WACOM.NOTE]
> In questo esempio (e in altri esempi in questo articolo) si presuppone che siano state installate le librerie client PHP per Azure tramite Composer. Se le librerie sono state installate manualmente o come pacchetto PEAR, sarà necessario fare riferimento al file autoloader `WindowsAzure.php`.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Negli esempi seguenti, l'istruzione `require_once` verrà sempre visualizzata, ma si farà riferimento solo alle classi necessarie per eseguire l'esempio.

<h2><a id="ConnectionString"></a>Configurazione di una connessione di Archiviazione di Azure</h2>

Per creare un'istanza di un client del servizio BLOB di Azure, è necessario innanzitutto disporre di una stringa di connessione valida. Il formato della stringa di connessione del servizio BLOB è:

Per accedere a un servizio attivo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Per accedere alla memoria dell'emulatore:

	UseDevelopmentStorage=true


Per creare un client di servizio di Azure, è necessario usare la classe **ServicesBuilder**. È possibile:

* passare la stringa di connessione direttamente a essa o
* usare **CloudConfigurationManager (CCM)** per cercare la stringa di connessione in più origini esterne:
	* per impostazione predefinita viene fornito con il supporto per un'origine esterna, ovvero le variabili ambientali
	* è possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource**

Per gli esempi illustrati in questo articolo, la stringa di connessione verrà passata direttamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

<h2><a id="CreateContainer"></a>Procedura: Creare un contenitore</h2>

Un oggetto **BlobRestProxy** consente di creare un contenitore BLOB con il metodo **createContainer**. Quando si crea un contenitore, è possibile impostare le opzioni per il contenitore, anche se tale operazione non è necessaria. Nell'esempio seguente viene illustrato come impostare l'elenco ACL del contenitore e i metadati del contenitore.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Blob\Models\CreateContainerOptions;
	use WindowsAzure\Blob\Models\PublicAccessType;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	// OPTIONAL: Set public access policy and metadata.
	// Create container options object.
	$createContainerOptions = new CreateContainerOptions();	

	// Set public access policy. Possible values are 
	// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
	// CONTAINER_AND_BLOBS: 	
	// Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous 
	// request, but cannot enumerate containers within the storage account.
	//
	// BLOBS_ONLY:
	// Specifies public read access for blobs. Blob data within this 
    // container can be read via anonymous request, but container data is not 
    // available. proxys cannot enumerate blobs within the container via 
	// anonymous request.
	// If this value is not specified in the request, container data is 
	// private to the account owner.
	$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
	
	// Set container metadata
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");
	
	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/it-it/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Con la chiamata a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** il contenitore e i dati BLOB diventano accessibili tramite richieste anonime. Con la chiamata a **setPublicAccess(PublicAccessType::BLOBS_ONLY)**, invece, solo i dati BLOB diventano accessibili tramite richieste anonime. Per altre informazioni sugli elenchi ACL del contenitore, vedere [Set Container ACL (REST API)][container-acl].

Per altre informazioni sui codici di errore del servizio BLOB, vedere [Codici di errore del servizio BLOB][error-codes].

<h2><a id="UploadBlob"></a>Procedura: Caricare un BLOB in un contenitore</h2>

Per caricare un file come BLOB, usare il metodo **BlobRestProxy->createBlockBlob**. Questa operazione consentirà di creare il BLOB se non esistente o di sovrascriverlo se esistente. Nell'esempio di codice seguente si presuppone che il contenitore sia già stato creato e che usi [fopen][fopen] per aprire il file come flusso.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";
	
	try	{
		//Upload blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/it-it/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Si noti che nell'esempio precedente un BLOB viene caricato come flusso. Un blob può tuttavia essere caricato anche come stringa, ad esempio mediante la funzione [file\_get\_contents][file_get_contents]. A questo scopo, sostituire `$content = fopen("c:\myfile.txt", "r");` dell'esempio precedente con `$content = file_get_contents("c:\myfile.txt");`.

<h2><a id="ListBlobs"></a>Procedura: Elencare i BLOB in un contenitore</h2>

Per elencare i BLOB in un contenitore, usare il metodo **BlobRestProxy->listBlobs** con un ciclo **foreach** per eseguire il ciclo nel risultato. Il codice seguente consente di inviare al browser il nome di ogni BLOB in un contenitore e il relativo URI.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// List blobs.
		$blob_list = $blobRestProxy->listBlobs("mycontainer");
		$blobs = $blob_list->getBlobs();
		
		foreach($blobs as $blob)
		{
			echo $blob->getName().": ".$blob->getUrl()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/it-it/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="DownloadBlob"></a>Procedura: Scaricare un BLOB</h2>

Per scaricare un BLOB, chiamare il metodo **BlobRestProxy->getBlob**, quindi chiamare il metodo **getContentStream** sull'oggetto **GetBlobResult** risultante.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/it-it/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Si noti che con l'esempio precedente si ottiene un BLOB come risorsa di flusso (comportamento predefinito). È tuttavia possibile usare la funzione [stream\_get\_contents][stream-get-contents] per convertire il flusso restituito in una stringa.

<h2><a id="DeleteBlob"></a>Procedura: Eliminare un BLOB</h2>

Per eliminare un BLOB, passare il nome del contenitore e il nome del BLOB a **BlobRestProxy->deleteBlob**. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/it-it/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="DeleteContainer"></a>Procedura: Eliminare un contenitore BLOB</h2>

Per eliminare un contenere di BLOB, infine, passare il nome del contenitore a **BlobRestProxy->deleteContainer**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/it-it/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="NextSteps"></a>Passaggi successivi</h2>

A questo punto, dopo aver appreso le nozioni di base del servizio BLOB di Azure, usare i collegamenti seguenti per altre informazioni su come eseguire attività di archiviazione più complesse.

- Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure] []
- Blog del team di Archiviazione di Azure: <http://blogs.msdn.com/b/windowsazurestorage/>
- Vedere l'esempio relativo al BLOB in blocchi all'indirizzo <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- Vedere l'esempio relativo al BLOB di pagine all'indirizzo <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/it-it/library/windowsazure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/it-it/library/windowsazure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
