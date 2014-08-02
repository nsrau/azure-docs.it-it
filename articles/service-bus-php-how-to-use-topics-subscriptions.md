<properties linkid="develop-php-how-to-guides-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (PHP) - Azure" metaKeywords="" description="Learn how to use Service Bus topics with PHP in Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Topics/Subscriptions" authors="" solutions="" manager="" editor="" />

Come utilizzare gli argomenti e le sottoscrizioni del bus di servizio
=====================================================================

In questa guida verrà descritto come utilizzare gli argomenti e le sottoscrizioni del bus di servizio. Gli esempi sono scritti in PHP e utilizzano [Azure SDK per PHP](http://go.microsoft.com/fwlink/?LinkId=252473). Gli scenari presentati includono **creazione di argomenti e sottoscrizioni**, **creazione di filtri per le sottoscrizioni**, **invio di messaggi a un argomento**, **ricezione di messaggi da una sottoscrizione** ed **eliminazione di argomenti e sottoscrizioni**.

Sommario
--------

-   [Informazioni su argomenti e sottoscrizioni del bus di servizio](#what-are-service-bus-topics)
-   [Creazione di uno spazio dei nomi servizio](#create-a-service-namespace)
-   [Recupero delle credenziali di gestione predefinite per lo spazio dei nomi](#obtain-default-credentials)
-   [Creazione di un'applicazione PHP](#CreateApplication)
-   [Acquisizione delle librerie client di Azure](#GetClientLibrary)
-   [Configurazione dell'applicazione per l'utilizzo del bus di servizio](#ConfigureApp)
-   [Procedura: Creare un argomento](#CreateTopic)
-   [Procedura: Creare una sottoscrizione](#CreateSubscription)
-   [Procedura: Inviare messaggi a un argomento](#SendMessage)
-   [Procedura: Ricevere messaggi da una sottoscrizione](#ReceiveMessages)
-   [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione](#HandleCrashes)
-   [Procedura: Eliminare argomenti e sottoscrizioni](#DeleteTopicsAndSubscriptions)
-   [Passaggi successivi](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

Creazione di un'applicazione PHP
--------------------------------

Per creare un'applicazione PHP che accede al servizio BLOB di Azure, è sufficiente fare riferimento alle classi in [Azure SDK per PHP](http://go.microsoft.com/fwlink/?LinkId=252473) dall'interno del codice. Per creare l'applicazione, è possibile utilizzare qualsiasi strumento di sviluppo, incluso il Blocco note.

> [WACOM.NOTE] L'installazione di PHP deve avere l'estensione [OpenSSL](http://php.net/openssl) installata e abilitata.

In questa guida si utilizzeranno le funzionalità del servizio che possono essere chiamate in un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

Acquisizione delle librerie client di Azure
-------------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Configurazione dell'applicazione per l'utilizzo del bus di servizio
-------------------------------------------------------------------

Per utilizzare le API degli argomenti del bus di servizio di Azure, è necessario:

1.  Fare riferimento al file autoloader mediante l'istruzione [require\_once](http://php.net/require_once).
2.  Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe **ServiceBusService**.

    > [WACOM.NOTE]
    > In questo esempio (e in altri esempi in questo articolo) si presuppone che siano state installate le librerie client PHP per Azure tramite Composer. Se le librerie sono state installate manualmente o come pacchetto PEAR, sarà necessario fare riferimento al file autoloader <code>WindowsAzure.php</code>.

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

Procedura: Creare un argomento
------------------------------

Per eseguire operazioni di gestione per le code del bus di servizio, è possibile utilizzare la classe **ServiceBusRestProxy** class. Un oggetto **ServiceBusRestProxy** viene costruito tramite il metodo factory **ServicesBuilder::createServiceBusService** con una stringa di connessione appropriata che incapsula le autorizzazioni di token per la relativa gestione.

Nell'esempio seguente viene illustrato come creare un'istanza di **ServiceBusRestProxy** e chiamare **ServiceBusRestProxy-\>createTopic** per creare un argomento denominato `mytopic` all'interno di uno spazio dei nomi servizio `MySBNamespace`:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\TopicInfo;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
        // Create topic.
        $topicInfo = new TopicInfo("mytopic");
        $serviceBusRestProxy->createTopic($topicInfo);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    > [WACOM.NOTE]
    > È possibile utilizzare il metodo <b>listTopics</b> su <b>ServiceBusRestProxy</b> per verificare se in uno spazio dei nomi servizio esiste già un argomento con il nome specificato.

Procedura: Creare una sottoscrizione
------------------------------------

È possibile utilizzare il metodo **ServiceBusRestProxy-\>createSubscription** per creare sottoscrizioni ad argomenti. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi passati alla coda virtuale della sottoscrizione.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene utilizzato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si utilizza il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata 'mysubscription' e viene utilizzato il filtro predefinito **MatchAll**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {
        // Create subscription.
        $subscriptionInfo = new SubscriptionInfo("mysubscription");
        $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

### Creare sottoscrizioni con i filtri

È inoltre possibile configurare filtri che consentono di specificare i messaggi inviati a un argomento da visualizzare in una sottoscrizione all'argomento specifica. Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per ulteriori informazioni su SqlFilters, vedere [SqlFilter.SqlExpression Property](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

    > [WACOM.NOTE]
    > Ogni regola in una sottoscrizione elabora i messaggi in arrivo indipendentemente, aggiungendo i messaggi risultanti alla sottoscrizione stessa. Ogni nuova sottoscrizione presenta inoltre un oggetto <b>Rule</b> con un filtro che aggiunge tutti i messaggi dall'argomento alla sottoscrizione. Per ricevere solo messaggi corrispondenti al filtro in uso, è necessario rimuovere la regola predefinita. È possibile rimuovere la regola predefinita utilizzando il metodo <b>ServiceBusRestProxy->deleteRule</b>.

Nell'esempio seguente viene creata una sottoscrizione denominata "HighMessages" con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata **MessageNumber** è maggiore di 3 (per ulteriori informazioni sull'aggiunta di proprietà personalizzate ai messaggi, vedere [Procedura: Inviare messaggi a un argomento](#SendMessage)):

	$subscriptionInfo = new SubscriptionInfo("HighMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

  	$ruleInfo = new RuleInfo("HighMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber > 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Si noti che il codice sopra richiede l'utilizzo di uno spazio dei nomi aggiuntivo: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Analogamente, nell'esempio seguente viene creata una sottoscrizione denominata "LowMessages" con un filtro SqlFilter che seleziona solo i messaggi in cui il valore della proprietà MessageNumber è minore o uguale a 3:

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

Un messaggio inviato all'argomento `mytopic` verrà ora sempre recapitato ai ricevitori aderenti alla sottoscrizione `mysubscription` e recapitato selettivamente ai ricevitori aderenti alla sottoscrizione "HighMessages e "LowMessages", a seconda del contenuto del messaggio.

Procedura: Inviare messaggi a un argomento
------------------------------------------

Per inviare un messaggio a in argomento del bus di servizio, l'applicazione chiamerà il metodo **ServiceBusRestProxy-\>sendTopicMessage**. Nel codice seguente viene illustrato come inviare un messaggio per l'argomento `mytopic` creato in precedenza all'interno dello spazio dei nomi servizio `MySBNamespace`.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\BrokeredMessage;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Create message.
        $message = new BrokeredMessage();
        $message->setBody("my message");

        // Send message.
        $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/hh780775
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

I messaggi inviati ad argomenti del bus di servizio sono istanze della classe **BrokeredMessage**. Gli oggetti **BrokeredMessage** includono un set di proprietà e metodi standard, ad esempio **getLabel**, **getTimeToLive**, **setLabel** e **setTimeToLive**, nonché proprietà dell'applicazione utilizzate per contenere proprietà specifiche dell'applicazione. Nell'esempio seguente viene illustrato come impostare cinque messaggi di test all'argomento `mytopic` creato in precedenza. Il metodo **setProperty** viene utilizzato per aggiungere una proprietà personalizzata (`MessageNumber`) a ogni messaggio. Si noti come il valore della proprietà `MessageNumber` varia per ogni messaggio, consentendo di determinare quali sottoscrizioni lo riceveranno, come descritto nella sezione [Procedura: Creare una sottoscrizione](#CreateSubscription) più indietro in questo argomento:

    for($i = 0; $i < 5; $i++){
        // Create message.
        $message = new BrokeredMessage();
        $message->setBody("my message ".$i);
            
        // Set custom property.
        $message->setProperty("MessageNumber", $i);
            
        // Send message.
        $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
        }

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Il limite massimo della dimensione di una coda è di 5 GB.

Procedura: Ricevere messaggi da una sottoscrizione
--------------------------------------------------

Il modo principale per ricevere i messaggi da una sottoscrizione consiste nell'utilizzare un metodo **ServiceBusRestProxy-\>receiveSubscriptionMessage**. È possibile utilizzare i messaggi ricevuti in due diverse modalità: **ReceiveAndDelete** (predefinito) e **PeekLock**.

Quando si utilizza la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve una richiesta di lettura relativa a un messaggio in una sottoscrizione, lo contrassegna come utilizzato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da utilizzare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione passando il messaggio ricevuto a **ServiceBusRestProxy-\>deleteMessage**. Quando il bus di servizio vede la chiamata **deleteMessage**, contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi utilizzando la modalità **PeekLock** non predefinita.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Set receive mode to PeekLock (default is ReceiveAndDelete)
        $options = new ReceiveMessageOptions();
        $options->setPeekLock();

        // Get message.
        $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", 
                                                                    "mysubscription", 
                                                                    $options);
        echo "Body: ".$message->getBody()."<br />";
        echo "MessageID: ".$message->getMessageId()."<br />";
        
        /*---------------------------
            Process message here.
        ----------------------------*/
        
        // Delete message. Not necessary if peek lock is not set.
        echo "Deleting message...<br />";
        $serviceBusRestProxy->deleteMessage($message);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/en-us/library/windowsazure/hh780735
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione
---------------------------------------------------------------------------

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlockMessage**, anziché **deleteMessage**, sul messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'invio della richiesta **deleteMessage**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso utilizzato il metodo **getMessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

Come eliminare argomenti e sottoscrizioni
-----------------------------------------

Per eliminare un argomento o sottoscrizione, utilizzare rispettivamente i metodi **ServiceBusRestProxy-\>deleteTopic** o **ServiceBusRestProxy-\>deleteSubscripton**. Si noti che l'eliminazione di un argomento determina anche l'eliminazione di tutte le sottoscrizioni registrate con l'argomento stesso.

Nell'esempio seguente viene illustrato come eliminare un argomento (`mytopic`) con le relative sottoscrizioni registrate.

    require_once 'vendor\autoload.php';

    use WindowsAzure\ServiceBus\ServiceBusService;
    use WindowsAzure\ServiceBus\ServiceBusSettings;
    use WindowsAzure\Common\ServiceException;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
        // Delete topic.
        $serviceBusRestProxy->deleteTopic("mytopic");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

È possibile eliminare una sottoscrizione in modo indipendente utilizzando il metodo **deleteSubscription**:

    $serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base sulle code del bus di servizio, vedere l'argomento [Code, argomenti e sottoscrizioni](http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx) su MSDN per ulteriori informazioni.

