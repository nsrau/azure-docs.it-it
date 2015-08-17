<properties
    pageTitle="Come usare le code del bus di servizio (.NET) - Azure"
    description="Informazioni su come usare le code di Bus di servizio in Azure. Gli esempi di codice sono scritti in C# mediante l'API .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="sethm"/>

# Come usare le code del bus di servizio

Questa guida illustra come usare le code del bus di servizio. Negli esempi, scritti in C#, viene usata l'API .NET. Gli scenari presentati includono **creazione di code** e **invio e ricezione di messaggi**. Per ulteriori informazioni sulle code, vedere la sezione [Passaggi successivi](#Next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Configurazione dell'applicazione per l'uso del bus di servizio

Quando si crea un'applicazione che usa il bus di servizio, è necessario aggiungere un riferimento all'assembly del bus di servizio e includere gli spazi dei nomi corrispondenti.

## Aggiungere il pacchetto NuGet del bus di servizio

Il pacchetto **NuGet** del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio. L'estensione NuGet di Visual Studio semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e in Visual Studio Express. Il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio.

Per installare il pacchetto NuGet nell'applicazione, eseguire le operazioni seguenti:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su **Manage NuGet Packages**.
2.  Cercare "Bus di servizio" e selezionare l'elemento **Bus di servizio di Microsoft Azure**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

    ![][7]

È ora possibile scrivere codice per il bus di servizio.

## Come configurare una stringa di connessione per il bus di servizio

Per archiviare endpoint e credenziali, nel bus di servizio viene usata una stringa di connessione. È possibile inserire la stringa di connessione in un file di configurazione, anziché impostarla come hardcoded:

- Quando si usa Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione usando il sistema di configurazione dei servizi di Azure (file ****.csdef** e ****.cscfg**).
- Quando si usa Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ad esempio il file **Web.config**.

In entrambi i casi è possibile recuperare la stringa di connessione usando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### Configurazione della stringa di connessione per l'uso con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione. Aggiungere ad esempio un'etichetta `Setting` al file di definizione del servizio (****.csdef**), come illustrato di seguito:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Specificare quindi i valori nel file di configurazione del servizio (****.cscfg**):

    <ServiceConfiguration serviceName="Azure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString"
                         value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Usare i valori relativi alla chiave e al nome della chiave di firma di accesso condiviso recuperati dal portale di gestione come descritto nella sezione precedente.

### Configurazione della stringa di connessione durante l'uso di Siti Web o Macchine virtuali

Con Siti Web o Macchine virtuali è consigliabile usare il sistema di configurazione .NET, ad esempio il file **Web.config**. Per archiviare la stringa di connessione, usare l'elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Usare i valori relativi alla chiave e al nome della firma di accesso condiviso recuperati dal portale di gestione come descritto nella sezione precedente.

## Come creare una coda

Per eseguire operazioni di gestione per le code del bus di servizio, è possibile usare la [classe `NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Questa classe fornisce i metodi per creare, enumerare ed eliminare le code.

In questo esempio viene costruito un oggetto `NamespaceManager` mediante la classe `CloudConfigurationManager` di Azure con una stringa di connessione costituita dall'indirizzo di base di uno spazio dei nomi del servizio del bus di servizio e dalle credenziali di firma di accesso condiviso appropriate che dispongono delle autorizzazioni per gestirlo. Il formato della stringa di connessione è il seguente:

    Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey

Considerate, ad esempio, le impostazioni di configurazione della sezione precedente:

    // Create the queue if it does not exist already
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Per il metodo [`CreateQueue`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) sono disponibili overload che consentono di ottimizzare le proprietà della coda, ad esempio per impostare il valore di durata TTL predefinito da applicare ai messaggi inviati alla coda. Per applicare queste impostazioni, viene usata la classe [`QueueDescription`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). L'esempio seguente illustra come creare una coda denominata "TestQueue" con una dimensione massima pari a 5 GB e una durata predefinita per i messaggi pari a 1 minuto:

    // Configure queue settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new queue with custom settings
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

> [AZURE.NOTE]È possibile usare il metodo [`QueueExists`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) negli oggetti [`NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) per verificare se in uno spazio dei nomi del servizio esiste già una coda con il nome specificato.

## Come inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, nell'applicazione viene usata la stringa di connessione per creare un oggetto [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx).

Nel codice seguente viene illustrato come creare un oggetto [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) per la coda "TestQueue" appena creata usando la chiamata API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx):

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client =
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

