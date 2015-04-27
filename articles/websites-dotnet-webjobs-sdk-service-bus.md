<properties 
	pageTitle="Come usare il bus di servizio di Azure con WebJobs SDK" 
	description="Informazioni su come usare le code e gli argomenti del bus di servizio di Azure con WebJobs SDK." 
	services="web-sites, service-bus" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Come usare il bus di servizio di Azure con WebJobs SDK

## Panoramica

In questa guida vengono forniti esempi di codice C# che illustrano come attivare un processo quando viene creato o aggiornato un BLOB di Azure. Gli esempi di codice usano [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versione 1.x.

La guida presuppone che siano già state acquisite le informazioni su [come creare un progetto di processo Web in Visual Studio con stringhe di connessione che puntano all'account di archiviazione](websites-dotnet-webjobs-sdk-get-started.md).

I frammenti di codice mostrano solo funzioni, non il codice che crea l'oggetto  `JobHost` come nell'esempio seguente:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## <a id="prerequisites"></a> Prerequisiti

Per lavorare con il bus di servizio è necessario installare il pacchetto NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) oltre agli altri pacchetti WebJobs SDK. 

È anche necessario impostare la stringa di connessione AzureWebJobsServiceBus oltre alle stringhe di connessione di archiviazione.

## <a id="trigger"></a> Come attivare una funzione quando viene ricevuto un messaggio in coda del bus di servizio

Per scrivere una funzione che WebJobs SDK chiama quando viene ricevuto un messaggio in coda, usare l'attributo  `ServiceBusTrigger`. Il costruttore di attributo accetta un parametro che specifica il nome della coda di cui eseguire il polling.

### Funzionamento di ServicebusTrigger

L'SDK riceve un messaggio in modalità  `PeekLock` e chiama  `Complete` sul messaggio se la funzione termina correttamente oppure chiama  `Abandon`se la funzione non riesce. Se la funzione viene eseguita oltre il timeout  `PeekLock`, il blocco viene rinnovato automaticamente.

Il bus di servizio esegue la gestione della propria coda non elaborabile, in modo che non sia controllata né configurabile in WebJobs SDK. 

### Messaggio stringa in coda

L'esempio di codice seguente legge un messaggio in coda che contiene una stringa e scrive la stringa nel dashboard WebJobs SDK.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Nota:** se si stanno creando messaggi in coda in un'applicazione che non usa WebJobs SDK, assicurarsi di impostare [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) su "text/plain".

### Messaggio POCO in coda

L'SDK deserializza automaticamente un messaggio in coda contenente JSON per un tipo POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)). L'esempio di codice seguente legge un messaggio in coda che contiene un oggetto  `BlobInformation` con una proprietà  `BlobName`:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Per esempi di codice che illustrano come usare le proprietà del tipo POCO assieme ai BLOB e alle tabelle nella stessa funzione, vedere la [versione delle code di archiviazione in questo articolo](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs).

### Tipi con cui funziona ServiceBusTrigger

Oltre ai tipi  `string` e POCO,  è possibile usare l'attributo  `ServiceBusTrigger` con una matrice di byte o un oggetto  `BrokeredMessage`.

## <a id="create"></a> Come creare messaggi in coda del bus di servizio

Per scrivere una funzione che crei un nuovo messaggio in coda usare l'attributo  `ServiceBus` e passare il nome della coda al costruttore dell'attributo. 


### Creare un singolo messaggio in coda in una funzione non asincrona

L'esempio di codice seguente usa un parametro di output per creare un nuovo messaggio nella coda denominata "outputqueue" con lo stesso contenuto del messaggio ricevuto nella coda denominata "inputqueue".

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

Il parametro di output per la creazione di un singolo messaggio della coda può essere uno dei seguenti tipi:

* `string`
* `byte[]`
* `BrokeredMessage`
* Un tipo serializzabile POCO definito dall'utente. Serializzato automaticamente in formato JSON.

Per i parametri di tipo POCO, viene sempre creato un messaggio in coda quando la funzione termina; se il parametro è null, l'SDK crea un messaggio in coda che restituirà null quando il messaggio viene ricevuto e deserializzato. Per gli altri tipi, se il parametro è null non viene creato alcun messaggio in coda.

### Creare più messaggi in coda o in funzioni asincrone

Per creare più messaggi, usare l'attributo  `ServiceBus` con  `ICollector<T>` oppure  `IAsyncCollector<T>`, come illustrato nell'esempio di codice seguente:

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Ogni messaggio in coda viene creato immediatamente quando viene chiamato il metodo  `Add`

## <a id="topics"></a>Come usare gli argomenti del bus di servizio

Per scrivere una funzione che l'SDK chiama quando viene ricevuto un messaggio su un argomento del bus di servizio, usare l'attributo  `ServiceBusTrigger` con il costruttore che accetta il nome di argomento e sottoscrizione, come illustrato nell'esempio di codice riportato di seguito:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Per creare un messaggio su un argomento, usare l'attributo  `ServiceBus` con un nome di argomento simile a quello usato con un nome di coda.

## <a id="queues"></a>Argomenti correlati rientranti nell'articolo sulle procedure di archiviazione delle code

Per informazioni sugli scenari di WebJobs SDK non specifici del bus di servizio, vedere [come usare l'archiviazione delle code di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Gli argomenti trattati in questo articolo includono quanto segue:

* Funzioni asincrone
* Più istanze
* Arresto normale
* Usare gli attributi di WebJobs SDK nel corpo di una funzione
* Impostare le stringhe di connessione SDK nel codice
* Impostare i valori per i parametri del costruttore WebJobs SDK nel codice
* Attivare manualmente una funzione
* Scrivere i log

## <a id="nextsteps"></a> Passaggi successivi

Questa guida ha fornito esempi di codice che illustrano come gestire scenari comuni per l'utilizzo del bus di servizio di Azure. Per altre informazioni su come usare Azure WebJobs e WebJobs SDK, vedere [Risorse consigliate per Processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=45--> 
