---
title: "Cos&quot;è Azure WebJobs SDK"
description: "Introduzione ad Azure WebJobs SDK. Spiega che cos&quot;è l&quot;SDK e gli scenari tipici in cui è utile con alcuni esempi di codice."
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: 8281267b-572b-4b14-a328-6704493ea682
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2d21cd34427921ad789b4c95212c07caddd5a966
ms.lasthandoff: 11/17/2016


---
# <a name="what-is-the-azure-webjobs-sdk"></a>Cos'è Azure WebJobs SDK
## <a id="overview"></a>Panoramica
Questo articolo spiega cos'è WebJobs SDK, analizza alcuni scenari comuni in cui si rivela utile e traccia una panoramica della modalità d'uso nel codice personalizzato.

[!INCLUDE [app-service-web-webjobs-corenote](../../includes/app-service-web-webjobs-corenote.md)]

[WebJobs](websites-webjobs-resources.md) è una funzionalità di Servizio app di Azure che consente di eseguire un programma o uno script nello stesso contesto di un'app Web, un'app per le API o un'app per dispositivi mobili. Lo scopo di [WebJobs SDK](websites-webjobs-resources.md) è semplificare il codice scritto per le attività comuni che possono essere eseguite da un processo Web, ad esempio l'elaborazione di immagini, l'elaborazione della coda, l'aggregazione RSS, la manutenzione di file e l'invio di messaggi di posta elettronica. WebJobs SDK offre funzionalità incorporate per l'uso dell'Archiviazione di Azure e del bus di servizio, per la pianificazione di attività, per la gestione degli errori e per molti altri scenari comuni. Inoltre, è progettato per essere estendibile. [WebJobs SDK è open source](https://github.com/Azure/azure-webjobs-sdk/) ed è presente un [repository open source per le estensioni](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

In WebJobs SDK sono inclusi i componenti seguenti:

* **Pacchetti NuGet**. I pacchetti NuGet aggiunti a un progetto di applicazione console di Visual Studio forniscono un framework usato dal codice associando al metodo attributi di WebJobs SDK.
* **Dashboard**. Parte di WebJobs SDK, questo componente è incluso in Servizio app di Azure e fornisce monitoraggio e diagnostica avanzati per i programmi che usano i pacchetti NuGet. Non è necessario scrivere il codice per usare queste funzionalità di monitoraggio e diagnostica.

## <a id="scenarios"></a>Scenari
Ecco alcuni scenario tipici che è possibile gestire più facilmente con Azure WebJobs SDK:

* Elaborazione di immagini o altre operazioni con un uso intensivo della CPU. Una comune funzionalità dei siti Web è il caricamento di immagini o video. Spesso è necessario intervenire sul contenuto dopo averlo caricato, ma non si vuole far attendere l'utente nel frattempo.
* Elaborazione di code. Per un front-end Web un modo comune di comunicare con un servizio back-end è usare le code. Quando il sito Web deve eseguire delle attività, effettua il push di un messaggio in una coda. Un servizio back-end effettua il pull dei messaggi dalla coda ed esegue le attività. È possibile usare le code per l'elaborazione delle immagini: ad esempio, dopo che l'utente ha caricato numerosi file, inserire i nomi dei file in un messaggio di coda da prelevare con il back-end per l'elaborazione. È possibile usare le code anche per migliorare la risposta del sito. Ad esempio, invece di scrivere direttamente in un database SQL, scrivere in una coda, informare l'utente che l'operazione è stata completata e lasciar gestire al servizio back-end il lavoro dei database relazionali con latenza elevata. Per un esempio di elaborazione di code con elaborazione di immagini, vedere l' [esercitazione di introduzione a WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md).
* Aggregazione RSS. Se si ha un sito che gestisce un elenco di feed RSS, è possibile effettuare il pull di tutti gli articoli dai feed in un processo in background.
* Manutenzione di file, ad esempio aggregazione o pulizia di file di log.  Potrebbe essere necessario combinare file di log creati da più siti o in periodi di tempo diversi per eseguire processi di analisi. O potrebbe essere necessario pianificare un'attività settimanale per pulire i file di log vecchi.
* Ingresso alle tabelle di Azure. È probabile che siano stati archiviati file e BLOB da analizzare per poi archiviare i dati nelle tabelle. La funzione di ingresso potrebbe scrivere molte righe (milioni, in alcuni casi) e WebJobs SDK rende possibile implementare facilmente questa funzionalità. L'SDK fornisce anche il monitoraggio in tempo reale degli indicatori di stato come il numero di righe scritte nella tabella.
* Altre attività con esecuzione prolungata da eseguire in un thread in background, ad esempio l' [invio di messaggi di posta elettronica](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). 
* Eventuali attività che si vuole eseguire in base a una pianificazione, ad esempio un'operazione di backup ogni notte.

