<properties linkid="" pageTitle="Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK" metaKeywords="WebJobs SDK Azure queue storage .NET C#" description="Learn how to work with Azure queue storage in the WebJobs SDK. Create and delete queues; insert, peek, get, and delete queue messages, and more." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK

Questa guida illustra come scrivere codice C# per gli scenari comuni usando il
servizio di archiviazione di accodamento di Azure e Azure WebJobs SDK, versione 1.0.0.

Nella guida si presuppone una [conoscenza di base di Webjobs SDK](../websites-webjobs-sdk-storage-queues-how-to) e di [come eseguire le attività di base],(../websites-dotnet-webjobs-sdk-get-started/)ad esempio l'installazione del pacchetto NuGet per WebJobs SDK, la creazione di un account di archiviazione di Azure e la creazione di stringhe di connessione per WebJobs SDK che indirizzino al proprio account di archiviazione.

La maggior parte dei frammenti di codice mostra solo funzioni, non il codice che crea l'oggetto 'JobHost' come nel seguente esempio:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Sommario

-   [Come attivare una funzione quando viene ricevuto un messaggio in coda](#trigger)
	- Messaggi stringa in coda
	- Messaggi oggetto POCO in coda
	- Funzioni asincrone
	- Algoritmo di polling
	- Esecuzione parallela
	- Ottenere i metadati della coda o del messaggio in coda
	- Arresto normale
-   [Come creare un messaggio in coda durante l'elaborazione di un messaggio in coda](#createqueue)
	- Messaggi stringa in coda
	- Messaggi oggetto POCO in coda
	- Creare più messaggi
	- Usare gli attributi della coda nel corpo di una funzione
-   [Come leggere e scrivere BLOB durante l'elaborazione di un messaggio in coda](#blobs)
	- Messaggi stringa in coda
	- Messaggi oggetto POCO in coda
	- Usare gli attributi BLOB nel corpo di una funzione
-   [Come gestire i messaggi non elaborabili](#poison)
	- Gestione automatica dei messaggi non elaborabili
	- Gestione manuale dei messaggi non elaborabili
-   [Come impostare le opzioni di configurazione](#config)
	- Impostare le stringhe di connessione SDK nel codice
	- Configurare le impostazioni QueueTrigger
	- Ottenere i nomi delle code dalla configurazione
-   [Come attivare manualmente una funzione](#manual)
-   [Come scrivere i log](#logs)
-   [Passaggi successivi](#nextsteps)

## <a id="trigger"></a> Come attivare una funzione quando viene ricevuto un messaggio in coda

Per scrivere una funzione che l'SDK chiama quando viene ricevuto un messaggio della coda, usare l'attributo 'QueueTrigger' con una stringa o un parametro POCO a seconda di cosa ci si aspetta di ricevere nel messaggio. Il costruttore di attributo accetta un parametro di stringa che specifica il nome della coda di cui eseguire il polling. È anche possibile [configurare dinamicamente il nome della coda](#config).

Se il sito Web viene eseguito su più macchine virtuali, il processo Web verrà eseguito su ogni macchina e ogni macchina attenderà i trigger e proverà a eseguire le funzioni. Poiché in alcuni scenari è possibile che per questo motivo alcune funzioni elaborino gli stessi dati due volte, le funzioni dovrebbero essere idempotent (scritte in modo tale che, anche se chiamate più volte con gli stessi dati di input, non generano risultati duplicati).  

### Messaggi stringa in coda

Nell'esempio seguente, la coda contiene una stringa di messaggio, in modo che l'attributo 'QueueTrigger' venga applicato a un parametro di stringa denominato 'logMessage' che include il contenuto del messaggio in coda. La funzione [scrive un messaggio di log al Dashboard](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Oltre a 'string', il parametro può essere una matrice di byte, un oggetto 'CloudQueueMessage' o un oggetto POCO definito dall'utente.

### Messaggi oggetto POCO in coda

Nell'esempio seguente, il messaggio in coda contiene JSON per un oggetto 'BlobInformation' che include una proprietà 'BlobName'. L'SDK deserializza automaticamente l'oggetto.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

L'esempio seguente illustra come creare un messaggio, analizzabile con l'SDK, nella coda POCO senza usare WebJobs SDK. L'SDK usa il [pacchetto Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) per serializzare e deserializzare i messaggi.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Funzioni asincrone

La seguente funzione asincrona [scrive un log al Dashboard](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Le funzioni asincrone possono richiedere un token di annullamento, come illustrato nell'esempio seguente che consente di copiare un BLOB (per una spiegazione del segnaposto 'queueTrigger', vedere la sezione [BLOB](#blobs) ).

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### Algoritmo di polling

L'SDK implementa un algoritmo di backoff esponenziale casuale per ridurre l'effetto del polling delle code inattive sui costi delle transazioni di archiviazione.  Quando viene trovato un messaggio, l'SDK attende due secondi e controlla la presenza di un altro messaggio. Quando non viene trovato alcun messaggio, rimane in attesa per circa quattro secondi prima di riprovare. Dopo alcuni tentativi non riusciti per ottenere un messaggio nella coda, il tempo di attesa continua ad aumentare finché non raggiunge il tempo massimo di attesa, che per impostazione predefinita è di un minuto. [Il tempo di attesa massimo è configurabile](#config).

### Esecuzione parallela

Se si dispongono di più funzioni in ascolto in code diverse, l'SDK le chiamerà in parallelo quando i messaggi vengono ricevuti simultaneamente. 

Lo stesso vale quando si ricevono più messaggi per una singola coda. Per impostazione predefinita, l'SDK ottiene un batch di 16 messaggi in coda alla volta ed esegue la funzione che li elabora in parallelo. [La dimensione del batch è configurabili](#config). Quando il numero elaborato viene ridotto alla metà della dimensione del batch, l'SDK ottiene un altro batch e inizia l'elaborazione dei messaggi. Di conseguenza, il numero massimo di messaggi simultanei elaborati per ogni funzione è pari a una volta e mezza la dimensione del batch. Questo limite si applica separatamente a ogni funzione caratterizzata da un attributo 'QueueTrigger'. Se non si vuole avviare l'esecuzione parallela per i messaggi ricevuti su una coda, impostare le dimensioni del batch su 1.

### <a id="queuemetadata"></a>Ottenere i metadati della coda o del messaggio in coda

Con l'aggiunta di parametri alla firma del metodo, è possibile ottenere le proprietà del messaggio seguenti:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contains message text)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Se si vuole lavorare direttamente con l'API di archiviazione di Azure, è anche possibile aggiungere un parametro 'CloudStorageAccount'.

Nell'esempio seguente tutti questi metadati vengono scritti in un log applicazione INFO. Nell'esempio, gli attributi logMessage e queueTrigger includono il contenuto del messaggio in coda.

		public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
		    DateTimeOffset expirationTime,
		    DateTimeOffset insertionTime,
		    DateTimeOffset nextVisibleTime,
		    string id,
		    string popReceipt,
		    int dequeueCount,
		    string queueTrigger,
		    CloudStorageAccount cloudStorageAccount,
		    TextWriter logger)
		{
		    logger.WriteLine(
		        "logMessage={0}\n"
				nexpirationTime={1}\ninsertionTime={2}\n" +
		        "nextVisibleTime={3}\n" +
		        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
		        "queue endpoint={7} queueTrigger={8}",
		        logMessage, expirationTime,
		        insertionTime,
		        nextVisibleTime, id,
		        popReceipt, dequeueCount,
		        cloudStorageAccount.QueueEndpoint,
		        queueTrigger);
		}

Ecco un log di esempio scritto dal codice di esempio:

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>Arresto normale

Una funzione che viene eseguita in un processo Web continuo può accettare un parametro 'CancellationToken' che consente al sistema operativo di notificare la funzione quando il processo Web sta per essere terminato. È possibile usare questa notifica per assicurarsi che la funzione non termini in modo imprevisto lasciando i dati in uno stato incoerente.

L'esempio seguente illustra come controllare la chiusura imminente di un processo Web in una funzione.

	public static void GracefulShutdownDemo(
	            [QueueTrigger("inputqueue")] string inputText,
	            TextWriter logger,
	            CancellationToken token)
	{
	    for (int i = 0; i < 100; i++)
	    {
	        if (token.IsCancellationRequested)
	        {
	            logger.WriteLine("Function was cancelled at iteration {0}", i);
	            break;
	        }
	        Thread.Sleep(1000);
	        logger.WriteLine("Normal processing for queue message={0}", inputText);
	    }
	}

**Nota:** è possibile che nel dashboard non vengano mostrati correttamente lo stato e il risultato delle funzioni che sono state interrotte.
 
Per altre informazioni, vedere l'articolo sull'[arresto normale dei processi Web](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a> Come creare un messaggio in coda durante l'elaborazione di un messaggio in coda

Per scrivere una funzione che crea un nuovo messaggio in coda, usare l'attributo 'Queue' su una stringa di output o un parametro POCO. Come per l'attributo 'QueueTrigger', si passa il nome della coda come stringa oppure è possibile [configurare dinamicamente il nome della coda](#config).

### Messaggi stringa in coda

Nell'esempio seguente viene creato un nuovo messaggio nella coda denominata "outputqueue" con lo stesso contenuto del messaggio in coda ricevuto nella coda denominata "inputqueue".

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

Se il tipo di parametro di output è uno dei seguenti e l'oggetto non è null quando la funzione termina, l'SDK crea un messaggio in coda:

* 'string' 
* 'byte[]'
* Un tipo serializzabile POCO definito dall'utente
* 'CloudQueueMessage'

Per creare più messaggi, vedere **Creare più messaggi** più avanti in questa sezione.

È anche possibile usare 'CloudQueue' come tipo di parametro di output se si vogliono inviare manualmente i messaggi.

### Messaggi oggetto POCO in coda

Per creare un messaggio in coda che contiene un oggetto POCO anziché una stringa, passare il tipo POCO come parametro di output al costruttore dell'attributo 'Queue'. 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

L'SDK deserializza automaticamente l'oggetto in JSON. Viene sempre creato un messaggio in coda, anche se l'oggetto è null.

### Creare più messaggi

Per creare più messaggi, verificare il tipo di parametro per la coda di output 'ICollector<T>` o `IAsyncCollector', come illustrato nell'esempio seguente.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Ogni messaggio in coda viene creato immediatamente quando viene chiamato il metodo 'Add'.

### <a id="queuebody"></a>Usare gli attributi della coda nel corpo di una funzione

Se è necessario lavorare in una funzione prima di usare l'attributo 'Queue' per creare un messaggio nella coda, è possibile usare l'interfaccia 'IBinder' per ottenere un oggetto 'CloudQueue' che consente di lavorare direttamente con una coda. 

Nell'esempio seguente viene accettato un messaggio di coda di input e creato un nuovo messaggio con lo stesso contenuto in una coda di output. Il nome della coda di output viene impostato dal codice nel corpo della funzione.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> Come leggere e scrivere BLOB e tabelle durante l'elaborazione di un messaggio in coda

Gli attributi 'Blob' e 'Table' consentono di leggere e scrivere BLOB e tabelle. Gli esempi in questa sezione si applicano ai BLOB.

Alcuni dei tipi con cui l'attributo 'Blob' può essere usato includono 'Stream', 'TextReader', 'TextWriter' e 'CloudBlockBlob'. Il costruttore dell'attributo accetta un parametro 'blobPath' che specifica il contenitore e il nome del BLOB. Quando l'attributo decora un oggetto 'Stream', un altro parametro del costruttore specifica la modalità 'FileAccess' come lettura, scrittura o lettura/scrittura. Se è necessario eseguire alcune operazioni nella funzione prima di associare un BLOB a un oggetto, usare l'attributo nel corpo della funzione, [come mostrato in precedenza per l'attributo Queue](#queuebody).

### Messaggi stringa in coda

Per un messaggio in coda che contiene una stringa 'queueTrigger' è un segnaposto che è possibile usare nel parametro 'blobPath' dell'attributo 'Blob' che include il contenuto del messaggio. 

L'esempio seguente usa gli oggetti 'Stream' per leggere e scrivere i BLOB. Il messaggio in coda fornisce il nome di un BLOB nel contenitore textblobs. Una copia del BLOB con "-new" aggiunto al nome viene creata nello stesso contenitore. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Nell'esempio seguente viene usato un oggetto 'CloudBlockBlob' per eliminare un BLOB.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### Messaggi oggetto POCO in coda

Per gli oggetti POCO archiviati nel formato JSON nel messaggio della coda, è possibile usare i segnaposto che denominano le proprietà dell'oggetto nel parametro 'blobPath' dell'attributo 'Queue'. È anche possibile usare i [nomi delle proprietà dei metadati di coda](#queuemetadata) come segnaposto. 

Nell'esempio seguente viene copiato un BLOB in un nuovo BLOB con un'estensione diversa, usando le proprietà dell'oggetto 'BlobInformation' per specificare i nomi dei BLOB di input e output. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

L'esempio seguente illustra come creare un messaggio, analizzabile con l'SDK, nella coda POCO senza usare WebJobs SDK. L'SDK usa il [pacchetto Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) per serializzare e deserializzare i messaggi.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> Come gestire i messaggi non elaborabili

I messaggi il cui contenuto comporta l'esito negativo di una funzione sono denominati *messaggi non elaborabili*. Quando la funzione non riesce, il messaggio in coda non viene eliminato e infine viene prelevato, causando la ripetizione del ciclo. L'SDK può interrompere automaticamente il ciclo dopo un numero limitato di iterazioni oppure è possibile farlo manualmente.

### Gestione automatica dei messaggi non elaborabili

L'SDK chiamerà una funzione fino a 5 volte per elaborare un messaggio nella coda. Se il quinto tentativo non riesce, il messaggio viene spostato in una coda non elaborabile. [Il numero massimo di tentativi è configurabile](#config). 

La coda non elaborabile viene denominata in *{nomecodaoriginale} *-poison. È possibile scrivere una funzione per elaborare i messaggi dalla coda non elaborabile archiviandoli o inviando una notifica della necessità di un intervento manuale. 

Nell'esempio seguente, la funzione 'CopyBlob' non riesce quando un messaggio della coda contiene il nome di un BLOB inesistente. In questo caso, il messaggio viene spostato dalla coda copyblobqueue alla coda non elaborabile copyblobqueue-poison. 'ProcessPoisonMessage' registra quindi il messaggio non elaborabile.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}
		
		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

Nella figura seguente viene illustrato l'output della console di queste funzioni quando viene elaborato un messaggio non elaborabile.

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Gestione manuale dei messaggi non elaborabili

È possibile ottenere il numero di volte in cui un messaggio è stato selezionato per l'elaborazione aggiungendo un parametro 'int' denominato 'dequeueCount' alla funzione. Sarà quindi possibile controllare il conteggio di rimozione dalla coda nel codice della funzione ed eseguire la gestione dei messaggi non elaborabili quando il numero supera una certa soglia, come illustrato nell'esempio seguente.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 5)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

Affinché questo codice funzioni come previsto, è necessario aumentare il numero massimo di tentativi per la gestione automatica dei messaggi non elaborabili, altrimenti il conteggio di rimozione dalla coda in questo esempio non supererebbe mai 5.

## <a id="config"></a> Come impostare le opzioni di configurazione

È possibile usare il tipo 'JobHostConfiguration' per impostare le opzioni di configurazione seguenti:

* Impostare le stringhe di connessione SDK nel codice.
* Configurare le impostazioni di 'QueueTrigger', ad esempio il conteggio rimozione dalla coda.
* Ottenere i nomi delle code dalla configurazione.

### <a id="setconnstr"></a>Impostare le stringhe di connessione SDK nel codice

Impostare le stringhe di connessione SDK nel codice per poter usare i propri nomi di stringa di connessione nei file di configurazione o nelle variabili di ambiente, come illustrato nell'esempio seguente.

		static void Main(string[] args)
		{
		    var _storageConn = ConfigurationManager
		        .ConnectionStrings["MyStorageConnection"].ConnectionString;
		
		    var _dashboardConn = ConfigurationManager
		        .ConnectionStrings["MyDashboardConnection"].ConnectionString;
		
		    var _serviceBusConn = ConfigurationManager
		        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
		
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.StorageConnectionString = _storageConn;
		    config.DashboardConnectionString = _dashboardConn;
		    config.ServiceBusConnectionString = _serviceBusConn;
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueue"></a>Configurare le impostazioni della coda

È possibile configurare le seguenti impostazioni valide per l'elaborazione del messaggio di coda:

- Numero massimo di messaggi in coda che vengono prelevati contemporaneamente per l'esecuzione in parallelo (il valore predefinito è 16).
- Numero massimo di tentativi prima che un messaggio nella coda venga inviato a una coda non elaborabile (il valore predefinito è 5).
- Tempo massimo di attesa prima di eseguire nuovamente il polling quando una coda è vuota (il valore predefinito è 1 minuto).

L'esempio seguente illustra come configurare queste impostazioni:

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>Ottenere i nomi delle code dalla configurazione

Il tipo 'JobHostConfiguration' consente di passare un oggetto 'NameResolver' che fornisce il nome della coda per l'SDK per gli attributi 'QueueTrigger' e 'Queue'.

Si supponga, ad esempio, di voler usare una coda denominata logqueuetest nell'ambiente di test e una coda denominata logqueueprod nell'ambiente di produzione. Invece di un nome di coda hardcoded, è preferibile specificare il nome di una voce nella raccolta 'appSettings' caratterizzata dal nome della coda effettivo. Se la chiave 'appSettings' è logqueue, la funzione potrebbe essere simile all'esempio seguente.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

La classe 'NameResolver' potrebbe quindi ottenere il nome della coda dalla raccolta 'appSettings', come illustrato nell'esempio seguente:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

È possibile passare la classe 'NameResolver' all'oggetto 'JobHost', come illustrato nell'esempio seguente.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>Come attivare manualmente una funzione

Per attivare manualmente una funzione, usare il metodo 'Call' o 'CallAsync' sull'oggetto 'JobHost' e l'attributo 'NoAutomaticTrigger' nella funzione, come illustrato nell'esempio seguente. 

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), nuovo {valore = "Hello world!" });
		    }
		
		    [NoAutomaticTrigger]
		    public static void CreateQueueMessage(
		        TextWriter logger, 
		        string value, 
		        [Queue("outputqueue")] out string message)
		    {
		        message = value;
		        logger.WriteLine("Creating queue message: ", message);
		    }
		}

## <a id="logs"></a>Come scrivere i log

Per scrivere i log che vengono visualizzati nella pagina dashboard dei processi Web collegata a una chiamata di funzione specifica, usare un oggetto 'TextWriter' che è possibile ottenere da un parametro nella firma del metodo.

Per scrivere [log di traccia dell'applicazione],(../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)usare 'Console.Out' (crea log contrassegnati come INFO) e 'Console.Error' (crea log contrassegnati come ERROR). Un'alternativa consiste nell'usare la classe [Trace o TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx).

I log dell'applicazione vengono visualizzati nei file di log del sito Web, nelle tabelle di Azure, o oggetti BLOB di Azure a seconda di come è possibile configurare il sito Web di Azure. Anche i 100 log dell'applicazione più recenti vengono visualizzati nel Dashboard se il programma è in esecuzione in un processo Web di Azure (nessun log dell'applicazione viene visualizzato nel dashboard da un programma che è in esecuzione in locale o in qualche altro ambiente).   

È possibile disabilitare la registrazione [impostando la stringa di connessione del dashboard su Null](#config).

Nell'esempio seguente vengono illustrati diversi modi per scrivere log:

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

Nel dashboard di WebJobs SDK, viene visualizzato l'output dell'oggetto 'TextWriter' quando si visita la pagina relativa a una chiamata di funzione specifica e si fa clic su **Attiva/Disattiva Output**:

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Nel dashboard di WebJobs SDK, vengono visualizzate le 100 righe più recenti del log dell'applicazione quando si visita la pagina relativa al processo Web (non alla chiamata di funzione) e si fa clic su **Attiva/Disattiva Output**:
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

In un processo Web continuo, i log dell'applicazione vengono visualizzati in /data/jobs/continuous/*{webjobname}*/job_log.txt nel file system del sito Web.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

In un BLOB di Azure l'aspetto dei log dell'applicazione è simile al seguente:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

In una tabella di Azure i log 'Console' e 'Console.Error' hanno un aspetto simile al seguente:

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Passaggi successivi

In questo argomento sono stati forniti esempi di codice che illustrano come gestire scenari comuni per l'utilizzo di code di Azure. Per altre informazioni su come usare i processi Web Azure e su WebJobs SDK, vedere le [risorse consigliate per i processi Web Azure](http://go.microsoft.com/fwlink/?linkid=390226).
