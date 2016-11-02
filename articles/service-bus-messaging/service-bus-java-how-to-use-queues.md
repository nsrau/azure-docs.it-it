<properties
    pageTitle="Come usare le code del bus di servizio con Java | Microsoft Azure"
    description="Informazioni su come usare le code del bus di servizio in Azure. Gli esempi di codice sono scritti in Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-queues"></a>Come usare le code del bus di servizio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Questo articolo illustra come usare le code del bus di servizio. Gli esempi sono scritti in Java e usano [Azure SDK per Java][]. Gli scenari presentati includono la **creazione di code**, l'**invio e la ricezione di messaggi** e l'**eliminazione di code**.

## <a name="what-are-service-bus-queues?"></a>Informazioni sulle code del bus di servizio

Le code del bus di servizio supportano un modello di comunicazione con **messaggistica negoziata** . Quando si usano le code, i componenti di un'applicazione distribuita non comunicano direttamente l'uno con l'altro, ma scambiano messaggi tramite una coda, che agisce da intermediario (broker). Un producer di messaggi (mittente) invia un messaggio alla coda e quindi prosegue con la relativa elaborazione.
In modo asincrono, il consumer di messaggi (ricevitore) recupera il messaggio dalla coda e lo elabora. Il producer non deve attendere la risposta del consumer per continuare a elaborare e inviare ulteriori messaggi. Le code consentono un recapito dei messaggi di tipo **FIFO (First In, First Out)** a uno o più consumer concorrenti. In base a questo metodo, in genere i messaggi vengono ricevuti ed elaborati nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer.

![Concetti relativi alle code](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Le code del bus di servizio sono una tecnologia di carattere generale che può essere usata in numerosi scenari:

- Comunicazione tra ruoli Web e di lavoro in un'applicazione Azure multilivello.
- Comunicazione tra app locali e app ospitate in Azure in una soluzione ibrida.
- Comunicazione tra componenti di un'applicazione distribuita in esecuzione in locale in organizzazioni diverse o in reparti diversi della stessa organizzazione.

L'uso delle code consente di scalare orizzontalmente in modo più semplice le applicazioni e garantisce maggiore resilienza nell'architettura.

## <a name="create-a-service-namespace"></a>Creare uno spazio dei nomi del servizio

Per iniziare a usare le code del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi. Uno spazio dei nomi fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio

Assicurarsi di aver installato [Azure SDK per Java][] prima di compilare questo esempio. Se si usa Eclipse, è possibile installare [Toolkit di Azure per Eclipse][] che include Azure SDK per Java. È quindi possibile aggiungere le **librerie di Microsoft Azure per Java** al progetto:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Aggiungere le seguenti istruzioni `import` all'inizio del file Java:

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Creare una coda

Per eseguire operazioni di gestione per le code del bus di servizio, è possibile usare la classe **ServiceBusContract**. Un oggetto **ServiceBusContract** è costruito con una configurazione appropriata che incapsula il token di firma di accesso condiviso con le autorizzazioni necessarie a gestirlo. La classe **ServiceBusContract** rappresenta l'unico punto di comunicazione con Azure.

La classe **ServiceBusService** definisce i metodi per creare, enumerare ed eliminare le code. L'esempio seguente illustra come usare un oggetto **ServiceBusService** per creare una coda denominata "TestQueue" con uno spazio dei nomi denominato "HowToSample":

```
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
```

In **QueueInfo** sono disponibili metodi che consentono di ottimizzare le proprietà della coda, ad esempio il valore TTL predefinito da applicare ai messaggi inviati alla coda. Il seguente esempio illustra come creare una coda denominata `TestQueue` con una dimensione massima pari a 5 GB:

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Si noti che è possibile usare il metodo **listQueues** su oggetti **ServiceBusContract** per verificare se in uno spazio dei nomi servizio esiste già una coda con il nome specificato.

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, l'applicazione ottiene un oggetto **ServiceBusContract**. Il seguente codice illustra come inviare un messaggio per la coda `TestQueue` creata in precedenza all'interno dello spazio dei nomi `HowToSample`.

```
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
```

I messaggi inviati e ricevuti dalla coda del bus di servizio sono istanze della classe [BrokeredMessage][] . Gli oggetti [BrokeredMessage][] includono un insieme di proprietà standard, ad esempio [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx), un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Per impostare il corpo del messaggio, un'applicazione può passare qualsiasi oggetto serializzabile nel costruttore di [BrokeredMessage][]. Per serializzare l'oggetto verrà usato il serializzatore appropriato. In alternativa, è possibile specificare un oggetto **java.IO.InputStream**.

L'esempio seguente illustra come inviare cinque messaggi di prova all'oggetto `TestQueue` **MessageSender** ottenuto nel frammento di codice precedente:

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Le code del bus di servizio supportano messaggi di dimensioni fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda

Per ricevere i messaggi da una coda si usa principalmente un oggetto **ServiceBusContract**. I messaggi ricevuti possono essere usati in due modalità diverse: **ReceiveAndDelete** e **PeekLock**.

Quando si usa la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un messaggio in una coda, lo contrassegna come usato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** predefinita costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione.
Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per successive elaborazioni, l'applicazione esegue la seconda fase del processo di ricezione chiamando **Delete** sul messaggio ricevuto. Quando il bus di servizio vede la chiamata **Delete**, contrassegna il messaggio come usato e lo rimuove dalla coda.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi usando la modalità **PeekLock** non predefinita. Nel seguente esempio viene creato un ciclo infinito e i messaggi vengono elaborati non appena arrivano in "TestQueue":

```
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
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlockMessage**, anziché **deleteMessage**, per il messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'invio della richiesta **deleteMessage**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usato il metodo **getMessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, vedere [Code, argomenti e sottoscrizioni][] per altre informazioni.

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Java](/develop/java/).


  [Azure SDK per Java]: http://azure.microsoft.com/develop/java/
  [Toolkit di Azure per Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Code, argomenti e sottoscrizioni del bus di servizio]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx




<!--HONumber=Oct16_HO2-->


