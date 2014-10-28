<properties linkid="dev-ruby-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Ruby) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Ruby" description="Learn how to use Service Bus queues in Azure. Code samples written in Ruby." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Queues" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Come usare le code del bus di servizio

In questa guida verrà descritto come usare le code del bus di servizio. Gli esempi sono scritti
in Ruby e usano la gemma di Azure. Gli scenari
presentati includono **creazione di code invio e ricezione di messaggi**, nonché
**eliminazione di code**. Per altre informazioni sulle code, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni sulle code del bus di servizio][Informazioni sulle code del bus di servizio]
-   [Creare uno spazio dei nomi servizio][Creare uno spazio dei nomi servizio]
-   [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi][Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]
-   [Creazione di un'applicazione Ruby][Creazione di un'applicazione Ruby]
-   [Configurare l'applicazione per l'uso del bus di servizio][Configurare l'applicazione per l'uso del bus di servizio]
-   [Configurazione di una connessione del bus di servizio di Azure][Configurazione di una connessione del bus di servizio di Azure]
-   [Come creare una coda][Come creare una coda]
-   [Come inviare messaggi a una coda][Come inviare messaggi a una coda]
-   [Come ricevere messaggi da una coda][Come ricevere messaggi da una coda]
-   [Come gestire arresti anomali e messaggi illeggibili dell'applicazione][Come gestire arresti anomali e messaggi illeggibili dell'applicazione]
-   [Passaggi successivi][Passaggi successivi]

[WACOM.INCLUDE [howto-service-bus-queues][howto-service-bus-queues]]

## <span id="create-a-ruby-application"></span></a>Creazione di un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni, vedere [Creazione di un'applicazione Ruby in Azure][Creazione di un'applicazione Ruby in Azure].

## <span id="configure-your-application-to-use-service-bus"></span></a>Configurare l'applicazione per l'uso del bus di servizio

Per usare il bus di servizio di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di librerie che comunicano con i servizi di archiviazione REST.

### Usare RubyGems per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows,) **Terminale** (Mac) o **Bash** (Unix).

2.  Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Utilizzando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

    require "azure"

## <span id="setup-a-windows-azure-service-bus-connection"></span></a>Configurazione di una connessione del bus di servizio di Azure

Il modulo di Azure leggerà le variabili di ambiente **AZURE\_SERVICEBUS\_NAMESPACE** e **AZURE\_SERVICEBUS\_ACCESS\_KEY**
per ottenere le informazioni necessarie per la connessione allo spazio dei nomi del bus di servizio di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative allo spazio dei nomi prima di usare **Azure::ServiceBusService** con il codice seguente:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <span id="how-to-create-a-queue"></span></a>Come creare una coda

L'oggetto **Azure::ServiceBusService** consente di usare le code. Per creare una coda, usare il metodo **create\_queue()**. Nell'esempio seguente viene creata una coda o stampato l'eventuale errore.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

È inoltre possibile passare un oggetto **Azure::ServiceBus::Queue** con opzioni aggiuntive che consentono di sostituire le impostazioni predefinite degli argomenti, come ad esempio la durata dei messaggi o la dimensione massima della coda. Nell'esempio seguente viene illustrato come impostare la dimensione massima della coda su 5 GB e una durata di 1 minuto:

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

## <span id="how-to-send-messages-to-a-queue"></span></a>Come inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiamerà il metodo **send\_queue\_message()** sull'oggetto **Azure::ServiceBusService**. I messaggi inviati e ricevuti dalle code del bus di servizio sono oggetti **Azure::ServiceBus::BrokeredMessage** e includono un set di proprietà standard, ad esempio **label** e **time\_to\_live**, un dizionario utilizzato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo del messaggio passando un valore stringa come messaggio, in modo da popolare le proprietà standard necessarie con valori predefiniti.

Nell'esempio seguente viene illustrato come inviare un messaggio di prova alla coda denominata "test-queue" usando **send\_queue\_message()**:

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## <span id="how-to-receive-messages-from-a-queue"></span></a>Come ricevere messaggi da una coda

I messaggi vengono ricevuti da una coda tramite il metodo **receive\_queue\_message()** sull'oggetto **Azure::ServiceBusService**: Per impostazione predefinita, i messaggi vengono letti e bloccati senza essere eliminati dalla coda. È tuttavia possibile eliminare i messaggi dalla coda dopo essere stati letti impostando l'opzione **:peek\_lock** su **false**.

In base al comportamento predefinito, la lettura e l'eliminazione vengono incluse in un'operazione di ricezione suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando il metodo **delete\_queue\_message()** e fornendo il messaggio da eliminare come parametro. Il metodo **delete\_queue\_message()** contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

Se il parametro **:peek\_lock** è impostato su **false**, la lettura e l'eliminazione del messaggio costituiscono il modello più semplice, adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi usando **receive\_queue\_message()**. Nell'esempio viene innanzitutto ricevuto ed eliminato un messaggio con **:peek\_lock** impostato su **false**, quindi viene ricevuto un altro messaggio e il messaggio viene eliminato usando **delete\_queue\_message()**:

    message = azure_service_bus_service.receive_queue_message("test-queue", 
      { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

## <span id="how-to-handle-application-crashes-and-unreadable-messages"></span></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlock\_queue\_message()** sull'oggetto **Azure::ServiceBusService**. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata al metodo **delete\_queue\_message()**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso utilizzata la proprietà **message\_id** del messaggio, che rimane costante in tutti i tentativi di recapito.

## <span id="next-steps"></span></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, usare i collegamenti seguenti per altre informazioni.

-   Riferimento in MSDN: [Code, argomenti e sottoscrizioni][Code, argomenti e sottoscrizioni]
-   Repository [Azure SDK for Ruby][Azure SDK for Ruby] su GitHub

Per un confronto tra le code del bus di servizio di Azure discusse in questo articolo e il servizio di accodamento di Azure discusso nell'articolo [Come usare il servizio di accodamento di Azure][Come usare il servizio di accodamento di Azure], vedere [Code di Azure e Azure Service Bus: confronto e contrapposizioni][Code di Azure e Azure Service Bus: confronto e contrapposizioni]

  [Passaggi successivi]: #next-steps
  [Informazioni sulle code del bus di servizio]: #what-are-service-bus-queues
  [Creare uno spazio dei nomi servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Creazione di un'applicazione Ruby]: #create-a-ruby-application
  [Configurare l'applicazione per l'uso del bus di servizio]: #configure-your-application-to-use-service-bus
  [Configurazione di una connessione del bus di servizio di Azure]: #setup-a-windows-azure-service-bus-connection
  [Come creare una coda]: #how-to-create-a-queue
  [Come inviare messaggi a una coda]: #how-to-send-messages-to-a-queue
  [Come ricevere messaggi da una coda]: #how-to-receive-messages-from-a-queue
  [Come gestire arresti anomali e messaggi illeggibili dell'applicazione]: #how-to-handle-application-crashes-and-unreadable-messages
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [Creazione di un'applicazione Ruby in Azure]: /it-it/develop/ruby/tutorials/web-app-with-linux-vm/
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/it-it/library/windowsazure/hh367516.aspx
  [Azure SDK for Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
  [Come usare il servizio di accodamento di Azure]: /it-it/develop/ruby/how-to-guides/queue-service/
  [Code di Azure e Azure Service Bus: confronto e contrapposizioni]: http://msdn.microsoft.com/it-it/library/windowsazure/hh767287.aspx
