<properties 
    pageTitle="Come usare gli argomenti del bus di servizio (.NET) - Azure" 
    description="Informazioni su come usare le sottoscrizioni e gli argomenti di Bus di servizio in Azure. Gli esempi di codice sono scritti per applicazioni .NET." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor="mattshel"/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="02/26/2015" 
    ms.author="sethm"/>





# Come usare gli argomenti e le sottoscrizioni del bus di servizio

Questa guida descrive come usare gli argomenti e le sottoscrizioni del bus di servizio. Negli esempi, scritti in C#, viene usata l'API .NET. Gli scenari presentati includono **creazione di argomenti e sottoscrizioni, creazione di filtri per le sottoscrizioni, invio di messaggi** a un argomento, **ricezione di messaggi da una sottoscrizione** ed **eliminazione di argomenti e sottoscrizioni**. Per altre informazioni su argomenti e sottoscrizioni, vedere la sezione [Passaggi successivi][].

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Configurazione dell'applicazione per l'utilizzo del bus di servizio

Quando si crea un'applicazione che utilizza il bus di servizio, è necessario aggiungere un riferimento all'assembly del bus di servizio e includere gli spazi dei nomi corrispondenti.

## Ottenere il pacchetto NuGet del bus di servizio

Il pacchetto **NuGet** del bus di servizio è la soluzione più semplice per ottenere l'API del bus di servizio e per configurare l'applicazione con tutte le dipendenze del bus di servizio. L'estensione NuGet di Visual Studio semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e in Visual Studio Express. Il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio.

Per installare il pacchetto NuGet nell'applicazione, eseguire le seguenti operazioni:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliere **Gestisci pacchetti NuGet**.
2.  Cercare "Service Bus" e selezionare l'elemento **Bus di servizio di Microsoft Azure**. Fare clic su **Installa** per completare l'installazione, quindi chiudere la finestra di dialogo.

    ![][7]

È ora possibile scrivere codice per il bus di servizio.

## Come configurare una stringa di connessione per il bus di servizio

Per archiviare endpoint e credenziali, nel bus di servizio viene usata una stringa di connessione. È possibile inserire la stringa di connessione in un file di configurazione, anziché impostarla come hardcoded nel codice:

- Quando si usa Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione dei servizi di Azure (file`*.csdef` e `*.cscfg`).
- Quando si usa Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ad esempio il file `web.config`.

In entrambi i casi è possibile recuperare la stringa di connessione usando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### Configurazione della stringa di connessione per l'uso con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione.  Aggiungere ad esempio un'impostazione al file di definizione del servizio (`*.csdef`), come illustrato di seguito:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Specificare quindi i valori nel file di configurazione del servizio (`*.cscfg`):

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

Usare i valori chiave recuperati dal portale di gestione come descritto nella sezione precedente.

### Configurazione della stringa di connessione durante l'uso di Siti Web o Macchine virtuali

Con Siti Web o Macchine virtuali è consigliabile usare il sistema di configurazione .NET, ad esempio il file `web.config`).  Per archiviare la stringa di connessione, usare l'elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Usare i valori relativi ad autorità emittente e chiave recuperati dal portale di gestione come descritto nella sezione precedente.

## Come creare un argomento

Per eseguire operazioni di gestione per gli argomenti e le sottoscrizioni del bus di servizio, è possibile usare la classe **NamespaceManager**. La classe **NamespaceManager** fornisce i metodi per creare, enumerare ed eliminare le code. 

In questo esempio viene costruito un oggetto **NamespaceManager** mediante la classe **CloudConfigurationManager** di Azure con una stringa di connessione costituita dall'indirizzo di base di uno spazio dei nomi del bus di servizio e dalle credenziali appropriate che dispongono delle autorizzazioni per gestirlo. Il formato della stringa di connessione è il seguente:

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

Considerate, ad esempio, le impostazioni di configurazione della sezione precedente:

    // Create the topic if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Per il metodo **CreateTopic** sono disponibili overload che consentono di ottimizzare le proprietà dell'argomento, ad esempio impostare il valore predefinito della durata TTL da applicare ai messaggi inviati all'argomento. Per applicare queste impostazioni, viene usata la classe **TopicDescription**. Il seguente esempio illustra come creare un argomento denominato "TestTopic" con una dimensione massima pari a 5 GB e una durata predefinita per i messaggi pari a 1 minuto.

    // Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Topic with custom settings
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**Nota:** è possibile usare il metodo **TopicExists** su oggetti **NamespaceManager** per verificare se in uno spazio dei nomi del servizio esiste già un argomento con il nome specificato.

<h2>Come creare le sottoscrizioni</h2>

È anche possibile usare la classe **NamespaceManager** per creare sottoscrizioni di argomenti. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita l'insieme dei messaggi passati alla coda virtuale della sottoscrizione.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene usato se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nel seguente esempio viene creata una sottoscrizione denominata "AllMessages" e viene usato il filtro predefinito **MatchAll**.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Creare sottoscrizioni con i filtri

È inoltre possibile configurare filtri che consentono di specificare i messaggi inviati a un argomento da visualizzare in una sottoscrizione dell'argomento specifica.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **SqlFilter**, che implementa un subset di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altri dettagli sulle espressioni che è possibile usare con un filtro SQL, esaminare la sintassi di [SqlFilter.SqlExpression][SqlFilter].

Nel seguente esempio viene creata una sottoscrizione denominata "HighMessages" con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà **MessageNumber** personalizzata è maggiore di 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

Analogamente, nel seguente esempio viene creata una sottoscrizione denominata "LowMessages" con un filtro **SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà **MessageNumber** è minore o uguale a 3:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

