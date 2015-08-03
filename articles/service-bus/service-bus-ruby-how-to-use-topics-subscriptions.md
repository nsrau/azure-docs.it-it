<properties
	pageTitle="Come usare gli argomenti del bus di servizio (Ruby) - Azure"
	description="Informazioni su come usare le sottoscrizioni e gli argomenti di Bus di servizio in Azure. Gli esempi di codice sono scritti per applicazioni Ruby."
	services="service-bus"
	documentationCenter="ruby"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="03/20/2015"
	ms.author="tomfitz"/>





# Come usare gli argomenti e le sottoscrizioni del bus di servizio

In questa guida verrà descritto come utilizzare gli argomenti e le sottoscrizioni del bus di servizio da applicazioni Ruby. Gli scenari presentati includono **creazione di argomenti e sottoscrizioni, creazione di filtri per le sottoscrizioni, invio di messaggi** a un argomento, **ricezione di messaggi da una sottoscrizione** ed **eliminazione di argomenti e sottoscrizioni**. Per ulteriori informazioni su argomenti e sottoscrizioni, vedere la sezione [Passaggi successivi](#NextSteps).

## Informazioni su argomenti e sottoscrizioni del bus di servizio

Gli argomenti e le code del bus di servizio supportano un modello di **comunicazione con messaggistica di pubblicazione-sottoscrizione**. Quando si usano gli argomenti e le sottoscrizioni, i componenti di un'applicazione distribuita non comunicano direttamente l'uno con l'altro ma scambiano messaggi tramite un argomento, che agisce da intermediario.

![Concetti relativi agli argomenti](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Diversamente dalle code del bus di servizio, in cui ogni messaggio viene elaborato da un unico consumer, gli argomenti e le sottoscrizioni offrono una forma di comunicazione **uno a molti** tramite un modello di pubblicazione-sottoscrizione. È possibile registrare più sottoscrizioni a un argomento. Quando un messaggio viene inviato a un argomento, viene reso disponibile affinché ogni sottoscrizione possa gestirlo o elaborarlo in modo indipendente.

La sottoscrizione a un argomento è simile a una coda virtuale che riceve copie dei messaggi che sono stati inviati all'argomento. È possibile registrare regole di filtro per un argomento in base alla sottoscrizione in modo da poter filtrare/limitare i messaggi a un argomento ricevuti dalle diverse sottoscrizioni degli argomenti.

Gli argomenti e le sottoscrizioni del bus di servizio garantiscono scalabilità, consentendo di elaborare grandi quantità di messaggi tra un numero elevato di utenti e applicazioni.

## Creare uno spazio dei nomi del servizio

Per iniziare a usare le code del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi del servizio che fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione. È necessario creare lo spazio dei nomi tramite l'interfaccia della riga di comando perché nel portale non è possibile creare il bus di servizio con una connessione ACS.

Per creare uno spazio dei nomi:

1. Aprire la console di Azure PowerShell.

2. Digitare il comando per creare uno spazio dei nomi del bus di servizio di Azure, come mostrato di seguito. Specificare il valore dello spazio dei nomi e specificare la stessa area dell'applicazione.

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

      ![Creare lo spazio dei nomi](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## Recuperare le credenziali di gestione predefinite per lo spazio dei nomi

Per poter eseguire le operazioni di gestione, ad esempio creare una coda, nel nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione per lo spazio dei nomi.

Il cmdlet PowerShell che è stato eseguito per creare lo spazio dei nomi del bus di servizio di Azure consente di visualizzare la chiave che è possibile usare per gestire lo spazio dei nomi. Copiare il valore **DefaultKey**. Questo valore verrà usato nel codice più avanti in questa esercitazione.

      ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]È anche possibile trovare questa chiave se si accede al [portale di gestione di Azure](http://manage.windowsazure.com/) e si passa alle informazioni di connessione per lo spazio dei nomi del bus di servizio.

## Creare un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni, vedere [Creazione di un'applicazione Ruby in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configurare l'applicazione per l'uso del bus di servizio

Per usare il bus di servizio di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Utilizzare RubyGems per ottenere il pacchetto

1. Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows,) **Terminale** (Mac) o **Bash** (Unix).

2. Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

    require "azure"

## Configurazione di una connessione del bus di servizio di Azure

Il modulo di Azure leggerà le variabili di ambiente **AZURE_SERVICEBUS_NAMESPACE** e **AZURE_SERVICEBUS_ACCESS_KEY**per ottenere le informazioni necessarie per la connessione allo spazio dei nomi del bus di servizio di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative allo spazio dei nomi prima di usare **Azure::ServiceBusService** con il codice seguente:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

Impostare il valore dello spazio dei nomi bus di servizio sul valore creato invece che sull'intero URL. Ad esempio, usare **"yourexamplenamespace"** e non "yourexamplenamespace.servicebus.windows.net".

## Come creare un argomento

L'oggetto **Azure::ServiceBusService** consente di utilizzare gli argomenti. Il codice seguente consente di creare un oggetto **Azure::ServiceBusService**. Per creare un argomento, utilizzare il metodo **create_topic()**. Nel seguente esempio viene creato un argomento o stampato l'eventuale errore.

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

È inoltre possibile passare un oggetto **Azure::ServiceBus::Topic** con ulteriori opzioni, che consentono di sostituire le impostazioni degli argomenti predefinite, come ad esempio la durata dei messaggi o la dimensione massima della coda. Il seguente esempio illustra come impostare la dimensione massima della coda su 5 GB e una durata di 1 minuto:

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## Come creare le sottoscrizioni

È possibile creare le sottoscrizioni a un argomento tramite l'oggetto **Azure::ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi recapitati alla coda virtuale della sottoscrizione.

le sottoscrizioni sono persistenti e continueranno a esistere fintanto che esse, o l'argomento a cui sono associate, non vengono eliminate. Se l'applicazione contiene la logica per la creazione di una sottoscrizione, è innanzitutto necessario verificare se la sottoscrizione esiste già usando il metodo getSubscription.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene utilizzato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si utilizza il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata "all-messages" e viene utilizzato il filtro predefinito **MatchAll**.

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>Creare sottoscrizioni con i filtri

È anche possibile configurare filtri che consentono di specificare i messaggi inviati a un argomento da visualizzare in una specifica sottoscrizione dell'argomento.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **Azure::ServiceBus::SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per ulteriori dettagli sulle espressioni che è possibile utilizzare con un filtro SQL, esaminare la sintassi di [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

È possibile aggiungere i filtri a una sottoscrizione tramite il metodo **create_rule()** dell'oggetto **Azure::ServiceBusService**. Questo metodo consente di aggiungere nuovi filtri a una sottoscrizione esistente.

Poiché il filtro predefinito viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario innanzitutto rimuovere il filtro predefinito, altrimenti **MatchAll** sovrascriverà qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita tramite il metodo **delete_rule()** dell'oggetto **Azure::ServiceBusService**.

Nell'esempio seguente viene creata una sottoscrizione denominata "high-messages" con un filtro **Azure::ServiceBus::SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata **message_number** è maggiore di 3:

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

Analogamente, nell'esempio seguente viene creata una sottoscrizione denominata "low-messages" con un filtro **Azure::ServiceBus::SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà **message_number** è minore o uguale a 3:

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

## Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, l'applicazione deve utilizzare il metodo **send_topic_message()** dell'oggetto **Azure::ServiceBusService**. I messaggi inviati ad argomenti del bus di servizio sono oggetti **Azure::ServiceBus::BrokeredMessage**. Gli oggetti **Azure::ServiceBus::BrokeredMessage** includono un insieme di proprietà standard, ad esempio **label** e **time_to_live**, un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati di tipo stringa. Un'applicazione può impostare il corpo del messaggio passando un valore stringa a **send_topic_message()** in modo da popolare le proprietà standard necessarie con valori predefiniti.

Il seguente esempio illustra come inviare cinque messaggi di test a "test-topic". Si noti come il valore della proprietà personalizzata **message_number** di ogni messaggio varia nell'iterazione del ciclo, determinando la sottoscrizione che lo riceverà:

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 MB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 MB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## Come ricevere messaggi da una sottoscrizione

I messaggi vengono ricevuti da una sottoscrizione tramite il metodo **receive_subscription_message()** sull'oggetto **Azure::ServiceBusService**: Per impostazione predefinita, i messaggi vengono letti (picco) e bloccati senza essere eliminati dalla sottoscrizione. È possibile leggere ed eliminare il messaggio dalla sottoscrizione, impostando l'opzione **peek_lock** su **false**.

In base al comportamento predefinito, la lettura e l'eliminazione vengono incluse in un'operazione di ricezione suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da utilizzare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando il metodo **delete_subscription_message()** e fornendo il messaggio da eliminare come parametro. Il metodo **delete_subscription_message()** contrassegna il messaggio come utilizzato e lo rimuove dalla sottoscrizione.

Se il parametro **:peek_lock** è impostato su **false**, la lettura e l'eliminazione del messaggio costituiscono il modello più semplice, adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi usando la modalità **receive_subscription_message()**. Nell'esempio viene innanzitutto ricevuto ed eliminato un messaggio dalla sottoscrizione "low-messages" tramite **:peek_lock** impostato su **false**, quindi viene ricevuto un altro messaggio da "high-messages" e il messaggio viene eliminato tramite **delete_subscription_message()**:

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlock_subscription_message()** sull'oggetto **Azure::ServiceBusService**. In questo modo, il bus di servizio sbloccherà il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo **delete_subscription_message()**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso utilizzata la proprietà **message_id** del messaggio, che rimane costante in tutti i tentativi di recapito.

## Come eliminare argomenti e sottoscrizioni

Gli argomenti e le sottoscrizioni sono persistenti e devono pertanto essere eliminati in modo esplicito tramite il [portale di gestione di Azure](https://manage.windowsazure.com) o a livello di codice. Nell'esempio seguente viene illustrato come eliminare l'argomento denominato "test-topic".

	azure_service_bus_service.delete_topic("test-topic")

Se si elimina un argomento, verranno eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Il seguente codice illustra come eliminare la sottoscrizione denominata "high-messages" dall'argomento "test-topic":

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i seguenti collegamenti per altre informazioni.

-   Vedere le informazioni di riferimento in MSDN: [Code, argomenti e sottoscrizioni](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx)
-   Riferimento sulle API per [SqlFilter](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx)
-	Archivio [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub
 

<!---HONumber=July15_HO4-->