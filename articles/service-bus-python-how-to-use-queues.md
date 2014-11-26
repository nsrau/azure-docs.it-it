<properties linkid="develop-python-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Come usare le code del bus di servizio

In questa guida verrà descritto come usare le code del bus di servizio. Gli esempi sono scritti in Python e usano il modulo Python di Azure. Gli scenari presentati includono **creazione di code invio e ricezione di messaggi**, nonché **eliminazione di code**. Per altre informazioni sulle code, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni sulle code del bus di servizio][Informazioni sulle code del bus di servizio]
-   [Creare uno spazio dei nomi servizio][Creare uno spazio dei nomi servizio]
-   [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi][Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]
-   [Procedura: Creare una coda][Procedura: Creare una coda]
-   [Procedura: Inviare messaggi a una coda][Procedura: Inviare messaggi a una coda]
-   [Procedura: Ricevere messaggi da una coda][Procedura: Ricevere messaggi da una coda]
-   [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione][Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]
-   [Passaggi successivi][Passaggi successivi]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Nota:** se è necessario installare Python o le librerie client, vedere la [guida all'installazione di Python][guida all'installazione di Python].

## <a name="create-queue"> </a>Come creare una coda

L'oggetto **ServiceBusService** consente di usare le code. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere al bus di servizio di Azure a livello di codice:

    from azure.servicebus import *

Il codice seguente consente di creare un oggetto **ServiceBusService**. Sostituire 'mynamespace', 'sharedaccesskeyname' e 'sharedaccesskey' con lo spazio dei nomi effettivo e il nome e il valore della chiave Firma di accesso condiviso (SAS, Shared Access Signature).

    bus_service = ServiceBusService(
        service_namespace='mynamespace',
        shared_access_key_name='sharedaccesskeyname',
        shared_access_key_value='sharedaccesskey')

I valori relativi al nome e al valore della chiave SAS sono disponibili nelle informazioni di connessione del portale di Azure o nella finestra Proprietà di Visual Studio quando si seleziona lo spazio dei nomi del bus di servizio in Esplora server, come illustrato nella sezione precedente.

    bus_service.create_queue('taskqueue')

**create\_queue** supporta inoltre opzioni aggiuntive che permettono di sostituire le impostazioni predefinite delle code, come ad esempio la durata (TTL) dei messaggi o la dimensione massima della coda. L'esempio seguente illustra come impostare la dimensione massima della coda su 5 GB e una durata di 1 minuto:

    queue_options = Queue()
    queue_options.max_size_in_megabytes = '5120'
    queue_options.default_message_time_to_live = 'PT1M'

    bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Come inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiamerà il metodo **send\_queue\_message** sull'oggetto **ServiceBusService**.

L'esempio seguente illustra come inviare un messaggio di prova alla coda *taskqueue usando* **send\_queue\_message**:

    msg = Message('Test Message')
    bus_service.send_queue_message('taskqueue', msg)

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="receive-messages"> </a>Come ricevere messaggi da una coda

I messaggi vengono ricevuti da una coda tramite il metodo **receive\_queue\_message** sull'oggetto **ServiceBusService**:

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
    print(msg.body)

I messaggi vengono eliminati dalla coda non appena vengono letti, quando il parametro **peek\_lock** è impostato su **False**. È possibile leggere (visualizzare) e bloccare il messaggio senza eliminarlo dalla cosa impostando il parametro **peek\_lock** su **True**.

Il comportamento di lettura ed eliminazione del messaggio nell'ambito dell'operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come usato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio usato prima dell'arresto anomalo risulterà perso.

Se il parametro **peek\_lock** è impostato su **True**, 'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve la richiesta, individua il successivo messaggio da usare, lo blocca per impedirne la ricezione da parte di altri consumer e lo restituisce quindi all'applicazione.Quando l'applicazione ha completato l'elaborazione del messaggio oppure quando lo ha archiviato in modo affidabile per l'elaborazione futura, completa la seconda fase del processo di ricezione mediante la chiamata del metodo **delete** sull'oggetto **Message**.Il metodo **delete** contrassegna il messaggio come usato e lo rimuove dalla coda.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
    print(msg.body)

    msg.delete()

## <a name="handle-crashes"> </a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo,può chiamare il metodo **unlock** sull'oggetto **Message**. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo **delete**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione,gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="next-steps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, usare i collegamenti seguenti per altre informazioni.

-   Riferimento in MSDN: [Code, argomenti e sottoscrizioni][Code, argomenti e sottoscrizioni]

  [Passaggi successivi]: #next-steps
  [Informazioni sulle code del bus di servizio]: #what-are-service-bus-queues
  [Creare uno spazio dei nomi servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Procedura: Creare una coda]: #create-queue
  [Procedura: Inviare messaggi a una coda]: #send-messages
  [Procedura: Ricevere messaggi da una coda]: #receive-messages
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #handle-crashes
  [guida all'installazione di Python]: ../python-how-to-install/
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/it-it/library/windowsazure/hh367516.aspx
