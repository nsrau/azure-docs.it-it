<properties urlDisplayName="Service Bus Topics" pageTitle="Come usare gli argomenti del Bus di servizio (Python) - Azure" metaKeywords="Get started Azure Service Bus topics publising subscribe messaging Python" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Python applications." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Topics/Subscriptions" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />





# Come usare gli argomenti e le sottoscrizioni del bus di servizio
In questa guida verrà descritto come usare gli argomenti e le sottoscrizioni del bus di servizio da applicazioni Python. Gli scenari presentati includono **creazione di argomenti e sottoscrizioni, creazione di filtri per le sottoscrizioni, invio di messaggi** a un argomento, **ricezione di messaggi da una sottoscrizione** ed **eliminazione di argomenti e sottoscrizioni**. Per altre informazioni su argomenti e sottoscrizioni, vedere la sezione [Passaggi successivi](#Next_Steps).

## Sommario

-   [Informazioni su argomenti e sottoscrizioni del bus di servizio](#what-are-service-bus-topics)
-   [Creare uno spazio dei nomi servizio](#create-a-service-namespace)
-   [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi](#obtain-default-credentials)
-   [Procedura: Creare un argomento](#How_to_Create_a_Topic)
-   [Procedura: Creare sottoscrizioni](#How_to_Create_Subscriptions)
-   [Procedura: Inviare messaggi a un argomento](#How_to_Send_Messages_to_a_Topic)
-   [Procedura: Ricevere messaggi da una sottoscrizione](#How_to_Receive_Messages_from_a_Subscription)
-   [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione](#How_to_Handle_Application_Crashes_and_Unreadable_Messages)
-   [Procedura: Eliminare argomenti e sottoscrizioni](#How_to_Delete_Topics_and_Subscriptions)
-   [Passaggi successivi](#Next_Steps)


[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**Nota:** se è necessario installare Python o le librerie client, vedere la [guida all'installazione di Python](../python-how-to-install/).


##<a name="How_to_Create_a_Topic"></a>Come creare un argomento

L'oggetto **ServiceBusService** consente di usare gli argomenti. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere al bus di servizio di Azure a livello di codice:

	from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME

Il codice seguente consente di creare un oggetto **ServiceBusService**. Sostituire 'mynamespace', 'sharedaccesskeyname' e 'sharedaccesskey' con lo spazio dei nomi effettivo e il nome e il valore della chiave Firma di accesso condiviso (SAS, Shared Access Signature).

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

I valori relativi al nome e al valore della chiave SAS sono disponibili nelle informazioni di connessione del portale di Azure o nella finestra Proprietà di Visual Studio quando si seleziona lo spazio dei nomi del bus di servizio in Esplora server, come illustrato nella sezione precedente.

	bus_service.create_topic('mytopic')

**create_topic** supporta inoltre opzioni aggiuntive che
permettono di sostituire le impostazioni degli argomenti predefinite, come ad esempio la
durata dei messaggi o la dimensione massima dell'argomento. L'esempio seguente illustra come
impostare la dimensione massima dell'argomento su 5 GB e una durata di 1 minuto:

	topic_options = Topic()
	topic_options.max_size_in_megabytes = '5120'
	topic_options.default_message_time_to_live = 'PT1M'

	bus_service.create_topic('mytopic', topic_options)

##<a name="How_to_Create_Subscriptions"></a>Come creare le sottoscrizioni

È possibile creare le sottoscrizioni a un argomento tramite l'oggetto **ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi recapitati alla coda virtuale della sottoscrizione.

**Nota**: le sottoscrizioni sono persistenti e continueranno a esistere fintanto che esse, o l'argomento a cui sono associate, non vengono eliminate.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene usato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata 'AllMessages' e viene usato il filtro predefinito **MatchAll**.

	bus_service.create_subscription('mytopic', 'AllMessages')

### Creare sottoscrizioni con i filtri

È anche possibile configurare filtri che consentono di specificare i messaggi inviati
a un argomento da visualizzare in una sottoscrizione all'argomento specifica.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altri dettagli sulle espressioni che è possibile usare con un filtro SQL, esaminare la sintassi di [SqlFilter.SqlExpression][].

È possibile aggiungere i filtri a una sottoscrizione tramite il metodo **create\_rule dell'oggetto **ServiceBusService**. Questo metodo consente di aggiungere nuovi filtri a una sottoscrizione esistente.

**Nota**: Poiché il filtro predefinito viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario prima di tutto rimuovere il filtro predefinito, altrimenti **MatchAll** sovrascriverà qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita tramite il metodo**delete\_rule** dell'oggetto **ServiceBusService**.

Nell'esempio seguente viene creata una sottoscrizione denominata 'HighMessages' con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata **messagenumber** è maggiore di 3:

	bus_service.create_subscription('mytopic', 'HighMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber > 3'

	bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

Analogamente, nell'esempio seguente viene creata una sottoscrizione denominata
'LowMessages' con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà **messagenumber** è minore o uguale a 3: 
	bus_service.create_subscription('mytopic', 'LowMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber <= 3'

	bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

Un messaggio inviato a 'mytopic' verrà sempre recapitato ai ricevitori con sottoscrizione all'argomento 'AllMessages' e recapitato selettivamente ai ricevitori con sottoscrizioni agli argomenti 'HighMessages' e 'LowMessages', a seconda del contenuto del messaggio.

##<a name="How_to_Send_Messages_to_a_Topic"></a>Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, l'applicazione deve usare il metodo **send\_topic\_message** dell'oggetto **ServiceBusService**.

L'esempio seguente illustra come inviare cinque messaggi di test a 'mytopic'. Si noti che il valore della proprietà **messagenumber** di ogni messaggio varia nell'iterazione del ciclo, determinando le sottoscrizioni che lo riceveranno:

	for i in range(5):
		msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
		bus_service.send_topic_message('mytopic', msg)

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 MB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 MB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

##<a name="How_to_Receive_Messages_from_a_Subscription"></a>Come ricevere messaggi da una sottoscrizione

I messaggi vengono ricevuti da una sottoscrizione tramite il metodo **receive\_subscription\_message** sull'oggetto **ServiceBusService**:

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
	print(msg.body)

I messaggi vengono eliminati dalla sottoscrizione non appena vengono letti, quando il parametro **peek\_lock** è impostato su **False**. È possibile leggere (visualizzare) e bloccare il messaggio senza eliminarlo dalla cosa impostando il parametro **peek\_lock** su **True**.

Il comportamento di lettura ed eliminazione del messaggio nell'ambito dell'operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come usato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio usato prima dell'arresto anomalo risulterà perso.


Se il parametro **peek\_lock** è impostato su **True**, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando il metodo **delete** sull'oggetto **Message**. Il metodo **delete** contrassegna il messaggio come usato e lo rimuove dalla sottoscrizione.

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
	print(msg.body)

	msg.delete()


  ##<a name="How_to_Handle_Application_Crashes_and_Unreadable_Messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlock** sull'oggetto **Message**. In questo modo, il bus di servizio sbloccherà il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo **delete**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

##<a name="How_to_Delete_Topics_and_Subscriptions"></a>Come eliminare argomenti e sottoscrizioni

Gli argomenti e le sottoscrizioni sono persistenti e devono pertanto essere eliminati
in modo esplicito tramite il portale di gestione di Azure o a livello di codice.
Nell'esempio seguente viene illustrato come eliminare l'argomento denominato 'mytopic':

	bus_service.delete_topic('mytopic')

Se si elimina un argomento, verranno eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Il codice seguente illustra come eliminare una sottoscrizione denominata 'HighMessages' dall'argomento 'mytopic':

	bus_service.delete_subscription('mytopic', 'HighMessages')

##<a name="Next_Steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i
collegamenti seguenti per altre informazioni.

-   Vedere il riferimento in MSDN: [Code, argomenti e sottoscrizioni][].
-   Riferimento sulle API per [SqlFilter][].

  [Passaggi successivi]: #nextsteps
  [Informazioni su argomenti e sottoscrizioni del bus di servizio]: #what-are-service-bus-topics
  [Creare uno spazio dei nomi servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Procedura: Creare un argomento]: #How_to_Create_a_Topic
  [Procedura: Creare sottoscrizioni]: #How_to_Create_Subscriptions
  [Procedura: Inviare messaggi a un argomento]: #How_to_Send_Messages_to_a_Topic
  [Procedura: Ricevere messaggi da una sottoscrizione]: #How_to_Receive_Messages_from_a_Subscription
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Procedura: Eliminare argomenti e sottoscrizioni]: #How_to_Delete_Topics_and_Subscriptions
  
  [Concetti relativi agli argomenti]: ../../../DevCenter/dotNet/Media/sb-topics-01.png
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/it-it/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx

  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  
  [Topic Concepts]: ../../../DevCenter/dotNet/Media/sb-topics-01.png
  [Azure Management Portal]: http://manage.windowsazure.com
  
  
  
  
  
  
  [Queues, Topics, and Subscriptions]: http://msdn.microsoft.com/it-it/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
