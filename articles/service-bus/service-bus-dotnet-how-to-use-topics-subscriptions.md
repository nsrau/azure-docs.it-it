<properties
    pageTitle="Come usare gli argomenti del bus di servizio (.NET) | Microsoft Azure"
	description="Informazioni su come usare le sottoscrizioni e gli argomenti di Bus di servizio in Azure. Gli esempi di codice sono scritti per applicazioni .NET."
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

# Come usare gli argomenti e le sottoscrizioni del bus di servizio di Azure

Questo articolo descrive come usare gli argomenti e le sottoscrizioni del bus di servizio. Gli esempi sono scritti in C# e usano le API .NET. Gli scenari presentati includono creazione di argomenti e sottoscrizioni, creazione di filtri per le sottoscrizioni, invio di messaggi a un argomento, ricezione di messaggi da una sottoscrizione ed eliminazione di argomenti e sottoscrizioni. Per ulteriori informazioni sugli argomenti e sulle sottoscrizioni, vedere la sezione [Passaggi successivi](#Next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Configurazione dell'applicazione per l'uso del bus di servizio

Quando si crea un'applicazione che usa il bus di servizio, è necessario aggiungere un riferimento all'assembly del bus di servizio e includere gli spazi dei nomi corrispondenti.

## Ottenere il pacchetto NuGet del bus di servizio

Il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio. L'estensione NuGet di Visual Studio semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e in Visual Studio Express. Il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio.

Per installare il pacchetto NuGet nell'applicazione, eseguire le operazioni seguenti:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su **Manage NuGet Packages**.
2.  Cercare "Bus di servizio" e selezionare l'elemento **Bus di servizio di Microsoft Azure**. Fare clic su **Installa** per completare l'installazione, quindi chiudere la finestra di dialogo successiva.

    ![][7]

È ora possibile scrivere codice per il bus di servizio.

## Come configurare una stringa di connessione per il bus di servizio

Per archiviare endpoint e credenziali, nel bus di servizio viene usata una stringa di connessione. È possibile inserire la stringa di connessione in un file di configurazione, anziché impostarla come hardcoded:

- Quando si usa Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione usando il sistema di configurazione dei servizi di Azure (file .csdef e .cscfg).
- Quando si usa Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ad esempio il file Web.config.

In entrambi i casi è possibile recuperare la stringa di connessione usando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### Configurazione della stringa di connessione per l'uso con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione. Aggiungere ad esempio un'etichetta `Setting` al file di definizione del servizio (****.csdef**), come illustrato di seguito.

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Specificare quindi i valori nel file di configurazione del servizio (.cscfg).

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

### Configurazione della stringa di connessione durante l'uso di siti Web Azure o macchine virtuali di Azure

Con Siti Web o Macchine virtuali è consigliabile usare il sistema di configurazione .NET, ad esempio il file Web.config. Per archiviare la stringa di connessione, usare l'elemento `<appSettings>`.

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Usare i valori relativi alla chiave e al nome della firma di accesso condiviso recuperati dal portale di gestione come descritto nella sezione precedente.

## Come creare un argomento

È possibile eseguire le operazioni di gestione per le sottoscrizioni e gli argomenti del bus di servizio usando la classe [`NamespaceManager`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx). Questa classe fornisce metodi per creare, enumerare ed eliminare gli argomenti.

L'esempio seguente crea un oggetto `NamespaceManager` usando la classe `CloudConfigurationManager` di Azure con una stringa di connessione composta dall'indirizzo di base di uno spazio dei nomi del servizio di bus di servizio e dalle appropriate credenziali di firma di accesso condiviso con le autorizzazioni per gestirlo. Il formato della stringa di connessione è il seguente.

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

