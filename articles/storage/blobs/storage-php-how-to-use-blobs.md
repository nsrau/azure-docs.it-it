---
title: Come usare l'archiviazione BLOB (archiviazione di oggetti) da PHP | Microsoft Docs
description: Archiviare i dati non strutturati nel cloud con l'archivio BLOB (archivio di oggetti) di Azure.
documentationcenter: php
services: storage
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 1af56b59-b3f0-4b46-8441-aab463ae088e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 9de2f7e81d75669267fe6448030c118d06b3f88a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-php"></a>Come usare l'archiviazione BLOB da PHP
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica
L'archiviazione BLOB di Azure è un servizio che archivia dati non strutturati nel cloud come oggetti/BLOB. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.

Questa guida illustra diversi scenari di utilizzo comuni del servizio BLOB di Azure. Gli esempi sono scritti in PHP e usano la [libreria client di Archiviazione di Azure per PHP][download]. Gli scenari illustrati includono **caricamento**, **visualizzazione in elenchi**, **download** e **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi](#next-steps) .

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Creare un'applicazione PHP
Per creare un'applicazione PHP che acceda al servizio BLOB di Azure, è sufficiente fare riferimento alle classi in [Libreria client di Archiviazione di Azure per PHP][download] dall'interno del codice. Per creare l'applicazione, è possibile usare qualsiasi strumento di sviluppo, incluso il Blocco note.

In questa guida vengono usate le funzionalità del servizio di archiviazione BLOB che possono essere chiamate in un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

## <a name="get-the-azure-client-libraries"></a>Acquisire le librerie client di Azure
[!INCLUDE [get-client-libraries](../../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>Configurare l'applicazione per l'accesso al servizio BLOB
Per usare le API del servizio BLOB di Azure, è necessario:

1. Fare riferimento al file autoloader mediante l'istruzione [require_once].
2. Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe **ServicesBuilder** .

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Common\ServicesBuilder;
```

Negli esempi seguenti viene mostrata sempre l'istruzione `require_once`, ma si fa riferimento solo alle classi necessarie per l'esecuzione dell'esempio.

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure
Per creare un'istanza di un client del servizio BLOB di Azure, è necessario innanzitutto disporre di una stringa di connessione valida. Il formato della stringa di connessione del servizio BLOB è:

Per accedere a un servizio attivo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Per accedere alla memoria dell'emulatore:

```php
UseDevelopmentStorage=true
```

Per creare un client di servizio di Azure, è necessario usare la classe **ServicesBuilder** . È possibile:

* passare la stringa di connessione direttamente a essa o
* Usare variabili di ambiente nell'app Web per archiviare la stringa di connessione. Vedere il documento sulle [impostazioni di configurazione di app Web di Azure](../../app-service/web-sites-configure.md) per la configurazione delle stringhe di connessione.

Per gli esempi illustrati in questo articolo, la stringa di connessione viene passata direttamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);
```

## <a name="create-a-container"></a>Creare un contenitore
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Un oggetto **BlobRestProxy** consente di creare un contenitore BLOB con il metodo **createContainer**. Quando si crea un contenitore, è possibile impostare le opzioni per il contenitore, anche se tale operazione non è necessaria. L'esempio seguente illustra come impostare l'elenco ACL e i metadati del contenitore.

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
use MicrosoftAzure\Storage\Common\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

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

// Set container metadata.
$createContainerOptions->addMetaData("key1", "value1");
$createContainerOptions->addMetaData("key2", "value2");

try    {
    // Create container.
    $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Con la chiamata a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** il contenitore e i dati BLOB diventano accessibili tramite richieste anonime. Con la chiamata a **setPublicAccess(PublicAccessType::BLOBS_ONLY)**, invece, solo i dati BLOB diventano accessibili tramite richieste anonime. Per altre informazioni sugli ACL contenitore, vedere [Set container ACL (REST API)][container-acl] (Configurare ACL contenitore - API REST).

Per altre informazioni sui codici errore del servizio BLOB, vedere [Blob Service Error Codes][error-codes] (Codici errore del servizio BLOB).

## <a name="upload-a-blob-into-a-container"></a>Caricare un BLOB in un contenitore
Per caricare un file come BLOB, usare il metodo **BlobRestProxy->createBlockBlob**. Questa operazione consentirà di creare il BLOB se non esistente o di sovrascriverlo se esistente. Nell'esempio di codice seguente si presuppone che il contenitore sia già stato creato e che usi [fopen][fopen] per aprire il file come flusso.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

$content = fopen("c:\myfile.txt", "r");
$blob_name = "myblob";

try    {
    //Upload blob
    $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Si noti che l'esempio precedente consente di caricare un BLOB come flusso. Un BLOB può tuttavia essere caricato anche come stringa, ad esempio mediante la funzione [file\_get\_contents][file_get_contents]. A tale scopo utilizzare l'esempio precedente, modificare `$content = fopen("c:\myfile.txt", "r");` con `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore
Per elencare i BLOB in un contenitore, usare il metodo **BlobRestProxy->listBlobs** con un ciclo **foreach** per eseguire il ciclo nel risultato. Il codice seguente mostra il nome di ogni BLOB come output in un contenitore e mostra il relativo URI al browser.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

try    {
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
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="download-a-blob"></a>Scaricare un BLOB
Per scaricare un BLOB, chiamare il metodo **BlobRestProxy->getBlob**, quindi chiamare il metodo **getContentStream** nell'oggetto **GetBlobResult** risultante.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


try    {
    // Get blob.
    $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
    fpassthru($blob->getContentStream());
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Si noti che con l'esempio precedente si ottiene un BLOB come risorsa di flusso (comportamento predefinito). È tuttavia possibile usare la funzione [stream\_get\_contents][stream-get-contents] per convertire il flusso restituito in una stringa.

## <a name="delete-a-blob"></a>Eliminare un BLOB
Per eliminare un BLOB, passare il nome del contenitore e il nome del BLOB a **BlobRestProxy->deleteBlob**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

try    {
    // Delete blob.
    $blobRestProxy->deleteBlob("mycontainer", "myblob");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-a-blob-container"></a>Eliminare un contenitore BLOB
Per eliminare un contenere di BLOB, infine, passare il nome del contenitore a **BlobRestProxy->deleteContainer**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

try    {
    // Delete container.
    $blobRestProxy->deleteContainer("mycontainer");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base del servizio BLOB di Azure, usare i collegamenti seguenti per altre informazioni su attività di archiviazione più complesse.

* Consultare le [Informazioni di riferimento API sulla libreria client di Archiviazione di Azure per PHP](http://azure.github.io/azure-storage-php/)
* Vedere l'esempio di [BLOB avanzato](https://github.com/Azure/azure-storage-php/blob/master/samples/BlobSamples.php).

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di PHP](/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
