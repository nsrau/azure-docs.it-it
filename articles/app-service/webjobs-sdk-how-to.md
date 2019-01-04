---
title: Come usare WebJobs SDK - Azure
description: Altre informazioni su come scrivere codice per WebJobs SDK. Creare processi di elaborazione in background guidata dagli eventi che accedono ai dati nei servizi di Azure e di terze parti.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: f6d343d42bf9d918bf23c9f5f442d977a5caca96
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343718"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Come usare Azure WebJobs SDK per l'elaborazione in background guidata dagli eventi

Questo articolo offre materiale sussidiario su come scrivere codice per [Azure WebJobs SDK](webjobs-sdk-get-started.md). La documentazione si applica alle versioni 2.x e 3.x, salvo dove specificato diversamente. La principale modifica introdotta dalla versione 3.x è l'uso di .NET Core invece di .NET Framework.

>[!NOTE]
> [Funzioni di Azure](../azure-functions/functions-overview.md) si basa su WebJobs SDK e per alcuni argomenti questo articolo include collegamenti alla documentazione su Funzioni di Azure. Si notino le differenze seguenti tra Funzioni e WebJobs SDK:
> * La versione 1.x di Funzioni di Azure corrisponde alla versione 2.x di WebJobs SDK, mentre la versione 2.x di Funzioni di Azure corrisponde alla versione 3.x di WebJobs SDK. I repository di codice sorgente seguono la numerazione di WebJobs SDK e molti di loro hanno rami della versione 2. x, mentre il ramo principale ha attualmente il codice 3.x.
> * Il codice di esempio per le librerie di classe C# di Funzioni di Azure è simile al codice di WebJobs SDK, con la differenza che in un progetto WebJobs SDK non è necessario un attributo `FunctionName`.
> * Alcuni tipi di associazione sono supportati solo in Funzioni, ad esempio HTTP, webhook e Griglia di eventi (basata su HTTP). 
> 
> Per altre informazioni, vedere [Confrontare WebJobs SDK e Funzioni di Azure](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>prerequisiti

Questo articolo presuppone che sia stato letto [Introduzione a WebJobs SDK](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

L'oggetto `JobHost` è il contenitore di runtime per le funzioni: ascolta i trigger e chiama le funzioni. L'utente crea `JobHost` nel codice e scrive il codice per personalizzarne il comportamento.

Questa è una differenza chiave tra l'uso diretto di WebJobs SDK e il suo uso indiretto tramite Funzioni di Azure. In Funzioni di Azure il servizio controlla `JobHost`, che non può essere personalizzato mediante la scrittura di codice. Funzioni di Azure consente di personalizzare il comportamento dell'host tramite le impostazioni del file *host.json*. Tali impostazioni sono stringhe, non codice, di conseguenza i tipi di personalizzazioni possibile sono limitati.

### <a name="jobhost-connection-strings"></a>Stringhe di connessione di JobHost

WebJobs SDK cerca le stringhe di connessione a bus di servizio e archiviazione in *local.settings.json* se eseguito in locale, altrimenti nell'ambiente di WebJob se eseguito in Azure. Se si desidera usare i propri nomi per queste stringhe di connessione oppure archiviarle da un'altra parte, è possibile impostarle nel codice, come illustrato di seguito:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>Impostazioni di sviluppo di JobHost

La classe `JobHostConfiguration` ha un metodo `UseDevelopmentSettings` che è possibile chiamare per rendere più efficiente lo sviluppo locale. Di seguito sono riportate alcune delle impostazioni che cambiano con questo metodo:

| Proprietà | Impostazione di sviluppo |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` per massimizzare l'output del log. |
| `Queues.MaxPollingInterval`  | Un valore basso per garantire che i metodi di coda vengano attivati immediatamente.  |
| `Singleton.ListenerLockPeriod` | 15 secondi per rendere più rapido lo sviluppo iterativo. |

L'esempio seguente mostra come usare le impostazioni di sviluppo. Per fare in modo che `config.IsDevelopment` restituisca `true` se eseguito in locale, impostare una variabile di ambiente locale denominata `AzureWebJobsEnv` con il valore `Development`.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>Impostazioni ServicePointManager di JobHost

.NET Framework contiene un'API denominata [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), che controlla il numero di connessioni simultanee a un host. È consigliabile aumentare questo valore rispetto al valore predefinito di 2 prima di avviare l'host WebJobs.

Tutte le richieste HTTP in uscita effettuate da una funzione attraverso `HttpClient` passano attraverso `ServicePointManager`. Dopo aver raggiunto il valore `DefaultConnectionLimit`, `ServicePointManager` inizia a inserire nella coda le richieste per poi inviarle. Si supponga che il valore `DefaultConnectionLimit` sia impostato su 2 e che il codice effettui 1000 richieste HTTP. All'inizio sono consentite solo 2 richieste al sistema operativo. Le altre 998 vengono inserite nella coda finché ci sarà spazio. Ciò significa che `HttpClient` potrebbe raggiungere il timeout perché *crede* di aver effettuato la richiesta, che però non è mai stata inviata al server di destinazione dal sistema operativo. Pertanto si potrebbe osservare un comportamento apparentemente senza senso: `HttpClient` locale richiede 10 secondi per completare una richiesta, ma il servizio restituisce ogni richiesta in 200 ms. 

Il valore predefinito per le applicazioni ASP.NET è `Int32.MaxValue` ed è probabile che funzioni bene per WebJobs in esecuzione in un piano di servizio app Basic o superiore. Generalmente, WebJobs richiede l'impostazione Always On, supportata solo dai piani di servizio app Basic e superiori. 

Se WebJob è in esecuzione in un piano di servizio app Gratuito o Condiviso, l'applicazione è limitata dalla sandbox del servizio app, che al momento ha un [limite di connessioni pari a 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Con un limite di connessioni non associate in `ServicePointManager`, c'è maggiore probabilità che venga raggiunto il limite di connessioni della sandbox e che il sito venga arrestato. In tal caso, l'impostazione di `DefaultConnectionLimit` su un valore inferiore, ad esempio 50 o 100, può impedire che si verifichi questa situazione e allo stesso tempo consentire una velocità effettiva sufficiente.

L'impostazione deve essere configurata prima che venga effettuata qualsiasi richiesta HTTP. Per questo motivo, l'host WebJobs non deve provare a regolare automaticamente l'impostazione; alcune richieste HTTP potrebbero verificarsi prima dell'avvio dell'host e questo può causare un comportamento imprevisto. L'approccio migliore consiste nell'impostare immediatamente il valore nel metodo `Main`prima di inizializzare `JobHost`, come mostrato nell'esempio seguente

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Trigger

Le funzioni devono essere metodi pubblici e devono avere un attributo di trigger o l'attributo [NoAutomaticTrigger](#manual-trigger).

### <a name="automatic-trigger"></a>Trigger automatico

I trigger automatici chiamano una funzione in risposta a un evento. Per trovare un esempio, vedere il trigger della coda nell'articolo [Introduzione a WebJobs SDK](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Trigger manuale

Per attivare manualmente una funzione, usare l'attributo `NoAutomaticTrigger`, come illustrato nell'esempio seguente:

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>Associazioni di input e output

Le associazioni di input offrono una modalità dichiarativa per rendere disponibili nel codice i dati dei servizi di Azure o di terze parti. Le associazioni di output garantiscono un modo per aggiornare i dati. L'articolo [Introduzione a WebJobs SDK](webjobs-sdk-get-started.md) illustra un esempio di ciascun tipo di associazione.

È possibile usare un valore restituito dal metodo per un'associazione di output applicando l'attributo a tale valore. Vedere l'esempio nell'articolo [Trigger e associazioni di Funzioni di Azure](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value).

## <a name="binding-types"></a>Tipi di associazioni

Il pacchetto `Microsoft.Azure.WebJobs` include i tipi di trigger e associazioni seguenti:

* Archiviazione BLOB
* Archiviazione code
* Archiviazione tabelle

Per usare altri tipi di trigger e associazioni, installare il pacchetto NuGet che li contiene e chiamare un metodo `Use<binding>` nell'oggetto `JobHostConfiguration`. Ad esempio, se si desidera usare un trigger Timer, installare `Microsoft.Azure.WebJobs.Extensions` e chiamare `UseTimers` nel metodo `Main`, come nell'esempio seguente:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

È possibile trovare il pacchetto di installazione per un particolare tipo di associazione nella sezione **Pacchetti** dell'[articolo di riferimento](#binding-reference-information) per Funzioni di Azure. Fanno eccezione il trigger e l'associazione File (per il file system locale), che non sono supportati da Funzioni di Azure. Per usare l'associazione File, installare `Microsoft.Azure.WebJobs.Extensions` e chiamare `UseFiles`.

### <a name="usecore"></a>UseCore

Il pacchetto `Microsoft.Azure.WebJobs.Extensions` citato in precedenza offre anche un tipo di associazione speciale che è possibile registrare chiamando il metodo `UseCore`. Questa associazione consente di definire un parametro [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) nella firma della funzione. L'oggetto di contesto consente di accedere all'ID di chiamata, che può essere usato per correlare tutti i log prodotti da una determinata chiamata di funzione. Ad esempio:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

## <a name="binding-configuration"></a>Configurazione dell'associazione

Molti tipi di trigger e associazione consentono di configurare il comportamento impostando le proprietà in un oggetto di configurazione trasmesso a `JobHost`.

### <a name="queue-trigger-configuration"></a>Configurazione di trigger di coda

Le impostazioni che è possibile configurare per il trigger di coda Archiviazione sono illustrate in [Riferimento host.json per Funzioni di Azure](../azure-functions/functions-host-json.md#queues). Nell'esempio seguente viene illustrato come impostarle in un progetto WebJobs SDK:

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configuration-for-other-bindings"></a>Configurazione per altre associazioni

Alcuni tipi di trigger e associazione definiscono il proprio tipo di configurazione personalizzata. Ad esempio, il trigger File consente di specificare il percorso radice per il monitoraggio:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Espressioni di associazione

Nei parametri dei costruttori di attributi, è possibile usare espressioni che vengono risolte in valori provenienti da varie origini. Ad esempio, nel codice seguente il percorso all'attributo `BlobTrigger` crea un'espressione denominata `filename`. Quando viene usato per l'associazione di output, `filename` risolve il nome del BLOB di attivazione.
 
```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Per altre informazioni sulle espressioni di associazione, vedere [Modelli ed espressioni di associazione](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) nella documentazione di Funzioni di Azure.

### <a name="custom-binding-expressions"></a>Espressioni di associazione personalizzate

A volte si desidera specificare un nome di coda, un nome di BLOB o un contenitore oppure un nome di tabella nel codice anziché impostarlo come hardcoded. Ad esempio, si desidera specificare il nome della coda per l'attributo `QueueTrigger` in una variabile di ambiente o in un file di configurazione.

Per farlo, trasmettere un oggetto `NameResolver` all'oggetto `JobHostConfiguration`. Includere segnaposto nei parametri del costruttore dell'attributo di trigger o associazione e il codice `NameResolver` offrirà i valori effettivi da usare in sostituzione di questi segnaposto. I segnaposto sono identificati dai segni di percentuale (%) che li precedono e li seguono, come illustrato nell'esempio seguente:
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Questo codice consente di usare una coda denominata logqueuetest nell'ambiente di test e una coda denominata logqueueprod nell'ambiente di produzione. Invece di impostare un nome di coda come hardcoded, specificare il nome di una voce nella raccolta `appSettings`. 

Se non viene specificato un nome personalizzato, viene usato il valore predefinito NameResolver. Il valore predefinito riceve i valori dalle impostazioni dell'app o dalle variabili di ambiente.

La classe `NameResolver` ottiene il nome della coda da `appSettings`, come illustrato nell'esempio seguente:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Passare la classe `NameResolver` all'oggetto `JobHost`, come illustrato nell'esempio seguente:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Funzioni di Azure implementa `INameResolver` per ottenere valori dalle impostazioni dell'app, come mostrato nell'esempio. Quando si usa direttamente WebJobs SDK, è possibile scrivere un'implementazione personalizzata che ottiene i valori da usare in sostituzione dei segnaposto dall'origine preferita. 

## <a name="binding-at-runtime"></a>Associazione in fase di esecuzione

Se è necessario eseguire alcune operazioni nella funzione prima di usare un attributo di associazione come `Queue`, `Blob` o `Table`, è possibile usare l'interfaccia `IBinder`.

Nell'esempio seguente viene accettato un messaggio di coda di input e creato un nuovo messaggio con lo stesso contenuto in una coda di output. Il nome della coda di output viene impostato dal codice nel corpo della funzione.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Per altre informazioni, vedere [Associazione in fase di esecuzione](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) nella documentazione di Funzioni di Azure.

## <a name="binding-reference-information"></a>Informazioni di riferimento sull'associazione

Le informazioni di riferimento su ogni tipo di associazione sono descritte nella documentazione di Funzioni di Azure. Prendendo come esempio la coda Archiviazione, in ogni articolo di riferimento sull'associazione sono disponibili le informazioni seguenti:

* [Pacchetti](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) - Il pacchetto da installare per includere il supporto per l'associazione in un progetto WebJobs SDK.
* [Esempi](../azure-functions/functions-bindings-storage-queue.md#trigger---example) - L'esempio di libreria di classe C# si applica a WebJobs SDK; basta omettere l'attributo `FunctionName`.
* [Attributi](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) - Gli attributi da usare per il tipo di associazione.
* [Configurazione](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) - Le spiegazioni delle proprietà di attributo e dei parametri del costruttore.
* [Utilizzo](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) - I tipi con cui è possibile effettuare un'associazione e le informazioni sul funzionamento dell'associazione. Ad esempio: algoritmo di polling, elaborazione di una coda non elaborabile.
  
Per un elenco di articoli di riferimento sull'associazione, vedere **Associazioni supportate** nell'articolo [Trigger e associazioni di Funzioni di Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings). In tale elenco, le associazioni HTTP, webhook e Griglia eventi sono supportate solo da Funzioni di Azure, non da WebJobs SDK.

## <a name="disable-attribute"></a>Attributo Disable 

L'attributo [Disable](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) consente di controllare se una funzione può essere attivata. 

Nell'esempio seguente se l'impostazione dell'app `Disable_TestJob` ha il valore "1" o "True" (senza distinzione maiuscole/minuscole), la funzione non verrà eseguita. In tal caso, il runtime crea un messaggio di log *Function 'Functions.TestJob' is disabled*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando si modificano i valori delle impostazioni dell'app nel portale di Azure, WebJob viene riavviato e viene scelta la nuova impostazione.

L'attributo può essere dichiarato a livello di parametro, metodo o classe. Il nome dell'impostazione può inoltre contenere espressioni di associazione.

## <a name="timeout-attribute"></a>Attributo Timeout

L'attributo [Timeout](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) fa sì che una funzione venga annullata se non viene completata entro un periodo di tempo specificato. Nell'esempio seguente la funzione verrà eseguita per un giorno senza il timeout. Con il timeout, la funzione viene annullata dopo 15 secondi.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

È possibile applicare l'attributo Timeout a livello di classe o metodo ed è possibile specificare un timeout globale usando `JobHostConfiguration.FunctionTimeout`. I timeout a livello di classe o metodo eseguono l'override del timeout globale.

## <a name="singleton-attribute"></a>Attributo Singleton

Usare l'attributo [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) per garantire che venga eseguita solo un'istanza di una funzione anche se sono presenti più istanze dell'app Web host. Questo è possibile mediante l'implementazione del [blocco distribuito](#viewing-lease-blobs).

Nell'esempio seguente viene eseguita solo una singola istanza della funzione `ProcessImage` in un determinato momento:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Alcuni trigger dispongono del supporto integrato per la gestione della concorrenza:

* **QueueTrigger** - Impostare `JobHostConfiguration.Queues.BatchSize` su 1.
* **ServiceBusTrigger** - Impostare `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` su 1.
* **FileTrigger** - Impostare `FileProcessor.MaxDegreeOfParallelism` su 1.

È possibile usare queste impostazioni per assicurarsi che la funzione viene eseguita come singleton in una singola istanza. Per garantire che una sola istanza della funzione sia in esecuzione quando l'app Web aumenta il numero di istanze, applicare un blocco Singleton a livello di listener nella funzione (`[Singleton(Mode = SingletonMode.Listener)]`). All'avvio di JobHost vengono acquisiti blocchi di listener. Se tutte e tre le istanze aumentate vengono avviate nello stesso momento, una sola istanza acquisisce il blocco e viene avviato un solo listener.

### <a name="scope-values"></a>Valori di ambito

È possibile specificare **un'espressione o un valore di ambito** nel singleton che garantisca che tutte le esecuzioni della funzione in quell'ambito vengano serializzate. Un'implementazione più granulare del blocco con questo metodo può consentire un certo livello di parallelismo per la funzione, consentendo allo stesso tempo la serializzazione altre chiamate in base alle esigenze. Nell'esempio seguente, ad esempio, l'espressione di ambito è associata al valore `Region` del messaggio in arrivo. Se la coda contiene 3 messaggi rispettivamente nelle Region "East", "East" e "West", i messaggi nella Region "East" vengono eseguiti in modo sequenziale, mentre il messaggio nella Region "West" viene eseguito in parallelo a questi.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

L'ambito predefinito per un blocco è `SingletonScope.Function`, il che significa che l'ambito del blocco (il percorso di lease del BLOB) è associato al nome completo della funzione. Per applicare un blocco alle funzioni, specificare `SingletonScope.Host` e usare un nome di ID di ambito identico in tutte le funzioni che non si desidera eseguire simultaneamente. Nell'esempio seguente viene eseguita una sola istanza di `AddItem` o `RemoveItem` alla volta:

```charp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>Visualizzazione dei BLOB di lease

WebJobs SDK usa i [lease del BLOB di Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) sullo sfondo per implementare il blocco distribuito. I BLOB di lease usati da Singleton sono disponibili nel contenitore `azure-webjobs-host` dell'account di archiviazione `AzureWebJobsStorage` nei blocchi del percorso. Ad esempio, il percorso del BLOB di lease per il primo esempio `ProcessImage` mostrato sopra potrebbe essere `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Tutti i percorsi includono l'ID JobHost, in questo caso 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Funzioni asincrone

Per informazioni su come codificare le funzioni asincrone, vedere la documentazione su [Async](../azure-functions/functions-dotnet-class-library.md#async) di Funzioni Azure.

## <a name="cancellation-tokens"></a>Token di annullamento

Per informazioni su come gestire i token di annullamento, vedere la documentazione su [token di annullamento e arresto normale](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens) di Funzioni di Azure.

## <a name="multiple-instances"></a>Più istanze

Se l'app Web viene eseguita in più istanze, WebJob viene eseguito continuativamente in ogni istanza, in ascolto dei trigger e chiamando le funzioni. Le diverse associazioni di trigger sono progettate per condividere in modo efficiente la collaborazione tra le istanze, così che l'aumento delle istanze consenta di gestire un carico maggiore.

I trigger della coda e del BLOB impediscono automaticamente che una funzione elabori più volte un messaggio o un BLOB della coda. Le funzioni non devono essere idempotenti.

Il trigger del timer garantisce automaticamente che venga eseguita una sola istanza del timer, in modo che non si ottenga più di un'istanza della funzione in esecuzione in un determinato momento pianificato.

Se si desidera garantire che solo un'istanza di una funzione venga eseguita anche se sono presenti più istanze dell'app Web host, è possibile usare l'attributo [Singleton](#singleton-attribute).
    
## <a name="filters"></a>Filtri 

I filtri funzione (anteprima) offrono un modo per personalizzare la pipeline di esecuzione di WebJobs con la propria logica. I filtri sono simili ai [filtri di ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Possono essere implementati come attributi dichiarativi che vengono applicati alle funzioni o alle classi. Per altre informazioni, vedere [Function Filters](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

È consigliabile usare il framework di registrazione sviluppato per ASP.NET. L'articolo [Introduzione a WebJobs SDK](webjobs-sdk-get-started.md) illustra come usarlo. 

### <a name="log-filtering"></a>Filtro del log

Ogni log creato da un'istanza `ILogger` ha `Category` e `Level` associati. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) è un'enumerazione e il codice integer ne indica la relativa importanza:

|LogLevel    |Codice|
|------------|---|
|Trace       | 0 |
|Debug       | 1 |
|Informazioni | 2 |
|Avviso     | 3 |
|Tipi di errore       | 4 |
|Critico    | 5 |
|Nessuna        | 6 |

Ogni categoria può essere filtrata in modo indipendente a un determinato [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel). Ad esempio, si potrebbe voler vedere tutti i log per l'elaborazione del trigger del log ma solo `Error` e superiori per tutto il resto.

Per rendere più semplice specificare le regole di filtro, WebJobs SDK offre `LogCategoryFilter` che può essere trasmesso a molti dei provider di log esistenti, inclusi Application Insights e Console.

`LogCategoryFilter` ha una proprietà `Default` con un valore iniziale `Information`. Ciò significa che tutti i messaggi con i livelli `Information`, `Warning`, `Error` o `Critical` vengono registrati, ma tutti i messaggi con i livelli `Debug` o `Trace` vengono esclusi.

La proprietà `CategoryLevels` consente di specificare i livelli di log per determinate categorie in modo che sia possibile ottimizzare l'output della registrazione. Se non vengono trovate corrispondenze nel dizionario `CategoryLevels`, il filtro resta al valore `Default` quando stabilisce se filtrare il messaggio.

L'esempio seguente crea un filtro che per impostazione predefinita filtra tutti i log al livello `Warning`. Le categorie `Function` o `Host.Results` vengono filtrate al livello `Error`. `LogCategoryFilter` confronta la categoria corrente con tutti i valori `CategoryLevels` registrati e sceglie la corrispondenza più lunga. Ciò significa che il livello `Debug` registrato per `Host.Triggers` corrisponderà a `Host.Triggers.Queue` o `Host.Triggers.Blob`. In questo modo è possibile controllare categorie più ampie senza bisogno di aggiungerle singolarmente.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Telemetria personalizzata per Application Insights

Internamente, il valore `TelemetryClient` creato dal provider di Application Insights per WebJobs SDK usa [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Quando l'endpoint di Application Insights non è disponibile o limita le richieste in ingresso, il canale [salva le richieste nel file system dell'app Web e le invia di nuovo in un secondo momento](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

`TelemetryClient` è creato da una classe che implementa `ITelemetryClientFactory`. Per impostazione predefinita, è [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Se si desidera modificare una parte qualsiasi della pipeline di Application Insights, è possibile specificare il proprio `ITelemetryClientFactory` e l'host utilizzerà la classe per costruire `TelemetryClient`. Ad esempio, questo codice esegue l'override di `DefaultTelemetryClientFactory` per modificare una proprietà di `ServerTelemetryChannel`:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

L'oggetto SamplingPercentageEstimatorSettings configura il [campionamento adattivo](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Ciò significa che in determinati scenari con volumi elevati, App Insights invia un subset selezionato di dati di telemetria al server.

Dopo aver creato la factory di telemetria, trasmetterla al provider di log di Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Passaggi successivi

In questa guida sono stati descritti esempi di frammenti di codice che mostrano come gestire scenari comuni per l'uso di WebJobs SDK. Per trovare esempi completi, vedere [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).