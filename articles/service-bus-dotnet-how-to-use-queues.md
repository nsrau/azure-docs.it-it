<properties  linkid="dev-net-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (.NET) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues C#, Azure queues .NET" description="Learn how to use Service Bus queues in Azure. Code samples written in C# using the .NET API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

# Come utilizzare le code del bus di servizio

<span>In questa guida verrà descritto come utilizzare le code del bus di servizio. Negli esempi, scritti in C#, viene utilizzata l'API .NET. Gli scenari presentati includono **creazione di code, invio e ricezione di messaggi**, nonché **eliminazione di code**. Per ulteriori informazioni sulle code, vedere la sezione [Passaggi successivi](#next-steps). </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

<h2><span class="short-header">Configurazione dell'applicazione</span>Configurazione dell'applicazione per l'utilizzo del bus di servizio</h2>

Quando si crea un'applicazione che utilizza il bus di servizio, è necessario aggiungere un riferimento all'assembly del bus di servizio e includere gli spazi dei nomi corrispondenti.

<h2><span class="short-header">Recupero del pacchetto NuGet</span>Recupero del pacchetto NuGet del bus di servizio</h2>

Il pacchetto **NuGet** del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio. L'estensione NuGet di Visual Studio semplifica l'installazione e l'aggiornamento di librerie e strumenti in Visual Studio e in Visual Studio Express 2012 per il Web. Il pacchetto NuGet del bus di servizio è il modo più semplice per recuperare l'API del bus di servizio e configurare l'applicazione con tutte le dipendenze di tale servizio.

Per installare il pacchetto NuGet nell'applicazione, eseguire le operazioni seguenti:

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su **Manage NuGet Packages**.
2.  Cercare "WindowsAzure" e selezionare l'elemento **Azure Service Bus**. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.
    
    ![](./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png)

È ora possibile scrivere codice per il bus di servizio.

<h2><span class="short-header">Configurazione della stringa di connessione</span>Come configurare una stringa di connessione per il bus di servizio</h2>

Per archiviare endpoint e credenziali, nel bus di servizio viene utilizzata una stringa di connessione. È possibile inserire la stringa di connessione in un file di configurazione, anziché impostarla come hardcoded nel codice:

* Quando si utilizza Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione dei servizi di Azure (file `*.csdef` e `*.cscfg`).
* Quando si utilizza Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ovvero il file `web.config`.

In entrambi i casi è possibile recuperare la stringa di connessione utilizzando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### <a name="config-connstring"> </a>Configurazione della stringa di connessione per l'utilizzo con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione. Aggiungere ad esempio un'impostazione al file di definizione del servizio (`*.csdef`), come illustrato di seguito:

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

Utilizzare i valori relativi ad autorità emittente e chiave recuperati dal portale di gestione come descritto nella sezione precedente.

### Configurazione della stringa di connessione quando si utilizza Siti Web o Macchine virtuali

Con Siti Web o Macchine virtuali è consigliabile utilizzare il sistema di configurazione .NET, ovvero il file `web.config`. Per archiviare la stringa di connessione, utilizzare l'elemento `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Utilizzare i valori relativi ad autorità emittente e chiave recuperati dal portale di gestione come descritto nella sezione precedente.

<h2><span  class="short-header">Come creare una coda</span>Come creare una coda</h2>

Per eseguire operazioni di gestione per le code del bus di servizio, è possibile utilizzare la classe **NamespaceManager**. La classe **NamespaceManager** fornisce i metodi per creare, enumerare ed eliminare le code.

In questo esempio viene costruito un oggetto **NamespaceManager** utilizzando la classe **CloudConfigurationManager** di Azure con una stringa di connessione costituita dall'indirizzo di base di uno spazio dei nomi del servizio bus e dalle credenziali appropriate che dispongono delle autorizzazioni per gestirlo. Il formato della stringa di connessione è il seguente:

    Endpoint=sb://[SpazioDeiNomiServizio].servicebus.windows.net/;SharedSecretIssuer=[NomeAutoritàEmittente];SharedSecretValue=[ChiavePredefinita]

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

