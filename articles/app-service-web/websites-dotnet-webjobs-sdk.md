<properties 
	pageTitle="Informazioni su Azure WebJobs SDK" 
	description="Introduzione ad Azure WebJobs SDK. Spiega che cos'è l'SDK e gli scenari tipici in cui è utile con alcuni esempi di codice." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="tdykstra"/>

# Informazioni su Azure WebJobs SDK

## <a id="overview"></a>Panoramica

Questo articolo spiega cos'è WebJobs SDK, analizza alcuni scenari comuni in cui si rivela utile e traccia una panoramica della modalità d'uso nel codice personalizzato.

[WebJobs](websites-webjobs-resources.md) è una funzionalità del servizio app di Azure che consente di eseguire un programma o uno script nello stesso contesto di un'app Web. Lo scopo di WebJobs SDK è semplificare l'attività di scrittura del codice che viene eseguito come processo Web e funziona con code, BLOB e tabelle di archiviazione di Azure e code del bus di servizio.

In WebJobs SDK sono inclusi i componenti seguenti:

* **Pacchetti NuGet**. I pacchetti NuGet aggiunti a un progetto di applicazione console di Visual Studio forniscono un framework usato dal codice per lavorare con le code dei servizi di archiviazione di Azure o del bus del servizio.   
  
* **Dashboard**. Parte di WebJobs SDK, questo componente è incluso nel servizio app di Azure e fornisce monitoraggio e diagnostica avanzati per i programmi che usano i pacchetti NuGet. Non è necessario scrivere codice per usare queste funzionalità di monitoraggio e diagnostica.

## <a id="scenarios"></a>Scenari

Ecco alcuni scenari tipici che è possibile gestire più facilmente con Azure WebJobs SDK:

* Elaborazione di immagini o altre operazioni con un uso intensivo della CPU. Una comune funzionalità dei siti Web è il caricamento di immagini o video. Spesso è necessario intervenire sul contenuto dopo averlo caricato, ma non si vuole far attendere l'utente nel frattempo.

* Elaborazione di code. Per un front-end Web un modo comune di comunicare con un servizio back-end è usare le code. Quando il sito Web deve eseguire delle attività, effettua il push di un messaggio in una coda. Un servizio back-end effettua il pull dei messaggi in coda ed esegue le attività. È possibile usare le code per l'elaborazione delle immagini: ad esempio, dopo che l'utente ha caricato numerosi file, inserire i nomi dei file in un messaggio in coda che sarà prelevato dal back-end per l'elaborazione. In alternativa, è possibile usare le code per migliorare la velocità di risposta del sito. Ad esempio, invece di scrivere direttamente in un database SQL, scrivere in una coda, informare l'utente che l'operazione è stata completata e lasciar gestire al servizio back-end il lavoro dei database relazionali con latenza elevata. Per un esempio di elaborazione di code con elaborazione di immagini, vedere l'[esercitazione di introduzione a WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md).

* Aggregazione RSS. Se si ha un sito che gestisce un elenco di feed RSS, è possibile effettuare il pull di tutti gli articoli dai feed in un processo in background.

* Manutenzione di file, ad esempio aggregazione o pulizia di file di log. Potrebbe essere necessario combinare file di log creati da più siti o in periodi di tempo diversi per eseguire processi di analisi. O potrebbe essere necessario pianificare un'attività settimanale per pulire i file di log vecchi.

* Ingresso alle tabelle di Azure. È probabile che siano stati archiviati file e BLOB da analizzare per poi archiviare i dati nelle tabelle. La funzione di ingresso potrebbe scrivere molte righe (milioni, in alcuni casi) e WebJobs SDK rende possibile implementare facilmente questa funzionalità. L'SDK fornisce anche il monitoraggio in tempo reale degli indicatori di stato come il numero di righe scritte nella tabella.

* Altre attività con esecuzione prolungata da eseguire in un thread in background, ad esempio l'[invio di messaggi di posta elettronica](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure).

## <a id="code"></a> Esempi di codice

Il codice per gestire le attività tipiche che usano l'archiviazione di Azure è semplice. In un'applicazione console scrivere i metodi per le attività in background da eseguire e decorarli con attributi da WebJobs SDK. Il metodo `Main` crea un oggetto `JobHost` che coordina le chiamate ai metodi scritti. Il framework WebJobs SDK sa quando chiamare i metodi in base agli attributi di WebJobs SDK usati.

Ecco un semplice programma che esegue il polling di una coda e crea un BLOB per ogni messaggio in coda ricevuto:

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

