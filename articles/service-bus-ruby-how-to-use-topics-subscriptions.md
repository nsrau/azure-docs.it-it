<properties urlDisplayName="Service Bus Topics" pageTitle="Come usare gli argomenti del bus di servizio (Ruby) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic ruby" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Ruby applications." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Topics/Subscriptions" authors="guayan" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />





# Come usare gli argomenti e le sottoscrizioni del bus di servizio

In questa guida verrà descritto come usare gli argomenti e le sottoscrizioni del bus di servizio da applicazioni Ruby. Gli scenari presentati includono **creazione di argomenti e sottoscrizioni, creazione di filtri per le sottoscrizioni, invio di messaggi** a un argomento, **ricezione di messaggi da una sottoscrizione** ed **eliminazione di argomenti e sottoscrizioni**. Per altre informazioni su argomenti e sottoscrizioni, vedere la sezione [Passaggi successivi](#NextSteps).

## Sommario
* [Informazioni su argomenti e sottoscrizioni del bus di servizio](#what-are-service-bus-topics)
* [Creare uno spazio dei nomi servizio](#create-a-service-namespace)
* [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi](#obtain-default-credentials)
* [Creazione di un'applicazione Ruby](#create-a-ruby-application)
* [Configurare l'applicazione per l'uso del bus di servizio](#configure-your-application-to-use-service-bus)
* [Configurazione di una connessione del bus di servizio di Azure](#setup-a-windows-azure-service-bus-connection)
* [Come creare un argomento](#how-to-create-a-topic)
* [Come creare le sottoscrizioni](#how-to-create-subscriptions)
* [Come inviare messaggi a un argomento](#how-to-send-messages-to-a-topic)
* [Come ricevere messaggi da una sottoscrizione](#how-to-receive-messages-from-a-subscription)
* [Come gestire arresti anomali e messaggi illeggibili dell'applicazione](#how-to-handle-application-crashes-and-unreadable-messages)
* [Come eliminare argomenti e sottoscrizioni](#how-to-delete-topics-and-subscriptions)
* [Passaggi successivi](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a id="create-a-ruby-application"></a>Creazione di un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni,vedere [Creazione di un'applicazione Ruby in Azure](/it-it/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio

Per usare il bus di servizio di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di librerie che comunicano con i servizi di archiviazione REST.

### Usare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

    require "azure"

## <a id="setup-a-windows-azure-service-bus-connection"></a>Configurazione di una connessione del bus di servizio di Azure

Il modulo azure leggerà le variabili di ambiente **AZURE\_SERVICEBUS\_NAMESPACE** and **AZURE\_SERVICEBUS\_ACCESS\_KEY** 
Per informazioni necessarie per connettersi allo spazio dei nomi del bus di servizio di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative allo spazio dei nomi prima di usare **Azure::ServiceBusService** con il codice seguente:

    Azure.config.sb_namespace = "<spazio dei nomi del bus di servizio di azure>"
    Azure.config.sb_namespace = "<chiave di accesso al bus di servizio di azure>"

## <a id="how-to-create-a-topic"></a>Come creare un argomento

L'oggetto **Azure::ServiceBusService** consente di usare gli argomenti. Il codice seguente consente di creare un oggetto **Azure::ServiceBusService**. Per creare un argomento, usare il metodo **create\_topic()**. Nell'esempio seguente viene creato un argomento o stampato l'eventuale errore.

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

È inoltre possibile passare un oggetto **Azure::ServiceBus::Topic** con altre opzioni, che consentono di sostituire le impostazioni degli argomenti predefinite, come ad esempio la durata dei messaggi o la dimensione massima della coda. Nell'esempio seguente viene illustrato come impostare la dimensione massima della coda su 5 GB e una durata di 1 minuto:

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## <a id="how-to-create-subscriptions"></a>Come creare le sottoscrizioni

È possibile creare le sottoscrizioni a un argomento tramite l'oggetto **Azure::ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi recapitati alla coda virtuale della sottoscrizione.

**Nota**
le sottoscrizioni sono persistenti e continueranno a esistere fintanto che esse, o l'argomento a cui sono associate, non vengono eliminate. Se l'applicazione contiene la logica per la creazione di una sottoscrizione, è innanzitutto necessario verificare se la sottoscrizione esiste già usando il metodo getSubscription.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene usato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata "all-messages" e viene usato il filtro predefinito **MatchAll**.

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>Creare sottoscrizioni con i filtri

È inoltre possibile configurare filtri che consentono di specificare i messaggi inviati a un argomento da visualizzare in una sottoscrizione all'argomento specifica.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **Azure::ServiceBus::SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altri dettagli sulle espressioni che è possibile usare con un filtro SQL, esaminare la sintassi di [SqlFilter.SqlExpression](http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

È possibile aggiungere i filtri a una sottoscrizione tramite il metodo **create\_rule()** dell'oggetto **Azure::ServiceBusService**. Questo metodo consente di aggiungere nuovi filtri a una sottoscrizione esistente.

**Nota**
Poiché il filtro predefinito viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario prima di tutto rimuovere il filtro predefinito, altrimenti **MatchAll**sovrascriverà qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita tramite il metodo **delete\_rule()** dell'oggetto **Azure::ServiceBusService**.

Nell'esempio seguente viene creata una sottoscrizione denominata "high-messages" con un filtro **Azure::ServiceBus::SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata **message\_number** è maggiore di 3:

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "high-messages")
	azure_service_bus_service.delete_rule("test-topic", "high-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("high-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "high-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number > 3" })
	rule = azure_service_bus_service.create_rule(rule)

Similarly, the following example creates a subscription named "low-messages" with a **Azure::ServiceBus::SqlFilter** that only selects messages that have a **message_number** property less than or equal to 3:

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "low-messages")
	azure_service_bus_service.delete_rule("test-topic", "low-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("low-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "low-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number <= 3" })
	rule = azure_service_bus_service.create_rule(rule)

Un messaggio inviato a "test-topic" verrà sempre recapitato ai ricevitori con sottoscrizione all'argomento "all-messages" e recapitato selettivamente ai ricevitori con sottoscrizioni agli argomenti "high-messages" e "low-messages", a seconda del contenuto del messaggio.

## <a id="how-to-send-messages-to-a-topic"></a>Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, l'applicazione deve usare il metodo **send\_topic\_message()** dell'oggetto **Azure::ServiceBusService**. I messaggi inviati ad argomenti del bus di servizio sono oggetti **Azure::ServiceBus::BrokeredMessage**. Gli oggetti **Azure::ServiceBus::BrokeredMessage** includono un insieme di proprietà standard, ad esempio **label** e **time\_to\_live**, un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati di tipo stringa. Un'applicazione può impostare il corpo del messaggio passando un valore stringa a **send\_topic\_message()** in modo da popolare le proprietà standard necessarie con valori predefiniti.

Nell'esempio seguente viene illustrato come impostare cinque messaggi di test su "test-topic". Si noti come il valore della proprietà personalizzata **message_number** di ogni messaggio varia nell'iterazione del ciclo, determinando la sottoscrizione che lo riceverà:

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 MB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 MB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a id="how-to-receive-messages-from-a-subscription"></a>Come ricevere messaggi da una sottoscrizione

I messaggi vengono ricevuti da una sottoscrizione tramite il metodo **receive\_subscription\_message()** sull'oggetto **Azure::ServiceBusService**. Per impostazione predefinita, i messaggi vengono letti (picco) e bloccati senza essere eliminati dalla sottoscrizione. È possibile leggere ed eliminare il messaggio dalla sottoscrizione, impostando l'opzione **peek\_lock** su **false**.

In base al comportamento predefinito, la lettura e l'eliminazione vengono incluse in un'operazione di ricezione suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando il metodo **delete\_subscription\_message()** e fornendo il messaggio da eliminare come parametro. Il metodo **delete\_subscription\_message()** contrassegna il messaggio come usato e lo rimuove dalla sottoscrizione.

Se il parametro **:peek\_lock** è impostato su **false**, la lettura e l'eliminazione del messaggio costituiscono il modello più semplice, adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come usato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio usato prima dell'arresto anomalo risulterà perso.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi usando la modalità **receive\_subscription\_message()**. Nell'esempio viene innanzitutto ricevuto ed eliminato un messaggio dalla sottoscrizione "low-messages" tramite **:peek\_lock** impostato su **false**, quindi viene ricevuto un altro messaggio da "high-messages" e il messaggio viene eliminato tramite **delete\_subscription\_message()**:

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## <a id="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlock\_subscription\_message()** sull'oggetto **Azure::ServiceBusService**. In questo modo, il bus di servizio sbloccherà il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo **delete\_subscription\_message()**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **message\_id** del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a id="how-to-delete-topics-and-subscriptions"></a>Come eliminare argomenti e sottoscrizioni

Gli argomenti e le sottoscrizioni sono persistenti e devono pertanto essere eliminati in modo esplicito tramite il [portale di gestione di Azure](https://manage.windowsazure.com) o a livello di codice. Nell'esempio seguente viene illustrato come eliminare l'argomento denominato "test-topic".

	azure_service_bus_service.delete_topic("test-topic")

Se si elimina un argomento, verranno eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Nel codice seguente viene illustrato come eliminare la sottoscrizione denominata "high-messages" dall'argomento "test-topic":

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## <a id="NextSteps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i collegamenti seguenti per altre informazioni.

-   Vedere il riferimento in MSDN: [Code, argomenti e sottoscrizioni](http://msdn.microsoft.com/it-it/library/windowsazure/hh367516.aspx)
-   Riferimento sulle API per [SqlFilter](http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx)
-	Repository [Azure SDK per Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub
