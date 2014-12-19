<properties urlDisplayName="Service Bus Queues" pageTitle="Come usare le code del bus di servizio (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />




# Come usare le code del bus di servizio
Questa guida illustra come usare le code del bus di servizio. Gli esempi sono
scritti in Python e usano il modulo Python di Azure. Gli scenari
presentati includono **creazione di code invio e ricezione di messaggi**, nonché
**eliminazione di code**. Per altre informazioni sulle code, vedere la sezione [Passaggi successivi][].

## Sommario

-   [Informazioni sulle code del bus di servizio][]
-   [Creare uno spazio dei nomi servizio][]
-   [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi][]
-   [Procedura: Creare una coda][]
-   [Procedura: Inviare messaggi a una coda][]
-   [Procedura: Ricevere messaggi da una coda][]
-   [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione][]
-   [Passaggi successivi][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Nota:** se è necessario installare Python o le librerie client, vedere la [guida all'installazione di Python](../python-how-to-install/).


## <a name="create-queue"> </a>Come creare una coda

L'oggetto **ServiceBusService** consente di usare le code. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere al bus di servizio di Azure a livello di codice:

	from azure.servicebus import ServiceBusService, Message, Queue

Il codice seguente consente di creare un oggetto **ServiceBusService**. Sostituire 'mynamespace', 'sharedaccesskeyname' e 'sharedaccesskey' con lo spazio dei nomi effettivo e il nome e il valore della chiave Firma di accesso condiviso (SAS, Shared Access Signature).

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

I valori relativi al nome e al valore della chiave SAS sono disponibili nelle informazioni di connessione del portale di Azure o nella finestra Proprietà di Visual Studio quando si seleziona lo spazio dei nomi del bus di servizio in Esplora server, come illustrato nella sezione precedente.

	bus_service.create_queue('taskqueue')

**create_queue** supporta inoltre opzioni aggiuntive che
permettono di sostituire le impostazioni predefinite delle code, come ad esempio la durata (TTL)
dei messaggi o la dimensione massima della coda. L'esempio seguente illustra come impostare la
dimensione massima della coda su 5 GB e una durata di 1 minuto:

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Come inviare messaggi a una coda

Per inviare un messaggio a una coda del Bus di servizio, l'applicazione chiamerà il
metodo **send\_queue\_message** sull'oggetto **ServiceBusService**.

L'esempio seguente illustra come inviare un messaggio di prova alla
coda *taskqueue usando* **send\_queue\_message**:

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB,in cui la dimensione massima dell'intestazione,
che include le proprietà standard e personalizzate dell'applicazione, non può
superare 64 KB. Non esiste alcun limite al numero di messaggi
mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei
messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un
limite massimo di 5 GB.

## <a name="receive-messages"> </a>Come ricevere messaggi da una coda

I messaggi vengono ricevuti da una coda tramite il metodo **receive\_queue\_message**
sull'oggetto **ServiceBusService**:

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

I messaggi vengono eliminati dalla coda non appena vengono letti, quando il parametro
**peek\_lock** è impostato su **False**. È possibile leggere (visualizzare) e bloccare il
messaggio senza eliminarlo dalla cosa impostando il parametro
**peek\_lock** su **True**.

Il comportamento di lettura ed eliminazione del messaggio nell'ambito dell'
operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in
cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso
di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il
consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima
dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come usato,
quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi,
il messaggio usato prima dell'arresto anomalo risulterà perso.


Se il parametro **peek\_lock** è impostato su **True**, l'operazione di ricezione viene
suddivisa in due fasi, in modo da consentire il supporto di applicazioni
che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una
richiesta, individua il messaggio successivo da usare, lo blocca per impedirne
la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione.
Dopo aver elaborato il messaggio, o averlo archiviato
in modo affidabile per una successiva elaborazione, esegue la seconda fase del
processo di ricezione chiamando il metodo **delete** sull'oggetto **Message**
. Il metodo **delete** contrassegna il messaggio come usato
e lo rimuove dalla coda.

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso
di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se
un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo,
può chiamare il metodo **unlock** sull'oggetto
**Message**. In questo modo, il bus di servizio sbloccherà il
messaggio nella coda rendendolo nuovamente disponibile per la ricezione
da parte della stessa o da un'altra applicazione
consumer.

Al messaggio bloccato nella coda è inoltre associato un
timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del
timeout, ad esempio a causa di un arresto anomalo, il bus di
servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile
per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio
ma prima della chiamata al metodo **delete**, il messaggio verrà
nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo
**At-Least-Once**, per indicare che ogni messaggio verrà elaborato
almeno una volta ma che in determinate situazioni potrà essere recapitato
una seconda volta. Se lo scenario non tollera la doppia elaborazione,
gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione
per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà
**MessageId** del messaggio, che rimane costante in tutti i
tentativi di recapito.

## <a name="next-steps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, usare
i collegamenti seguenti per altre informazioni.

-   Vedere il riferimento in MSDN: [Code, argomenti e sottoscrizioni][]

  [Passaggi successivi]: #next-steps
  [Informazioni sulle code del bus di servizio]: #what-are-service-bus-queues
  [Creare uno spazio dei nomi servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Procedura: Creare una coda]: #create-queue
  [Procedura: Inviare messaggi a una coda]: #send-messages
  [Procedura: Ricevere messaggi da una coda]: #receive-messages
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #handle-crashes
  [Concetti relativi alle code]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Code, argomenti e sottoscrizioni.]: http://msdn.microsoft.com/it-it/library/windowsazure/hh367516.aspx

	msg.delete()

## <a name="handle-crashes"> </a>How to Handle Application Crashes and Unreadable Messages

Service Bus provides functionality to help you gracefully recover from
errors in your application or difficulties processing a message. If a
receiver application is unable to process the message for some reason,
then it can call the **unlock** method on the
**Message** object. This will cause Service Bus to unlock the
message within the queue and make it available to be received again,
either by the same consuming application or by another consuming
application.

There is also a timeout associated with a message locked within the
queue, and if the application fails to process the message before the
lock timeout expires (e.g., if the application crashes), then Service
Bus will unlock the message automatically and make it available to be
received again.

In the event that the application crashes after processing the message
but before the **delete** method is called, then the message will
be redelivered to the application when it restarts. This is often called
**At Least Once Processing**, that is, each message will be processed at
least once but in certain situations the same message may be
redelivered. If the scenario cannot tolerate duplicate processing, then
application developers should add additional logic to their application
to handle duplicate message delivery. This is often achieved using the
**MessageId** property of the message, which will remain constant across
delivery attempts.

## <a name="next-steps"> </a>Next Steps

Now that you have learned the basics of Service Bus queues, follow these
links to learn more.

-   See the MSDN Reference: [Queues, Topics, and Subscriptions.][]

  [Next Steps]: #next-steps
  [What are Service Bus Queues?]: #what-are-service-bus-queues
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [How to: Create a Queue]: #create-queue
  [How to: Send Messages to a Queue]: #send-messages
  [How to: Receive Messages from a Queue]: #receive-messages
  [How to: Handle Application Crashes and Unreadable Messages]: #handle-crashes
  [Queue Concepts]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Azure Management Portal]: http://manage.windowsazure.com
  
  
  
  
  
  [Queues, Topics, and Subscriptions.]: http://msdn.microsoft.com/it-it/library/windowsazure/hh367516.aspx
