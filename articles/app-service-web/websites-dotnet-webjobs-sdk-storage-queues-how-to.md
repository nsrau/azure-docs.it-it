<properties 
	pageTitle="Come usare il servizio di archiviazione code di Azure con WebJobs SDK" 
	description="Informazioni su come usare il servizio di archiviazione code di Azure con WebJobs SDK. Creare ed eliminare code, inserire, visualizzare, ottenere ed eliminare messaggi dalla coda e altro ancora." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/14/2015" 
	ms.author="tdykstra"/>

# Come usare il servizio di archiviazione code di Azure con WebJobs SDK

## Panoramica

Questa guida fornisce esempi di codice C# che illustrano come usare Azure WebJobs SDK versione 1.x con il servizio di archiviazione code di Azure.

Nella guida si presuppone che si sappia come [creare un progetto WebJob in Visual Studio con stringhe di connessione che puntano all'account di archiviazione](websites-dotnet-webjobs-sdk-get-started.md#configure-storage) o [più account di archiviazione](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

La maggior parte dei frammenti di codice mostra solo le funzioni, non il codice che crea l'oggetto `JobHost` come nel seguente esempio:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
La guida include i seguenti argomenti:

-   [Come attivare una funzione quando viene ricevuto un messaggio in coda](#trigger)
	- Messaggi stringa in coda
	- Messaggi di coda POCO
	- Funzioni asincrone
	- Tipi usati dall'attributo QueueTrigger
	- Algoritmo di polling
	- Più istanze
	- Esecuzione parallela
	- Ottenere i metadati della coda o del messaggio in coda
	- Arresto normale
-   [Come creare un messaggio in coda durante l'elaborazione di un messaggio in coda](#createqueue)
	- Messaggi stringa in coda
	- Messaggi di coda POCO
	- Creare più messaggi o in funzioni asincrone
	- Tipi usati dall'attributo Queue
	- Usare gli attributi di WebJobs SDK nel corpo di una funzione
-   [Come leggere e scrivere BLOB durante l'elaborazione di un messaggio in coda](#blobs)
	- Messaggi stringa in coda
	- Messaggi di coda POCO
	- Tipi usati dall'attributo Blob
-   [Come gestire i messaggi non elaborabili](#poison)
	- Gestione automatica dei messaggi non elaborabili
	- Gestione manuale dei messaggi non elaborabili
-   [Come impostare le opzioni di configurazione](#config)
	- Impostare le stringhe di connessione SDK nel codice
	- Configurare le impostazioni di QueueTrigger
	- Impostare i valori per i parametri del costruttore WebJobs SDK nel codice
-   [Come attivare manualmente una funzione](#manual)
-   [Come scrivere i log](#logs) 
-   [Come gestire gli errori e configurare i timeout](#errors)
-   [Passaggi successivi](#nextsteps)

## <a id="trigger"></a> Come attivare una funzione quando viene ricevuto un messaggio in coda

Per scrivere una funzione che viene chiamata da WebJobs SDK quando viene ricevuto un messaggio di coda, usare l'attributo `QueueTrigger`. Il costruttore dell'attributo accetta un parametro di stringa che specifica il nome della coda di cui eseguire il polling. È anche possibile [impostare il nome della coda in modo dinamico](#config).

### Messaggi stringa in coda

Nel seguente esempio la coda contiene un messaggio stringa in modo che l'attributo `QueueTrigger` venga applicato a un parametro di stringa denominato `logMessage` che include il contenuto del messaggio di coda. La funzione [scrive un messaggio di log nel dashboard](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Oltre a `string`, il parametro può essere una matrice di byte, un oggetto `CloudQueueMessage` o un oggetto POCO definito dall'utente.

### Messaggi di coda POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Nel seguente esempio il messaggio di coda contiene JSON per un oggetto `BlobInformation` che include una proprietà `BlobName`. L'SDK deserializza automaticamente l'oggetto.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

L'SDK usa il pacchetto [Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) per serializzare e deserializzare i messaggi. Se si creano messaggi di coda in un programma che non usa WebJobs SDK, è possibile scrivere codice simile a quello del seguente esempio per creare un messaggio di coda POCO analizzabile dall'SDK.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Funzioni asincrone

La seguente funzione asincrona [scrive un log nel dashboard](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Le funzioni asincrone possono usare un [token di annullamento](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), come illustrato nel seguente esempio che copia un BLOB (per una spiegazione del segnaposto `queueTrigger`, vedere la sezione [BLOB](#blobs)).

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### <a id="qtattributetypes"></a> Tipi usati dall'attributo QueueTrigger

È possibile usare `QueueTrigger` con i seguenti tipi:

* `string`
* Tipo POCO serializzato come JSON
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a> Algoritmo di polling

L'SDK implementa un algoritmo di backoff esponenziale casuale per ridurre l'effetto del polling delle code inattive sui costi delle transazioni di archiviazione. Quando viene trovato un messaggio, l'SDK attende due secondi e controlla la presenza di un altro messaggio. Quando non viene trovato alcun messaggio, rimane in attesa per circa quattro secondi prima di riprovare. Dopo alcuni tentativi non riusciti per ottenere un messaggio nella coda, il tempo di attesa continua ad aumentare finché non raggiunge il tempo massimo di attesa, che per impostazione predefinita è di un minuto. [Il tempo di attesa massimo è configurabile](#config).

### <a id="instances"></a> Più istanze

Se l'app Web viene eseguita in più istanze, in ogni computer i WebJob verranno eseguiti in modalità continua e ogni computer attenderà i trigger e proverà a eseguire le funzioni. Il trigger delle code SDK di WebJobs impedisce automaticamente a una funzione di elaborare un messaggio di coda più volte. Le funzioni non devono essere scritte per essere idempotenti. Tuttavia, se si desidera garantire che solo un'istanza di una funzione venga eseguita anche se sono presenti più istanze dell’app web host, è possibile utilizzare l’attributo `Singleton`.

### <a id="parallel"></a> Esecuzione parallela

Se si dispongono di più funzioni in ascolto in code diverse, l'SDK le chiamerà in parallelo quando i messaggi vengono ricevuti simultaneamente.

Lo stesso vale quando si ricevono più messaggi per una singola coda. Per impostazione predefinita, l'SDK ottiene un batch di 16 messaggi in coda alla volta ed esegue la funzione che li elabora in parallelo. [La dimensione del batch è configurabile](#config). Quando il numero elaborato viene ridotto alla metà della dimensione del batch, l'SDK ottiene un altro batch e inizia l'elaborazione dei messaggi. Di conseguenza, il numero massimo di messaggi simultanei elaborati per ogni funzione è pari a una volta e mezza la dimensione del batch. Questo limite si applica separatamente a ogni funzione caratterizzata da un attributo `QueueTrigger`.

Se non si vuole avviare l'esecuzione parallela per i messaggi ricevuti su una coda, impostare le dimensioni del batch su 1. Vedere anche **Maggiore controllo sull'elaborazione delle code** in [Azure WebJobs SDK 1.1.0 RTM](/blog/azure-webjobs-sdk-1-1-0-rtm/).

### <a id="queuemetadata"></a>Ottenere i metadati della coda o del messaggio in coda

Con l'aggiunta di parametri alla firma del metodo, è possibile ottenere le proprietà del messaggio seguenti:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contiene il testo del messaggio)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Se si desidera usare direttamente l'API di archiviazione di Azure, è anche possibile aggiungere un parametro `CloudStorageAccount`.

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
		        "logMessage={0}\n" +
			"expirationTime={1}\ninsertionTime={2}\n" +
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

Una funzione eseguita in un processo Web continuo può accettare un parametro `CancellationToken` che consente al sistema operativo di notificare alla funzione quando il processo Web sta per essere terminato. È possibile usare questa notifica per assicurarsi che la funzione non termini in modo imprevisto lasciando i dati in uno stato incoerente.

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

**Nota:** è possibile che il dashboard non mostri correttamente lo stato e il risultato delle funzioni che sono state interrotte.
 
Per altre informazioni, vedere l'articolo sull'[arresto normale dei processi Web](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).

## <a id="createqueue"></a> Come creare un messaggio di coda durante l'elaborazione di un messaggio di coda

Per scrivere una funzione che crea un nuovo messaggio di coda, usare l'attributo `Queue`. Come per l'attributo `QueueTrigger`, è possibile passare il nome della coda come stringa oppure [impostare dinamicamente il nome della coda](#config).

### Messaggi stringa in coda

Il seguente esempio di codice non asincrono crea nella coda denominata "outputqueue" un nuovo messaggio di coda con lo stesso contenuto del messaggio di coda ricevuto nella coda denominata "inputqueue". Per le funzioni asincrone, usare `IAsyncCollector<T>` come illustrato più avanti in questa sezione.


		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}
  
### Messaggi di coda POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Per creare un messaggio di coda contenente un oggetto POCO anziché una stringa, passare il tipo POCO come parametro di output al costruttore dell'attributo `Queue`.
 
		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

L'SDK deserializza automaticamente l'oggetto in JSON. Viene sempre creato un messaggio in coda, anche se l'oggetto è null.

### Creare più messaggi o in funzioni asincrone

Per creare più messaggi, impostare il tipo di parametro per la coda di output `ICollector<T>` o `IAsyncCollector<T>`, come illustrato nel seguente esempio.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Ogni messaggio di coda viene creato immediatamente quando viene chiamato il metodo `Add`.

### Tipi usati dall'attributo Queue

È possibile usare l'attributo `Queue` nei seguenti tipi di parametri:

* `out string` (crea il messaggio di coda se il valore del parametro è diverso da null quando termina la funzione)
* `out byte[]` (funziona come `string`) 
* `out CloudQueueMessage` (funziona come `string`) 
* `out POCO` (tipo serializzabile, crea un messaggio con un oggetto null se il parametro è null quando termina la funzione)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (per la creazione manuale dei messaggi direttamente con l'API di archiviazione di Azure)

### <a id="ibinder"></a>Usare gli attributi di WebJobs SDK nel corpo di una funzione

Se è necessario eseguire alcune operazioni nella funzione prima di usare un attributo di WebJobs SDK come `Queue`, `Blob` o `Table`, è possibile usare l'interfaccia `IBinder`.

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

L'interfaccia `IBinder`può essere usata anche con gli attributi `Table` e `Blob`.

## <a id="blobs"></a> Come leggere e scrivere BLOB e tabelle durante l'elaborazione di un messaggio in coda

Gli attributi `Blob` e `Table` consentono di leggere e scrivere BLOB e tabelle. Gli esempi in questa sezione si applicano ai BLOB. Per esempi di codice che illustrano come attivare processi quando i BLOB vengono creati o aggiornati, vedere [Come utilizzare l'archiviazione BLOB di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) e per esempi di codice che leggono e scrivono tabelle, vedere [Come utilizzare l'archiviazione tabelle di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### Messaggi di coda stringa che attivano operazioni BLOB

Per un messaggio di coda contenente una stringa, `queueTrigger` è un segnaposto che è possibile usare nel parametro `blobPath` dell'attributo `Blob` che include il contenuto del messaggio.

Il seguente esempio usa oggetti `Stream` per leggere e scrivere i BLOB. Il messaggio di coda è il nome di un BLOB presente nel contenitore textblobs. Una copia del BLOB con "-new" aggiunto al nome viene creata nello stesso contenitore.

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Il costruttore dell'attributo `Blob` usa un parametro `blobPath` che specifica il contenitore e il nome del BLOB. Per altre informazioni su questo segnaposto, vedere [Come usare il servizio di archiviazione BLOB di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md).

Quando l'attributo decora un oggetto `Stream`, un altro parametro del costruttore specifica la modalità `FileAccess` come lettura, scrittura o lettura/scrittura.

Il seguente esempio usa un oggetto `CloudBlockBlob` per eliminare un BLOB. Il messaggio di coda è il nome del BLOB.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### <a id="pocoblobs"></a> Messaggi di coda POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))

Per un oggetto POCO archiviato come JSON nel messaggio di coda, è possibile usare i segnaposto che denominano le proprietà dell'oggetto nel parametro `blobPath` dell'attributo `Queue`. È anche possibile usare [nomi di proprietà dei metadati di coda](#queuemetadata) come segnaposto.

Il seguente esempio copia un BLOB in un nuovo BLOB con un'estensione diversa. Il messaggio di coda è un oggetto `BlobInformation` che include le proprietà `BlobName` e `BlobNameWithoutExtension`. I nomi delle proprietà vengono usati come segnaposto nel percorso BLOB per gli attributi `Blob`.
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

L'SDK usa il pacchetto [Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) per serializzare e deserializzare i messaggi. Se si creano messaggi di coda in un programma che non usa WebJobs SDK, è possibile scrivere codice simile a quello del seguente esempio per creare un messaggio di coda POCO analizzabile dall'SDK.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

Se è necessario eseguire alcune operazioni nella funzione prima di associare un BLOB a un oggetto, è possibile usare l'attributo nel corpo della funzione, [come mostrato in precedenza per l'attributo Queue](#ibinder).

### <a id="blobattributetypes"></a> Tipi con cui è possibile usare l'attributo Blob
 
L'attributo `Blob` può essere usato con i seguenti tipi:

* `Stream` (lettura o scrittura, specificata tramite il parametro del costruttore FileAccess)
* `TextReader`
* `TextWriter`
* `string` (lettura)
* `out string` (scrittura; crea un BLOB solo se il parametro di stringa è diverso da null quando la funzione restituisce un valore)
* POCO (lettura)
* out POCO (scrittura; crea sempre un BLOB come oggetto null se il parametro POCO è null quando la funzione restituisce un valore)
* `CloudBlobStream` (scrittura)
* `ICloudBlob` (lettura o scrittura)
* `CloudBlockBlob` (lettura o scrittura) 
* `CloudPageBlob` (lettura o scrittura) 

## <a id="poison"></a> Come gestire i messaggi non elaborabili

I messaggi il cui contenuto comporta l'esito negativo di una funzione sono denominati *messaggi non elaborabili*. Quando la funzione non riesce, il messaggio in coda non viene eliminato e infine viene prelevato, causando la ripetizione del ciclo. L'SDK può interrompere automaticamente il ciclo dopo un numero limitato di iterazioni oppure è possibile farlo manualmente.

### Gestione automatica dei messaggi non elaborabili

L'SDK chiamerà una funzione fino a 5 volte per elaborare un messaggio nella coda. Se il quinto tentativo non riesce, il messaggio viene spostato in una coda non elaborabile. [Il numero massimo di tentativi è configurabile](#config).

La coda non elaborabile è denominata *{nomecodaoriginale}*-poison. È possibile scrivere una funzione per elaborare i messaggi dalla coda non elaborabile archiviandoli o inviando una notifica della necessità di un intervento manuale.

Nel seguente esempio la funzione `CopyBlob` ha esito negativo quando un messaggio di coda contiene il nome di un BLOB inesistente. In questo caso, il messaggio viene spostato dalla coda copyblobqueue alla coda non elaborabile copyblobqueue-poison. `ProcessPoisonMessage` registra quindi il messaggio non elaborabile.

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

Nella figura seguente viene illustrato l'output di console di queste funzioni quando viene elaborato un messaggio non elaborabile.

![Output di console per la gestione dei messaggi non elaborabili](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Gestione manuale dei messaggi non elaborabili

È possibile ottenere il numero di volte in cui un messaggio è stato selezionato per l'elaborazione aggiungendo alla funzione un parametro `int` denominato `dequeueCount`. Sarà quindi possibile controllare il conteggio di rimozione dalla coda nel codice della funzione ed eseguire la gestione dei messaggi non elaborabili quando il numero supera una certa soglia, come illustrato nell'esempio seguente.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 3)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

## <a id="config"></a> Come impostare le opzioni di configurazione

È possibile usare il tipo `JobHostConfiguration` per impostare le seguenti opzioni di configurazione:

* Impostare le stringhe di connessione SDK nel codice.
* Configurare le impostazioni di `QueueTrigger`, ad esempio il conteggio rimozione dalla coda.
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

### <a id="configqueue"></a>Configurare le impostazioni di QueueTrigger

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
		    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="setnamesincode"></a>Impostare i valori per i parametri del costruttore WebJobs SDK nel codice

A volte si desidera specificare un nome di coda, un nome di BLOB o un contenitore oppure un nome di tabella nel codice anziché impostarlo come hardcoded. È ad esempio possibile specificare il nome della coda per `QueueTrigger` in una variabile di ambiente o in un file di configurazione.

A tale scopo, passare un oggetto `NameResolver` al tipo `JobHostConfiguration`. Includere segnaposto speciali racchiusi tra segni di percentuale (%) nei parametri del costruttore dell'attributo di SDK e il codice `NameResolver` specifica i valori effettivi da usare in sostituzione di questi segnaposto.

Si supponga, ad esempio, di voler usare una coda denominata logqueuetest nell'ambiente di test e una coda denominata logqueueprod nell'ambiente di produzione. Invece di impostare un nome di coda come hardcoded, è possibile specificare il nome di una voce nella raccolta `appSettings` con il nome effettivo della coda. Se la chiave `appSettings` è logqueue, la funzione potrebbe essere simile al seguente esempio.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

La classe `NameResolver` può quindi ottenere il nome della coda da `appSettings`, come illustrato nell'esempio seguente:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Passare la classe `NameResolver` all'oggetto `JobHost`, come illustrato nel seguente esempio.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 
**Nota:** i nomi di coda, tabella e BLOB vengono risolti ogni volta che viene chiamata una funzione, ma i nomi di contenitore di BLOB vengono risolti solo all'avvio dell'applicazione. Non è possibile modificare il nome del contenitore di BLOB durante l'esecuzione del processo.

## <a id="manual"></a>Come attivare manualmente una funzione

Per attivare manualmente una funzione, usare il metodo `Call` o `CallAsync` per l'oggetto `JobHost` e l'attributo `NoAutomaticTrigger` per la funzione, come illustrato nel seguente esempio.

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
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

Il dashboard visualizza i log in due posizioni: la pagina del processo Web e la pagina di una particolare chiamata al processo Web.

![Log nella pagina del processo Web](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Log nella pagina di una chiamata di funzione](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

L'output dei metodi Console chiamati in una funzione o nel metodo `Main()` viene visualizzato nella pagina Dashboard del WebJob, non nella pagina di una chiamata a un metodo specifico. L'output dell'oggetto TextWriter ottenuto da un parametro nella firma del metodo viene visualizzato nella pagina Dashboard di una chiamata al metodo.

L'output di Console non può essere collegato a una chiamata a un metodo particolare perché Console è a thread singolo, mentre potrebbero essere in esecuzione più funzioni di processo contemporaneamente. Per questo motivo l'SDK fornisce a ogni chiamata di funzione il proprio oggetto writer di log univoco.

Per scrivere [log di traccia dell’applicazione](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), usare `Console.Out` (crea log contrassegnati come INFO) e `Console.Error` (crea log contrassegnati come ERROR). In alternativa, è possibile usare [Trace o TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), che fornisce livelli dettagliati, di avviso e critici, oltre ai livelli di informazioni e di errore. I log di traccia dell'applicazione vengono visualizzati nei file di log dell'app Web, nelle tabelle di Azure o nei BLOB di Azure a seconda di come è configurata l'app Web di Azure. Come per tutti gli output di Console, anche i 100 registri applicazioni più recenti vengono visualizzati nella pagina Dashboard del processo Web e non nella pagina di una chiamata di funzione.

L'output di Console viene visualizzato nel dashboard solo se il programma viene eseguito in un processo Web di Azure e non se il programma viene eseguito localmente o in un altro ambiente.

Disabilitare la registrazione dei dashboard per scenari con velocità effettiva elevata. Per impostazione predefinita, l’SDK scrive i log nella memoria e questa attività potrebbe influire negativamente sulle prestazioni quando si elaborano numerosi messaggi. Per disabilitare la registrazione, impostare la stringa di connessione dashboard su null, come illustrato nell'esempio seguente.

		JobHostConfiguration config = new JobHostConfiguration();       
		config.DashboardConnectionString = “”;        
		JobHost host = new JobHost(config);
		host.RunAndBlock();

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

Nel dashboard di WebJobs SDK, l'output dell'oggetto `TextWriter` viene visualizzato quando si visita la pagina relativa a una chiamata di funzione specifica e si fa clic su **Attiva/Disattiva output**:

![Fare clic sul collegamento della chiamata di funzione](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Log nella pagina di una chiamata di funzione](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Nel dashboard di WebJobs SDK le 100 righe più recenti dell'output di Console vengono visualizzate quando si visita la pagina relativa al processo Web (non alla chiamata di funzione) e si fa clic su **Attiva/Disattiva output**.
 
![Fare clic su Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

In un processo Web continuo, i log dell'applicazione vengono visualizzati in /data/jobs/continuous/*{webjobname}*/job\_log.txt nel file system dell’app Web.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

In un BLOB di Azure, l'aspetto dei registri applicazione è simile al seguente: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

In una tabella di Azure, i log `Console.Out` e `Console.Error` hanno un aspetto simile al seguente:

![Log delle informazioni nella tabella](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Log degli errori nella tabella](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

Se si desidera collegare un proprio logger, vedere [questo esempio](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Program.cs).

## <a id="errors"></a>Come gestire gli errori e configurare i timeout

WebJobs SDK include anche un attributo [Timeout](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs) che è possibile utilizzare per annullare una funzione se non viene completata entro un periodo di tempo specificato. E se si desidera generare un avviso quando si verificano numerosi errori entro un periodo di tempo specificato, è possibile utilizzare l’attributo `ErrorTrigger`. Ecco un [esempio ErrorTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Error-Monitoring).

```
public static void ErrorMonitor(
[ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
[SendGrid(
    To = "admin@emailaddress.com",
    Subject = "Error!")]
 SendGridMessage message)
{
    // log last 5 detailed errors to the Dashboard
   log.WriteLine(filter.GetDetailedMessage(5));
   message.Text = filter.GetDetailedMessage(1);
}
```

Anche in modo dinamico, è possibile disabilitare e abilitare le funzioni per controllare se possono essere attivate, utilizzando un'opzione di configurazione che potrebbe essere un'impostazione app o il nome della variabile dell’ambiente. Per codici di esempio, vedere l’attributo `Disable` nel [repository degli esempi di WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs).

## <a id="nextsteps"></a> Passaggi successivi

Questa guida ha fornito esempi di codice che illustrano come gestire scenari comuni per l'uso di code di Azure. Per altre informazioni su come usare i processi Web di Azure e su WebJobs SDK, vedere le [risorse consigliate per i processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=AcomDC_1217_2015-->