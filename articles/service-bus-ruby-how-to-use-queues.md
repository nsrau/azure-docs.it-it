<properties 
	pageTitle="Come usare le code del bus di servizio (Ruby) - Azure" 
	description="Informazioni su come usare le code di Bus di servizio in Azure. Gli esempi di codice sono scritti in Ruby." 
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
	ms.date="11/25/2014" 
	ms.author="tomfitz"/>




# Come usare le code del bus di servizio

Questa guida descrive come usare le code del bus di servizio. Gli esempi sono scritti in Ruby e usano la gemma di Azure. Gli scenari presentati includono **creazione di code, invio e ricezione di messaggi**, nonché **eliminazione di code**. Per altre informazioni sulle code, vedere la sezione [Passaggi successivi](#next-steps) .

## Informazioni sulle code del bus di servizio

Le code del bus di servizio supportano un modello di **comunicazione con messaggistica negoziata**. Quando si utilizzano le code, i componenti di un'applicazione distribuita non comunicano direttamente l'uno con l'altro ma scambiano messaggi tramite una coda, che agisce da intermediario. Un producer di messaggi (mittente) invia un messaggio alla coda e quindi prosegue con la relativa elaborazione.
In modo asincrono, il consumer di messaggi (ricevitore) recupera il messaggio dalla coda e lo elabora. Il producer non deve attendere la risposta del consumer per continuare a elaborare e inviare ulteriori messaggi. Le code consentono un recapito dei messaggi di tipo **First In, First Out (FIFO)** a uno o più consumer concorrenti. In base a questo metodo, in genere i messaggi vengono ricevuti ed elaborati nell'ordine temporale in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuto ed elaborato da un solo consumer.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Le code del bus di servizio sono una tecnologia di carattere generale che può essere usata in numerosi scenari:

-   Comunicazione tra ruoli Web e di lavoro in un'applicazione 
    Azure multilivello
-   Comunicazione tra app locali e app ospitate in Azure in una soluzione ibrida
-   Comunicazione tra componenti di un'applicazione distribuita in esecuzione in locale in organizzazioni diverse o in reparti diversi della stessa organizzazione

L'uso delle code consente la scalabilità orizzontale delle applicazioni e garantisce maggiore resilienza all'architettura.

## Creare uno spazio dei nomi del servizio
Per iniziare a usare le code del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi del servizio che fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione. È necessario creare lo spazio dei nomi tramite l'interfaccia della riga di comando perché nel portale non è possibile creare il bus di servizio con una connessione ACS.

Per creare uno spazio dei nomi del servizio:

1. Aprire la console di Azure PowerShell.

2. Digitare il comando per creare uno spazio dei nomi del bus di servizio di Azure, come mostrato di seguito. Specificare il valore dello spazio dei nomi e specificare la stessa area dell'applicazione. 

    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)

## Recuperare le credenziali di gestione per lo spazio dei nomi
Per poter eseguire le operazioni di gestione, ad esempio creare una coda, nel nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione per lo spazio dei nomi.

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).

2. Selezionare lo spazio dei nomi bus di servizio creato.

     ![Select namespace](./media/service-bus-ruby-how-to-use-queues/selectns.png)

3. In basso selezionare **Informazioni di connessione**.

      ![Select connection](./media/service-bus-ruby-how-to-use-queues/selectconnection.png)

4. Copiare la chiave predefinita. Questo valore verrà usato nel codice.

       ![Copy key](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

## Creare un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni, vedere la pagina relativa alla [creazione di un'applicazione Ruby in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configurare l'applicazione per l'uso del bus di servizio

Per usare il bus di servizio di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Usare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix).

2. Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

    require "azure"

## Configurare una connessione del bus di servizio di Azure