In molti di questi scenari è possibile scalare un'applicazione Web per l’esecuzione su più VM, che eseguirebbero contemporaneamente più processi Web. In questo modo in alcuni scenari gli stessi dati verrebbero elaborati più volte, ma ciò non costituisce un problema quando si utilizza la coda, i BLOB e i trigger del bus di servizio integrati dell’SDK dei processi Web. L’SDK garantisce che le funzioni verranno elaborate una sola volta per ogni messaggio o BLOB.

WebJobs SDK semplifica anche la gestione di scenari comuni di gestione degli errori. È possibile impostare gli avvisi per inviare notifiche quando una funzione ha esito negativo, ed è possibile impostare i timeout in modo che una funzione venga annullata automaticamente se non viene completata entro un limite di tempo specificato.

## <a id="code"></a> Esempi di codice
Il codice per gestire le attività tipiche che usano l'archiviazione di Azure è semplice. Nel metodo `Main` dell'applicazione console si crea un oggetto `JobHost` che coordina le chiamate ai metodi scritti. Il framework WebJobs SDK sa quando chiamare i metodi e conosce i valori dei parametri da usare in base agli attributi di WebJobs SDK usati. L'SDK fornisce i *trigger* che specificano le condizioni che hanno causato la chiamata della funzione e i *binder* che specificano come ottenere informazioni nei e dai parametri del metodo.

Ad esempio, l'attributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) comporta la chiamata a una funzione quando viene ricevuto un messaggio in una coda e la deserializzazione automatica del messaggio, se il formato di quest'ultimo è JSON per una matrice di byte o un tipo personalizzato. L'attributo [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) attiva un processo quando viene creato un nuovo BLOB in un account di Archiviazione di Azure.

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

L'oggetto `JobHost` è un contenitore per un set di funzioni in background. L'oggetto `JobHost` monitora le funzioni, cerca gli eventi che le attivano ed esegue le funzioni quando si verificano eventi di attivazione. Si chiama un metodo `JobHost` per indicare se eseguire il processo del contenitore nel thread corrente o in un thread in background. Nell'esempio, il metodo `RunAndBlock` esegue il processo ininterrottamente nel thread corrente.

Poiché il metodo `ProcessQueueMessage` in questo esempio ha un attributo `QueueTrigger`, il trigger per tale funzione è la ricezione di un nuovo messaggio in coda. L'oggetto `JobHost` cerca nuovi messaggi nella coda specificata (in questo esempio "webjobsqueue") e, quando ne trova uno, chiama `ProcessQueueMessage`. 

L'attributo `QueueTrigger` associa il parametro `inputText` al valore del messaggio in coda. L'attributo `Blob` collega un oggetto `TextWriter` a un BLOB denominato "nomeblob" in un contenitore denominato "nomecontenitore".  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

La funzione usa quindi questi parametri per scrivere il valore del messaggio della coda nel BLOB:

        writer.WriteLine(inputText);