Per il metodo **CreateQueue** sono disponibili overload che consentono di ottimizzare le proprietà della coda, ad esempio per impostare il valore predefinito della durata da applicare ai messaggi inviati alla coda. Per applicare queste impostazioni, viene utilizzata la classe **QueueDescription**. Nell'esempio seguente viene illustrato come creare una coda denominata "TestQueue" con una dimensione massima pari a 5 GB e una durata predefinita per i messaggi pari a 1 minuto:

	// Configure Queue Settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

	// Create a new Queue with custom settings
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

**Nota:** è possibile utilizzare il metodo **QueueExists** su oggetti **NamespaceManager** per verificare se in uno spazio dei nomi servizio esiste già una coda con il nome specificato.

<h2><span  class="short-header">Invio di messaggi a una coda</span>Come inviare messaggi a una coda</h2>

Per inviare un messaggio a una coda del bus di servizio, nell'applicazione viene utilizzata la stringa di connessione per creare un oggetto **QueueClient**.

Nel codice seguente viene illustrato come creare un oggetto **QueueClient** per la coda "TestQueue" creata in precedenza tramite la chiamata API **CreateFromConnectionString**:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    
    QueueClient Client = 
    	QueueClient.CreateFromConnectionString(connectionString, "TestQueue");
    
    Client.Send(new BrokeredMessage());

I messaggi inviati e ricevuti dalla coda del bus di servizio sono istanze della classe **BrokeredMessage**. <strong>Gli oggetti </strong>BrokeredMessage<strong> includono un insieme di proprietà standard, ad esempio </strong>Label<strong> e </strong>TimeToLive, un dizionario utilizzato per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Per impostare il corpo del messaggio, un'applicazione può passare qualsiasi oggetto serializzabile nel costruttore di **BrokeredMessage**. In tal caso per serializzare l'oggetto, verrà utilizzato l'oggetto **DataContractSerializer** appropriato. In alternativa, è possibile fornire un oggetto **System.IO.Stream**.

Nell'esempio seguente viene illustrato come inviare cinque messaggi di prova all'oggetto **QueueClient** "TestQueue" ottenuto nel frammento di codice sopra riportato:

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

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB, in cui la dimensione massima del'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non può superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## <span  class="short-header">Ricezione di messaggi da una coda</span>Come ricevere messaggi da una coda

Il modo più semplice per ricevere i messaggi da una coda consiste nell'utilizzare un oggetto **QueueClient**. È possibile utilizzare tali oggetti in due diverse modalità: **ReceiveAndDelete** e **PeekLock**.

Quando si utilizza la modalità **ReceiveAndDelete**, l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve la richiesta di lettura relativa a un messaggio in una coda, lo contrassegna come utilizzato e lo restituisce all'applicazione. <strong>La modalità </strong>ReceiveAndDelete costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità **PeekLock** predefinita, l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da utilizzare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando
**Complete** sul messaggio ricevuto. Quando il bus di servizio vede la chiamata **Complete**, contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi utilizzando la modalità **PeekLock** predefinita. Per specificare un valore diverso per **ReceiveMode**, è possibile utilizzare un altro overload per **CreateFromConnectionString**. In questo esempio viene creato un ciclo infinito e i messaggi vengono elaborati non appena arrivano in "TestQueue":

    Client.Receive();
     
    // Continuously process messages sent to the "TestQueue" 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("Test Property: " + 
				message.Properties["TestProperty"]);

             // Remove message from queue
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in queue
             message.Abandon();
          }
       }
    }

<h2><span  class="short-header">Arresti anomali e messaggi illeggibili dell'applicazione</span>Come gestire arresti anomali e messaggi illeggibili dell'applicazione</h2>

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo **Abandon**, anziché **Complete**, sul messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima dell'invio della **Complete**, il messaggio verrà nuovamente recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo **At-Least-Once**, per indicare che ciascun messaggio verrà elaborato almeno una volta ma in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso utilizzata la proprietà **MessageId** del messaggio, che rimane costante in tutti i tentativi di recapito.

<h2><span  class="short-header">Passaggi successivi</span>Passaggi successivi</h2>

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, utilizzare i collegamenti seguenti per ulteriori informazioni.

* Riferimento in MSDN: [Code, argomenti e sottoscrizioni][1]
* Per creare un'applicazione funzionante che invia e riceve messaggi verso e da una coda del bus di servizio: [Esercitazione sulla messaggistica negoziata di Service Bus - .NET][2].



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/hh367512.aspx