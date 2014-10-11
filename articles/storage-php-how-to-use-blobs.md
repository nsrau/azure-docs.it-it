<properties title="How to use blob storage (PHP) - Azure feature guide" pageTitle="How to use blob storage (PHP) | Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs. Code samples are written in PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="robmcm" manager="wpickett" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Come utilizzare il servizio BLOB da PHP

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio BLOB di Azure. Gli esempi sono scritti in PHP e utilizzano [Azure SDK per PHP][]. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi][].

## Sommario

-   [Informazioni sull'archiviazione BLOB][]
-   [Concetti][]
-   [Creazione di un account di Archiviazione di Azure][]
-   [Creazione di un'applicazione PHP][]
-   [Configurazione dell'applicazione per l'accesso al servizio BLOB][]
-   [Configurazione di una connessione di Archiviazione di Azure][]
-   [Procedura: Creare un contenitore][]
-   [Procedura: Caricare un BLOB in un contenitore][]
-   [Procedura: Elencare i BLOB in un contenitore][]
-   [Procedura: Scaricare un BLOB][]
-   [Procedura: Eliminare un BLOB][]
-   [Procedura: Eliminare un contenitore BLOB][]
-   [Passaggi successivi][]

[WACOM.INCLUDE [howto-blob-storage][]]

## <span id="CreateAccount"></span></a>Creazione di un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account][]]

## <span id="CreateApplication"></span></a>Creazione di un'applicazione PHP

Per creare un'applicazione PHP che accede al servizio BLOB di Azure, è sufficiente fare riferimento alle classi in Azure SDK per PHP dall'interno del codice. Per creare l'applicazione, è possibile utilizzare qualsiasi strumento di sviluppo, incluso il Blocco note.

In questa guida si useranno le funzionalità del servizio che possono essere chiamate in un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

## <span id="GetClientLibrary"></span></a>Acquisizione delle librerie client di Azure

[WACOM.INCLUDE [get-client-libraries][]]

## <span id="ConfigureStorage"></span></a>Configurazione dell'applicazione per l'accesso al servizio BLOB

Per usare le API del servizio BLOB di Azure, è necessario:

1.  Fare riferimento al file autoloader mediante l'istruzione [require\_once][].
2.  Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe **ServicesBuilder**.

> [WACOM.NOTE]
> In questo esempio (e in altri esempi in questo articolo) si presuppone che siano state installate le librerie client PHP per Azure tramite Composer. Se le librerie sono state installate manualmente o come pacchetto PEAR, sarà necessario fare riferimento al file autoloader `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

Negli esempi seguenti, l'istruzione `require_once` verrà sempre visualizzata, ma si farà riferimento solo alle classi necessarie per eseguire l'esempio.

## <span id="ConnectionString"></span></a>Configurare una connessione di archiviazione di Azure

Per creare un'istanza di un client del servizio BLOB di Azure, è necessario innanzitutto disporre di una stringa di connessione valida. Il formato della stringa di connessione del servizio BLOB è:

Per accedere a un servizio attivo:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Per accedere all'archivio dell'emulatore:

    UseDevelopmentStorage=true

Per creare un client di servizio di Azure, è necessario usare la classe **ServicesBuilder**. È possibile:

-   passare la stringa di connessione direttamente a essa o
-   usare **CloudConfigurationManager (CCM)** per cercare la stringa di connessione in più origini esterne:

    -   per impostazione predefinita viene fornito con il supporto per un'origine esterna, ovvero le variabili ambientali
    -   è possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource**

Per gli esempi illustrati in questo articolo, la stringa di connessione verrà passata direttamente.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <span id="CreateContainer"></span></a>Procedura: Creare un contenitore

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

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Con la chiamata a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** il contenitore e i dati BLOB diventano accessibili tramite richieste anonime. Con la chiamata a **setPublicAccess(PublicAccessType::BLOBS\_ONLY)**, invece, solo i dati BLOB diventano accessibili tramite richieste anonime. Per ulteriori informazioni sugli elenchi ACL del contenitore, vedere [Set Container ACL (API REST)][].

Per ulteriori informazioni sui codici di errore del servizio BLOB, vedere [Codici di errore del servizio BLOB][].

## <span id="UploadBlob"></span></a>Procedura: Caricare un BLOB in un contenitore

Per caricare un file come BLOB, utilizzare il metodo **BlobRestProxy-\>createBlockBlob**. Questa operazione consentirà di creare il BLOB se non esistente o di sovrascriverlo se esistente. Nell'esempio di codice seguente si presuppone che il contenitore sia già stato creato e che utilizzi [fopen][] per aprire il file come flusso.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Si noti che nell'esempio precedente un BLOB viene caricato come flusso. Un blob può tuttavia essere caricato anche come stringa, ad esempio mediante la funzione file_get_contents. A questo scopo, sostituire `$content = fopen("c:\myfile.txt", "r");` nell'esempio precedente con `$content = file_get_contents("c:\myfile.txt");`.

## <span id="ListBlobs"></span></a>Procedura: Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, utilizzare il metodo **BlobRestProxy-\>listBlobs** con un ciclo **foreach** per eseguire il ciclo nel risultato. Il codice seguente consente di inviare al browser il nome di ogni BLOB in un contenitore e il relativo URI.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
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
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DownloadBlob"></span></a>Procedura: Scaricare un BLOB

Per scaricare un BLOB, chiamare il metodo **BlobRestProxy-\>getBlob**, quindi chiamare il metodo **getContentStream** sull'oggetto **GetBlobResult** risultante.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Si noti che con l'esempio precedente si ottiene un BLOB come risorsa di flusso (comportamento predefinito). È tuttavia possibile utilizzare la funzione stream_get_contents per convertire il flusso restituito in una stringa.

## <span id="DeleteBlob"></span></a>Procedura: Eliminare un BLOB

Per eliminare un BLOB, passare il nome del contenitore e il nome del BLOB a **BlobRestProxy-\>deleteBlob**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteContainer"></span></a>Procedura: Eliminare un contenitore BLOB

Per eliminare un contenere di BLOB, infine, passare il nome del contenitore a **BlobRestProxy-\>deleteContainer**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="NextSteps"></span></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio BLOB di Azure, visitare i collegamenti seguenti per ulteriori informazioni su come eseguire attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure][]
-   Blog del team di Archiviazione di Azure: <http://blogs.msdn.com/b/windowsazurestorage/>
-   Vedere l'esempio relativo al BLOB in blocchi all'indirizzo <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
-   Vedere l'esempio relativo al BLOB di pagine all'indirizzo <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

  [Azure SDK per PHP]: http://go.microsoft.com/fwlink/?LinkID=252473
  [Passaggi successivi]: #NextSteps
  [Informazioni sull'archiviazione BLOB]: #what-is
  [Concetti]: #concepts
  [Creazione di un account di Archiviazione di Azure]: #CreateAccount
  [Creazione di un'applicazione PHP]: #CreateApplication
  [Configurazione dell'applicazione per l'accesso al servizio BLOB]: #ConfigureStorage
  [Configurazione di una connessione di Archiviazione di Azure]: #ConnectionString
  [Procedura: Creare un contenitore]: #CreateContainer
  [Procedura: Caricare un BLOB in un contenitore]: #UploadBlob
  [Procedura: Elencare i BLOB in un contenitore]: #ListBlobs
  [Procedura: Scaricare un BLOB]: #DownloadBlob
  [Procedura: Eliminare un BLOB]: #DeleteBlob
  [Procedura: Eliminare un contenitore BLOB]: #DeleteContainer
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [get-client-libraries]: ../includes/get-client-libraries.md
  [require\_once]: http://php.net/require_once
  [Set Container ACL (API REST)]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179391.aspx
  [Codici di errore del servizio BLOB]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
  [fopen]: http://www.php.net/fopen
  [file\_get\_contents]: http://php.net/file_get_contents
  [stream\_get\_contents]: http://www.php.net/stream_get_contents
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
