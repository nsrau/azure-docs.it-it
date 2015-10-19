<properties
    pageTitle="Come usare le code del bus di servizio (.NET) | Microsoft Azure"
    description="Informazioni su come usare le code del bus di servizio in Azure. Gli esempi di codice sono scritti in C# mediante l'API .NET."
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
    ms.date="10/07/2015"
    ms.author="sethm"/>

# Come usare le code del bus di servizio di Azure

Questo articolo illustra come usare le code del bus di servizio. Negli esempi, scritti in C#, viene usata l'API .NET. Gli scenari presentati includono la creazione di code e l'invio e la ricezione di messaggi. Per altre informazioni sulle code, vedere la sezione [Passaggi successivi](#next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Configurare l'applicazione per l'uso del bus di servizio

Quando si crea un'applicazione che usa il bus di servizio, è necessario aggiungere un riferimento all'assembly del bus di servizio e includere gli spazi dei nomi corrispondenti.

## Aggiungere il pacchetto NuGet del bus di servizio

Il pacchetto **NuGet** del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio. L'estensione NuGet di Visual Studio semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e in Visual Studio Express. Il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio.

Per installare il pacchetto NuGet nell'applicazione, seguire questa procedura:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su **Gestisci pacchetti NuGet**.
2.  Cercare "Bus di servizio" e selezionare l'elemento **Bus di servizio di Microsoft Azure**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

    ![][7]

È ora possibile scrivere codice per il bus di servizio.

## Configurare una stringa di connessione per il bus di servizio

Per archiviare endpoint e credenziali, nel bus di servizio viene usata una stringa di connessione. È possibile inserire la stringa di connessione in un file di configurazione, anziché impostarla come hardcoded:

- Quando si usa Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione usando il sistema di configurazione dei servizi di Azure (file .csdef e .cscfg).
- Quando si usa Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ad esempio il file Web.config.

In entrambi i casi è possibile recuperare la stringa di connessione usando il metodo [CloudConfigurationManager.GetSetting][GetSetting], come illustrato più avanti in questo articolo.

### Configurare la stringa di connessione per l'uso con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione. Aggiungere ad esempio un'etichetta `Setting` al file di definizione del servizio (.csdef), come illustrato nell'esempio seguente.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```
Specificare quindi i valori nel file di configurazione (.cscfg) del servizio, come illustrato nell'esempio successivo.

```
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
```

Usare i valori relativi alla chiave e al nome della chiave di firma di accesso condiviso recuperati dal portale di gestione come descritto nella sezione precedente.

### Configurare la stringa di connessione durante l'uso di Siti Web o Macchine virtuali di Azure

Con Siti Web o Macchine virtuali è consigliabile usare il sistema di configurazione .NET, ad esempio il file **Web.config**. Per archiviare la stringa di connessione, usare l'elemento `<appSettings>`.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Usare i valori relativi alla chiave e al nome della firma di accesso condiviso recuperati dal portale di gestione come descritto nella sezione precedente.

## Creare una coda

Per eseguire operazioni di gestione per le code del bus di servizio, usare la classe [NamespaceManager][]. Questa classe fornisce i metodi per creare, enumerare ed eliminare le code.

Questo esempio crea un oggetto [NamespaceManager][] mediante la classe [CloudConfigurationManager][] di Azure con una stringa di connessione costituita dall'indirizzo di base di uno spazio dei nomi del servizio del bus di servizio e dalle credenziali di firma di accesso condiviso appropriate con le autorizzazioni per gestirlo. Il formato di questa stringa di connessione è illustrato nell'esempio seguente.

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

Usare l'esempio seguente, per le impostazioni di configurazione della sezione precedente.

```
// Create the queue if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

Per il metodo [CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) sono disponibili overload che consentono di ottimizzare le proprietà della coda, ad esempio per impostare il valore predefinito della durata (TTL) da applicare ai messaggi inviati alla coda. Per applicare queste impostazioni, usare la classe [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). L'esempio seguente illustra come creare una coda denominata `TestQueue` con una dimensione massima pari a 5 GB e una durata TTL predefinita per i messaggi pari a 1 minuto.

```
// Configure queue settings.
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new queue with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE]È possibile usare il metodo [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) su oggetti [NamespaceManager][] per verificare se in uno spazio dei nomi del servizio esiste già una coda con il nome specificato.