Il modulo di Azure leggerà le variabili di ambiente **AZURE\_SERVICEBUS\_NAMESPACE** e **AZURE\_SERVICEBUS\_ACCESS_KEY** per ottenere le informazioni necessarie per la connessione allo spazio dei nomi del bus di servizio di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative allo spazio dei nomi prima di usare **Azure::ServiceBusService** con il seguente codice:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

Impostare il valore dello spazio dei nomi bus di servizio sul valore creato invece che sull'intero URL. Ad esempio, usare **"yourexamplenamespace"** e non "yourexamplenamespace.servicebus.windows.net". 

## Come creare una coda

L'oggetto **Azure::ServiceBusService** consente di usare le code. Per creare una coda, usare il metodo **create_queue()**. Nel seguente esempio viene creata una coda o stampato l'eventuale errore.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

È anche possibile passare un oggetto **Azure::ServiceBus::Queue** con opzioni aggiuntive che permettono di sostituire le impostazioni predefinite delle code, come ad esempio la durata (TTL) dei messaggi o la dimensione massima della coda. Il seguente esempio illustra come impostare la dimensione massima della coda su 5 GB e una durata di 1 minuto:

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

## Come inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiamerà il metodo **send\_queue\_message()** sull'oggetto **Azure::ServiceBusService**. I messaggi inviati e ricevuti dalle code del bus di servizio sono oggetti **Azure::ServiceBus::BrokeredMessage** e includono un set di proprietà standard, ad esempio **label** e**time\_to\_live**, un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo del messaggio passando un valore stringa come messaggio, in modo da popolare le proprietà standard necessarie con valori predefiniti.

Il seguente esempio illustra come inviare un messaggio di prova alla coda denominata "test-queue" usando **send\_queue\_message()**:

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## Come ricevere messaggi da una coda

I messaggi vengono ricevuti da una coda tramite il metodo **receive\_queue\_message()** sull'oggetto **Azure::ServiceBusService**. Per impostazione predefinita, i messaggi vengono letti e bloccati senza essere eliminati dalla coda. È tuttavia possibile eliminare dalla coda i messaggi che sono stati letti impostando l'opzione **:peek_lock** su **false**.

In base al comportamento predefinito, la lettura e l'eliminazione vengono incluse in un'operazione di ricezione suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando il metodo **delete\_queue\_message()** e fornendo il messaggio da eliminare come parametro. Il metodo **delete\_queue\_message()** contrassegna il messaggio come usato e lo rimuove dalla sottoscrizione.

Se il parametro **:peek\_lock** è impostato su **false**, la lettura e l'eliminazione del messaggio costituiscono il modello più semplice, adatto per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come usato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio usato prima dell'arresto anomalo risulterà perso.

Il seguente esempio illustra come ricevere ed elaborare messaggi usando **receive\_queue\_message()**. Nell'esempio viene innanzitutto ricevuto ed eliminato un messaggio con **:peek\_lock** impostato su **false**, quindi viene ricevuto un altro messaggio, che viene eliminato mediante **delete\_queue\_message()**:

    message = azure_service_bus_service.receive_queue_message("test-queue", 
	  { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

## Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **unlock\_queue\_message()** sull'oggetto **Azure::ServiceBusService**. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio, ma prima della chiamata al metodo **delete\_queue\_message()**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **message\_id** del messaggio, che rimane costante in tutti i tentativi di recapito.

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, usare i seguenti collegamenti per altre informazioni.

-   Vedere le informazioni di riferimento in MSDN: [Code, argomenti e sottoscrizioni](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx)
-   Repository [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub

Per un confronto tra le code del bus di servizio di Azure illustrate in questo articolo e il servizio di accodamento di Azure illustrato nell'articolo relativo all'[uso del servizio di accodamento di Azure](/develop/ruby/how-to-guides/queue-service/), vedere l'articolo relativo alle [analogie e differenze tra le code di Azure e le code del bus di servizio di Azure](http://msdn.microsoft.com/library/windowsazure/hh767287.aspx)

<!--HONumber=47-->
