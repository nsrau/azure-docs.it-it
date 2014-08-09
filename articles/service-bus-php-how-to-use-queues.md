<properties linkid="develop-php-how-to-guides-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (PHP) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues PHP" description="Learn how to use Service Bus queues in Azure. Code samples written in PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Queues" authors="" solutions="" manager="" editor="" />

Come utilizzare le code del bus di servizio
===========================================

In questa guida verrà descritto come utilizzare le code del bus di servizio con PHP. Gli esempi sono scritti in PHP e utilizzano [Azure SDK per PHP](http://go.microsoft.com/fwlink/?LinkId=252473). Gli scenari presentati includono **creazione di code**, **invio e ricezione di messaggi**, nonché **eliminazione di code**.

Sommario
--------

-   [Informazioni sulle code del bus di servizio](#what-are-service-bus-queues)
-   [Creazione di uno spazio dei nomi servizio](#create-a-service-namespace)
-   [Recupero delle credenziali di gestione predefinite per lo spazio dei nomi](#obtain-default-credentials)
-   [Creazione di un'applicazione PHP](#CreateApplication)
-   [Acquisizione delle librerie client di Azure](#GetClientLibrary)
-   [Configurazione dell'applicazione per l'utilizzo del bus di servizio](#ConfigureApp)
-   [Procedura: Creare una coda](#CreateQueue)
-   [Procedura: Inviare messaggi a una coda](#SendMessages)
-   [Procedura: Ricevere messaggi da una coda](#ReceiveMessages)
-   [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione](#HandleCrashes)
-   [Passaggi successivi](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

Creazione di un'applicazione PHP
--------------------------------

Per creare un'applicazione PHP che accede al servizio BLOB di Azure, è sufficiente fare riferimento alle classi in [Azure SDK per PHP](http://go.microsoft.com/fwlink/?LinkId=252473) dall'interno del codice. Per creare l'applicazione, è possibile utilizzare qualsiasi strumento di sviluppo, incluso il Blocco note.

> [WACOM.NOTE]
> L'installazione di PHP deve avere l'estensione [OpenSSL](http://php.net/openssl) installata e abilitata.

In questa guida si utilizzeranno le funzionalità del servizio che possono essere chiamate in un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

Acquisizione delle librerie client di Azure
-------------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Configurazione dell'applicazione per l'utilizzo del bus di servizio
-------------------------------------------------------------------

Per utilizzare le API delle code del bus di servizio di Azure, è necessario:

1.  Fare riferimento al file autoloader mediante l'istruzione [require\_once](http://php.net/require_once).
2.  Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe **ServicesBuilder**.

> [WACOM.NOTE]
> In questo esempio (e in altri esempi in questo articolo) si presuppone che siano state installate le librerie client PHP per Azure tramite Composer. Se le librerie sono state installate manualmente o come pacchetto PEAR, sarà necessario fare riferimento al file autoloader `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

Negli esempi seguenti, l'istruzione `require_once` verrà sempre visualizzata, ma si farà riferimento solo alle classi necessarie per eseguire l'esempio.

Configurazione di una connessione del bus di servizio di Azure
--------------------------------------------------------------

Per creare un'istanza di un client del bus di servizio di Azure, è necessario innanzitutto disporre di una stringa di connessione valida conforme al formato seguente:

    Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Dove l'endpoint è in genere nel formato `https://[yourNamespace].servicebus.windows.net`.

Per creare un client di servizio di Azure, è necessario utilizzare la classe **ServicesBuilder**. È possibile:

-   passare la stringa di connessione direttamente a essa o
-   utilizzare **CloudConfigurationManager (CCM)** per cercare la stringa di connessione in più origini esterne:
    -   per impostazione predefinita viene fornito con il supporto per un'origine esterna, ovvero le variabili ambientali
    -   è possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource**

Per gli esempi illustrati in questo articolo, la stringa di connessione verrà passata direttamente.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

Procedura: Creare una coda
--------------------------

Per eseguire operazioni di gestione per le code del bus di servizio, è possibile utilizzare la classe **ServiceBusRestProxy** class. Un oggetto **ServiceBusRestProxy** viene costruito tramite il metodo factory **ServicesBuilder::createServiceBusService** con una stringa di connessione appropriata che incapsula le autorizzazioni di token per la relativa gestione.

Nell'esempio seguente viene illustrato come creare un'istanza di **ServiceBusRestProxy** e chiamare **ServiceBusRestProxy-\>createQueue** per creare una coda denominata `myqueue` all'interno di uno spazio dei nomi servizio `MySBNamespace`:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\QueueInfo;

    // Create Service Bus REST proxy.
        $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {
        $queueInfo = new QueueInfo("myqueue");
        
        // Create queue.
        $serviceBusRestProxy->createQueue($queueInfo);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

> [WACOM.NOTE]
> È possibile utilizzare il metodo **listQueues** su oggetti **ServiceBusRestProxy** per verificare se in uno spazio dei nomi servizio esiste già una coda con il nome specificato.

Procedura: Inviare messaggi a una coda
--------------------------------------

Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiamerà il metodo **ServiceBusRestProxy-\>sendQueueMessage**. Nel codice seguente viene illustrato come inviare un messaggio per la coda `myqueue` creata in precedenza all'interno dello spazio dei nomi servizio `MySBNamespace`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\models\BrokeredMessage;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Create message.
        $message = new BrokeredMessage();
        $message->setBody("my message");

        // Send message.
        $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/it-it/library/windowsazure/hh780775
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

I messaggi inviati e ricevuti dalla coda del bus di servizio sono istanze della classe **BrokeredMessage**. Gli oggetti **BrokeredMessage** includono un set di metodi standard, ad esempio **getLabel**, **getTimeToLive**, **setLabel** e **setTimeToLive**, nonché proprietà dell'applicazione utilizzate per contenere proprietà specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione.

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Il limite massimo della dimensione di una coda è di 5 GB.

Procedura: Ricevere messaggi da una coda
----------------------------------------

Il modo principale per ricevere i messaggi da una coda consiste nell'utilizzare un metodo **ServiceBusRestProxy-\>receiveQueueMessage**. È possibile ricevere i messaggi in due diverse modalità: **ReceiveAndDelete** (predefinito) e **PeekLock**.

Quando si utilizza la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un messaggio in una coda, lo contrassegna come utilizzato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da utilizzare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione passando il messaggio ricevuto a **ServiceBusRestProxy-\>deleteMessage**. Quando il bus di servizio vede la chiamata **deleteMessage**, contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi utilizzando la modalità **PeekLock** non predefinita.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Set the receive mode to PeekLock (default is ReceiveAndDelete).
        $options = new ReceiveMessageOptions();
        $options->setPeekLock();
        
        // Receive message.
        $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
        echo "Body: ".$message->getBody()."<br />";
        echo "MessageID: ".$message->getMessageId()."<br />";
        
        /*---------------------------
            Process message here.
        ----------------------------*/
        
        // Delete message. Not necessary if peek lock is not set.
        echo "Message deleted.<br />";
        $serviceBusRestProxy->deleteMessage($message);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/it-it/library/windowsazure/hh780735
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione
---------------------------------------------------------------------------

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlockMessage**, anziché **deleteMessage**, sul messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'invio della richiesta **deleteMessage**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, è consigliabile aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso utilizzato il metodo **getMessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base sulle code del bus di servizio, vedere l'argomento [Code, argomenti e sottoscrizioni](http://msdn.microsoft.com/it-it/library/windowsazure/hh367516.aspx) su MSDN per ulteriori informazioni.