I messaggi inviati e ricevuti dalle code del bus di servizio sono istanze della classe [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Gli oggetti `BrokeredMessage` hanno un set di proprietà standard, ad esempio [`Label`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [`TimeToLive`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx), un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Per impostare il corpo del messaggio, un'applicazione può passare qualsiasi oggetto serializzabile nel costruttore dell'oggetto [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). In tal caso per serializzare l'oggetto, verrà usato l'oggetto **DataContractSerializer** appropriato. In alternativa, è possibile fornire un oggetto **System.IO.Stream**.

L'esempio seguente illustra come inviare cinque messaggi di prova all'oggetto [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) "TestQueue" ottenuto nel frammento di codice riportato sopra:

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;

       // Send message to the queue
       Client.Send(message);
     }

Le code del bus di servizio supportano [messaggi di dimensioni massime pari a 256 KB](service-bus-quotas.md), in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB. Se il partizionamento è abilitato, il limite superiore è più elevato. Per altre informazioni, vedere [Partizionamento delle entità di messaggistica](https://msdn.microsoft.com/library/azure/dn520246.aspx).

## Come ricevere messaggi da una coda

Il metodo consigliato per ricevere messaggi da una coda consiste nell'usare un oggetto [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Gli oggetti `QueueClient` possono essere usati in due modalità diverse: [`ReceiveAndDelete` e `PeekLock`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Quando si usa la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un messaggio in una coda, lo contrassegna come utilizzato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** predefinita, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. Quando il bus di servizio vede la chiamata `Complete`, contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

L'esempio seguente illustra come ricevere ed elaborare messaggi usando la modalità **PeekLock** predefinita. Per specificare un valore [`ReceiveMode`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) diverso, è possibile usare un altro overload di [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx). In questo esempio viene usato il callback [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) e i messaggi vengono elaborati non appena arrivano in **TestQueue**.

    string connectionString =
      CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    QueueClient Client =
      QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    // Callback to handle received messages
    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from queue
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Test Property: " +
            message.Properties["TestProperty"]);

            // Remove message from queue
            message.Complete();
        }
            catch (Exception)
        {
            // Indicates a problem, unlock message in queue
            message.Abandon();
        }
    }, options);

In questo esempio viene configurato il callback [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) usando un oggetto [`OnMessageOptions`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [`AutoComplete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) è impostato su **false** per abilitare il controllo manuale quando è necessario chiamare [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. [`AutoRenewTimeout`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) è impostato su 1 minuto, quindi il client attende di controllare i messaggi per un minuto al massimo, prima che si verifichi il timeout della chiamata e il client effettui una nuova chiamata. Il valore di questa proprietà riduce il numero di volte in cui il client effettua chiamate addebitabili che non consentono di recuperare i messaggi.

## Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso  
di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevente non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo [`Abandon`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) anziché [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. In questo modo, il bus di servizio sblocca il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio, ma prima dell'invio della richiesta [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), il messaggio verrà recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà [`MessageId`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del messaggio, che rimane costante in tutti i tentativi di recapito.

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, usare i seguenti collegamenti per altre informazioni.

-   Vedere i cenni preliminari in MSDN: [Code, argomenti e sottoscrizioni][].
-   Per creare un'applicazione funzionante che invia e riceve messaggi verso e da una coda del bus di servizio: [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET].
-   Esempi del bus di servizio: scaricarli dagli [esempi di Azure][] o vedere i cenni preliminari in [MSDN][].

  [What are Service Bus Queues]: #what-queues
  [Create a Service Namespace]: #create-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-creds
  [Configure Your Application to Use Service Bus]: #configure-app
  [How to: Set Up a Service Bus Connection String]: #set-up-connstring
  [How to: Configure your Connection String]: #config-connstring
  [How to: Create a Queue]: #create-queue
  [How to: Send Messages to a Queue]: #send-messages
  [How to: Receive Messages from a Queue]: #receive-messages
  [How to: Handle Application Crashes and Unreadable Messages]: #handle-crashes
  [Azure Management Portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/library/azure/hh367516.aspx
  [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET]: http://msdn.microsoft.com/library/azure/hh367512.aspx
  [esempi di Azure]: https://code.msdn.microsoft.com/windowsazure/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [MSDN]: https://msdn.microsoft.com/library/azure/dn194201.aspx
 

<!---HONumber=August15_HO6-->