L'oggetto `JobHost` è un contenitore per un set di funzioni in background. L'oggetto `JobHost` monitora le funzioni, cerca gli eventi che le attivano ed esegue le funzioni quando si verificano eventi di attivazione. Si chiama un metodo `JobHost` per indicare se eseguire il processo del contenitore nel thread corrente o in un thread in background. Nell'esempio il metodo `RunAndBlock` esegue il processo ininterrottamente nel thread corrente.

Poiché il metodo `ProcessQueueMessage` in questo esempio ha un attributo `QueueTrigger`, il trigger per tale funzione è la ricezione di un nuovo messaggio in coda. L'oggetto `JobHost` cerca nuovi messaggi nella coda specificata (in questo esempio "webjobsqueue") e, quando ne trova uno, chiama `ProcessQueueMessage`. L'attributo `QueueTrigger` notifica anche al framework di associare il parametro `inputText` al valore del messaggio in coda:

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] <mark>string inputText</mark>, 
    [Blob("containername/blobname")]TextWriter writer)</pre>

Il framework associa anche un oggetto `TextWriter` a un BLOB denominato "blobname" in un contenitore denominato "containername":

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
    <mark>[Blob("containername/blobname")]TextWriter writer</mark>)</pre>

La funzione usa quindi questi parametri per scrivere il valore del messaggio della coda nel BLOB:

		writer.WriteLine(inputText);

Le funzionalità di trigger e di binder di WebJobs SDK semplificano notevolmente il codice da scrivere per usare l'archiviazione di Azure e le code di Service Bus. Il codice di livello base necessario per gestire l'elaborazione della coda e del BLOB viene compilato automaticamente dal framework WebJobs SDK. Il framework crea code che non esistono ancora, le apre, legge i messaggi delle code, li elimina al termine dell'elaborazione, crea contenitori di BLOB che non esistono ancora, scrive nei BLOB e così via.

WebJobs SDK consente di usare l'archiviazione di Azure in molti modi diversi. Ad esempio, se il parametro con cui si associa l'attributo `QueueTrigger` è una matrice di byte o un tipo personalizzato, viene automaticamente deserializzato da JSON. È anche possibile usare un attributo `BlobTrigger` per attivare un processo quando viene creato un nuovo BLOB in un account di archiviazione di Azure. Tenere presente che, mentre `QueueTrigger` trova i nuovi messaggi in coda in pochi secondi, `BlobTrigger` può impiegare fino a 20 minuti per rilevare un nuovo BLOB. `BlobTrigger` cerca i BLOB quando viene avviato `JobHost` e quindi controlla periodicamente i log di archiviazione di Azure per rilevare nuovi BLOB.

## <a id="workerrole"></a>Uso di WebJobs SDK al di fuori dei processi Web

Un programma che usa WebJobs SDK è un'applicazione console standard che può essere eseguita ovunque, non obbligatoriamente come processo Web. È possibile testare il programma a livello locale sul computer di sviluppo e, in fase di produzione, eseguirlo in un ruolo di lavoro del servizio cloud oppure un servizio Windows, se si preferisce uno di questi ambienti.

Il dashboard è tuttavia disponibile solo come estensione per un'app Web del servizio app di Azure. Per l'esecuzione all'esterno di un processo Web usando comunque il dashboard, è possibile configurare un'app Web per l'uso dello stesso account di archiviazione a cui fa riferimento la stringa di connessione del dashboard di WebJobs SDK, nel quale saranno quindi visualizzati i dati relativi all'esecuzione della funzione dal programma eseguito altrove. È possibile accedere al dashboard con l'URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Per altre informazioni, vedere il post di blog relativo all'[accesso a un dashboard per lo sviluppo locale con WebJobs SDK](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), in cui tuttavia viene usato un vecchio nome per la stringa di connessione.

## <a id="nostorage"></a>Uso di WebJobs SDK per richiamare una funzione qualsiasi

WebJobs SDK offre diversi vantaggi, anche se non è necessario usare direttamente le code, le tabella o i BLOB di archiviazione di Azure o le code del bus di servizio:

* È possibile richiamare le funzioni dal dashboard.
* È possibile riprodurre le funzioni dal dashboard.
* È possibile visualizzare nel dashboard i log collegati al particolare processo Web (registri applicazioni scritti usando Console.Out, Console.Error, Trace e così via) oppure alla particolare chiamata di funzione che li ha generati (log scritti usando l'oggetto `TextWriter` passato alla funzione come parametro dall'SDK). 

* Per altre informazioni, vedere le sezioni su [come chiamare manualmente una funzione](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) e [come scrivere i log](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs).

## <a id="nextsteps"></a>Passaggi successivi

Per altre informazioni su WebJobs SDK, vedere le [risorse consigliate per Processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=July15_HO3-->