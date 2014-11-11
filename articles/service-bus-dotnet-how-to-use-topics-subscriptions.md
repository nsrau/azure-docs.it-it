<properties linkid="dev-net-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (.NET) - Azure" metaKeywords="Get started Azure Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions C# " description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for .NET applications. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Come utilizzare gli argomenti e le sottoscrizioni del bus di servizio

<span>Questa guida mostra come utilizzare gli argomenti e le sottoscrizioni
del bus di servizio. Negli esempi, scritti in C#, viene utilizzata l'API .NET. Gli
scenari presentati includono **creazione di argomenti e sottoscrizioni,
creazione di filtri per le sottoscrizioni, invio di messaggi a un argomento**, **ricezione
di messaggi da una sottoscrizione** ed **eliminazione di argomenti e
sottoscrizioni**. Per altre informazioni su argomenti e sottoscrizioni, vedere
la sezione [Passaggi successivi][Passaggi successivi]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <span class="short-header">Configurazione dell'applicazione</span>Configurazione dell'applicazione per l'utilizzo del bus di servizio

Quando si crea un'applicazione che usa il bus di servizio, è necessario
aggiungere un riferimento all'assembly del bus di servizio e includere
gli spazi dei nomi corrispondenti.

## <span class="short-header">Recupero del pacchetto NuGet</span>Recupero del pacchetto NuGet del bus di servizio

Il pacchetto **NuGet** del bus di servizio è il modo più semplice per
recuperare l'API del bus di servizio e configurare l'applicazione con tutte le
dipendenze di tale servizio. L'estensione NuGet di Visual Studio semplifica
l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e
Visual Studio Express 2012 per il Web. Il pacchetto NuGet del bus di servizio è il modo più
semplice per recuperare l'API del bus di servizio e configurare l'applicazione
con tutte le dipendenze di tale servizio.

Per installare il pacchetto NuGet nell'applicazione, eseguire le operazioni seguenti:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su
    **Manage NuGet Packages**.
2.  Cercare "WindowsAzure" e selezionare l'elemento **Azure
    Service Bus**. Fare clic su **Installa** per completare l'installazione,
    quindi chiudere questa finestra di dialogo.

    ![][0]

È ora possibile scrivere codice per il bus di servizio.

## <span class="short-header">Come configurare la stringa di connessione</span>Come configurare una stringa di connessione per il bus di servizio

Per archiviare endpoint e credenziali, nel bus di servizio viene utilizzata una stringa di connessione. È possibile inserire la stringa di connessione in un file di configurazione, anziché impostarla come hardcoded nel codice:

-   Quando si usa Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione dei servizi di Azure (file `*.csdef` e `*.cscfg`).
-   Quando si usa Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ad esempio il file `web.config`.

In entrambi i casi è possibile recuperare la stringa di connessione usando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### <a name="config-connstring"> </a>Configurazione della stringa di connessione per l'utilizzo con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud
di Azure e consente di modificare dinamicamente le impostazioni
di configurazione dal portale di gestione di Azure senza ridistribuire
l'applicazione. Aggiungere ad esempio un'impostazione al file di definizione del servizio (`*.csdef`), come illustrato di seguito:

    <ServiceDefinition name="WindowsAzure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Specificare quindi i valori nel file di configurazione del servizio (`*.cscfg`):

    <ServiceConfiguration serviceName="WindowsAzure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" 
                         value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Usare i valori relativi ad autorità emittente e chiave recuperati dal portale
di gestione, come descritto nella sezione precedente.

### Configurazione della stringa di connessione durante l'uso di Siti Web o Macchine virtuali

Con Siti Web o Macchine virtuali è consigliabile usare il sistema di configurazione .NET, ad esempio il file `web.config`. Per archiviare la stringa di connessione, usare l'elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
        </appSettings>
    </configuration>

Usare i valori relativi ad autorità emittente e chiave recuperati dal portale
di gestione, come descritto nella sezione precedente.

## <span class="short-header">Come creare un argomento</span>Come creare un argomento

Per eseguire operazioni di gestione per gli argomenti e le sottoscrizioni del bus di servizio, è possibile utilizzare la classe **NamespaceManager**. La classe **NamespaceManager** fornisce i metodi per creare, enumerare ed eliminare le code.

In questo esempio viene costruito un oggetto **NamespaceManager** mediante la classe **CloudConfigurationManager** di Azure
con una stringa di connessione costituita dall'indirizzo di base di uno spazio dei nomi del bus di servizio e dalle credenziali
appropriate che dispongono delle autorizzazioni per gestirlo. Il formato della stringa di connessione è il seguente:

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedSecretIssuer=<issuerName>;SharedSecretValue=<yourDefaultKey>

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

