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

Questa guida fornisce esempi di codice C# che illustrano come attivare un processo quando viene creato o aggiornato un BLOB di Azure. Gli esempi di codice usano [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versione 1.x.

Nella guida si presuppone che si sappia [come creare un progetto di processo Web in Visual Studio con stringhe di connessione che puntano all'account di archiviazione](websites-dotnet-webjobs-sdk-get-started.md).

I frammenti di codice mostrano solo funzioni, non il codice che crea l'oggetto  `JobHost` come nel seguente esempio:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Sommario

-   [Prerequisiti](#prerequisites)
-   [Come attivare una funzione quando viene ricevuto un messaggio di coda](#trigger)
-   [Come creare messaggi di coda](#create)
-   [Come usare gli argomenti del bus di servizio](#topics)
-   [Argomenti correlati trattati nell'articolo sulle code di archiviazione](#queues)
-   [Passaggi successivi](#nextsteps)

## <a id="prerequisites"></a> Prerequisiti

Per usare il bus di servizio è necessario installare il pacchetto NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) oltre agli altri pacchetti WebJobs SDK. 

È inoltre necessario impostare la stringa di connessione AzureWebJobsServiceBus oltre alle stringhe di connessione di archiviazione.

## <a id="trigger"></a> Come attivare una funzione quando viene ricevuto un messaggio di coda del bus di servizio

Per scrivere una funzione che viene chiamata da WebJobs SDK quando viene ricevuto un messaggio di coda, usare l'attributo  `ServiceBusTrigger`. Il costruttore dell'attributo usa un parametro di stringa che specifica il nome della coda di cui eseguire il polling.

### Funzionamento di ServicebusTrigger

L'SDK riceve un messaggio in modalità  `PeekLock` e chiama  `Complete` sul messaggio se la funzione viene completata correttamente oppure  `Abandon` se la funzione ha esito negativo. Se il tempo di esecuzione della funzione supera il timeout di  `PeekLock`, il blocco viene rinnovato automaticamente.

Il bus di servizio esegue la gestione delle code non elaborabili, in modo che non sia controllato o configurabile in WebJobs SDK. 

### Messaggio di coda stringa

Il seguente esempio di codice legge un messaggio di coda che contiene una stringa e scrive la stringa nel dashboard WebJobs SDK.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Nota:** se si stanno creando messaggi di coda in un'applicazione che non usa WebJobs SDK, impostare [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) su "text/plain".

### Messaggi di coda POCO

L'SDK deserializzerà automaticamente un messaggio di coda contenente JSON per un tipo POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)). Il seguente esempio di codice legge un messaggio di coda che contiene un oggetto  `BlobInformation` con una proprietà  `BlobName`:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Per esempi di codice che illustrano come usare le proprietà di POCO per l'uso con BLOB e tabelle nella stessa funzione, vedere la [versione delle code di archiviazione di questo articolo](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs).

### Tipi usati da ServiceBusTrigger

Oltre ai tipi  `string` e POCO, è possibile usare l'attributo  `ServiceBusTrigger` con una matrice di byte o un oggetto  `BrokeredMessage`.

## <a id="create"></a> Come creare messaggi di coda del bus di servizio

Per scrivere una funzione che crea un nuovo messaggio di coda, usare l'attributo  `ServiceBus` e passare il nome della coda al costruttore dell'attributo. 


### Creare un singolo messaggio di coda in una funzione non asincrona

Il seguente esempio di codice usa un parametro di output per creare un nuovo messaggio nella coda denominata "outputqueue" con lo stesso contenuto del messaggio ricevuto nella coda denominata "inputqueue".

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

Il parametro di output per la creazione di un singolo messaggio di coda può essere uno dei seguenti tipi:

* `string`
* `byte[]`
* `BrokeredMessage`
* Un tipo serializzabile POCO definito dall'utente. Viene serializzato automaticamente in formato JSON.

Per i parametri di tipo POCO, viene sempre creato un messaggio di coda quando termina la funzione. Se il parametro è null, l'SDK crea un messaggio di coda che restituisce null quando il messaggio viene ricevuto e deserializzato. Per gli altri tipi, se il parametro è null non viene creato alcun messaggio di coda.

### Creare più messaggi di coda o in funzioni asincrone

Per creare più messaggi, usare l'attributo  `ServiceBus` con  `ICollector<T>` o  `IAsyncCollector<T>`, come illustrato nel seguente esempio di codice:

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Ogni messaggio di coda viene creato immediatamente quando viene chiamato il metodo  `Add`.

## <a id="topics"></a>Come usare gli argomenti del bus di servizio

Per scrivere una funzione che viene chiamata dall'SDK quando viene ricevuto un messaggio su un argomento del bus di servizio, usare l'attributo  `ServiceBusTrigger` con il costruttore che accetta il nome di argomento e il nome di sottoscrizione, come illustrato nel seguente esempio di codice:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Per creare un messaggio su un argomento, usare l'attributo  `ServiceBus` con un nome di argomento nello stesso modo in cui viene usato con un nome di coda.

## <a id="queues"></a>Argomenti correlati trattati nell'articolo delle procedure per le code di archiviazione

Per informazioni sugli scenari di WebJobs SDK non specifici del bus di servizio, vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Tra gli argomenti trattati nell'articolo sono inclusi i seguenti:

* Funzioni asincrone
* Istanze multiple
* Arresto normale
* Usare gli attributi WebJobs SDK nel corpo di una funzione
* Impostare le stringhe di connessione SDK nel codice
* Impostare i valori per i parametri del costruttore WebJobs SDK nel codice
* Attivare manualmente una funzione
* Scrivere log

## <a id="nextsteps"></a> Passaggi successivi

Questa guida ha fornito esempi di codice che illustrano come gestire scenari comuni per l'uso del bus di servizio di Azure. Per altre informazioni su come usare i processi Web di Azure e WebJobs SDK, vedere [Risorse consigliate per Processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).


<!--HONumber=42-->
