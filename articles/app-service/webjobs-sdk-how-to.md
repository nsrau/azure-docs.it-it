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
ms.date: 01/19/2019
ms.author: glenga
ms.openlocfilehash: ab502c25a632977065e55d2eeafd684203636b14
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109912"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Come usare Azure WebJobs SDK per l'elaborazione in background guidata dagli eventi

Questo articolo offre materiale sussidiario su come scrivere codice per [Azure WebJobs SDK](webjobs-sdk-get-started.md). La documentazione si applica alle versioni 3.x e 2.x di WebJobs SDK. In caso di differenze a livello di API, vengono forniti esempi di entrambe le versioni. La modifica principale introdotta dalla versione 3.x è l'uso di .NET Core anziché di .NET Framework.

>[!NOTE]
> [Funzioni di Azure](../azure-functions/functions-overview.md) si basa su WebJobs SDK e per alcuni argomenti questo articolo include collegamenti alla documentazione su Funzioni di Azure. Si notino le differenze seguenti tra Funzioni e WebJobs SDK:
> * La versione 2.x di Funzioni di Azure corrisponde alla versione 3.x di WebJobs SDK, mentre la versione 1.x di Funzioni di Azure corrisponde alla versione 2.x di WebJobs SDK. I repository di codice sorgente seguono la numerazione di WebJobs SDK.
> * Il codice di esempio per le librerie di classe C# di Funzioni di Azure è simile al codice di WebJobs SDK, con la differenza che in un progetto WebJobs SDK non è necessario un attributo `FunctionName`.
> * Alcuni tipi di associazione sono supportati solo in Funzioni, ad esempio HTTP, webhook e Griglia di eventi (basata su HTTP).
> 
> Per altre informazioni, vedere [Confrontare WebJobs SDK e Funzioni di Azure](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia stato letto [Introduzione a WebJobs SDK](webjobs-sdk-get-started.md) e che siano state completate le attività in esso riportate.

## <a name="webjobs-host"></a>Host WebJobs

L'host è un contenitore di runtime per le funzioni.  Ascolta i trigger e chiama le funzioni. Nella versione 3.x l'host è un'implementazione di `IHost`, mentre nella versione 2.x si usa l'oggetto `JobHost`. L'utente crea un'istanza host nel codice e scrive il codice per personalizzarne il comportamento.

Questa è una differenza chiave tra l'uso diretto di WebJobs SDK e il suo uso indiretto tramite Funzioni di Azure. In Funzioni di Azure il servizio controlla l'host, che non può essere personalizzato mediante la scrittura di codice. Funzioni di Azure consente di personalizzare il comportamento dell'host tramite le impostazioni del file *host.json*. Tali impostazioni sono stringhe, non codice, di conseguenza i tipi di personalizzazioni possibile sono limitati.

### <a name="host-connection-strings"></a>Stringhe di connessione dell'host 

WebJobs SDK cerca le stringhe di connessione ad Archiviazione di Azure e al bus di servizio di Azure nel file *local.settings.json* se eseguito in locale, altrimenti nell'ambiente di WebJob se eseguito in Azure. Per impostazione predefinita, è richiesta un'impostazione della stringa di connessione di archiviazione denominata `AzureWebJobsStorage`.  

La versione 2.x dell'SDK consente di usare i propri nomi per queste stringhe di connessione oppure di archiviarle da un'altra parte. È possibile impostarle nel codice, come illustrato di seguito:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Dal momento che vengono usate le API di configurazione di .NET Core predefinite, nella versione 3.x non è disponibile alcuna API per la modifica dei nomi delle stringhe di connessione.

### <a name="host-development-settings"></a>Impostazioni di sviluppo dell'host

È possibile eseguire l'host in modalità di sviluppo per rendere più efficiente lo sviluppo locale. Di seguito sono riportate alcune delle impostazioni che cambiano durante l'esecuzione in modalità di sviluppo:

| Proprietà | Impostazione di sviluppo |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` per massimizzare l'output del log. |
| `Queues.MaxPollingInterval`  | Un valore basso per garantire che i metodi di coda vengano attivati immediatamente.  |
| `Singleton.ListenerLockPeriod` | 15 secondi per rendere più rapido lo sviluppo iterativo. |

Il modo in cui si abilita la modalità di sviluppo dipende dalla versione dell'SDK. 

#### <a name="version-3x"></a>Versione 3.x

La versione 3.x usa le API ASP.NET Core standard. Chiamare il metodo [UseEnvironment](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) sull'istanza [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Passare una stringa denominata `development`, come nell'esempio seguente:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versione 2.x

La classe `JobHostConfiguration` ha un metodo `UseDevelopmentSettings` che abilita la modalità di sviluppo.  L'esempio seguente mostra come usare le impostazioni di sviluppo. Per fare in modo che `config.IsDevelopment` restituisca `true` se eseguito in locale, impostare una variabile di ambiente locale denominata `AzureWebJobsEnv` con il valore `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Gestione di connessioni simultanee (v2.x)

Per impostazione predefinita, nella versione 3.x il limite di connessioni è infinito. Se per qualche motivo è necessario modificare questo limite, è possibile usare la proprietà [MaxConnectionsPerServer](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) della classe [WinHttpHander](/dotnet/api/system.net.http.winhttphandler).

Per la versione 2.x, è possibile controllare il numero di connessioni simultanee a un host tramite l'API [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit). Nella versione 2.x è consigliabile aumentare questo valore rispetto al valore predefinito di 2 prima di avviare l'host WebJobs.

Tutte le richieste HTTP in uscita effettuate da una funzione attraverso `HttpClient` passano attraverso `ServicePointManager`. Dopo aver raggiunto il valore `DefaultConnectionLimit`, `ServicePointManager` inizia a inserire nella coda le richieste per poi inviarle. Si supponga che il valore `DefaultConnectionLimit` sia impostato su 2 e che il codice effettui 1000 richieste HTTP. All'inizio sono consentite solo due richieste al sistema operativo. Le altre 998 vengono inserite nella coda finché ci sarà spazio. Ciò significa che `HttpClient` potrebbe raggiungere il timeout perché *crede* di aver effettuato la richiesta, che però non è mai stata inviata al server di destinazione dal sistema operativo. Pertanto si potrebbe osservare un comportamento apparentemente senza senso: `HttpClient` locale richiede 10 secondi per completare una richiesta, ma il servizio restituisce ogni richiesta in 200 ms. 

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
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Il modo in cui si attiva manualmente la funzione dipende dalla versione dell'SDK.

#### <a name="version-3x"></a>Versione 3.x

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Versione 2.x

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Associazioni di input e output

Le associazioni di input offrono una modalità dichiarativa per rendere disponibili nel codice i dati dei servizi di Azure o di terze parti. Le associazioni di output garantiscono un modo per aggiornare i dati. L'articolo [Introduzione a WebJobs SDK](webjobs-sdk-get-started.md) illustra un esempio di ciascun tipo di associazione.

È possibile usare un valore restituito dal metodo per un'associazione di output applicando l'attributo a tale valore. Vedere l'esempio nell'articolo [Trigger e associazioni di Funzioni di Azure](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value).

## <a name="binding-types"></a>Tipi di associazioni

Il modo in cui i tipi di associazioni vengono installati e gestiti varia tra le versioni 3.x e 2.x dell'SDK. È possibile trovare il pacchetto di installazione per un particolare tipo di associazione nella sezione **Pacchetti** dell'[articolo di riferimento](#binding-reference-information) per Funzioni di Azure. Fanno eccezione il trigger e l'associazione File (per il file system locale), che non sono supportati da Funzioni di Azure.

#### <a name="version-3x"></a>Versione 3.x

Nella versione 3.x le associazioni di archiviazione sono incluse nel pacchetto `Microsoft.Azure.WebJobs.Extensions.Storage`. Chiamare il metodo di estensione `AddAzureStorage` nel metodo `ConfigureWebJobs`, come illustrato nell'esempio seguente:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Per usare altri tipi di trigger e associazioni, installare il pacchetto NuGet che li contiene e chiamare il metodo di estensione `Add<binding>` implementato nell'estensione. Ad esempio, se si vuole usare un'associazione di Azure Cosmos DB, installare `Microsoft.Azure.WebJobs.Extensions.CosmosDB` e chiamare `AddCosmosDB`, come nell'esempio seguente:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Per usare il trigger Timer o l'associazione File, che fanno parte dei servizi di base, chiamare rispettivamente i metodi di estensione `AddTimers` o `AddFiles`.

#### <a name="version-2x"></a>Versione 2.x

La versione 2.x del pacchetto `Microsoft.Azure.WebJobs` include i tipi di trigger e associazioni seguenti:

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

Per usare l'associazione File, installare `Microsoft.Azure.WebJobs.Extensions` e chiamare `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

WebJobs consente di eseguire l'associazione a un [`ExecutionContext`]. Con questa associazione, è possibile accedere a [`ExecutionContext`] come parametro nella firma della funzione. Ad esempio, il codice seguente usa l'oggetto di contesto per accedere all'ID di chiamata, che può essere usato per correlare tutti i log prodotti da una determinata chiamata di funzione.  

```cs
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

Il modo in cui si esegue l'associazione al [`ExecutionContext`] dipende dalla versione dell'SDK.

#### <a name="version-3x"></a>Versione 3.x

Chiamare il metodo di estensione `AddExecutionContextBinding` nel metodo `ConfigureWebJobs`, come illustrato nell'esempio seguente:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versione 2.x

Il pacchetto `Microsoft.Azure.WebJobs.Extensions` citato in precedenza offre anche un tipo di associazione speciale che è possibile registrare chiamando il metodo `UseCore`. Questa associazione consente di definire un parametro [`ExecutionContext`] nella firma della funzione, che viene abilitato come segue:

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
```

## <a name="binding-configuration"></a>Configurazione dell'associazione

Per alcuni trigger e associazioni è possibile configurare il comportamento. La modalità di configurazione dipende dalla versione dell'SDK.

* **Versione 3.x:** la configurazione viene impostata quando viene chiamato il metodo `Add<Binding>` in `ConfigureWebJobs`.
* **Versione 2.x:** tramite l'impostazione delle proprietà in un oggetto di configurazione trasmesso a `JobHost`.

### <a name="queue-trigger-configuration"></a>Configurazione di trigger di coda

Le impostazioni che è possibile configurare per il trigger di coda Archiviazione sono illustrate in [Riferimento host.json per Funzioni di Azure](../azure-functions/functions-host-json.md#queues). Gli esempi seguenti illustrano come impostarle nella configurazione:

#### <a name="version-3x"></a>Versione 3.x

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versione 2.x

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

Alcuni tipi di trigger e associazione definiscono il proprio tipo di configurazione personalizzata. Ad esempio, il trigger File consente di specificare il percorso radice per il monitoraggio, come negli esempi seguenti:

#### <a name="version-3x"></a>Versione 3.x

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versione 2.x

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

Questo codice consente di usare una coda denominata `logqueuetest` nell'ambiente di test e una coda denominata `logqueueprod` nell'ambiente di produzione. Invece di impostare un nome di coda come hardcoded, specificare il nome di una voce nella raccolta `appSettings`.

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

#### <a name="version-3x"></a>Versione 3.x

Il resolver viene configurato tramite l'inserimento delle dipendenze. Questi esempi richiedono l'istruzione `using` seguente:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Il resolver viene aggiunto chiamando il metodo di estensione [`ConfigureServices`] su [HostBuilder](/dotnet/api/microsoft.extensions.hosting.hostbuilder), come nell'esempio seguente:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versione 2.x

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
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
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

È possibile specificare **un'espressione o un valore di ambito** nel singleton che garantisca che tutte le esecuzioni della funzione in quell'ambito vengano serializzate. Un'implementazione più granulare del blocco con questo metodo può consentire un certo livello di parallelismo per la funzione, consentendo allo stesso tempo la serializzazione altre chiamate in base alle esigenze. Nell'esempio seguente, ad esempio, l'espressione di ambito è associata al valore `Region` del messaggio in arrivo. Quando la coda contiene tre messaggi rispettivamente nelle regioni "East", "East" e "West", i messaggi nella regione "East" vengono eseguiti in modo sequenziale, mentre il messaggio nella regione "West" viene eseguito in parallelo a quelli in "East".

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

```csharp
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

#### <a name="version-3x"></a>Versione 3.x

La versione 3.x dell'SDK si basa sui filtri incorporati in .NET Core. La classe `LogCategories` consente di definire categorie per funzioni, trigger o utenti specifici. Definisce anche i filtri per gli specifici stati dell'host, ad esempio `Startup` e `Results`. In questo modo, è possibile ottimizzare l'output della registrazione. Se non vengono trovate corrispondenze nelle categorie definite, il filtro resta al valore `Default` quando stabilisce se filtrare il messaggio.

`LogCategories` richiede l'istruzione using seguente:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

L'esempio seguente crea un filtro che per impostazione predefinita filtra tutti i log al livello `Warning`. Le categorie `Function` o `results` (equivalenti a `Host.Results` nella versione 2.x) vengono filtrate al livello `Error`. Il filtro confronta la categoria corrente con tutti i livelli registrati nell'istanza `LogCategories` e sceglie la corrispondenza più lunga. Ciò significa che il livello `Debug` registrato per `Host.Triggers` corrisponderà a `Host.Triggers.Queue` o `Host.Triggers.Blob`. In questo modo è possibile controllare categorie più ampie senza bisogno di aggiungerle singolarmente.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versione 2.x

Nella versione 2.x dell'SDK la classe `LogCategoryFilter` viene usata per controllare il filtro. `LogCategoryFilter` ha una proprietà `Default` con un valore iniziale `Information`. Ciò significa che tutti i messaggi con i livelli `Information`, `Warning`, `Error` o `Critical` vengono registrati, ma tutti i messaggi con i livelli `Debug` o `Trace` vengono esclusi.

Come con `LogCategories` nella versione 23.x, la proprietà `CategoryLevels` consente di specificare i livelli di log per determinate categorie in modo che sia possibile ottimizzare l'output della registrazione. Se non vengono trovate corrispondenze nel dizionario `CategoryLevels`, il filtro resta al valore `Default` quando stabilisce se filtrare il messaggio.

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

La modalità di implementazione della telemetria personalizzata per [Application Insights](../azure-monitor/app/app-insights-overview.md) dipende dalla versione dell'SDK in uso. Per informazioni su come configurare Application Insights, vedere [Aggiungere la registrazione in Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versione 3.x

Dal momento che la versione 3.x di WebJobs SDK si basa sull'host generico di .NET Core, non viene più fornita una factory di telemetria personalizzata. Tuttavia, è possibile aggiungere dati di telemetria personalizzati alla pipeline tramite l'inserimento delle dipendenze. Gli esempi in questa sezione richiedono le istruzioni `using` seguenti:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

L'implementazione personalizzata seguente di [`ITelemetryInitializer`] consente di aggiungere la propria [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) al valore predefinito [`TelemetryConfiguration`].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Chiamare [`ConfigureServices`] nel generatore per aggiungere l'[`ITelemetryInitializer`] personalizzato alla pipeline.

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add Logging Providers
        b.AddConsole();

        // If this key exists in any config, use it to enable App Insights
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Quando il valore [`TelemetryConfiguration`] viene costruito, tutti i tipi di registrati di [`ITelemetryInitializer`] sono inclusi. Per altre informazioni, vedere l'[API di Application Insights per metriche ed eventi personalizzati](../azure-monitor/app/api-custom-events-metrics.md).

Nella versione 3.x non è più necessario scaricare [`TelemetryClient`] quando l'host viene arrestato. Il sistema di inserimento delle dipendenze di .NET Core elimina automaticamente l'`ApplicationInsightsLoggerProvider` registrato, che consente di scaricare [`TelemetryClient`].

#### <a name="version-2x"></a>Versione 2.x

Nella versione 2.x il valore [`TelemetryClient`] creato internamente dal provider di Application Insights per WebJobs SDK usa [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Quando l'endpoint di Application Insights non è disponibile o limita le richieste in ingresso, il canale [salva le richieste nel file system dell'app Web e le invia di nuovo in un secondo momento](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[`TelemetryClient`] è creato da una classe che implementa `ITelemetryClientFactory`. Per impostazione predefinita, è [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Se si vuole modificare una parte qualsiasi della pipeline di Application Insights, è possibile specificare il proprio `ITelemetryClientFactory` e l'host userà la classe per costruire un [`TelemetryClient`]. Ad esempio, questo codice esegue l'override di `DefaultTelemetryClientFactory` per modificare una proprietà di `ServerTelemetryChannel`:

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

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
