<properties linkid="dev-java-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Java) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Java" description="Learn how to use Service Bus queues in Azure. Code samples written in Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Queues" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Come usare le code del bus di servizio

In questa guida verrà descritto come usare le code del bus di servizio. Gli esempi sono scritti in Java e usano [Azure SDK for Java][Azure SDK for Java]. Gli scenari presentati includono **creazione di code**, **invio e ricezione di messaggi**, nonché **eliminazione di code**.

## Sommario

-   [Informazioni sulle code del bus di servizio][Informazioni sulle code del bus di servizio]
-   [Creare uno spazio dei nomi servizio][Creare uno spazio dei nomi servizio]
-   [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi][Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]
-   [Configurare l'applicazione per l'uso del bus di servizio][Configurare l'applicazione per l'uso del bus di servizio]
-   [Procedura: Creare un provider di token di sicurezza][Procedura: Creare un provider di token di sicurezza]
-   [Procedura: Creare una coda][Procedura: Creare un provider di token di sicurezza]
-   [Procedura: Inviare messaggi a una coda][Procedura: Inviare messaggi a una coda]
-   [Procedura: Ricevere messaggi da una coda][Procedura: Ricevere messaggi da una coda]
-   [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione][Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]
-   [Passaggi successivi][Passaggi successivi]

[WACOM.INCLUDE [howto-service-bus-queues][howto-service-bus-queues]]

## <a name="bkmk_ConfigApp"> </a>Configurare l'applicazione per l'uso del bus di servizio

Aggiungere le istruzioni import seguenti all'inizio del file Java:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*; 
    import com.microsoft.windowsazure.services.core.*; 
    import javax.xml.datatype.*;

## <a name="bkmk_HowToCreateQueue"> </a>Come creare una coda

Per eseguire operazioni di gestione per le code del bus di servizio, è possibile usare la classe **ServiceBusContract** class. Un oggetto **ServiceBusContract** è costruito con una configurazione appropriata che incapsula le autorizzazioni dei token necessarie a gestirlo. La classe **ServiceBusContract** rappresenta l'unico punto di comunicazione con Azure.

La classe **ServiceBusService** fornisce i metodi per creare, enumerare ed eliminare le code. Nell'esempio seguente viene illustrato come usare un oggetto **ServiceBusService** per creare una coda denominata "TestQueue" con uno spazio dei nomi denominato "HowToSample":

    Configuration config = 
        ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

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

Alcuni metodi di QueueInfo consentono di ottimizzare le proprietà della coda, ad esempio di impostare il valore di durata "TTL" da applicare ai messaggi inviati alla coda. Nell'esempio seguente viene illustrato come creare una coda denominata "TestQueue" con una dimensione massima pari a 5 GB:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Si noti che è possibile usare il metodo **listQueues** su oggetti **ServiceBusContract** per verificare se in uno spazio dei nomi servizio esiste già una coda con il nome specificato.

## <a name="bkmk_HowToSendMsgs"> </a>Come inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, l'applicazione otterrà un oggetto **ServiceBusContract**. Nel codice seguente viene illustrato come inviare un messaggio per la coda "TestQueue" creata in precedenza all'interno dello spazio dei nomi servizio "HowToSample":

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

I messaggi inviati e ricevuti dalla coda del bus di servizio sono istanze della classe **BrokeredMessage**. Gli oggetti **BrokeredMessage** includono un set di metodi standard, ad esempio **getLabel**, **getTimeToLive**, **setLabel** e **setTimeToLive**, un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Per impostare il corpo del messaggio, un'applicazione può passare qualsiasi oggetto serializzabile nel **costruttore di BrokeredMessage**. In tal caso, per serializzare l'oggetto verrà usato il serializzatore appropriato. In alternativa, è possibile fornire un oggetto **java.IO.InputStream**.

Nell'esempio seguente viene illustrato come inviare cinque messaggi di prova all'oggetto **MessageSender**
"TestQueue" ottenuto nel frammento di codice riportato sopra:

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

## <a name="bkmk_HowToReceiveMsgs"> </a>Come ricevere messaggi da una coda

Il modo principale per ricevere i messaggi da una coda consiste nell'usare un oggetto **ServiceBusContract**. È possibile usare i messaggi ricevuti in due diverse modalità: **ReceiveAndDelete** e **PeekLock**.

Quando si usa la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un messaggio in una coda, lo contrassegna come usato e lo restituisce all'applicazione. La modalità predefinita **ReceiveAndDelete** costituisce il modello più semplice e più idoneo per gli scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come usato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio usato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, trova il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando **Delete** sul messaggio ricevuto. Quando il bus di servizio vede la chiamata **Delete**, contrassegna il messaggio come usato e lo rimuove dalla coda.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi usando la modalità **PeekLock** (non predefinita). Nell'esempio seguente viene creato un ciclo infinito e i messaggi vengono elaborati non appena arrivano in "TestQueue":

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

## <a name="bkmk_HowToHandleAppCrashes"> </a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlockMessage**, anziché **deleteMessage**, sul messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'invio della richiesta **deleteMessage**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere ulteriore logica all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usato il metodo **getMessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="bkmk_NextSteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, vedere l'argomento [Code, argomenti e sottoscrizioni][Code, argomenti e sottoscrizioni] su MSDN per altre informazioni.

  [Azure SDK for Java]: http://azure.microsoft.com/it-it/develop/java/
  [Informazioni sulle code del bus di servizio]: #what-are-service-bus-queues
  [Creare uno spazio dei nomi servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Configurare l'applicazione per l'uso del bus di servizio]: #bkmk_ConfigApp
  [Procedura: Creare un provider di token di sicurezza]: #bkmk_HowToCreateQueue
  [Procedura: Inviare messaggi a una coda]: #bkmk_HowToSendMsgs
  [Procedura: Ricevere messaggi da una coda]: #bkmk_HowToReceiveMsgs
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #bkmk_HowToHandleAppCrashes
  [Passaggi successivi]: #bkmk_NextSteps
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/it-it/library/windowsazure/hh367516.aspx