Usare il seguente esempio, per le impostazioni di configurazione della sezione precedente.

    // Create the topic if it does not exist already.
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Sono presenti overload del metodo [`CreateTopic`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.createtopic.aspx) che consentono di ottimizzare le proprietà dell'argomento, ad esempio, per impostare il valore predefinito della durata da applicare ai messaggi inviati all'argomento. Per applicare queste impostazioni, viene usata la classe [`TopicDescription`](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.topicdescription.aspx). L'esempio seguente illustra come creare un argomento denominato TestTopic con una dimensione massima di 5 GB e una durata predefinita del messaggio pari a 1 minuto.

    // Configure Topic Settings.
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Topic with custom settings.
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

> [AZURE.NOTE]È possibile usare il metodo [`TopicExists`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.topicexists.aspx) sugli oggetti [`NamespaceManager`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx) per verificare se in uno spazio dei nomi del servizio esiste già un argomento con il nome specificato.

## Come creare una sottoscrizione

È inoltre possibile creare sottoscrizioni dell'argomento usando la classe [`NamespaceManager`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx). Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi passati alla coda virtuale della sottoscrizione.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene utilizzato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata "AllMessages" e viene utilizzato il filtro predefinito **MatchAll**.

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Creare sottoscrizioni con i filtri

È inoltre possibile impostare i filtri che consentono di specificare quali messaggi inviati a un argomento devono essere presenti in una specifica sottoscrizione dell'argomento.

Il tipo di filtro più flessibile supportato dalle sottoscrizioni è la classe [SqlFilter] che implementa un subset di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altre informazioni sulle espressioni che possono essere usate con un filtro SQL, vedere la sintassi [SqlFilter.SqlExpression][].

L'esempio seguente crea una sottoscrizione denominata **HighMessages** con un oggetto [SqlFilter] che seleziona solo i messaggi che dispongono di una proprietà personalizzata **MessageNumber** maggiore di 3.

     // Create a "HighMessages" filtered subscription.
     SqlFilter highMessagesFilter =
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic",
        "HighMessages",
        highMessagesFilter);

Analogamente, l'esempio seguente crea una sottoscrizione denominata **LowMessages** con un filtro [SqlFilter] che seleziona solo i messaggi in cui il valore della proprietà **MessageNumber** è minore o uguale a 3.

     // Create a "LowMessages" filtered subscription.
     SqlFilter lowMessagesFilter =
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic",
        "LowMessages",
        lowMessagesFilter);

Quando un messaggio viene inviato a `TestTopic`, viene sempre recapitato ai ricevitori aderenti alla sottoscrizione dell'argomento **AllMessages** e viene recapitato selettivamente ai ricevitori aderenti alle sottoscrizioni dell'argomento **HighMessages** e **LowMessages** (a seconda del contenuto del messaggio).

## Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, l'applicazione crea un oggetto [`TopicClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) usando la stringa di connessione.

Il codice seguente illustra come creare un oggetto [`TopicClient`](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.topicclient.aspx) per l'argomento **TestTopic** creato in precedenza tramite la chiamata all'API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx).

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client =
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());