Per il metodo **CreateTopic** sono disponibili overload che consentono di ottimizzare
le proprietà dell'argomento, ad esempio impostare il valore predefinito
della durata TTL da applicare ai messaggi inviati all'argomento. Queste impostazioni
vengono applicate mediante la classe **TopicDescription**. L'esempio seguente mostra come
creare un argomento denominato "TestTopic" con una dimensione massima pari
a 5 GB e una durata TTL predefinita per i messaggi pari a 1 minuto.

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

**Nota:** è possibile usare il metodo **TopicExists** su oggetti **NamespaceManager**
per verificare se in uno spazio dei nomi del servizio esiste già un argomento
con il nome specificato.

## <span class="short-header">Come creare sottoscrizioni</span>Come creare sottoscrizioni

È inoltre possibile utilizzare la classe **NamespaceManager**
 per creare sottoscrizioni di argomenti. Le sottoscrizioni hanno un nome e possono includere un filtro
facoltativo che limita il set dei messaggi passati alla coda virtuale
della sottoscrizione.

### Creare una sottoscrizione con il filtro (MatchAll) predefinito

Il filtro predefinito **MatchAll** viene usato se non si sono specificati
altri filtri durante la creazione di una nuova sottoscrizione. Quando si usa il filtro **MatchAll**
, tutti i messaggi pubblicati nell'argomento vengono inseriti
nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una
sottoscrizione denominata "AllMessages" e viene usato il filtro **MatchAll**
predefinito.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Creare sottoscrizioni con i filtri

È inoltre possibile configurare filtri che consentono di specificare i messaggi
inviati a un argomento da visualizzare in una specifica sottoscrizione dell'argomento.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è
**SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono
sulle proprietà dei messaggi pubblicati nell'argomento. Per
ulteriori dettagli sulle espressioni che è possibile usare con un filtro SQL,
esaminare la sintassi di [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Nell'esempio seguente viene creata una sottoscrizione denominata "HighMessages" con un filtro
**SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata
**MessageNumber** è maggiore di 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

Analogamente, nell'esempio seguente viene creata una sottoscrizione
denominata "LowMessages" con un filtro **SqlFilter** che seleziona solo i messaggi
in cui il valore della proprietà **MessageNumber** è minore o uguale a 3:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

A questo punto un messaggio inviato a "TestTopic" verrà sempre
recapitato ai ricevitori con sottoscrizione all'argomento "AllMessages"
e recapitato selettivamente ai ricevitori con sottoscrizioni
agli argomenti "HighMessages" e "LowMessages", a seconda del contenuto
del messaggio.

## <span class="short-header">Invio di messaggi a un argomento</span>Come inviare messaggi a un argomento

Per inviare un messaggio a un argomento del bus di servizio, nell'applicazione viene utilizzata la stringa di connessione per creare un oggetto
**TopicClient**.

Il codice seguente illustra come creare un oggetto **TopicClient** per l'argomento
"TestTopic" creato in precedenza tramite la chiamata API **CreateFromConnectionString**:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());

I messaggi inviati ad argomenti del bus di servizio sono istanze della classe
**BrokeredMessage**. Gli oggetti **BrokeredMessage** hanno un set di
proprietà standard, ad esempio **Label** e **TimeToLive**, un dizionario
usato per contenere le proprietà personalizzate specifiche dell'applicazione
e un corpo di dati arbitrari dell'applicazione. Per impostare il corpo del messaggio,
un'applicazione può passare qualsiasi oggetto serializzabile nel costruttore di
**BrokeredMessage**. In tal caso, per serializzare l'oggetto, verrà usata la
classe **DataContractSerializer** appropriata. In alternativa, è possibile fornire un oggetto
**System.IO.Stream**.

L'esempio seguente illustra come inviare cinque messaggi di prova all'oggetto
"TestTopic" **TopicClient** ottenuto nel frammento di codice riportato sopra.
Si noti come il valore della proprietà **MessageNumber** di ciascun messaggio
varia nell'iterazione del ciclo, determinando le sottoscrizioni che lo
riceveranno:

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

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB,
in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate
dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi
mantenuti in un argomento, mentre è prevista una limitazione alla dimensione
totale dei messaggi di un argomento. Questa dimensione della coda viene definita al momento
della creazione, con un limite massimo di 5 GB.

## <span class="short-header">Ricezione di messaggi da una sottoscrizione</span>Ricezione di messaggi da una sottoscrizione