Le funzionalità di trigger e binder di WebJobs SDK semplificano notevolmente il codice da scrivere. Il codice di livello base necessario per elaborare code, BLOB o file o per iniziare attività di pianificazione, viene compilato automaticamente dal framework WebJobs SDK. Ad esempio, il framework crea code non ancora esistenti, le apre, legge i messaggi delle code, li elimina al termine dell'elaborazione, crea contenitori BLOB non ancora esistenti, scrive nei BLOB e così via.

L'esempio di codice seguente illustra una serie di trigger in un processo Web: `QueueTrigger`, `FileTrigger`, `WebHookTrigger` e `ErrorTrigger`. 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

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
    }
```

## <a id="schedule"></a> Pianificazione
L'attributo `TimerTrigger` offre la possibilità di attivare funzioni da eseguire in base a una pianificazione. È possibile pianificare un processo Web nel suo complesso tramite Azure o pianificare singole funzioni di un processo Web mediante WebJobs SDK `TimerTrigger`. Ecco un esempio di codice.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Per altri esempi di codice, vedere [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) nel repository azure-webjobs-sdk-extensions in GitHub.com.

## <a name="extensibility"></a>Estensibilità
Non è necessario limitarsi all'uso delle funzionalità integrate, WebJobs SDK consente di scrivere trigger e binder personalizzati.  Ad esempio, è possibile scrivere trigger per gli eventi della cache e le pianificazioni periodiche. Un [repository open source](https://github.com/Azure/azure-webjobs-sdk-extensions) contiene una [guida dettagliata sull'estensibilità di WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) e un codice di esempio per iniziare a scrivere i propri trigger e binder.

## <a id="workerrole"></a>Uso di WebJobs SDK al di fuori dei processi Web
Un programma che usa WebJobs SDK è un'applicazione console standard che può essere eseguita ovunque, non obbligatoriamente come processo Web. È possibile testare il programma a livello locale sul computer di sviluppo e, in fase di produzione, eseguirlo in un ruolo di lavoro del servizio cloud oppure un servizio Windows, se si preferisce uno di questi ambienti. 

Tuttavia il dashboard è disponibile solo come estensione per un'app Web di Servizio app di Azure. Per l'esecuzione all'esterno di un processo Web usando comunque il dashboard, è possibile configurare un'app Web in modo da usare lo stesso account di archiviazione a cui fa riferimento la stringa di connessione del dashboard di WebJobs SDK, nel quale verranno quindi visualizzati i dati relativi all'esecuzione della funzione dal programma che viene eseguito altrove. Sarà possibile accedere al dashboard usando l'URL https://*{nomeappweb}*.scm.azurewebsites.net/azurejobs/#/functions. Per altre informazioni, vedere il post di blog riguardo l' [accesso a un dashboard per lo sviluppo locale con WebJobs SDK](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), in cui tuttavia viene usato un vecchio nome per la stringa di connessione. 

## <a id="nostorage"></a>Funzionalità del dashboard
WebJobs SDK offre diversi vantaggi, anche se non si usano i trigger o i binder di WebJobs SDK:

* È possibile richiamare le funzioni dal dashboard.
* È possibile riprodurre le funzioni dal dashboard.
* È possibile visualizzare nel dashboard i log collegati al particolare processo Web (log applicazioni scritti usando Console.Out, Console.Error, Trace e così via) oppure alla particolare chiamata di funzione che li ha generati (log scritti usando l'oggetto `TextWriter` passato alla funzione come parametro dall'SDK). 

Per altre informazioni, vedere le sezioni relative a [come chiamare manualmente una funzione](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) e [come scrivere i log](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a id="nextsteps"></a>Passaggi successivi
Per altre informazioni su WebJobs SDK, vedere le [risorse consigliate per i Processi Web Azure](http://go.microsoft.com/fwlink/?linkid=390226).

Per informazioni sui miglioramenti più recenti di WebJobs SDK, vedere le [note sulla versione](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).


