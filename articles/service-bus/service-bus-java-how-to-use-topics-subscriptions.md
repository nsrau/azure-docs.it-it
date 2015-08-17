<properties
	pageTitle="Come usare gli argomenti del bus di servizio (Java) - Azure"
	description="Informazioni su come usare le sottoscrizioni e gli argomenti di Bus di servizio in Azure. Gli esempi di codice sono scritti per applicazioni Java."
	services="service-bus"
	documentationCenter="java"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="06/19/2015"
	ms.author="sethm"/>

# Come usare gli argomenti e le sottoscrizioni del bus di servizio

Questa guida descrive come usare gli argomenti e le sottoscrizioni del bus di servizio. Gli esempi sono scritti in Java e utilizzano [Azure SDK for Java][]. Gli scenari presentati includono **creazione di argomenti e sottoscrizioni**, **creazione di filtri per le sottoscrizioni**, **invio di messaggi a un argomento**, **ricezione di messaggi da una sottoscrizione** ed **eliminazione di argomenti e sottoscrizioni**.

[AZURE.INCLUDE [service-bus-java-how-to-create-topic](../../includes/service-bus-java-how-to-create-topic.md)]

## Configurare l'applicazione per l'uso del bus di servizio
Assicurarsi di aver installato [Azure SDK per Java][] prima di compilare questo esempio. Se si utilizza Eclipse, è possibile installare [Azure Toolkit per Eclipse][] che include Azure SDK per Java. È quindi possibile aggiungere le **librerie di Microsoft Azure per Java** al progetto: ![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Aggiungere le seguenti istruzioni import all'inizio del file Java:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    import com.microsoft.windowsazure.core.*;
    import javax.xml.datatype.*;

Aggiungere le librerie di Azure per Java al percorso di compilazione e includerlo nell'assembly di distribuzione del progetto.

## Come creare un argomento

Per eseguire operazioni di gestione per le code del bus di servizio, è possibile utilizzare la classe **ServiceBusContract** class. Un oggetto **ServiceBusContract** è costruito con una configurazione appropriata che incapsula il token di firma di accesso condiviso con le autorizzazioni necessarie a gestirlo. La classe **ServiceBusContract** rappresenta l'unico punto di comunicazione con Azure.

La classe **ServiceBusService** fornisce i metodi per creare, enumerare ed eliminare le code. Nell'esempio seguente viene illustrato il modo in cui è possibile utilizzare un oggetto **ServiceBusService** per creare un argomento denominato "TestTopic" con uno spazio dei nomi denominato "HowToSample":

    Configuration config =
    	ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

	ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
	try  
	{
    	CreateTopicResult result = service.createTopic(topicInfo);
	}
	catch (ServiceException e) {
		System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
		System.exit(-1);
	}

In **TopicInfo** sono disponibili metodi che consentono di ottimizzare le proprietà dell'argomento, ad esempio per impostare il valore di durata TTL predefinito da applicare ai messaggi inviati all'argomento. Il seguente esempio illustra come creare un argomento denominato "TestTopic" con una dimensione massima pari a 5 GB:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Si noti che è possibile utilizzare il metodo **listTopics** su oggetti **ServiceBusContract** per verificare se in uno spazio dei nomi servizio esiste già un argomento con il nome specificato.

## Come creare le sottoscrizioni

È possibile creare le sottoscrizioni a un argomento tramite la classe **ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi passati alla coda virtuale della sottoscrizione.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene utilizzato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si utilizza il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata "AllMessages" e viene utilizzato il filtro predefinito **MatchAll**.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### Creare sottoscrizioni con i filtri

È inoltre possibile configurare filtri che consentono di specificare i messaggi inviati a un argomento da visualizzare in una sottoscrizione all'argomento specifica.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per ulteriori dettagli sulle espressioni che è possibile utilizzare con un filtro SQL, esaminare la sintassi di SqlFilter.SqlExpression.

Nell'esempio seguente viene creata una sottoscrizione denominata "HighMessages" con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata **MessageNumber** è maggiore di 3:

    // Create a "HighMessages" filtered subscription  
	SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result =
		service.createSubscription("TestTopic", subInfo);
	RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
	CreateRuleResult ruleResult =
		service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

Analogamente, nel seguente esempio viene creata una sottoscrizione denominata "LowMessages" con un filtro SqlFilter che seleziona solo i messaggi in cui il valore della proprietà MessageNumber è minore o uguale a 3:

    // Create a "LowMessages" filtered subscription
	SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
	CreateSubscriptionResult result =
		service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
	CreateRuleResult ruleResult =
		service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


Un messaggio inviato a "TestTopic" verrà sempre recapitato ai ricevitori con sottoscrizione all'argomento "AllMessages" e recapitato selettivamente ai ricevitori con sottoscrizioni agli argomenti "HighMessages" e "LowMessages", a seconda del contenuto del messaggio.

## Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, l'applicazione otterrà un oggetto **ServiceBusContract**. Nel codice seguente viene illustrato come inviare un messaggio per l'argomento "TestTopic" creato in precedenza all'interno dello spazio dei nomi servizio "HowToSample":

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

I messaggi inviati ad argomenti del bus di servizio sono istanze della classe **BrokeredMessage**. Gli oggetti **BrokeredMessage** includono un insieme di metodi standard, ad esempio **setLabel** e **TimeToLive**, un dizionario utilizzato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Per impostare il corpo del messaggio, un'applicazione può passare qualsiasi oggetto serializzabile nel costruttore di **BrokeredMessage**. In tal caso per serializzare l'oggetto, verrà utilizzato l'oggetto **DataContractSerializer** appropriato. In alternativa, è possibile fornire un oggetto **java.io.InputStream**.

Nell'esempio seguente viene illustrato come inviare cinque messaggi di prova all'oggetto **MessageSender** "TestTopic" ottenuto nel frammento di codice sopra riportato: Si noti come il valore della proprietà **MessageNumber** di ciascun messaggio varia nell'iterazione del ciclo, determinando le sottoscrizioni che lo riceveranno:

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 MB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 MB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## Come ricevere messaggi da una sottoscrizione

Il modo principale per ricevere i messaggi da una sottoscrizione consiste nell'utilizzare un oggetto **ServiceBusContract**. I messaggi ricevuti possono essere utilizzati in due modalità diverse: **ReceiveAndDelete** e **PeekLock**.

Quando si utilizza la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un messaggio, lo contrassegna come utilizzato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando **Delete** sul messaggio ricevuto. Quando il bus di servizio vede la chiamata **Delete**, contrassegna il messaggio come utilizzato e lo rimuove dall'argomento.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi utilizzando la modalità **PeekLock** non predefinita. Nel seguente esempio viene eseguito un ciclo, vengono elaborati i messaggi nella sottoscrizione "HighMessages" e infine si esce quando non vi sono più messaggi (in alternativa, l'esempio può essere impostato per attendere l'arrivo di nuovi messaggi).

	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

		while(true)  {
		    ReceiveSubscriptionMessageResult  resultSubMsg =
		        service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
		    BrokeredMessage message = resultSubMsg.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());
			    // Display the topic message.
			    System.out.print("From topic: ");
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
			        message.getProperty("MessageNumber"));
			    // Delete message.
			    System.out.println("Deleting this message.");
			    service.deleteMessage(message);
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

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlockMessage**, anziché **deleteMessage**, sul messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio nell'argomento rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nell'argomento è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'invio della richiesta **deleteMessage**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso utilizzato il metodo **getMessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## Come eliminare argomenti e sottoscrizioni