Il modo più semplice per ricevere i messaggi da una sottoscrizione consiste nell'utilizzare un oggetto
**SubscriptionClient**. È possibile usare gli oggetti **SubscriptionClient** in
due diverse modalità: **ReceiveAndDelete** e **PeekLock**.

Quando si usa la modalità **ReceiveAndDelete**, l'operazione di ricezione
viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve
la richiesta di lettura relativa a un messaggio in una sottoscrizione, lo contrassegna
come utilizzato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** costituisce il
modello più semplice ed è adatta a scenari in cui un'applicazione può
tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio
questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta
di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il
messaggio come utilizzato, quando l'applicazione viene riavviata e inizia
a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto
anomalo risulterà perso.

Nella modalità **PeekLock** predefinita, il processo di ricezione viene suddiviso in due fasi, in modo da consentire il supporto di applicazioni che non
possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta,
trova il messaggio successivo da utilizzare, lo blocca per impedirne la
ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo
aver elaborato il messaggio, o averlo archiviato in modo affidabile per
una successiva elaborazione, l'applicazione esegue la seconda fase del processo
di ricezione chiamando **Complete** sul messaggio ricevuto. Quando il bus di
servizio vede la chiamata **Complete**, contrassegna il messaggio come
utilizzato e lo rimuove dalla sottoscrizione.

L'esempio seguente illustra come ricevere ed elaborare
messaggi usando la modalità **PeekLock** predefinita. Per specificare un valore diverso per **ReceiveMode**, è possibile utilizzare un altro overload per **CreateFromConnectionString**. In questo esempio viene creato un ciclo infinito e i messaggi vengono elaborati non appena arrivano nella sottoscrizione "HighMessages": Si noti che il percorso della sottoscrizione
"HighMessages" è specificato nel formato "\<*percorso
argomento*\>/subscriptions/\<*nome sottoscrizione*\>".

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    Client.Receive();
     
    // Continuously process messages received from the HighMessages subscription 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("MessageNumber: " + 
                message.Properties["MessageNumber"]);

             // Remove message from subscription
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in subscription
             message.Abandon();
          }
       }
    } 

## <span class="short-header">Arresti anomali e messaggi illeggibili dell'applicazione</span>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente
in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se
un'applicazione ricevente non è in grado di elaborare il messaggio per qualsiasi
motivo, può chiamare il metodo **Abandon**, anziché
**Complete**, sul messaggio ricevuto. In questo modo, il bus di servizio sbloccherà
il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la
ricezione da parte della stessa applicazione consumer
o di un'altra.

Al messaggio bloccato nella sottoscrizione è inoltre associato un timeout.
Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout,
ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del
messaggio, ma prima dell'invio della richiesta **Complete**, il messaggio verrà
recapitato all'applicazione al riavvio del sistema. Questo processo è spesso definito
**At-Least-Once** per indicare che ogni messaggio verrà elaborato almeno
una volta ma che in determinate situazioni potrà essere recapitato
una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli
sviluppatori dovranno aggiungere altra logica all'applicazione per gestire
il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà
**MessageId** del messaggio, che rimane costante in tutti i tentativi
di recapito.

## <span class="short-header">Eliminazioni di argomenti e sottoscrizioni</span>Come eliminare argomenti e sottoscrizioni

Nell'esempio seguente viene illustrato come eliminare l'argomento denominato
**TestTopic** dallo spazio dei nomi del servizio
**HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

Se si elimina un argomento, vengono eliminate anche tutte le sottoscrizioni
registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Il codice
illustra come eliminare una sottoscrizione denominata
**HighMessages** dall'argomento **TestTopic**:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## <span class="short-header">Passaggi successivi</span>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base relative agli argomenti e alle sottoscrizioni del bus di servizio,
utilizzare i collegamenti seguenti per ulteriori informazioni.

-   Riferimento in MSDN: [Code, argomenti e sottoscrizioni][Code, argomenti e sottoscrizioni]
-   Riferimento sulle API per [SqlFilter][SqlFilter]
-   Per creare un'applicazione funzionante che invia e riceve messaggi verso
    e da una coda del bus di servizio: [Esercitazione sulla messaggistica negoziata
    di Service Bus - .NET][Esercitazione sulla messaggistica negoziata
    di Service Bus - .NET].

  [Passaggi successivi]: #nextsteps
  [create-account-note]: ../includes/create-account-note.md
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  [0]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/it-it/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/it-it/library/microsoft.servicebus.messaging.sqlfilter.aspx
