<properties 
	pageTitle="Come usare le code del bus di servizio con (PHP) | Microsoft Azure" 
	description="Informazioni su come usare le code del bus di servizio in Azure. Gli esempi di codice sono scritti in PHP." 
	services="service-bus" 
	documentationCenter="php" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/14/2015" 
	ms.author="sethm"/>

# Come usare le code del bus di servizio

Questa guida illustra come usare le code del bus di servizio. Gli esempi sono scritti in PHP e utilizzano [Azure SDK per PHP](../php-download-sdk.md). Gli scenari presentati includono **creazione di code**, **invio e ricezione di messaggi**, nonché **eliminazione di code**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Creazione di un'applicazione PHP

Per creare un'applicazione PHP che accede al servizio BLOB di Azure, è sufficiente fare riferimento alle classi in [Azure SDK per PHP](../php-download-sdk.md) dall'interno del codice. Per creare l'applicazione, è possibile usare qualsiasi strumento di sviluppo o il Blocco note.

> [AZURE.NOTE]L'installazione di PHP deve avere anche l'[estensione OpenSSL](http://php.net/openssl) installata e abilitata.

In questa guida si useranno le funzionalità del servizio che possono essere chiamate da un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

## Acquisire le librerie client di Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Configurare l'applicazione per l'uso del bus di servizio

Per usare le API delle code del bus di servizio di Azure, effettuare quanto segue:

1. Fare riferimento al file autoloader mediante l'istruzione [require\_once][require_once].
2. Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe **ServicesBuilder**.

> [AZURE.NOTE]In questo esempio (e in altri esempi in questo articolo) si presuppone che siano state installate le librerie client PHP per Azure tramite Composer. Se le librerie sono state installate manualmente o come pacchetto PEAR, è necessario fare riferimento al file autoloader **WindowsAzure.php**.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;

Nei seguenti esempi l'istruzione `require_once` verrà sempre visualizzata, ma si fa riferimento solo alle classi necessarie per eseguire l'esempio.

## Configurare una connessione del bus di servizio di Azure

Per creare un'istanza di un client del bus di servizio, è necessario innanzitutto disporre di una stringa di connessione valida nel seguente formato:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Dove **Endpoint** è in genere nel formato `https://[yourNamespace].servicebus.windows.net`.

Per creare un client di servizio di Azure, è necessario usare la classe **ServicesBuilder**. È possibile:

* Passare la stringa di connessione direttamente.
* Usare **CloudConfigurationManager (CCM)** per cercare la stringa di connessione in più origini esterne:
	* Per impostazione predefinita viene fornito con il supporto per un'origine esterna, ovvero le variabili ambientali
	* È possibile aggiungere nuove origini estendendo la classe **ConnectionStringSource**

Per gli esempi illustrati in questo articolo, la stringa di connessione verrà passata direttamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);


## Procedura: Creare una coda

È possibile eseguire operazioni di gestione per le code del bus di servizio utilizzando la classe **ServiceBusRestProxy**. Un oggetto **ServiceBusRestProxy** viene costruito tramite il metodo factory **ServicesBuilder::createServiceBusService** con una stringa di connessione appropriata che incapsula le autorizzazioni di token per la relativa gestione.

Il seguente esempio illustra come creare un'istanza di **ServiceBusRestProxy** e chiamare**ServiceBusRestProxy->createQueue** per creare una coda denominata `myqueue` all'interno di uno spazio dei nomi del servizio `MySBNamespace`:

    require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\QueueInfo;

	// Create Service Bus REST proxy.
		$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		$queueInfo = new QueueInfo("myqueue");
		
		// Create queue.
		$serviceBusRestProxy->createQueue($queueInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [AZURE.NOTE]È possibile usare il metodo `listQueues` negli oggetti `ServiceBusRestProxy` per verificare se in uno spazio dei nomi servizio esiste già una coda con il nome specificato.

## Procedura: Inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiama il metodo **ServiceBusRestProxy->sendQueueMessage**. Il seguente codice illustra come inviare un messaggio alla coda `myqueue` creata in precedenza all'interno dello spazio dei nomi del servizio `MySBNamespace`.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendQueueMessage("myqueue", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

I messaggi inviati e ricevuti dalla coda del bus di servizio sono istanze della classe **BrokeredMessage**. Gli oggetti **BrokeredMessage** includono un set di metodi standard, ad esempio **getLabel**, **getTimeToLive**, **setLabel** e **setTimeToLive**, nonché proprietà dell'applicazione utilizzate per contenere proprietà specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione.

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Il limite massimo della dimensione di una coda è di 5 GB.

## Come ricevere messaggi da una coda

Il modo migliore per ricevere i messaggi da una coda consiste nell'usare un metodo **ServiceBusRestProxy->receiveQueueMessage**. È possibile ricevere messaggi in due modalità diverse: **ReceiveAndDelete** (predefinita) e **PeekLock**.

Quando si utilizza la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un messaggio in una coda, lo contrassegna come utilizzato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione passando il messaggio ricevuto a **ServiceBusRestProxy->deleteMessage**. Quando il bus di servizio vede la chiamata **deleteMessage**, contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

Il seguente esempio mostra come ricevere ed elaborare messaggi usando la modalità **PeekLock** (non predefinita).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
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
		// http://msdn.microsoft.com/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlockMessage**, anziché **deleteMessage**, sul messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'invio della richiesta **deleteMessage**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione,è consigliabile aggiungere logica aggiuntiva alle applicazioni per gestire il secondo recapito del messaggio. A tale scopo viene spesso usato il metodo **getMessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, vedere [Code, Argomenti e Sottoscrizioni][] per altre informazioni.

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di PHP](/develop/php/).

[Code, Argomenti e Sottoscrizioni]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 

<!---HONumber=Oct15_HO3-->