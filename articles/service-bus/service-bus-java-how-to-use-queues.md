<properties
	pageTitle="Come usare le code del bus di servizio (Java) - Azure"
	description="Informazioni su come usare le code di Bus di servizio in Azure. Gli esempi di codice sono scritti in Java."
	services="service-bus"
	documentationCenter="java"
	authors="sethmanheim"
	manager="timlt"
	/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="06/19/2015"
	ms.author="sethm"/>

# Come usare le code del bus di servizio

Questa guida illustra come usare le code del bus di servizio. Gli esempi sono scritti in Java e utilizzano [Azure SDK for Java][]. Gli scenari presentati includono **creazione di code**, **invio e ricezione di messaggi**, nonché **eliminazione di code**.

[AZURE.INCLUDE [service-bus-java-how-to-create-queue](../../includes/service-bus-java-how-to-create-queue.md)]

## Configurare l'applicazione per l'uso del bus di servizio
Assicurarsi di aver installato [Azure SDK per Java][] prima di compilare questo esempio. Se si utilizza Eclipse, è possibile installare [Azure Toolkit per Eclipse][] che include Azure SDK per Java. È quindi possibile aggiungere le **librerie di Microsoft Azure per Java** al progetto: ![](media/service-bus-java-how-to-use-queues/eclipselibs.png)

Aggiungere le seguenti istruzioni import all'inizio del file Java:

	// Include the following imports to use Service Bus APIs
	import com.microsoft.windowsazure.services.servicebus.*;
	import com.microsoft.windowsazure.services.servicebus.models.*;
	import com.microsoft.windowsazure.core.*;
	import javax.xml.datatype.*;

## Come creare una coda

Per eseguire operazioni di gestione per le code del bus di servizio, è possibile utilizzare la classe **ServiceBusContract** class. Un oggetto **ServiceBusContract** è costruito con una configurazione appropriata che incapsula il token di firma di accesso condiviso con le autorizzazioni necessarie a gestirlo. La classe **ServiceBusContract** rappresenta l'unico punto di comunicazione con Azure.

La classe **ServiceBusService** fornisce i metodi per creare, enumerare ed eliminare le code. Nell'esempio seguente viene illustrato il modo in cui è possibile utilizzare un oggetto **ServiceBusService** per creare una coda denominata "TestQueue" con uno spazio dei nomi denominato "HowToSample":

		Configuration config =
			ServiceBusConfiguration.configureWithSASAuthentication(
					"HowToSample",
					"RootManageSharedAccessKey",
					"SAS_key_value",
					".servicebus.windows.net"
					);

    ServiceBusContract service = ServiceBusService.create(config);
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    try
    {
		CreateQueueResult result = service.createQueue(queueInfo);
    }
	catch (ServiceException e)
	{
	    System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

In QueueInfo sono disponibili metodi che consentono di ottimizzare le proprietà della coda, ad esempio per impostare il valore di durata TTL predefinito da applicare ai messaggi inviati alla coda. Il seguente esempio illustra come creare una coda denominata "TestQueue" con una dimensione massima pari a 5 GB:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateQueueResult result = service.createQueue(queueInfo);

Si noti che è possibile utilizzare il metodo **listQueues** su oggetti **ServiceBusContract** per verificare se in uno spazio dei nomi servizio esiste già una coda con il nome specificato.

## Come inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, l'applicazione otterrà un oggetto **ServiceBusContract**. Il codice seguente illustra come inviare un messaggio per la coda "TestQueue" creata in precedenza all'interno dello spazio dei nomi servizio "HowToSample":

    try
    {
        BrokeredMessage message = new BrokeredMessage("MyMessage");
        service.sendQueueMessage("TestQueue", message);
    }
    catch (ServiceException e)
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

I messaggi inviati e ricevuti dalla coda del bus di servizio sono istanze della classe **BrokeredMessage**. Gli oggetti **BrokeredMessage** includono un set di metodi standard, ad esempio **getLabel**, **getTimeToLive**, **setLabel** e **setTimeToLive**, un dizionario utilizzato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Per impostare il corpo del messaggio, un'applicazione può passare qualsiasi oggetto serializzabile nel costruttore di **BrokeredMessage**. In tal caso, per serializzare l'oggetto verrà utilizzato il serializzatore appropriato. In alternativa, è possibile fornire un oggetto **java.IO.InputStream**.

Nell'esempio seguente viene illustrato come inviare cinque messaggi di prova all'oggetto **MessageSender** "TestQueue" ottenuto nel frammento di codice sopra riportato:

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i);
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## Come ricevere messaggi da una coda

Il modo principale per ricevere i messaggi da una coda consiste nell'utilizzare un oggetto **ServiceBusContract**. I messaggi ricevuti possono essere utilizzati in due modalità diverse: **ReceiveAndDelete** e **PeekLock**.

Quando si utilizza la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un messaggio in una coda, lo contrassegna come utilizzato e lo restituisce all'applicazione. La modalità predefinita **ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando **Delete** sul messaggio ricevuto. Quando il bus di servizio vede la chiamata **Delete**, contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi utilizzando la modalità **PeekLock** non predefinita. Nel seguente esempio viene creato un ciclo infinito e i messaggi vengono elaborati non appena arrivano in "TestQueue":

    	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

		while(true)  {
	         ReceiveQueueMessageResult resultQM =
	     			service.receiveQueueMessage("TestQueue", opts);
		    BrokeredMessage message = resultQM.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());
			    // Display the queue message.
			    System.out.print("From queue: ");
			    byte[] b = new byte[200];
			    String s = null;
			    int numRead = message.getBody().read(b);
			    while (-1 != numRead)
	            {
	                s = new String(b);
	                s = s.trim();
	                System.out.print(s);
	                numRead = message.getBody().read(b);
			    }
	            System.out.println();
			    System.out.println("Custom Property: " +
			        message.getProperty("MyProperty"));
			    // Remove message from queue.
			    System.out.println("Deleting this message.");
			    //service.deleteMessage(message);
		    }  
		    else  
		    {
		        System.out.println("Finishing up - no more messages.");
		        break;
		        // Added to handle no more messages.
		        // Could instead wait for more messages to be added.
		    }
	    }
	}
	catch (ServiceException e) {
	    System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}
	catch (Exception e) {
	    System.out.print("Generic exception encountered: ");
	    System.out.println(e.getMessage());
	    System.exit(-1);
	}

## Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlockMessage**, anziché **deleteMessage**, sul messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'invio della richiesta **deleteMessage**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso utilizzato il metodo **getMessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sulle code del bus di servizio, vedere l'argomento [Code, argomenti e sottoscrizioni][] su MSDN per ulteriori informazioni.

  [Azure SDK for Java]: http://azure.microsoft.com/develop/java/
  [Azure SDK per Java]: http://azure.microsoft.com/develop/java/
  [Azure Toolkit per Eclipse]: https://msdn.microsoft.com/it-it/library/azure/hh694271.aspx
  [What are Service Bus Queues?]: #what-are-service-bus-queues
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Configure Your Application to Use Service Bus]: #bkmk_ConfigApp
  [How to: Create a Security Token Provider]: #bkmk_HowToCreateQueue
  [How to: Send Messages to a Queue]: #bkmk_HowToSendMsgs
  [How to: Receive Messages from a Queue]: #bkmk_HowToReceiveMsgs
  [How to: Handle Application Crashes and Unreadable Messages]: #bkmk_HowToHandleAppCrashes
  [Next Steps]: #bkmk_NextSteps
  [Azure Management Portal]: http://manage.windowsazure.com/
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
 

<!---HONumber=July15_HO4-->