<properties 
    pageTitle="Come usare le code del bus di servizio con Python | Microsoft Azure" 
    description="Informazioni su come usare le code del bus di servizio da Python." 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="sethm;lmazuel"/>



# <a name="how-to-use-service-bus-queues"></a>Come usare le code del bus di servizio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Questo articolo illustra come usare le code del bus di servizio. Gli esempi sono scritti in Python e usano il [pacchetto di Azure del bus di servizio per Python][]. Gli scenari presentati includono **la creazione di code, l’invio e la ricezione di messaggi** e **l’eliminazione di code**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] Per installare Python o il [pacchetto del bus di servizio di Azure per Python][], vedere la [guida all'installazione di Python](../python-how-to-install.md).

## <a name="create-a-queue"></a>Creare una coda

L'oggetto **ServiceBusService** consente di usare le code. Aggiungere il seguente codice all'inizio di ogni file Python da cui si desidera accedere al bus di servizio a livello di codice:

```
from azure.servicebus import ServiceBusService, Message, Queue
```

Il codice seguente consente di creare un oggetto **ServiceBusService**. Sostituire, `mynamespace`, `sharedaccesskeyname` e `sharedaccesskey` con lo spazio dei nomi e il nome e il valore della chiave di firma di accesso condiviso.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Il valore e il nome della chiave di firma di accesso condiviso sono disponibili nelle informazioni di connessione del [portale di Azure classico][] o nel pannello **Proprietà** di Visual Studio quando si seleziona lo spazio dei nomi del bus di servizio in Esplora server, come illustrato nella sezione precedente.

```
bus_service.create_queue('taskqueue')
```

**create_queue** supporta anche opzioni aggiuntive che consentono di eseguire l’override delle impostazioni predefinite delle code, come ad esempio la durata (TTL) dei messaggi o la dimensione massima della coda. Il seguente esempio illustra come impostare la dimensione massima della coda su 5 GB e la durata a 1 minuto:

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiama il metodo **send\_queue\_message** per l'oggetto **ServiceBusService**.

Nell'esempio seguente viene illustrato come inviare un messaggio di prova alla coda denominata *taskqueue* usando **send\_queue\_message**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e pari a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB. Per altre informazioni sulle quote, vedere [Quote del bus di servizio][].

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda

I messaggi vengono ricevuti da una coda tramite il metodo **receive\_queue\_message** per l'oggetto **ServiceBusService**:

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

I messaggi vengono eliminati dalla coda non appena vengono letti, quando il parametro **peek\_lock** è impostato su **False**. È possibile leggere e bloccare il messaggio senza eliminarlo dalla coda impostando il parametro **peek\_lock** su **True**.

Il comportamento di lettura ed eliminazione del messaggio nell'ambito dell'operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Se il parametro **peek\_lock** è impostato su **True**, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, l’applicazione esegue la seconda fase del processo di ricezione chiamando il metodo **delete** per l'oggetto **Message**. Il metodo **delete** contrassegna il messaggio come usato e lo rimuove dalla coda.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlock** per l'oggetto **Message**. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata del metodo **delete**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, utilizzare i collegamenti seguenti per ulteriori informazioni.

-   Vedere [Code, argomenti e sottoscrizioni del bus di servizio][].

[portale di Azure classico]: https://manage.windowsazure.com
[Pacchetto del bus di servizio di Azure per Python]: https://pypi.python.org/pypi/azure-servicebus  
[Code, argomenti e sottoscrizioni del bus di servizio]: service-bus-queues-topics-subscriptions.md
[Quote del bus di servizio]: service-bus-quotas.md
 



<!--HONumber=Oct16_HO2-->


