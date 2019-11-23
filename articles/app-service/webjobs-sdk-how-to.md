---
title: Come usare WebJobs SDK - Azure
description: Altre informazioni su come scrivere codice per WebJobs SDK. Creare processi di elaborazione in background basati sugli eventi che accedono ai dati nei servizi di Azure e nei servizi di terze parti.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 67cd7f82597d306c8bf3c463d11457199aec7277
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815734"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Come usare Azure WebJobs SDK per l'elaborazione in background guidata dagli eventi

Questo articolo fornisce indicazioni su come usare Azure webjobs SDK. Per iniziare subito a usare i processi Web, vedere [Introduzione ad Azure Webjobs SDK per l'elaborazione in background guidata dagli eventi](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versioni di WebJobs SDK

Queste sono le differenze principali tra la versione 3. *x* e versione 2. *x* di webjobs SDK:

* Versione 3. *x* aggiunge il supporto per .NET Core.
* Nella versione 3. *x*, è necessario installare in modo esplicito l'estensione di binding di archiviazione richiesta da webjobs SDK. Nella versione 2. *x*, le associazioni di archiviazione sono state incluse nell'SDK.
* Strumenti di Visual Studio per .NET Core (3. *x*) i progetti sono diversi dagli strumenti per .NET Framework (2. *x*) progetti. Per altre informazioni, vedere [sviluppare e distribuire processi Web usando Visual Studio-servizio app Azure](webjobs-dotnet-deploy-vs.md).

Quando possibile, vengono forniti alcuni esempi per entrambe le versioni 3. *x* e versione 2. *x*.

> [!NOTE]
> [Funzioni di Azure](../azure-functions/functions-overview.md) si basa su webjobs SDK e questo articolo contiene i collegamenti alla documentazione di funzioni di Azure per alcuni argomenti. Si notino le differenze tra le funzioni e webjobs SDK:
> * Funzioni di Azure versione 2. *x* corrisponde a webjobs SDK versione 3. *x*e funzioni di Azure 1. *x* corrisponde a webjobs SDK 2. *x*. I repository del codice sorgente usano la numerazione di webjobs SDK.
> * Il codice di esempio per C# le librerie di classi di funzioni di Azure è come il codice di webjobs SDK, ad eccezione del fatto che non è necessario un `FunctionName` attributo in un progetto webjobs SDK.
> * Alcuni tipi di binding sono supportati solo nelle funzioni, ad esempio HTTP (webhook) e griglia di eventi (basata su HTTP).
>
> Per altre informazioni, vedere [Confrontare WebJobs SDK e Funzioni di Azure](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host WebJobs

L'host è un contenitore di runtime per le funzioni.  Ascolta i trigger e chiama le funzioni. Nella versione 3. *x*, l'host è un'implementazione di `IHost`. Nella versione 2. *x*, viene usato l'oggetto `JobHost`. L'utente crea un'istanza host nel codice e scrive il codice per personalizzarne il comportamento.

Si tratta di una differenza fondamentale tra l'uso di webjobs SDK direttamente e l'uso indiretto tramite funzioni di Azure. In funzioni di Azure il servizio controlla l'host e non è possibile personalizzare l'host scrivendo codice. Funzioni di Azure consente di personalizzare il comportamento dell'host tramite le impostazioni nel file host. JSON. Queste impostazioni sono stringhe, non codice e questo limita i tipi di personalizzazioni che è possibile eseguire.

### <a name="host-connection-strings"></a>Stringhe di connessione dell'host

Webjobs SDK Cerca le stringhe di connessione di archiviazione di Azure e del bus di servizio di Azure nel file local. Settings. JSON quando viene eseguito localmente o nell'ambiente del processo Web quando si esegue in Azure. Per impostazione predefinita, è necessaria un'impostazione della stringa di connessione di archiviazione denominata `AzureWebJobsStorage`.  

Versione 2. *x* dell'SDK consente di usare i propri nomi per queste stringhe di connessione o di archiviarle altrove. È possibile impostare i nomi nel codice usando il [`JobHostConfiguration`], come illustrato di seguito:

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

A causa della versione 3. *x* usa le API di configurazione predefinite di .NET Core, non è disponibile alcuna API per modificare i nomi delle stringhe di connessione.

### <a name="host-development-settings"></a>Impostazioni di sviluppo dell'host

È possibile eseguire l'host in modalità di sviluppo per rendere più efficiente lo sviluppo locale. Di seguito sono riportate alcune delle impostazioni modificate quando si esegue in modalità di sviluppo:

| Proprietà | Impostazione di sviluppo |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` per massimizzare l'output del log. |
| `Queues.MaxPollingInterval`  | Un valore basso per garantire che i metodi di coda vengano attivati immediatamente.  |
| `Singleton.ListenerLockPeriod` | 15 secondi per rendere più rapido lo sviluppo iterativo. |

Il processo di abilitazione della modalità di sviluppo dipende dalla versione dell'SDK. 

#### <a name="version-3x"></a>Versione 3. *x*

Versione 3. *x* usa le API ASP.net core standard. Chiamare il metodo [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) sull'istanza di [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . Passare una stringa denominata `development`, come nell'esempio seguente:

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

#### <a name="version-2x"></a>Versione 2. *x*

La classe `JobHostConfiguration` ha un metodo `UseDevelopmentSettings` che abilita la modalità di sviluppo.  L'esempio seguente mostra come usare le impostazioni di sviluppo. Per fare in modo che `config.IsDevelopment` restituisca `true` quando viene eseguita localmente, impostare una variabile di ambiente locale denominata `AzureWebJobsEnv` con il valore `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Gestione delle connessioni simultanee (versione 2. *x*)

Nella versione 3. *x*, il valore predefinito per il limite di connessione è connessioni infinite. Se per qualche motivo è necessario modificare questo limite, è possibile usare la proprietà [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) della classe [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) .

Nella versione 2. *x*, è possibile controllare il numero di connessioni simultanee a un host usando l'API [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . In 2. *x*, è necessario aumentare questo valore dall'impostazione predefinita 2 prima di avviare l'host processi Web.

Tutte le richieste HTTP in uscita effettuate da una funzione tramite `HttpClient` passano attraverso `ServicePointManager`. Quando si raggiunge il valore impostato in `DefaultConnectionLimit`, `ServicePointManager` avvia le richieste di Accodamento prima di inviarle. Si supponga che il valore `DefaultConnectionLimit` sia impostato su 2 e che il codice effettui 1000 richieste HTTP. All'inizio sono consentite solo due richieste al sistema operativo. Le altre 998 vengono inserite nella coda finché ci sarà spazio. Ciò significa che è possibile che si sia verificato il timeout della `HttpClient` perché sembra aver effettuato la richiesta, ma la richiesta non è mai stata inviata dal sistema operativo al server di destinazione. Pertanto si potrebbe osservare un comportamento apparentemente senza senso: `HttpClient` locale richiede 10 secondi per completare una richiesta, ma il servizio restituisce ogni richiesta in 200 ms. 

Il valore predefinito per le applicazioni ASP.NET è `Int32.MaxValue`ed è probabile che funzioni correttamente per i processi Web in esecuzione in un piano di servizio App Basic o superiore. I processi Web richiedono in genere l'impostazione Always On e sono supportati solo da piani di servizio app di base e superiori.

Se WebJob è in esecuzione in un piano di servizio app Gratuito o Condiviso, l'applicazione è limitata dalla sandbox del servizio app, che al momento ha un [limite di connessioni pari a 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Con un limite di connessione non associato in `ServicePointManager`, è più probabile che venga raggiunta la soglia di connessione sandbox e il sito verrà arrestato. In tal caso, l'impostazione di `DefaultConnectionLimit` su un valore inferiore, ad esempio 50 o 100, può impedire che si verifichi questa situazione e allo stesso tempo consentire una velocità effettiva sufficiente.

L'impostazione deve essere configurata prima che venga effettuata qualsiasi richiesta HTTP. Per questo motivo, l'host processi Web non deve modificare automaticamente l'impostazione. Potrebbero essere presenti richieste HTTP che si verificano prima dell'avvio dell'host, il che potrebbe causare un comportamento imprevisto. L'approccio migliore consiste nell'impostare immediatamente il valore nel metodo `Main` prima di inizializzare `JobHost`, come illustrato di seguito:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Trigger

Le funzioni devono essere metodi pubblici e devono avere un solo attributo trigger o l'attributo [`NoAutomaticTrigger`](#manual-triggers) .

### <a name="automatic-triggers"></a>Trigger automatici

I trigger automatici chiamano una funzione in risposta a un evento. Si consideri questo esempio di una funzione attivata da un messaggio aggiunto all'archivio code di Azure. Risponde leggendo un BLOB dall'archivio BLOB di Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

L'attributo `QueueTrigger` indica al runtime di chiamare la funzione ogni volta che un messaggio della coda viene visualizzato nella coda di `myqueue-items`. L'attributo `Blob` indica al runtime di usare il messaggio della coda per leggere un BLOB nel contenitore *Sample-WorkItems* . Il contenuto del messaggio della coda, passato alla funzione nel parametro `myQueueItem`, è il nome del BLOB.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Trigger manuali

Per attivare una funzione manualmente, usare l'attributo `NoAutomaticTrigger`, come illustrato di seguito:

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

Il processo di attivazione manuale della funzione dipende dalla versione dell'SDK.

#### <a name="version-3x"></a>Versione 3. *x*

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

#### <a name="version-2x"></a>Versione 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Associazioni di input e output

Le associazioni di input offrono una modalità dichiarativa per rendere disponibili nel codice i dati dei servizi di Azure o di terze parti. Le associazioni di output garantiscono un modo per aggiornare i dati. L' [articolo introduttivo](webjobs-sdk-get-started.md) illustra un esempio di ciascuna di esse.

È possibile usare un valore restituito del metodo per un'associazione di output applicando l'attributo al valore restituito del metodo. Vedere l'esempio in [uso del valore restituito della funzione di Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipi di associazioni

Il processo per l'installazione e la gestione dei tipi di binding varia a seconda che si usi la versione 3. *x* o versione 2. *x* dell'SDK. È possibile trovare il pacchetto da installare per un particolare tipo di associazione nella sezione "pacchetti" dell' [articolo di riferimento](#binding-reference-information)di funzioni di Azure di questo tipo di binding. Un'eccezione è rappresentata dal trigger e dall'associazione dei file (per la file system locale), che non è supportata da funzioni di Azure.

#### <a name="version-3x"></a>Versione 3. *x*

Nella versione 3. *x*, le associazioni di archiviazione sono incluse nel pacchetto di `Microsoft.Azure.WebJobs.Extensions.Storage`. Chiamare il metodo di estensione `AddAzureStorage` nel metodo `ConfigureWebJobs`, come illustrato di seguito:

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

Per usare altri tipi di trigger e associazioni, installare il pacchetto NuGet che li contiene e chiamare il metodo di estensione `Add<binding>` implementato nell'estensione. Se, ad esempio, si desidera utilizzare un'associazione Azure Cosmos DB, installare `Microsoft.Azure.WebJobs.Extensions.CosmosDB` e chiamare `AddCosmosDB`, come indicato di seguito:

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

#### <a name="version-2x"></a>Versione 2. *x*

Questi tipi di trigger e binding sono inclusi nella versione 2. *x* del pacchetto di `Microsoft.Azure.WebJobs`:

* Archiviazione BLOB
* Archiviazione code
* Archiviazione tabelle

Per usare altri tipi di trigger e associazioni, installare il pacchetto NuGet che li contiene e chiamare un metodo `Use<binding>` nell'oggetto `JobHostConfiguration`. Se ad esempio si vuole usare un trigger timer, installare `Microsoft.Azure.WebJobs.Extensions` e chiamare `UseTimers` nel metodo `Main`, come illustrato di seguito:

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

Il processo per l'associazione al [`ExecutionContext`] dipende dalla versione dell'SDK.

#### <a name="version-3x"></a>Versione 3. *x*

Chiamare il metodo di estensione `AddExecutionContextBinding` nel metodo `ConfigureWebJobs`, come illustrato di seguito:

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

#### <a name="version-2x"></a>Versione 2. *x*

Il pacchetto `Microsoft.Azure.WebJobs.Extensions` citato in precedenza offre anche un tipo di associazione speciale che è possibile registrare chiamando il metodo `UseCore`. Questa associazione consente di definire un parametro di [`ExecutionContext`] nella firma della funzione, abilitata come segue:

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

È possibile configurare il comportamento di alcuni trigger e associazioni. Il processo di configurazione dipende dalla versione dell'SDK.

* **Versione 3. *x*:** impostare la configurazione quando viene chiamato il metodo `Add<Binding>` in `ConfigureWebJobs`.
* **Versione 2. *x*:** impostare la configurazione impostando le proprietà in un oggetto di configurazione passato al `JobHost`.

Queste impostazioni specifiche dell'associazione sono equivalenti alle impostazioni nel [file di progetto host. JSON](../azure-functions/functions-host-json.md) in funzioni di Azure.

È possibile configurare le associazioni seguenti:

* [Trigger Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Trigger di hub eventi](#event-hubs-trigger-configuration-version-3x)
* [Trigger di archiviazione code](#queue-storage-trigger-configuration)
* [Binding SendGrid](#sendgrid-binding-configuration-version-3x)
* [Trigger del bus di servizio](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configurazione del trigger Azure CosmosDB (versione 3. *x*)

Questo esempio illustra come configurare il trigger Azure Cosmos DB:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Per altri dettagli, vedere l'articolo relativo all' [associazione di Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) .

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configurazione del trigger di hub eventi (versione 3. *x*)

Questo esempio illustra come configurare il trigger di hub eventi:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Per altri dettagli, vedere l'articolo sull' [associazione di hub eventi](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) .

### <a name="queue-storage-trigger-configuration"></a>Configurazione del trigger di archiviazione code

Questi esempi illustrano come configurare il trigger di archiviazione code:

#### <a name="version-3x"></a>Versione 3. *x*

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

Per altri dettagli, vedere l'articolo sull' [associazione dell'archiviazione delle code](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) .

#### <a name="version-2x"></a>Versione 2. *x*

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

Per ulteriori informazioni, vedere il [riferimento host. JSON V1. x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configurazione dell'associazione SendGrid (versione 3. *x*)

Questo esempio Mostra come configurare l'associazione di output di SendGrid:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Per altri dettagli, vedere l'articolo relativo all' [associazione di SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) .

### <a name="service-bus-trigger-configuration-version-3x"></a>Configurazione del trigger del bus di servizio (versione 3. *x*)

Questo esempio illustra come configurare il trigger del bus di servizio:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Per altri dettagli, vedere l'articolo sull' [associazione del bus di servizio](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) .

### <a name="configuration-for-other-bindings"></a>Configurazione per altre associazioni

Alcuni tipi di trigger e binding definiscono tipi di configurazione personalizzati. Ad esempio, il trigger di file consente di specificare il percorso radice da monitorare, come negli esempi seguenti:

#### <a name="version-3x"></a>Versione 3. *x*

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

#### <a name="version-2x"></a>Versione 2. *x*

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

Per altre informazioni sulle espressioni di associazione, vedere [Modelli ed espressioni di associazione](../azure-functions/functions-bindings-expressions-patterns.md) nella documentazione di Funzioni di Azure.

### <a name="custom-binding-expressions"></a>Espressioni di associazione personalizzate

In alcuni casi si desidera specificare un nome di coda, un contenitore o un nome di BLOB oppure un nome di tabella nel codice anziché impostarlo come hardcoded. Ad esempio, si desidera specificare il nome della coda per l'attributo `QueueTrigger` in una variabile di ambiente o in un file di configurazione.

A tale scopo, è possibile passare un oggetto `NameResolver` nell'oggetto `JobHostConfiguration`. Includere segnaposto nei parametri del costruttore dell'attributo di trigger o associazione e il codice `NameResolver` offrirà i valori effettivi da usare in sostituzione di questi segnaposto. Per identificare i segnaposto, è necessario racchiuderli con la percentuale (%) Signs, come mostrato di seguito:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Questo codice consente di usare una coda denominata `logqueuetest` nell'ambiente di test e una coda denominata `logqueueprod` nell'ambiente di produzione. Invece di impostare un nome di coda come hardcoded, specificare il nome di una voce nella raccolta `appSettings`.

È presente un `NameResolver` predefinito che ha effetto se non ne viene fornito uno personalizzato. Il valore predefinito riceve i valori dalle impostazioni dell'app o dalle variabili di ambiente.

La classe `NameResolver` ottiene il nome della coda da `appSettings`, come illustrato di seguito:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Versione 3. *x*

Il resolver viene configurato tramite l'inserimento di dipendenze. Questi esempi richiedono l'istruzione `using` seguente:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Il resolver viene aggiunto chiamando il metodo di estensione [`ConfigureServices`] su [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder), come nell'esempio seguente:

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

#### <a name="version-2x"></a>Versione 2. *x*

Passare la classe `NameResolver` in all'oggetto `JobHost`, come illustrato di seguito:

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

Se è necessario eseguire alcune operazioni nella funzione prima di usare un attributo di associazione come `Queue`, `Blob`o `Table`, è possibile usare l'interfaccia `IBinder`.

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

La documentazione di funzioni di Azure fornisce informazioni di riferimento su ogni tipo di binding. In ogni articolo di riferimento dell'associazione sono disponibili le informazioni seguenti. Questo esempio è basato sulla coda di archiviazione.

* [Pacchetti](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Il pacchetto che è necessario installare per includere il supporto per l'associazione in un progetto di webjobs SDK.
* [Esempi](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Esempi di codice. L' C# esempio di libreria di classi si applica a webjobs SDK. Omettere solo l'attributo `FunctionName`.
* [Attributi](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Attributi da utilizzare per il tipo di associazione.
* [Configurazione](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Spiegazioni delle proprietà degli attributi e dei parametri del costruttore.
* [Utilizzo](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Tipi a cui è possibile eseguire l'associazione e informazioni sul funzionamento dell'associazione. Ad esempio: algoritmo di polling, elaborazione di una coda non elaborabile.
  
Per un elenco di articoli di riferimento sull'associazione, vedere "binding supportati" nell'articolo [trigger e associazioni](../azure-functions/functions-triggers-bindings.md#supported-bindings) per funzioni di Azure. In tale elenco, i binding HTTP, webhook e griglia di eventi sono supportati solo da funzioni di Azure, non da webjobs SDK.

## <a name="disable-attribute"></a>Attributo Disable 

L'attributo [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) consente di controllare se è possibile attivare una funzione. 

Nell'esempio seguente, se l'impostazione dell'app `Disable_TestJob` ha un valore `1` o `True` (senza distinzione tra maiuscole e minuscole), la funzione non viene eseguita. In tal caso, il runtime crea un messaggio di log *Function 'Functions.TestJob' is disabled*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando si modificano i valori delle impostazioni dell'app nel portale di Azure, il processo Web viene riavviato per prelevare la nuova impostazione.

L'attributo può essere dichiarato a livello di parametro, metodo o classe. Il nome dell'impostazione può inoltre contenere espressioni di associazione.

## <a name="timeout-attribute"></a>Attributo Timeout

L'attributo [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) fa sì che una funzione venga annullata se non viene completata entro un periodo di tempo specificato. Nell'esempio seguente la funzione viene eseguita per un giorno senza l'attributo timeout. Timeout causa l'annullamento della funzione dopo 15 secondi.

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

È possibile applicare l'attributo timeout a livello di classe o di metodo ed è possibile specificare un timeout globale usando `JobHostConfiguration.FunctionTimeout`. I timeout a livello di classe o di metodo eseguono l'override dei timeout globali.

## <a name="singleton-attribute"></a>Attributo Singleton

L'attributo [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) garantisce che venga eseguita una sola istanza di una funzione, anche se sono presenti più istanze dell'app Web host. A tale scopo, viene utilizzato il [blocco distribuito](#viewing-lease-blobs).

In questo esempio, solo una singola istanza della funzione `ProcessImage` viene eseguita in un determinato momento:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Alcuni trigger dispongono del supporto integrato per la gestione della concorrenza:

* **QueueTrigger**. Impostare `JobHostConfiguration.Queues.BatchSize` su `1`.
* **ServiceBusTrigger**. Impostare `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` su `1`.
* **FileTrigger**. Impostare `FileProcessor.MaxDegreeOfParallelism` su `1`.

È possibile usare queste impostazioni per assicurarsi che la funzione viene eseguita come singleton in una singola istanza. Per assicurarsi che venga eseguita una sola istanza della funzione quando l'app Web viene scalata in orizzontale a più istanze, applicare un blocco singleton a livello di listener sulla funzione (`[Singleton(Mode = SingletonMode.Listener)]`). I blocchi del listener vengono acquisiti all'avvio del JobHost. Se tutte e tre le istanze aumentate vengono avviate nello stesso momento, una sola istanza acquisisce il blocco e viene avviato un solo listener.

### <a name="scope-values"></a>Valori di ambito

È possibile specificare un' *espressione o un valore di ambito* per un singleton. L'espressione/valore garantisce che tutte le esecuzioni della funzione in un ambito specifico verranno serializzate. L'implementazione di un blocco più granulare in questo modo può consentire un certo livello di parallelismo per la funzione durante la serializzazione di altre chiamate, in base alle esigenze. Nel codice seguente, ad esempio, l'espressione di ambito viene associata al valore `Region` del messaggio in arrivo. Quando la coda contiene tre messaggi rispettivamente nelle aree est, est e ovest, i messaggi con area est vengono eseguiti in modo seriale mentre il messaggio con area ovest viene eseguito in parallelo con quelli in est.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
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

L'ambito predefinito per un blocco è `SingletonScope.Function`, ovvero l'ambito del blocco (il percorso del lease BLOB) è associato al nome completo della funzione. Per bloccare tutte le funzioni, specificare `SingletonScope.Host` e usare un nome di ID ambito identico in tutte le funzioni che non si desidera eseguire contemporaneamente. Nell'esempio seguente viene eseguita una sola istanza di `AddItem` o `RemoveItem` alla volta:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Visualizzazione dei BLOB di lease

WebJobs SDK usa i [lease del BLOB di Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) sullo sfondo per implementare il blocco distribuito. I BLOB di lease usati da Singleton sono disponibili nel contenitore `azure-webjobs-host` nell'account di archiviazione `AzureWebJobsStorage` nel percorso "Locks". Ad esempio, il percorso del BLOB di lease per il primo esempio `ProcessImage` mostrato sopra potrebbe essere `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Tutti i percorsi includono l'ID JobHost, in questo caso 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Funzioni asincrone

Per informazioni su come scrivere codice per le funzioni asincrone, vedere la [documentazione di funzioni di Azure](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Token di annullamento

Per informazioni su come gestire i token di annullamento, vedere la documentazione su [token di annullamento e arresto normale](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens) di Funzioni di Azure.

## <a name="multiple-instances"></a>Più istanze

Se l'app Web viene eseguita in più istanze, WebJob viene eseguito continuativamente in ogni istanza, in ascolto dei trigger e chiamando le funzioni. Le diverse associazioni di trigger sono progettate per condividere in modo efficiente la collaborazione tra le istanze, così che l'aumento delle istanze consenta di gestire un carico maggiore.

Sebbene alcuni trigger possano causare una doppia elaborazione, i trigger di archiviazione di Accodamento e BLOB impediscono automaticamente a una funzione di elaborare un messaggio o un BLOB in coda più di una volta. Per altre informazioni, vedere [progettazione di un input identico](../azure-functions/functions-idempotent.md) nella documentazione di funzioni di Azure.

Il trigger del timer garantisce automaticamente che venga eseguita una sola istanza del timer, in modo che non si ottenga più di un'istanza della funzione in esecuzione in un determinato momento pianificato.

Per assicurarsi che venga eseguita una sola istanza di una funzione anche quando sono presenti più istanze dell'app Web host, è possibile usare l'attributo [`Singleton`](#singleton-attribute) .

## <a name="filters"></a>Filtri

I filtri funzione (anteprima) offrono un modo per personalizzare la pipeline di esecuzione di WebJobs con la propria logica. I filtri sono simili ai [filtri ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). È possibile implementarli come attributi dichiarativi applicati alle funzioni o alle classi. Per altre informazioni, vedere [Function Filters](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

Si consiglia il Framework di registrazione sviluppato per ASP.NET. L' [articolo introduttivo](webjobs-sdk-get-started.md) illustra come usarlo. 

### <a name="log-filtering"></a>Filtro del log

Ogni log creato da un'istanza `ILogger` ha `Category` e `Level` associati. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) è un'enumerazione e il codice integer indica l'importanza relativa:

|LogLevel    |Codice|
|------------|---|
|Trace       | 0 |
|Debug       | 1 |
|Informazioni | 2 |
|Avviso     | 3 |
|Tipi di errore       | 4 |
|Critico    | 5 |
|nessuno        | 6 |

È possibile filtrare in modo indipendente ogni categoria in un particolare [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel). Ad esempio, si potrebbe voler vedere tutti i log per l'elaborazione del trigger del log ma solo `Error` e superiori per tutto il resto.

#### <a name="version-3x"></a>Versione 3. *x*

Versione 3. *x* dell'SDK si basa sul filtro incorporato in .NET Core. La classe `LogCategories` consente di definire categorie per funzioni, trigger o utenti specifici. Definisce inoltre i filtri per gli Stati host specifici, ad esempio `Startup` e `Results`. In questo modo è possibile ottimizzare l'output di registrazione. Se non vengono trovate corrispondenze nelle categorie definite, il filtro resta al valore `Default` quando stabilisce se filtrare il messaggio.

`LogCategories` richiede l'istruzione using seguente:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Nell'esempio seguente viene costruito un filtro che, per impostazione predefinita, filtra tutti i log a livello di `Warning`. Le categorie `Function` e `results`, equivalenti a `Host.Results` nella versione 2. *x*) viene applicato un filtro a livello di `Error`. Il filtro confronta la categoria corrente con tutti i livelli registrati nell'istanza `LogCategories` e sceglie la corrispondenza più lunga. Ciò significa che il livello di `Debug` registrato per `Host.Triggers` corrisponde `Host.Triggers.Queue` o `Host.Triggers.Blob`. In questo modo è possibile controllare categorie più ampie senza bisogno di aggiungerle singolarmente.

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

#### <a name="version-2x"></a>Versione 2. *x*

Nella versione 2. *x* dell'SDK, è possibile usare la classe `LogCategoryFilter` per controllare il filtro. Il `LogCategoryFilter` dispone di una proprietà `Default` con un valore iniziale di `Information`, ovvero tutti i messaggi a livello di `Information`, `Warning`, `Error`o `Critical` vengono registrati, ma tutti i messaggi a livello di `Debug` o `Trace` vengono filtrati.

Come per `LogCategories` nella versione 3. *x*, la proprietà `CategoryLevels` consente di specificare i livelli di registrazione per categorie specifiche, in modo da poter ottimizzare l'output di registrazione. Se non vengono trovate corrispondenze nel dizionario `CategoryLevels`, il filtro resta al valore `Default` quando stabilisce se filtrare il messaggio.

L'esempio seguente crea un filtro che per impostazione predefinita filtra tutti i log al livello `Warning`. Le categorie `Function` e `Host.Results` vengono filtrate a livello di `Error`. `LogCategoryFilter` confronta la categoria corrente con tutti i valori `CategoryLevels` registrati e sceglie la corrispondenza più lunga. Il livello di `Debug` registrato per `Host.Triggers` corrisponderà quindi `Host.Triggers.Queue` o `Host.Triggers.Blob`. In questo modo è possibile controllare categorie più ampie senza bisogno di aggiungerle singolarmente.

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

Il processo di implementazione della telemetria personalizzata per [Application Insights](../azure-monitor/app/app-insights-overview.md) dipende dalla versione dell'SDK. Per informazioni su come configurare Application Insights, vedere [Aggiungere la registrazione in Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versione 3. *x*

A causa della versione 3. *x* di WEBjobs SDK si basa sull'host generico .NET Core, ma non viene più fornita una factory di telemetria personalizzata. È tuttavia possibile aggiungere dati di telemetria personalizzati alla pipeline usando l'inserimento di dipendenze. Gli esempi in questa sezione richiedono le istruzioni `using` seguenti:

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
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
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

Quando il valore [`TelemetryConfiguration`] viene costruito, tutti i tipi di registrati di [`ITelemetryInitializer`] sono inclusi. Per altre informazioni, vedere [Application Insights API per le metriche e gli eventi personalizzati](../azure-monitor/app/api-custom-events-metrics.md).

Nella versione 3. *x*, non è più necessario scaricare il [`TelemetryClient`] quando l'host si arresta. Il sistema di inserimento delle dipendenze di .NET Core elimina automaticamente l'`ApplicationInsightsLoggerProvider` registrato, che consente di scaricare [`TelemetryClient`].

#### <a name="version-2x"></a>Versione 2. *x*

Nella versione 2. *x*, il [`TelemetryClient`] creato internamente dal provider Application Insights per webjobs SDK USA [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Quando l'endpoint di Application Insights non è disponibile o limita le richieste in ingresso, il canale [salva le richieste nel file system dell'app Web e le invia di nuovo in un secondo momento](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[`TelemetryClient`] è creato da una classe che implementa `ITelemetryClientFactory`. Per impostazione predefinita, è [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Se si vuole modificare una parte qualsiasi della pipeline di Application Insights, è possibile specificare il proprio `ITelemetryClientFactory` e l'host userà la classe per costruire un [`TelemetryClient`]. Questo codice, ad esempio, esegue l'override di `DefaultTelemetryClientFactory` per modificare una proprietà di `ServerTelemetryChannel`:

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

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

L'oggetto `SamplingPercentageEstimatorSettings` configura il [campionamento adattivo](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Ciò significa che in determinati scenari con volumi elevati, Application Insights invia al server un subset selezionato di dati di telemetria.

Dopo aver creato la factory di telemetria, passarla al provider di registrazione Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Passaggi successivi

Questo articolo ha fornito frammenti di codice che illustrano come gestire scenari comuni per l'uso di webjobs SDK. Per trovare esempi completi, vedere [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
