<properties
   pageTitle="Ottimizzazione del codice di Azure in Visual Studio | Microsoft Azure"
   description="Informazioni su come il codice di Azure come strumento di ottimizzazione in Visual Studio consente di rendere il codice più affidabile e migliorare le prestazioni."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/19/2015"
   ms.author="tarcher" />

# Ottimizzare il codice Azure

Quando si programmano app che utilizzano Microsoft Azure, esistono alcune procedure consigliate da seguire per evitare problemi con la scalabilità di app, il comportamento e le prestazioni in un ambiente cloud. Microsoft fornisce uno strumento di analisi del codice di Azure che riconosce molti di questi problemi comunemente riscontrati e aiuta a risolverli. È possibile scaricare lo strumento in Visual Studio tramite NuGet.

## Regole di analisi codice di Azure

Lo strumento di analisi del codice di Azure utilizza le regole seguenti per contrassegnare automaticamente il codice di Azure quando rileva i problemi noti che influiscono sulle prestazioni. I problemi rilevati vengono visualizzati come avvisi o errori del compilatore. Correzioni del codice o suggerimenti per risolvere l'avviso o l’errore vengono spesso forniti tramite un'icona a forma di lampadina.

## Evitare di utilizzare la modalità stato sessione (in-process) predefinita

### ID

AP0000

### Descrizione

Se si utilizza la modalità di stato sessione (in-process) predefinita per le applicazioni cloud, è possibile perdere lo stato della sessione.