Il modo principale per eliminare argomenti e sottoscrizioni consiste nell'utilizzare un oggetto **ServiceBusContract**. Se si elimina un argomento, verranno eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sulle code del bus di servizio, vedere l'argomento [Code, argomenti e sottoscrizioni del bus di servizio][] su MSDN per ulteriori informazioni.

  [Azure SDK for Java]: http://azure.microsoft.com/develop/java/
  [Azure SDK per Java]: http://azure.microsoft.com/develop/java/
  [Azure Toolkit per Eclipse]: https://msdn.microsoft.com/it-it/library/azure/hh694271.aspx
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Configure Your Application to Use Service Bus]: #bkmk_ConfigYourApp
  [How to: Create a Topic]: #bkmk_HowToCreateTopic
  [How to: Create Subscriptions]: #bkmk_HowToCreateSubscrip
  [How to: Send Messages to a Topic]: #bkmk_HowToSendMsgs
  [How to: Receive Messages from a Subscription]: #bkmk_HowToReceiveMsgs
  [How to: Handle Application Crashes and Unreadable Messages]: #bkmk_HowToHandleAppCrash
  [How to: Delete Topics and Subscriptions]: #bkmk_HowToDeleteTopics
  [Next Steps]: #bkmk_NextSteps
  [Service Bus Topics diagram]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Azure Management Portal]: http://manage.windowsazure.com/
  [Service Bus Node screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Create a New Namespace ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Namespace List screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Properties Pane screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Default Key screenshot]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Code, argomenti e sottoscrizioni del bus di servizio]: http://msdn.microsoft.com/library/hh367516.aspx
 

<!---HONumber=August15_HO6-->