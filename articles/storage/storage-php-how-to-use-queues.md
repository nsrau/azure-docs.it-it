---
title: Come usare l&quot;archiviazione code da PHP | Microsoft Docs
description: Informazioni su come usare il servizio di accodamento di Azure per creare ed eliminare code e per inserire, visualizzare ed eliminare messaggi. Gli esempi sono scritti in PHP.
documentationcenter: php
services: storage
author: robinsh
manager: carmonm
editor: tysonn
ms.assetid: 7582b208-4851-4489-a74a-bb952569f55b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 08/11/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0bc91ff65c3bff54bc341a23be987a30a1ccf454


---
# <a name="how-to-use-queue-storage-from-php"></a>Come usare l'archiviazione di accodamento da PHP
[!INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica
Questa guida illustra diversi scenari di utilizzo comuni del servizio di archiviazione code di Azure. Gli esempi sono scritti utilizzando le classi di Windows SDK per PHP Gli scenari presentati includono l'inserimento, la visualizzazione, il recupero e l'eliminazione dei messaggi in coda, oltre alle procedure di creazione ed eliminazione di code.

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Creare un'applicazione PHP
Per creare un'applicazione PHP che accede al Servizio di accodamento di Azure, è sufficiente fare riferimento alle classi di Azure SDK per PHP dall'interno del codice. Per creare l'applicazione, è possibile usare qualsiasi strumento di sviluppo, incluso il Blocco note.

In questa guida si useranno le funzionalità del Servizio di accodamento che possono essere chiamate in un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

## <a name="get-the-azure-client-libraries"></a>Acquisire le librerie client di Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione di accodamento
Per utilizzare le API per l'archiviazione di accodamento di Azure, è necessario:

1. Fare riferimento al file autoloader mediante l'istruzione [require_once].
2. Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe **ServicesBuilder** .

> [!NOTE]
> In questo esempio (e in altri esempi in questo articolo) si presuppone che siano state installate le librerie client PHP per Azure tramite Composer. Se le librerie sono state installate manualmente, sarà necessario fare riferimento al file autoloader `WindowsAzure.php` .
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;

```

Nei seguenti esempi l'istruzione `require_once` verrà sempre visualizzata, ma si farà riferimento solo alle classi necessarie per eseguire l'esempio.

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure
Per creare un'istanza di un client del Servizio di accodamento di Azure, è necessario innanzitutto disporre di una stringa di connessione valida. Il formato della stringa di connessione del servizio di accodamento è:

Per accedere a un servizio attivo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Per accedere alla memoria dell'emulatore:

```php
UseDevelopmentStorage=true
```

Per creare un client di servizio di Azure, è necessario usare la classe **ServicesBuilder** . È possibile utilizzare le tecniche seguenti:

* Passare la stringa di connessione direttamente.
* Utilizzare **CloudConfigurationManager (CCM)** per cercare la stringa di connessione in più origini esterne:
  * Per impostazione predefinita, viene fornito con il supporto per un'origine esterna, ovvero le variabili ambientali
  * è possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource**

Per gli esempi illustrati in questo articolo, la stringa di connessione verrà passata direttamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Creare una coda
Un oggetto **QueueRestProxy** consente di creare una coda usando il metodo **createQueue**. Quando si crea una coda, è possibile impostare le opzioni per la coda, anche se tale operazione non è necessaria. Nell'esempio seguente viene illustrato come impostare i metadati in una coda.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueRestProxy->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Non basarsi sulla distinzione maiuscole/minuscole nelle chiavi di metadati. Il servizio legge tutte le chiavi come scritte in minuscolo.
> 
> 

## <a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda
Per aggiungere un messaggio a una coda, usare **QueueRestProxy->createMessage**. Il metodo utilizza il nome della coda, il testo del messaggio e le opzioni messaggio (facoltative).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

try    {
    // Create message.
    $builder = new ServicesBuilder();
    $queueRestProxy->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Visualizzare il messaggio successivo
È possibile visualizzare il messaggio (o i messaggi successivi) in cima a una coda senza rimuoverlo dalla coda chiamando il metodo **QueueRestProxy->peekMessages**. Per impostazione predefinita, il metodo **peekMessage** restituisce un solo messaggio, ma è possibile modificare tale valore con il metodo **PeekMessagesOptions->setNumberOfMessages**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Rimuovere il messaggio successivo dalla coda
Il codice consente di rimuovere un messaggio da una coda in due passaggi. Chiamare **QueueRestProxy->listMessages** per rendere il messaggio invisibile a tutte le altre letture del codice dalla coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. (Se il messaggio non viene eliminato in questo periodo di tempo, diventerà nuovamente visibile nella coda.) Per completare la rimozione del messaggio dalla coda, è necessario chiamare **QueueRestProxy->deleteMessage**. Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **deleteMessage** immediatamente dopo l'elaborazione del messaggio.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueRestProxy->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Cambiare il contenuto di un messaggio in coda
È possibile modificare il contenuto di un messaggio inserito nella coda chiamando **QueueRestProxy->updateMessage**. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. Il codice seguente consente di aggiornare il messaggio in coda con nuovo contenuto e di impostarne il timeout di visibilità per prolungarlo di altri 60 secondi. In questo modo lo stato del lavoro associato al messaggio viene salvato e il client ha a disposizione un altro minuto per continuare l'elaborazione del messaggio. È possibile utilizzare questa tecnica per tenere traccia di flussi di lavoro composti da più passaggi nei messaggi in coda, senza la necessità di ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di errori hardware o software. In genere, è consigliabile mantenere anche un conteggio dei tentativi, in modo da eliminare i messaggi per cui vengono effettuati più di *n* tentativi. In questo modo è possibile evitare che un messaggio attivi un errore dell'applicazione ogni volta che viene elaborato.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueRestProxy->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueRestProxy->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Opzioni aggiuntive per rimuovere i messaggi dalla coda
È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di visibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo **getMessages** per recuperare 16 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo **for** . Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueRestProxy->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Recuperare la lunghezza della coda
È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **QueueRestProxy->getQueueMetadata** chiede al servizio di accodamento di restituire i metadati relativi alla coda. La chiamata al metodo **getApproximateMessageCount** sull'oggetto restituito fornisce il numero di messaggi presenti in una coda. Il conteggio è solo approssimativo, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Eliminare una coda
Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **QueueRestProxy->deleteQueue**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create queue REST proxy.
$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

try    {
    // Delete queue.
    $queueRestProxy->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base sull'archiviazione delle code, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

* [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/).

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Portale di Azure]: https://portal.azure.com




<!--HONumber=Nov16_HO3-->