Condividere le idee e i suggerimenti nei [Commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Per impostazione predefinita, la modalità stato sessione specificata nel file Web. config è in corso. Inoltre, se nessuna voce è specificata nel file di configurazione, la modalità di stato della sessione è predefinita per in-process. La modalità in-process memorizza lo stato della sessione sul server web. Quando un'istanza viene riavviata o una nuova istanza viene utilizzata per il bilanciamento del carico o il supporto di failover, non viene salvato lo stato della sessione archiviato in memoria sul server web. Questa situazione impedisce che l'applicazione sia scalabile nel cloud.

Lo stato della sessione ASP.NET supporta diverse opzioni di archiviazione per i dati dello stato sessione: InProc, StateServer, SQLServer, personalizzato e Off. È consigliabile utilizzare la modalità personalizzata per ospitare i dati in un archivio esterno dello stato della sessione, ad esempio [Provider di stato della sessione di Azure per Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### Soluzione

Una soluzione consigliata consiste nell'archiviare lo stato della sessione in un servizio cache gestito. Informazioni su come utilizzare [il provider di stato della sessione di Azure per Redis](http://go.microsoft.com/fwlink/?LinkId=401521) per archiviare lo stato della sessione. È anche possibile archiviare lo stato della sessione in altre posizioni per garantire che l'applicazione sia scalabile nel cloud. Per ulteriori informazioni sulle soluzioni alternative, leggere [Modalità dello stato di sessione](https://msdn.microsoft.com/library/ms178586).

## L’esecuzione del metodo non deve essere asincrona

### ID

AP1000

### Descrizione

Creare metodi asincroni (ad esempio [await](https://msdn.microsoft.com/library/hh156528.aspx)) fuori dal metodo [Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) e quindi chiamare i metodi asincroni da [Run ()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). La dichiarazione del metodo [[Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) come asincrona fa sì che il ruolo di lavoro immetta un ciclo di riavvio.

Condividere le idee e i suggerimenti nei [Commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

La chiamata di metodi asincroni all'interno del metodo [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) fa sì che la runtime del servizio cloud ricicli il ruolo di lavoro. Quando viene avviato un ruolo di lavoro, l'esecuzione del programma ha luogo all'interno del metodo [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Chiudere il metodo [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) fa sì che il ruolo di lavoro venga riavviato. Quando il runtime di ruolo di lavoro raggiunge il metodo asincrono, invia tutte le operazioni dopo il metodo asincrono e poi ritorna In questo modo, il ruolo di lavoro esce dal metodo [[[[Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) e si riavvia. Nell'iterazione successiva dell'esecuzione, il ruolo di lavoro raggiunge nuovamente il metodo asincrono e viene riavviato, causando nuovamente il riciclo anche del ruolo di lavoro.

### Soluzione

Posizionare tutte le operazioni asincrone fuori dal metodo [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Quindi, chiamare il metodo asincrono refactoring dall'interno del metodo [[Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx), ad esempio RunAsync().wait. Lo strumento di analisi del codice di Azure può aiutare a risolvere il problema.

Il seguente frammento di codice dimostra la correzione del codice per questo problema:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## Autenticazione della firma di accesso condiviso con il bus di servizio

### ID

AP2000

### Descrizione

Uso della firma di accesso condiviso per l’autenticazione. Il servizio di controllo di accesso (ACS) è deprecato per l'autenticazione di bus di servizio.

Condividere le idee e i suggerimenti nei [Commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Per una sicurezza ottimale, Azure Active Directory consiste nella sostituzione dell’autenticazione ACS con l'autenticazione SAS. Vedere [Azure Active Directory è il futuro di ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) per informazioni sul piano di transizione.

### Soluzione

Utilizzare l'autenticazione SAS nelle app. Nell'esempio seguente viene illustrato come utilizzare un token SAS esistente per accedere a uno spazio dei nomi o a un’entità del bus di servizio.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Per altre informazioni, vedere gli argomenti seguenti.

- Per una panoramica, vedere [autenticazione della firma di accesso condiviso con il bus di servizio](https://msdn.microsoft.com/library/dn170477.aspx)

- [Come usare l'autenticazione della firma di accesso condiviso con il bus di servizio](https://msdn.microsoft.com/library/dn205161.aspx)

- Per un progetto di esempio, vedere [l'autenticazione tramite firma di accesso condiviso (SAS) con le sottoscrizioni di Service Bus](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## È consigliabile utilizzare il metodo OnMessage per evitare il "ciclo di ricezione"

### ID

AP2002

### Descrizione

Per evitare di entrare in un "ciclo di ricezione" la chiamata al metodo **OnMessage** è una soluzione ottimale, poi chiamare il metodo **Ricezione**. Tuttavia, se è necessario utilizzare il metodo **Ricezione** e si specifica un tempo di attesa del server non predefinito, assicurarsi che il tempo di attesa del server sia più di un minuto.

Condividere le idee e i suggerimenti nei [Commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Quando si chiama **OnMessage**, il client avvia un pump del messaggio interno che esegue costantemente il polling della coda o della sottoscrizione. Questo pump del messaggio contiene un ciclo infinito che emette una chiamata per ricevere messaggi. Se la chiamata scade, genera una nuova chiamata. L'intervallo di timeout è determinato dal valore della proprietà [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) del [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)che viene utilizzato.

Il vantaggio dell'utilizzo di **OnMessage** rispetto alla **Ricezione** è che gli utenti non devono manualmente effettuare il polling dei messaggi, gestire le eccezioni, elaborare più messaggi in parallelo e completare i messaggi.

Se si chiama la **Ricezione** senza utilizzare il valore predefinito, assicurarsi che il valore *ServerWaitTime* sia superiore a un minuto. L'impostazione di *ServerWaitTime* a più di un minuto impedisce al server di scadere prima che il messaggio venga ricevuto completamente.

### Soluzione

Vedere gli esempi di codice seguenti per gli utilizzi consigliati. Per ulteriori informazioni, vedere [metodo QueueClient.OnMessage (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)e [metodo QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Per migliorare le prestazioni dell'infrastruttura di messaggistica di Azure, vedere il modello di progettazione [Nozioni di base di messaggistica asincrona](https://msdn.microsoft.com/library/dn589781.aspx).

Il seguente è un esempio dell'utilizzo di **OnMessage** per ricevere i messaggi.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Il seguente è un esempio dell'utilizzo di **Ricezione** con il tempo di attesa predefinito del server.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

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
```

Il seguente è un esempio dell'utilizzo di **Ricezione** con il tempo di attesa non predefinito del server.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

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
```
## Si consideri l'utilizzo di metodi asincroni del Bus di servizio

### ID

AP2003

### Descrizione

Utilizzare i metodi asincroni del Bus di servizio per migliorare le prestazioni con la messaggistica negoziata.

Condividere le idee e i suggerimenti nei [Commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

L’utilizzo di metodi asincroni consente la concorrenza del programma di applicazione perché l'esecuzione di ogni chiamata non blocca il thread principale. Quando si utilizzano i metodi di messaggistica del Bus di servizio, l’esecuzione di un'operazione (inviare, ricevere, eliminare, ecc) richiede tempo. Questa volta include l'elaborazione dell'operazione dal servizio Bus di servizio oltre alla latenza della richiesta e risposta. Per aumentare il numero di operazioni per volta, è necessario eseguire le operazioni contemporaneamente. Per ulteriori informazioni, consultare le [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio](https://msdn.microsoft.com/library/azure/hh528527.aspx)

### Soluzione

Vedere [QueueClient class (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) per ricevere informazioni su come utilizzare il metodo asincrono consigliato.

Per migliorare le prestazioni dell'infrastruttura di messaggistica di Azure, vedere il modello di progettazione [Nozioni di base di messaggistica asincrona](https://msdn.microsoft.com/library/dn589781.aspx).

## Prendere in considerazione il partizionamento delle code e degli argomenti del Bus di servizio.

### ID

AP2004

### Descrizione

Le code e gli argomenti del Bus di servizio della partizione per ottenere prestazioni migliori con la messaggistica del Bus di servizio.

Condividere idee e suggerimenti in [Commenti e suggerimenti sull'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Le code e gli argomenti del partizionamento del Bus di servizio aumenta la disponibilità dei servizi e la velocità effettiva delle prestazioni poiché la velocità effettiva complessiva di una coda o di un argomento partizionato non è più limitata dalle prestazioni di un singolo broker di messaggi o archivio di messaggistica. Inoltre, un'interruzione temporanea di un archivio di messaggistica non ha una coda o argomento partizionato non disponibile. Per ulteriori informazioni, vedere [Partizionamento delle entità di messaggistica](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### Soluzione

Il seguente frammento di codice mostra come suddividere le entità di messaggistica.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Per ulteriori informazioni, vedere [Code e argomenti partizionati del Bus di servizio | Blog di Microsoft Azure](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) ed estrarre l’esempio [Coda partizionata di Microsoft Azure Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f).

## Non impostare SharedAccessStartTime

### ID

AP3001

### Descrizione

È consigliabile evitare l'utilizzo di SharedAccessStartTimeset all'ora corrente per avviare immediatamente il criterio di accesso condiviso. È sufficiente impostare questa proprietà se si desidera avviare i criteri di accesso condivisi in un secondo momento.

Condividere idee e suggerimenti in [Commenti e suggerimenti sull'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

La sincronizzazione dell'orologio comporta una differenza oraria lieve tra i Data Center. Ad esempio, si pensa logicamente che impostando l'ora di inizio di un archivio criteri SAS come l'ora corrente utilizzando Now o un metodo simile si farà sì che i criteri SAS abbiano effetto immediato. Tuttavia, le differenze lievi di tempo tra i Data Center possono provocare problemi con questo poiché alcuni tempi del datacenter potrebbero essere leggermente oltre l'ora di inizio, mentre altri la precedono. Di conseguenza, i criteri di firma di accesso condiviso possono scadere rapidamente (o persino immediatamente) se la durata di criteri è troppo breve.

Per ulteriori informazioni sull'utilizzo di firma di accesso condiviso sull'archiviazione di Azure, vedere [Introduzione della tabella SAS (Shared Access Signature) della coda SAS coda del BLOB SAS - Blog del Team Microsoft Azure Storage - Home page - sito blog di MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### Soluzione

Rimuovere l'istruzione che imposta l'ora di inizio del criterio di accesso condiviso. Lo strumento di analisi del codice di Azure fornisce una correzione per questo problema. Per ulteriori informazioni sulla gestione della protezione, vedere il modello di progettazione [Modello passepartout](https://msdn.microsoft.com/library/dn568102.aspx).

Il seguente frammento di codice dimostra la correzione del codice per questo problema.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## L’ora di scadenza del criterio di accesso condiviso deve essere più di cinque minuti

### ID

AP3002

### Descrizione

Può esistere una differenza di circa cinque minuti negli orologi tra i Data Center in posizioni diverse a causa di una condizione nota come "sfasamento." Per evitare che il token del criterio SAS scada prima del previsto, impostare l'ora di scadenza a più di cinque minuti.

Condividere idee e suggerimenti in [Commenti e suggerimenti sull'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

I dataenter in diverse località del mondo sono sincronizzati da un segnale di orologio. Poiché occorre tempo affinché il segnale dell’orologio viaggi in località diverse, può esistere una varianza di tempo tra i datacenter in località geografiche diverse anche se apparentemente tutto ciò viene sincronizzato. Questa differenza di tempo può influenzare l’inizio del tempo dell'accesso condiviso e dell’intervallo di scadenza dei criteri. Per verificare che il criterio di accesso condiviso diventi effettivo immediatamente, pertanto, non specificare l'ora di inizio. Inoltre, assicurarsi che l'ora di scadenza sia maggiore di 5 minuti per evitare una scadenza anticipata.

Per ulteriori informazioni sull'utilizzo di firma di accesso condiviso sull'archiviazione di Azure, vedere [Introduzione della tabella SAS (Shared Access Signature) della coda SAS coda del BLOB SAS - Blog del Team Microsoft Azure Storage - Home page - sito blog di MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### Soluzione

Per ulteriori informazioni sulla gestione della protezione, vedere il modello di progettazione [Modello passepartout](https://msdn.microsoft.com/library/dn568102.aspx).

Di seguito è riportato un esempio di ora di inizio dei criteri di accesso condiviso non specificata.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Di seguito è riportato un esempio di un'ora di inizio dei criteri di accesso condiviso specificata, con un periodo di scadenza dei criteri maggiore di cinque minuti.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Per ulteriori informazioni, [Creare e usare una firma di accesso condiviso](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## Utilizzare CloudConfigurationManager

### ID

AP4000

### Descrizione

Utilizzando la classe [ConfigurationManager] (https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) per progetti come ad esempio il sito Web di Azure e i servizi mobili di Azure non si presentano problemi di runtime. Come procedura consigliata, tuttavia, è consigliabile utilizzare Cloud [ConfigurationManager] (https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) come modo unificato di gestione delle configurazioni per tutte le applicazioni Cloud di Azure.

Condividere idee e suggerimenti in [Commenti e suggerimenti sull'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

CloudConfigurationManager legge il file di configurazione appropriato per l'ambiente dell'applicazione.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### Soluzione

Effettuare il refactoring del codice per utilizzare il [CloudConfigurationManager classe](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Una correzione del codice per questo problema viene fornita dallo strumento di analisi del codice di Azure.

Il seguente frammento di codice dimostra la correzione del codice per questo problema. Replace

`var settings = ConfigurationManager.AppSettings["mySettings"];`

con

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Di seguito è riportato un esempio di come archiviare l'impostazione di configurazione in un file app. config o Web. config. Aggiungere le impostazioni per la sezione appSettings del file di configurazione. Di seguito è il file Web. config per l'esempio di codice precedente.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## Evitare di utilizzare stringhe di connessione hardcoded

### ID

AP4001

### Descrizione

Se si utilizzano stringhe di connessione hardcoded ed è necessario aggiornarle in un secondo momento, sarà necessario apportare modifiche al codice sorgente e ricompilare l'applicazione. Tuttavia, se si archiviano le stringhe di connessione in un file di configurazione, è possibile cambiarle successivamente semplicemente aggiornando il file di configurazione.

Condividere idee e suggerimenti in [Commenti e suggerimenti sull'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Impostare come hardcoded le stringhe di connessione è sconsigliato perché presenta problemi quando è necessario modificare rapidamente le stringhe di connessione. Inoltre, se il progetto deve essere controllato nel codice sorgente, le stringhe di connessione hardcoded presentano una vulnerabilità di protezione poiché le stringhe possono essere visualizzate nel codice sorgente.

### Soluzione

Archiviare le stringhe di connessione nel file di configurazione o negli ambienti di Azure.

- Per le applicazioni autonome utilizzare la config. dell’app per archiviare le impostazioni della stringa di connessione.

- Per le applicazioni web ospitate in IIS, utilizzare config. web per archiviare le stringhe di connessione.

- Per le applicazioni ASP.NET vNext utilizzare configuration.json per archiviare le stringhe di connessione.

Per informazioni sull'utilizzo di file di configurazione, ad esempio Web. config o App. config, vedere [linee guida configurazione di ASP.NET Web] (https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Per informazioni su come lavorano le variabili di ambiente di Azure, vedere [siti Web di Microsoft Azure sul funzionamento delle stringhe di applicazione e di connessione](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Per informazioni sull’archiviazione della stringa di connessione nel codice sorgente, vedere [evitare di inserire informazioni riservate come le stringhe di connessione nei file archiviati nel repository del codice sorgente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## Utilizzo del file di configurazione di Diagnostica.

### ID

AP5000

### Descrizione

Anziché configurare le impostazioni di diagnostica nel codice, ad esempio tramite l'API di programmazione della diagnostica di Microsoft.WindowsAzure, è necessario configurare le impostazioni di diagnostica nel file diagnostics wadcfg. (O, diagnostics.wadcfgx se si utilizza Azure SDK 2.5). In questo modo è possibile modificare le impostazioni di diagnostica senza dover ricompilare il codice.

Condividere idee e suggerimenti in [Commenti e suggerimenti dell'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Prima di Azure SDK 2.5 (che utilizza diagnostica Microsoft Azure 1.3), la diagnostica di Azure (WAD) può essere configurata utilizzando diversi metodi: aggiunta del BLOB di configurazione nel servizio di archiviazione, utilizzando il codice imperativo, la configurazione dichiarativa o la configurazione predefinita. Tuttavia, il modo migliore per configurare la diagnostica consiste nell'utilizzare un file di configurazione XML (wadcfg o diagnositcs.wadcfgx per SDK 2.5 e versioni successive) nel progetto di applicazione. In questo approccio, il file diagnostics wadcfg definisce completamente la configurazione e può essere aggiornato e ridistribuito a suo piacimento. Combinare l'utilizzo del file di configurazione wadcfg con i metodi a livello di codice di impostazione delle configurazioni tramite il [Diagnosticmonitorconfiguration](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)o [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)classi può generare confusione. Vedere [Avviare o modificare la configurazione della diagnostica di Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx) per ulteriori informazioni.

A partire da 1.3 WAD (incluso in Azure SDK 2.5), non è più possibile utilizzare il codice per configurare la diagnostica. Di conseguenza, è possibile specificare solo la configurazione quando si applica o si aggiorna l'estensione di diagnostica.

### Soluzione

Utilizzare la finestra di progettazione di configurazione di diagnostica per spostare le impostazioni di diagnostica per il file di configurazione di diagnostica (diagnositcs.wadcfg o diagnositcs.wadcfgx per SDK 2.5 e versioni successive). È inoltre consigliabile installare [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) e utilizzare la funzionalità di diagnostica più recente.

1. Dal menu di scelta rapida per il ruolo desiderato scegliere Proprietà, quindi selezionare la scheda Configurazione.

1. Nella sezione **Diagnostica** verificare che la casella di controllo **Abilita diagnostica** sia selezionata.

1. Scegliere il pulsante **Configura**.

  ![Accesso all'opzione Abilita diagnostica](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Vedere [Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).per ulteriori informazioni.


## Evitare di dichiarare gli oggetti DbContext come static

### ID

AP6000

### Descrizione

Per conservare memoria, evitare di dichiarare gli oggetti DbContext come static

Condividere idee e suggerimenti in [Commenti e suggerimenti sull'analisi del codice di Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Gli oggetti DBContext contengono i risultati della query da ogni chiamata. Gli oggetti DBContext statici non vengono eliminati fino a quando non viene scaricato il dominio dell'applicazione. Pertanto, un oggetto DBContext statico può utilizzare grandi quantità di memoria.

### Soluzione

Dichiarare DBContext come una variabile locale o un campo di istanza non statico, utilizzarlo per un'attività e poi eliminarlo dopo l'uso.

Il seguente esempio di classe controller MVC illustra come utilizzare l'oggetto DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## Passaggi successivi

Per altre informazioni sull’ottimizzazione e la risoluzione dei problemi, vedere [Risolvere i problemi di un'app Web nel servizio app di Azure tramite Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

<!---HONumber=AcomDC_0128_2016-->