Un messaggio inviato a "TestTopic" verrà sempre recapitato ai ricevitori con sottoscrizione dell'argomento "AllMessages" e recapitato selettivamente ai ricevitori con sottoscrizioni degli argomenti "HighMessages" e "LowMessages" (a seconda del contenuto del messaggio).

## Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, l'applicazione crea un oggetto **TopicClient** usando la stringa di connessione.

Il seguente codice illustra come creare un oggetto **TopicClient** per l'argomento "TestTopic" creato in precedenza tramite la chiamata API **CreateFromConnectionString**:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());
 

I messaggi inviati ad argomenti del bus di servizio sono istanze della classe **BrokeredMessage**. Gli oggetti **BrokeredMessage** includono un insieme di proprietà standard, ad esempio **Label** e **TimeToLive**, un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Per impostare il corpo del messaggio, un'applicazione può passare qualsiasi oggetto serializzabile nel costruttore di **BrokeredMessage**. In tal caso, per serializzare l'oggetto verrà usato il serializzatore **DataContractSerializer** appropriato. In alternativa, è possibile fornire un oggetto **System.IO.Stream**.

Il seguente esempio illustra come inviare cinque messaggi di test all'oggetto "TestTopic" **TopicClient** ottenuto nel frammento di codice riportato sopra.
Si noti come il valore della proprietà **MessageNumber** di ciascun messaggio varia nell'iterazione del ciclo, determinando le sottoscrizioni che lo riceveranno:

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;

       // Send message to the topic
       Client.Send(message);
     }

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB, in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## Come ricevere messaggi da una sottoscrizione

Il modo consigliato per ricevere i messaggi da una sottoscrizione consiste nell'usare un oggetto **SubscriptionClient**. È possibile usare gli oggetti **SubscriptionClient** in due diverse modalità: **ReceiveAndDelete** e **PeekLock**.

Quando si usa la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un messaggio in una sottoscrizione, lo contrassegna come utilizzato e lo restituisce all'applicazione. **La modalità ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** predefinita, il processo di ricezione viene suddiviso in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando **Complete** sul messaggio ricevuto. Quando il bus di servizio vede la chiamata **Complete**, contrassegna il messaggio come utilizzato e lo rimuove dalla sottoscrizione.

Il seguente esempio illustra come ricevere ed elaborare messaggi usando la modalità **PeekLock**. Per specificare un valore **ReceiveMode** diverso, è possibile usare un altro overload per **CreateFromConnectionString**. In questo esempio viene usato il callback **OnMessage** e i messaggi vengono elaborati non appena arrivano nella sottoscrizione "HighMessages".

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    subscriptionClientHigh.OnMessage((message) =>
    {
        try
        {
            // Process message from subscription
            Console.WriteLine("\n**High Messages**");
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Message Number: " +
                message.Properties["MessageNumber"]);

            // Remove message from subscription
            message.Complete();
        }
        catch (Exception)
        {
            // Indicates a problem, unlock message in subscription
            message.Abandon();
        }
    }, options);

In questo esempio viene usato il callback **OnMessage** usando un oggetto **OnMessageOptions**. **AutoComplete** è impostato su **false** per abilitare  il controllo manuale della chiamata **Complete** sul messaggio ricevuto.
Se **AutoRenewTimeout** è impostato su un minuto, il client dovrà attendere fino a un minuto per ricevere un messaggio prima del timeout della chiamata ed effettuerà una nuova chiamata per verificare i messaggi. In questo modo si riduce il numero di volte in cui il client effettua chiamate addebitabili che non consentono di recuperare i messaggi.

## Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevente non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **Abandon**, anziché **Complete**, sul messaggio ricevuto. In questo modo, il bus di servizio sblocca il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio, ma prima dell'invio della richiesta **Complete**, il messaggio verrà recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

## Come eliminare argomenti e sottoscrizioni

Il seguente esempio illustra come eliminare l'argomento denominato **TestTopic** dallo spazio dei nomi del servizio **HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

Se si elimina un argomento, verranno eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Il seguente codice illustra come eliminare una sottoscrizione denominata **HighMessages** dall'argomento **TestTopic**:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base degli argomenti e delle sottoscrizioni del bus di servizio, usare i seguenti collegamenti per altre informazioni.

-   Vedere le informazioni di riferimento in MSDN: [Code, argomenti e sottoscrizioni][].
-   Informazioni di riferimento sulle API per [SqlFilter][].
-   Per creare un'applicazione funzionante che invia e riceve messaggi verso e da una coda del bus di servizio: [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET][].

  [Passaggi successivi]: #nextsteps
  [Informazioni su argomenti e sottoscrizioni del bus di servizio]: #what-is
  [Creare uno spazio dei nomi del servizio]: #create-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-creds
  [Configurare l'applicazione per l'uso del bus di servizio]: #configure-app
  [Procedura: Configurare una stringa di connessione per il bus di servizio]: #set-up-connstring
  [Procedura: Configurare la stringa di connessione]: #config-connstring
  [Procedura: Creare un argomento]: #create-topic
  [Procedura: Creare sottoscrizioni]: #create-subscriptions
  [Procedura: Inviare messaggi a un argomento]: #send-messages
  [Procedura: Ricevere messaggi da una sottoscrizione]: #receive-messages
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #handle-crashes
  [Procedura: Eliminare argomenti e sottoscrizioni]: #delete-topics
  
  [Concetti relativi agli argomenti]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET]: http://msdn.microsoft.com/library/hh367512.aspx

<!--HONumber=47-->
 