I messaggi inviati ad argomenti del bus di servizio sono istanze della classe [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Gli oggetti **BrokeredMessage** dispongono di un set di proprietà standard (ad esempio [`Label`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [`TimeToLive`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo del messaggio passando un oggetto serializzabile al costruttore dell'oggetto [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) e l'elemento **DataContractSerializer** appropriato viene quindi usato per serializzare l'oggetto. In alternativa, è possibile fornire un oggetto **System.IO.Stream**.

L'esempio seguente illustra come inviare cinque messaggi di prova all'oggetto **TestTopic** [`TopicClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) ottenuto nell’esempio di codice precedente. Si noti che il valore della proprietà [`MessageNumber`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) di ogni messaggio varia a seconda dell'iterazione del ciclo (che determina quali sottoscrizioni lo riceveranno).

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body.
       BrokeredMessage message =
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property.
       message.Properties["MessageNumber"] = i;

       // Send message to the topic.
       Client.Send(message);
     }

Gli argomenti del bus di servizio supportano una [dimensione massima di 256 KB per il messaggio](service-bus-quotas.md) (l'intestazione, che include le proprietà standard e personalizzate dell'applicazione, può avere una dimensione massima di 64 KB). Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB. Se il partizionamento è abilitato, il limite superiore è più elevato. Per altre informazioni, vedere [Partizionamento delle entità di messaggistica](https://msdn.microsoft.com/library/azure/dn520246.aspx).

## Come ricevere messaggi da una sottoscrizione

Il metodo consigliato per ricevere messaggi da una sottoscrizione consiste nell'usare un oggetto [`SubscriptionClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx). Gli oggetti **SubscriptionClient** possono essere usati in due modalità diverse: [`ReceiveAndDelete` e `PeekLock`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Quando si utilizza la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve una richiesta di lettura relativa a un messaggio in una sottoscrizione, lo contrassegna come utilizzato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** predefinita, il processo di ricezione viene suddiviso in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. Quando il bus di servizio vede la chiamata `Complete`, contrassegna il messaggio come utilizzato e lo rimuove dalla sottoscrizione.

L'esempio seguente illustra come ricevere ed elaborare messaggi usando la modalità **PeekLock** predefinita. Per specificare un valore [`ReceiveMode`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) diverso, è possibile usare un altro overload per [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Questo esempio usa il callback [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) per elaborare i messaggi non appena arrivano nella sottoscrizione **HighMessages**.

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client =
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    // Configure the callback options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from subscription.
            Console.WriteLine("\n**High Messages**");
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Message Number: " +
                message.Properties["MessageNumber"]);

            // Remove message from subscription.
            message.Complete();
        }
        catch (Exception)
        {
            // Indicates a problem, unlock message in subscription.
            message.Abandon();
        }
    }, options);

Questo esempio configura il callback [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) usando un oggetto [`OnMessageOptions`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [`AutoComplete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) è impostato su **false** per abilitare il controllo manuale della chiamata [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. Se [`AutoRenewTimeout`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) è impostato su 1 minuto, il client dovrà attendere fino a un minuto per ricevere un messaggio prima del timeout della chiamata ed effettuerà una nuova chiamata per verificare i messaggi. Il valore di questa proprietà riduce il numero di volte in cui il client effettua chiamate addebitabili che non consentono di recuperare i messaggi.

## Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevente non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo [`Abandon`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) anziché [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sul messaggio ricevuto. In questo modo, il bus di servizio sblocca il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio, ma prima dell'invio della richiesta [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), il messaggio verrà recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà [`MessageId`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del messaggio, che rimane costante in tutti i tentativi di recapito.

## Come eliminare argomenti e sottoscrizioni

L'esempio seguente illustra come eliminare l'argomento denominato **TestTopic** dallo spazio dei nomi del servizio **HowToSample**.

     // Delete Topic.
     namespaceManager.DeleteTopic("TestTopic");

Se si elimina un argomento, vengono eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Nel codice seguente viene illustrato come eliminare una sottoscrizione denominata **HighMessages** dall'argomento **TestTopic**.

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base degli argomenti e delle sottoscrizioni del bus di servizio, usare i seguenti collegamenti per altre informazioni.

-   Vedere le informazioni di riferimento in MSDN: [Code, argomenti e sottoscrizioni][].
-   Riferimento sulle API per [SqlFilter][]
-   Per creare un'applicazione funzionante che invia e riceve messaggi verso e da una coda del bus di servizio: [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET][].
-   Esempi del bus di servizio: scaricarli dagli [esempi di Azure][] o vedere i cenni preliminari in [MSDN][].

  [Azure portal]: http://manage.windowsazure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET]: http://msdn.microsoft.com/library/azure/hh367512.aspx
  [esempi di Azure]: https://code.msdn.microsoft.com/windowsazure/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [MSDN]: https://msdn.microsoft.com/library/azure/dn194201.aspx

<!---HONumber=August15_HO9-->