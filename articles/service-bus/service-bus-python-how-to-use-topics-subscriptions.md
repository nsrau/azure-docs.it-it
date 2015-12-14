<properties 
	pageTitle="Come usare gli argomenti del bus di servizio con Python | Microsoft Azure" 
	description="Informazioni su come usare gli argomenti e le sottoscrizioni del bus di servizio da Python." 
	services="service-bus" 
	documentationCenter="python" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="sethm"/>

# Come usare gli argomenti e le sottoscrizioni del bus di servizio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questo articolo descrive come usare gli argomenti e le sottoscrizioni del bus di servizio. Gli esempi sono scritti in Python e utilizzano il [pacchetto Python di Azure][]. Gli scenari presentati includono **creazione di argomenti e sottoscrizioni**, **creazione di filtri per le sottoscrizioni**, **invio di messaggi a un argomento**, **ricezione di messaggi da una sottoscrizione** ed **eliminazione di argomenti e sottoscrizioni**. Per altre informazioni sugli argomenti e sulle sottoscrizioni, vedere la sezione [Passaggi successivi](#next-steps).

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Nota:** se è necessario installare Python o il [pacchetto Python di Azure][], vedere la [guida all'installazione di Python](../python-how-to-install.md).

## Creare un argomento

L'oggetto **ServiceBusService** consente di usare gli argomenti. Aggiungere il seguente codice vicino all'inizio del file Python da cui si desidera accedere al bus di servizio a livello di codice:

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Il codice seguente consente di creare un oggetto **ServiceBusService**. Sostituire `mynamespace`, `sharedaccesskeyname` e `sharedaccesskey` con lo spazio dei nomi effettivo e il nome e il valore della chiave di firma di accesso condiviso.

```
bus_service = ServiceBusService(
	service_namespace='mynamespace',
	shared_access_key_name='sharedaccesskeyname',
	shared_access_key_value='sharedaccesskey')
```

È possibile ottenere i valori per il nome e il valore della chiave di firma di accesso condiviso dalla finestra **Informazioni di connessione** del [portale di Azure classico][].

```
bus_service.create_topic('mytopic')
```

**create\_topic** supporta anche opzioni aggiuntive che permettono di sostituire le impostazioni predefinite degli argomenti, come ad esempio la durata dei messaggi o la dimensione massima. Il seguente esempio illustra come impostare la dimensione massima dell’argomento su 5 GB e una durata (TTL) di 1 minuto:

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## Creare sottoscrizioni

È possibile creare le sottoscrizioni a un argomento anche tramite l'oggetto **ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi recapitati alla coda virtuale della sottoscrizione.

> [AZURE.NOTE]Le sottoscrizioni sono persistenti e continueranno a esistere fintanto che esse, o l'argomento a cui sono relative, non vengono eliminate.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene utilizzato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si utilizza il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata 'AllMessages' e viene utilizzato il filtro predefinito **MatchAll**.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### Creare sottoscrizioni con i filtri

È inoltre possibile definire i filtri che consentono di specificare quali messaggi inviati a un argomento devono essere presenti in una specifica sottoscrizione dell'argomento.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altre informazioni sulle espressioni che possono essere usate con un filtro SQL, vedere la sintassi [SqlFilter.SqlExpression][].

È possibile aggiungere i filtri a una sottoscrizione tramite il metodo **create\_rule** dell'oggetto **ServiceBusService**. Questo metodo consente di aggiungere nuovi filtri a una sottoscrizione esistente.

> [AZURE.NOTE]Poiché il filtro predefinito viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario innanzitutto rimuovere il filtro predefinito, altrimenti **MatchAll** sovrascriverà qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita tramite il metodo **delete\_rule** dell'oggetto **ServiceBusService**.

L'esempio seguente crea una sottoscrizione denominata `HighMessages` con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà **messagenumber** personalizzata è maggiore di 3:

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Analogamente, nell'esempio seguente viene creata una sottoscrizione denominata`LowMessages` con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà **messagenumber** è minore o uguale a 3:

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Quando un messaggio viene inviato a `mytopic`, viene sempre recapitato ai ricevitori con una sottoscrizione dell'argomento denominata **AllMessages** e viene recapitato selettivamente ai ricevitori con sottoscrizioni dell'argomento denominate **HighMessages** e **LowMessages**, a seconda del contenuto del messaggio.

## Inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, l'applicazione deve utilizzare il metodo **send\_topic\_message** dell'oggetto **ServiceBusService**.

Il seguente esempio illustra come inviare cinque messaggi di test a `mytopic`. Si noti che il valore della proprietà **messagenumber** di ogni messaggio varia nell'iterazione del ciclo, determinando le sottoscrizioni che lo riceveranno:

```
for i in range(5):
	msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
	bus_service.send_topic_message('mytopic', msg)
```

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 MB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 MB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB. Per altre informazioni sulle quote, vedere [Code di Azure e code del bus di servizio][].

## Ricevere messaggi da una sottoscrizione

I messaggi vengono ricevuti da una sottoscrizione tramite il metodo **receive\_subscription\_message** sull'oggetto **ServiceBusService**:

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

I messaggi vengono eliminati dalla sottoscrizione non appena vengono letti, quando il parametro **peek\_lock** è impostato su **False**. È possibile leggere (visualizzare) e bloccare il messaggio senza eliminarlo dalla coda impostando il parametro **peek\_lock** su **True**.

Il comportamento di lettura ed eliminazione del messaggio nell'ambito dell'operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Se il parametro **peek\_lock** è impostato su **True**, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando il metodo **delete** sull'oggetto **Message**. Il metodo **delete** contrassegna il messaggio come usato e lo rimuove dalla sottoscrizione.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlock** sull'oggetto **Message**. In questo modo, il bus di servizio sbloccherà il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo **delete**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso utilizzata la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## Eliminare argomenti e sottoscrizioni

Gli argomenti e le sottoscrizioni sono persistenti e devono pertanto essere eliminati in modo esplicito tramite il [portale di Azure classico][] o a livello di codice. L'esempio seguente mostra come eliminare l’argomento denominato `mytopic`:

```
bus_service.delete_topic('mytopic')
```

Se si elimina un argomento, vengono eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Il codice seguente illustra come eliminare una sottoscrizione denominata `HighMessages` dall'argomento `mytopic`:

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i collegamenti seguenti per altre informazioni.

-   Vedere [Code, argomenti e sottoscrizioni][].
-   Informazioni di riferimento per [SqlFilter.SqlExpression][].

[portale di Azure classico]: http://manage.windowsazure.com
[pacchetto Python di Azure]: https://pypi.python.org/pypi/azure
[Code, argomenti e sottoscrizioni]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Code di Azure e code del bus di servizio]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas

<!---HONumber=AcomDC_1203_2015-->