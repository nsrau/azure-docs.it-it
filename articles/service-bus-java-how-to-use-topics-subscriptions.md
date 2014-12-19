<properties urlDisplayName="Service Bus Topics" pageTitle="Come usare gli argomenti del bus di servizio (Java) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Java" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Java applications." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Come usare gli argomenti e le sottoscrizioni del bus di servizio

Questa guida mostra come usare gli argomenti e le sottoscrizioni
del bus di servizio. Gli esempi sono scritti in Java e usano [Azure SDK per Java][]. Gli scenari presentati includono **creazione di argomenti
e sottoscrizioni**, **creazione di filtri per le sottoscrizioni**, **invio
di messaggi a un argomento**, **ricezione di messaggi da una sottoscrizione** ed
**eliminazione di argomenti e sottoscrizioni**.

## Sommario

-   [Informazioni su argomenti e sottoscrizioni del bus di servizio][]
-   [Creare uno spazio dei nomi servizio][]
-   [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi][]
-   [Configurare l'applicazione per l'uso del bus di servizio][]
-   [Procedura: Creare un argomento][]
-   [Procedura: Creare sottoscrizioni][]
-   [Procedura: Inviare messaggi a un argomento][]
-   [Procedura: Ricevere messaggi da una sottoscrizione][]
-   [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione][]
-   [Procedura: Eliminare argomenti e sottoscrizioni][]
-   [Passaggi successivi][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>Configurare l'applicazione per l'uso del bus di servizio

Aggiungere le istruzioni import seguenti all'inizio del file Java:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Aggiungere le librerie di Azure per Java al percorso di compilazione e includerlo nell'assembly di distribuzione del progetto.

## <a name="bkmk_HowToCreateTopic"> </a>Come creare un argomento

Per eseguire operazioni di gestione per le code del bus di servizio, è possibile usare la classe
**ServiceBusContract**. Un oggetto **ServiceBusContract** è
costruito con una configurazione appropriata che incapsula le autorizzazioni dei
token necessarie a gestirlo. La classe **ServiceBusContract** rappresenta
l'unico punto di comunicazione con Azure.

La classe **ServiceBusService** fornisce i metodi per creare, enumerare
ed eliminare le code. Nell'esempio seguente viene illustrato il modo in cui è possibile usare un oggetto **ServiceBusService** 
per creare un argomento denominato "TestTopic" con uno spazio dei nomi denominato "HowToSample":

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

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

Alcuni metodi di **TopicInfo** consentono di ottimizzare le
proprietà dell'argomento, ad esempio impostando il valore di durata "TTL" da
applicare i messaggi inviati all'argomento. Nell'esempio seguente viene illustrato come
creare un argomento denominato "TestTopic" con una dimensione massima pari a 5 GB:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Si noti che è possibile usare il metodo **listTopics** su
oggetti **ServiceBusContract** per verificare se in uno spazio dei nomi servizio
esiste già un argomento con il nome specificato.

## <a name="bkmk_HowToCreateSubscrip"> </a>Come creare le sottoscrizioni

È possibile creare le sottoscrizioni a un argomento tramite la classe **ServiceBusService**
. Le sottoscrizioni sono denominate e possono includere un filtro
facoltativo che limita l'insieme dei messaggi passati alla coda virtuale
della sottoscrizione.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene usato se non vengono specificati altri filtri
durante la creazione di una nuova sottoscrizione. Quando si usa il filtro **MatchAll**
,
tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. L'esempio seguente crea una
sottoscrizione denominata "AllMessages" e usa il filtro predefinito **MatchAll**
.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Creare sottoscrizioni con i filtri

È anche possibile configurare filtri che consentono di specificare i messaggi inviati
a un argomento da visualizzare in una sottoscrizione all'argomento specifica.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è
**SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono
sulle proprietà dei messaggi pubblicati nell'argomento. Per
altri dettagli sulle espressioni che è possibile usare con un filtro SQL,
esaminare la sintassi di SqlFilter.SqlExpression.

Nell'esempio seguente viene creata una sottoscrizione denominata 'HighMessages' con un filtro
**SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata
**MessageNumber** è maggiore di 3:

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

Analogamente, nell'esempio seguente viene creata una sottoscrizione denominata
"LowMessages" con 
 un filtro SqlFilter che seleziona solo i messaggi in cui il valore della proprietà MessageNumber
è inferiore 
 o uguale a 3:

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


Un messaggio inviato a "TestTopic" verrà sempre
recapitato ai ricevitori con sottoscrizione all'argomento "AllMessages" e
recapitato selettivamente ai ricevitori con sottoscrizioni agli
argomenti "HighMessages" e "LowMessages", a seconda del contenuto
del messaggio.

## <a name="bkmk_HowToSendMsgs"> </a>Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, l'applicazione otterrà un oggetto
**ServiceBusContract**. Il codice seguente illustra come inviare un
messaggio per l'argomento "TestTopic" creato in precedenza all'interno dello spazio dei nomi servizio
"HowToSample":

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

I messaggi inviati ad argomenti del bus di servizio sono istanze della classe
**BrokeredMessage**. Gli oggetti **BrokeredMessage** includono un insieme
di metodi standard, ad esempio **setLabel** e **TimeToLive**, un dizionario
usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo
di dati arbitrari dell'applicazione. Per impostare il corpo del
, un'applicazione può passare qualsiasi oggetto serializzabile nel costruttore di
**BrokeredMessage**. In tal caso, per serializzare l'oggetto verrà usato il serializzatore **DataContractSerializer** appropriato
. In alternativa, è possibile fornire un oggetto
**java.io.InputStream**.

Nell'esempio seguente viene illustrato come inviare cinque messaggi di prova all'oggetto
"TestTopic" **MessageSender** "TestTopic" ottenuto nel frammento di codice sopra riportato.
Si noti come il valore della proprietà **MessageNumber** di ciascun messaggio varia
nell'iterazione del ciclo, determinando le sottoscrizioni che
lo riceveranno:

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 MB, in cui la dimensione massima dell'intestazione,
che include le proprietà standard e personalizzate dell'applicazione, non
può superare 64 MB. Non esiste alcun limite al numero di messaggi
mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi
di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un
limite massimo di 5 GB.

## <a name="bkmk_HowToReceiveMsgs"> </a>Come ricevere messaggi da una sottoscrizione

Il modo principale per ricevere i messaggi da una sottoscrizione consiste nell'usare un oggetto
**ServiceBusContract**. È possibile usare i messaggi ricevuti in due
diverse modalità: **ReceiveAndDelete** e **PeekLock**.

Quando si usa la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica
fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un
messaggio, lo contrassegna come usato e lo restituisce
all'applicazione. La modalità **ReceiveAndDelete** costituisce il modello più semplice ed
è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di
un messaggio in caso di errore. Per comprendere meglio
questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene
arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il
messaggio come usato, quando l'applicazione viene riavviata e inizia a usare
nuovamente i messaggi, il messaggio usato prima dell'arresto
anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da
consentire il supporto di applicazioni che non possono
tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, trova il
messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo
restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo
archiviato in modo affidabile per una successiva elaborazione, esegue la
seconda fase del processo di ricezione chiamando **Delete**
sul messaggio ricevuto. Quando il bus di servizio vede la chiamata **Delete**, contrassegna
il messaggio come usato e lo rimuove dall'argomento.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi usando la
modalità **PeekLock** (non predefinita). Nell'esempio
seguente viene eseguito un ciclo, vengono elaborati i messaggi nella sottoscrizione "HighMessages" e infine si esce quando non vi sono più messaggi (in alternativa, l'esempio può essere impostato per attendere l'arrivo di nuovi messaggi).

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

## <a name="bkmk_HowToHandleAppCrash"> </a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso
di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione
ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo,
può chiamare il metodo **unlockMessage**,
anziché **deleteMessage**, sul messaggio ricevuto. In questo modo, il bus di servizio
sbloccherà il messaggio nell'argomento rendendolo nuovamente disponibile per la ricezione
da parte della stessa o da un'altra
applicazione consumer.

Al messaggio bloccato nell'argomento è inoltre associato un timeout
. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del
timeout, ad esempio a causa di un arresto anomalo, il bus di
servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile
per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio
ma prima dell'invio della richiesta **deleteMessage**, il messaggio verrà nuovamente recapitato
all'applicazione al riavvio del sistema. Questo processo di elaborazione
viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà
elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato
una seconda volta. Se lo scenario non tollera la doppia elaborazione,
gli sviluppatori dovranno aggiungere ulteriore logica
all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso
usato il metodo **getMessageId** del messaggio, che rimane costante in
tutti i tentativi di recapito.

## <a name="bkmk_HowToDeleteTopics"> </a>Come eliminare argomenti e sottoscrizioni

Il modo principale per eliminare argomenti e sottoscrizioni consiste nell'usare un oggetto
**ServiceBusContract**. Se si elimina un argomento, verranno eliminate anche tutte le sottoscrizioni
registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base sulle code del bus di servizio, vedere
l'argomento [Code, argomenti e sottoscrizioni del bus di servizio][] su MSDN per altre informazioni.

  [Azure SDK per Java]: http://www.windowsazure.com/it-it/develop/java/
  [Informazioni su argomenti e sottoscrizioni del bus di servizio]: #what-are-service-bus-topics
  [Creare uno spazio dei nomi servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Configurare l'applicazione per l'uso del bus di servizio]: #bkmk_ConfigYourApp
  [Procedura: Creare un argomento]: #bkmk_HowToCreateTopic
  [Procedura: Creare sottoscrizioni]: #bkmk_HowToCreateSubscrip
  [Procedura: Inviare messaggi a un argomento]: #bkmk_HowToSendMsgs
  [Procedura: Ricevere messaggi da una sottoscrizione]: #bkmk_HowToReceiveMsgs
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #bkmk_HowToHandleAppCrash
  [Procedura: Eliminare argomenti e sottoscrizioni]: #bkmk_HowToDeleteTopics
  [Passaggi successivi]: #bkmk_NextSteps
  [Diagramma relativo agli argomenti del bus di servizio]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Portale di gestione di Azure]: http://manage.windowsazure.com/
  [Schermata nodo bus di servizio]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Creare un nuovo spazio dei nomi ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Schermata relativa all'elenco degli spazi dei nomi]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Schermata pannello Proprietà]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Schermata Default Key]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Code, argomenti e sottoscrizioni del bus di servizio]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx

completes the second stage of the receive process by calling **Delete**
on the received message. When Service Bus sees the **Delete** call, it
will mark the message as being consumed and remove it from the topic.

The example below demonstrates how messages can be received and
processed using **PeekLock** mode (not the default mode). The example
below performs a loop and processes messages in the "HighMessages" subscription and then exits when there are no more messages (alternatively, it could be set to wait for new messages).

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

## <a name="bkmk_HowToHandleAppCrash"> </a>How to Handle Application Crashes and Unreadable Messages

Service Bus provides functionality to help you gracefully recover from
errors in your application or difficulties processing a message. If a
receiver application is unable to process the message for some reason,
then it can call the **unlockMessage** method on the received message
(instead of the **deleteMessage** method). This will cause Service Bus
to unlock the message within the topic and make it available to be
received again, either by the same consuming application or by another
consuming application.

There is also a timeout associated with a message locked within the
topic, and if the application fails to process the message before the
lock timeout expires (e.g., if the application crashes), then Service
Bus will unlock the message automatically and make it available to be
received again.

In the event that the application crashes after processing the message
but before the **deleteMessage** request is issued, then the message
will be redelivered to the application when it restarts. This is often
called **At Least Once Processing**, that is, each message will be
processed at least once but in certain situations the same message may
be redelivered. If the scenario cannot tolerate duplicate processing,
then application developers should add additional logic to their
application to handle duplicate message delivery. This is often achieved
using the **getMessageId** method of the message, which will remain
constant across delivery attempts.

## <a name="bkmk_HowToDeleteTopics"> </a>How to Delete Topics and Subscriptions

The primary way to delete topics and subscriptions is to use a
**ServiceBusContract** object. Deleting a topic will also delete any subscriptions that are registered
with the topic. Subscriptions can also be deleted independently.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Next Steps

Now that you've learned the basics of Service Bus queues, see the MSDN
topic [Service Bus Queues, Topics, and Subscriptions][] for more information.

  [Azure SDK for Java]: http://www.windowsazure.com/it-it/develop/java/
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
  [Service Bus Queues, Topics, and Subscriptions]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