## Inviare messaggi a una coda

Per inviare un messaggio a una coda del bus di servizio, nell'applicazione viene usata la stringa di connessione per creare un oggetto [QueueClient][].

Il codice seguente illustra come creare un oggetto [QueueClient][] per la coda `TestQueue` creata in precedenza tramite la chiamata API [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

I messaggi inviati e ricevuti dalle code del bus di servizio sono istanze della classe [BrokeredMessage][]. Gli oggetti [BrokeredMessage][] includono un insieme di proprietà standard, ad esempio [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx), un dizionario usato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo del messaggio passando un oggetto serializzabile al costruttore dell'oggetto [BrokeredMessage][] e l'elemento **DataContractSerializer** appropriato viene quindi usato per serializzare l'oggetto. In alternativa, è possibile fornire un oggetto **System.IO.Stream**.

L'esempio seguente illustra come inviare cinque messaggi di prova all'oggetto `TestQueue` [QueueClient][] ottenuto nell'esempio di codice precedente.

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set some addtional custom app-specific properties.
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  // Send message to the queue.
  Client.Send(message);
}
```

Le code del bus di servizio supportano [messaggi di dimensioni massime pari a 256 KB](service-bus-quotas.md), in cui la dimensione massima dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB. Se il partizionamento è abilitato, il limite superiore è più elevato. Per altre informazioni, vedere [Partizionamento delle entità di messaggistica](service-bus-partitioning.md).

## Come ricevere messaggi da una coda

Il modo più semplice per ricevere i messaggi da una coda consiste nell'uso di un oggetto [QueueClient][]. Gli oggetti [QueueClient][] agiscono in due modalità diverse: [ReceiveAndDelete e PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Quando si usa la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve una richiesta di lettura relativa a un messaggio in una coda, lo contrassegna come utilizzato e lo restituisce all'applicazione. La modalità **ReceiveAndDelete** costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** predefinita, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando [Complete][] sul messaggio ricevuto. Quando il bus di servizio vede la chiamata [Complete][], contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

L'esempio seguente illustra come ricevere ed elaborare messaggi usando la modalità **PeekLock** predefinita. Per specificare un valore diverso per [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), è possibile usare un altro overload di [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx). Questo esempio usa il callback [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) per elaborare i messaggi non appena arrivano in `TestQueue`.

```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

// Callback to handle received messages.
Client.OnMessage((message) =>
{
    try
    {
        // Process message from queue.
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Test Property: " +
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }
}, options);
```

Questo esempio configura il callback [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) usando un oggetto [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) è impostato su **false** per abilitare il controllo manuale della chiamata [Complete][] sul messaggio ricevuto. Se [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) è impostato su un minuto, il client dovrà attendere fino a un minuto per ricevere un messaggio prima del timeout della chiamata ed effettuerà una nuova chiamata per verificare i messaggi. Il valore di questa proprietà riduce il numero di volte in cui il client effettua chiamate addebitabili che non consentono di recuperare i messaggi.

## Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx), anziché [Complete][], sul messaggio ricevuto. In questo modo, il bus di servizio sblocca il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o di un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'invio della [Complete][], il messaggio verrà nuovamente recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At Least Once**, per indicare che ogni messaggio verrà elaborato almeno una volta ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del messaggio, che rimane costante in tutti i tentativi di recapito.

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, usare i collegamenti seguenti per altre informazioni.

-   Consultare le informazioni sulle entità di messaggistica del bus di servizio in [Code, argomenti e sottoscrizioni][].
-   Vedere [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET][] per creare un'applicazione funzionante che invia e riceve messaggi verso e da una coda del bus di servizio.
-   Scaricare esempi del bus di servizio da [esempi di Azure][] o vedere la [panoramica degli esempi del bus di servizio][].

  [Azure portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Code, argomenti e sottoscrizioni]: service-bus-queues-topics-subscriptions.md
  [Esercitazione sulla messaggistica negoziata del bus di servizio - .NET]: service-bus-brokered-tutorial-dotnet.md
  [esempi di Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [panoramica degli esempi del bus di servizio]: service-bus-samples.md
  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [Complete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx

<!---HONumber=Oct15_HO2-->