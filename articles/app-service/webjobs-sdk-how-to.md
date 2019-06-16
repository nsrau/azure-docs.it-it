---
title: Come usare WebJobs SDK - Azure
description: Altre informazioni su come scrivere codice per WebJobs SDK. Creare processi di elaborazione che accedono ai dati in servizi di Azure e servizi di terze parti basato sugli eventi in background.
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
ms.openlocfilehash: 38d8bdfcba48d2080b434ebec192b41f3663ae6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831793"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Come usare Azure WebJobs SDK per l'elaborazione in background guidata dagli eventi

Questo articolo fornisce indicazioni su come usare Azure WebJobs SDK. Per iniziare subito a usare processi Web, vedere [Introduzione a Azure WebJobs SDK per l'elaborazione in background basata su eventi](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versioni di WebJobs SDK

Queste sono le differenze principali tra la versione 3. *x* e la versione 2. *x* di WebJobs SDK:

* Versione 3. *x* aggiunge il supporto per .NET Core.
* Nella versione 3. *x*, è necessario installare esplicitamente l'estensione di binding di archiviazione richiesto da WebJobs SDK. Nella versione 2. *x*, le associazioni di archiviazione sono stati inclusi nel SDK.
* Strumenti per .NET Core di Visual Studio (3. *x*) progetti differisce da strumenti di .NET Framework (2. *x*) i progetti. Per altre informazioni, vedere [sviluppare e distribuire processi Web usando Visual Studio - servizio App di Azure](webjobs-dotnet-deploy-vs.md).

Quando possibile, vengono forniti esempi per entrambi versione 3. *x* e la versione 2. *x*.

> [!NOTE]
> [Funzioni di Azure](../azure-functions/functions-overview.md) si basa su WebJobs SDK, e questo articolo vengono forniti collegamenti alla documentazione di funzioni di Azure per alcuni argomenti. Tenere presente le differenze tra funzioni e WebJobs SDK:
> * Azure funzioni versione 2. *x* corrisponde a WebJobs SDK versione 3. *x*e funzioni di Azure 1. *x* corrisponde a WebJobs SDK 2. *x*. Repository di codice sorgente usare WebJobs SDK numerazione.
> * Esempi di codice per funzioni di Azure C# librerie di classi è simile a code di WebJobs SDK, ma non è necessario un `FunctionName` attributo di un progetto WebJobs SDK.
> * Alcuni tipi di associazione sono supportati solo nelle funzioni, quali HTTP (Webhook) e griglia di eventi (che è basato su HTTP).
>
> Per altre informazioni, vedere [Confrontare WebJobs SDK e Funzioni di Azure](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host WebJobs

L'host è un contenitore di runtime per le funzioni.  Ascolta i trigger e chiama le funzioni. Nella versione 3. *x*, l'host è un'implementazione di `IHost`. Nella versione 2. *x*, si utilizza il `JobHost` oggetto. L'utente crea un'istanza host nel codice e scrive il codice per personalizzarne il comportamento.

Questa è una differenza chiave tra l'uso di WebJobs SDK direttamente e usato indirettamente tramite funzioni di Azure. In funzioni di Azure, il servizio controlla l'host e non è possibile personalizzare l'host mediante la scrittura di codice. Funzioni di Azure consente di personalizzare il comportamento di host tramite le impostazioni nel file host. JSON. Tali impostazioni sono stringhe, non nel codice, e ciò limita i tipi di personalizzazioni che è possibile eseguire.

### <a name="host-connection-strings"></a>Stringhe di connessione dell'host

WebJobs SDK cerca le stringhe di connessione nel file local durante l'esecuzione in locale o nell'ambiente del processo Web di archiviazione di Azure e Bus di servizio di Azure quando si esegue in Azure. Per impostazione predefinita, una connessione di archiviazione di stringhe impostazione denominata `AzureWebJobsStorage` è obbligatorio.  

Versione 2. *x* del SDK consente di usare i propri nomi per le stringhe di connessione o di memorizzarli altrove. È possibile impostare i nomi nel codice utilizzando il [ `JobHostConfiguration` ], come illustrato di seguito:

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

In quanto versione 3. *x* Usa la configurazione di .NET Core predefinito le API, non è disponibile alcuna API per modificare i nomi di stringa di connessione.

### <a name="host-development-settings"></a>Impostazioni di sviluppo dell'host

È possibile eseguire l'host in modalità di sviluppo per rendere più efficiente lo sviluppo locale. Ecco alcune delle impostazioni che sono state modificate durante l'esecuzione in modalità di sviluppo:

| Proprietà | Impostazione di sviluppo |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` per massimizzare l'output del log. |
| `Queues.MaxPollingInterval`  | Un valore basso per garantire che i metodi di coda vengano attivati immediatamente.  |
| `Singleton.ListenerLockPeriod` | 15 secondi per rendere più rapido lo sviluppo iterativo. |

Il processo per l'abilitazione della modalità di sviluppo dipende dalla versione SDK. 

#### <a name="version-3x"></a>Versione 3. *x*

Versione 3. *x* Usa le API di base ASP.NET standard. Chiamare il [ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metodo sul [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) istanza. Passare una stringa denominata `development`, come in questo esempio:

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

La classe `JobHostConfiguration` ha un metodo `UseDevelopmentSettings` che abilita la modalità di sviluppo.  L'esempio seguente mostra come usare le impostazioni di sviluppo. Per rendere `config.IsDevelopment` restituire `true` quando è in esecuzione in locale, impostare una variabile di ambiente locale denominata `AzureWebJobsEnv` con il valore `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>La gestione delle connessioni simultanee (versione 2. *x*)

Nella versione 3. *x*, per impostazione predefinita il limite di connessione per connessioni infinite. Se per qualche motivo è necessario modificare questo limite, è possibile usare la [ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) proprietà della [ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler) classe.

Nella versione 2. *x*, controllare il numero di connessioni simultanee a un host tramite il [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. 2\. *x*, è necessario aumentare questo valore dal valore predefinito di 2 prima di avviare l'host di processi Web.

Tutte in uscita richieste HTTP eseguite da una funzione usando `HttpClient` fluiscono `ServicePointManager`. Dopo avere raggiunto il valore impostato nel `DefaultConnectionLimit`, `ServicePointManager` avvia le richieste di Accodamento prima di inviarli. Si supponga che il valore `DefaultConnectionLimit` sia impostato su 2 e che il codice effettui 1000 richieste HTTP. All'inizio sono consentite solo due richieste al sistema operativo. Le altre 998 vengono inserite nella coda finché ci sarà spazio. Ciò significa che il `HttpClient` potrebbe scadere perché sembra avere effettuato la richiesta, ma mai inviata dal sistema operativo nel server di destinazione. Pertanto si potrebbe osservare un comportamento apparentemente senza senso: `HttpClient` locale richiede 10 secondi per completare una richiesta, ma il servizio restituisce ogni richiesta in 200 ms. 

Il valore predefinito per le applicazioni ASP.NET è `Int32.MaxValue`, e che è probabile che funziona bene per i processi Web in esecuzione in una base o versione successiva piano di servizio App. Processi Web richiedono in genere l'impostazione Always On e che è supportato solo da Basic e superiore piani di servizio App.

Se WebJob è in esecuzione in un piano di servizio app Gratuito o Condiviso, l'applicazione è limitata dalla sandbox del servizio app, che al momento ha un [limite di connessioni pari a 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Con un limite di connessione non associati in `ServicePointManager`, è più probabile che verrà raggiunto il limite di connessione sandbox e il sito verrà arrestato. In tal caso, l'impostazione di `DefaultConnectionLimit` su un valore inferiore, ad esempio 50 o 100, può impedire che si verifichi questa situazione e allo stesso tempo consentire una velocità effettiva sufficiente.

L'impostazione deve essere configurata prima che venga effettuata qualsiasi richiesta HTTP. Per questo motivo, l'host di WebJobs non deve modificare l'impostazione automaticamente. È possibile che le richieste HTTP che si verificano prima dell'avvio dell'host, che potrebbe causare un comportamento imprevisto. L'approccio migliore consiste nell'impostare il valore immediatamente nel `Main` metodo prima di inizializzare `JobHost`, come illustrato di seguito:

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

Le funzioni devono essere metodi pubblici e deve avere un attributo trigger o il [ `NoAutomaticTrigger` ](#manual-triggers) attributo.

### <a name="automatic-triggers"></a>Trigger automatici

I trigger automatici chiamano una funzione in risposta a un evento. Si consideri l'esempio di una funzione attivata da un messaggio aggiunto all'archivio code di Azure. Risponde leggendo un blob da archiviazione Blob di Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Il `QueueTrigger` attributo indica al runtime di chiamare la funzione ogni volta che viene visualizzato un messaggio di coda nel `myqueue-items` coda. Il `Blob` attributo indica al runtime di usare il messaggio della coda per leggere un blob nel *esempio-workitems* contenitore. Il contenuto del messaggio della coda, passato alla funzione il `myQueueItem` parametro, è il nome del blob.


### <a name="manual-triggers"></a>Trigger manuale

Per attivare manualmente una funzione, usare il `NoAutomaticTrigger` attributo, come illustrato di seguito:

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

Il processo per attivare manualmente la funzione dipende dalla versione SDK.

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

Le associazioni di input offrono una modalità dichiarativa per rendere disponibili nel codice i dati dei servizi di Azure o di terze parti. Le associazioni di output garantiscono un modo per aggiornare i dati. Il [iniziare a usare](webjobs-sdk-get-started.md) articolo viene illustrato un esempio della ognuno.

È possibile utilizzare un valore restituito del metodo per un'associazione di output applicando l'attributo a valore restituito del metodo. Vedere l'esempio nella [usando la funzione di Azure restituiscono valore](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipi di associazioni

Il processo per l'installazione e la gestione dei tipi di associazione dipende dal fatto che si usi versione 3. *x* o la versione 2. *x* del SDK. È possibile trovare il pacchetto di installazione per un tipo particolare associazione nella sezione "Packages" del tipo dell'associazione funzioni di Azure [articolo di riferimento](#binding-reference-information). Un'eccezione è il trigger di file e l'associazione (per il file system locale), che non è supportato da funzioni di Azure.

#### <a name="version-3x"></a>Versione 3. *x*

Nella versione 3. *x*, le associazioni di archiviazione sono inclusi nel `Microsoft.Azure.WebJobs.Extensions.Storage` pacchetto. Chiamare il `AddAzureStorage` metodo di estensione nel `ConfigureWebJobs` metodo, come illustrato di seguito:

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

Per usare altri tipi di trigger e associazioni, installare il pacchetto NuGet che li contiene e chiamare il metodo di estensione `Add<binding>` implementato nell'estensione. Ad esempio, se si desidera utilizzare un'associazione di Azure Cosmos DB, installare `Microsoft.Azure.WebJobs.Extensions.CosmosDB` e chiamare `AddCosmosDB`, simile al seguente:

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

Questi tipi di trigger e associazione vengono incluse nella versione 2. *x* del `Microsoft.Azure.WebJobs` pacchetto:

* Archiviazione BLOB
* Archiviazione code
* Archiviazione tabelle

Per usare altri tipi di trigger e associazioni, installare il pacchetto NuGet che li contiene e chiamare un metodo `Use<binding>` nell'oggetto `JobHostConfiguration`. Ad esempio, se si desidera usare un trigger del Timer, installare `Microsoft.Azure.WebJobs.Extensions` e chiamare `UseTimers` nel `Main` metodo, come illustrato di seguito:

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

Il processo per l'associazione per il [ `ExecutionContext` ] varia a seconda della versione SDK.

#### <a name="version-3x"></a>Versione 3. *x*

Chiamare il `AddExecutionContextBinding` metodo di estensione nel `ConfigureWebJobs` metodo, come illustrato di seguito:

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

Il pacchetto `Microsoft.Azure.WebJobs.Extensions` citato in precedenza offre anche un tipo di associazione speciale che è possibile registrare chiamando il metodo `UseCore`. Questa associazione consente di definire un [ `ExecutionContext` ] parametro nella firma funzione, che è abilitata come segue:

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

È possibile configurare il comportamento di alcuni trigger e associazioni. Il processo per la configurazione dipende dalla versione SDK.

* **Versione 3. *x*:** Impostare la configurazione quando il `Add<Binding>` metodo viene chiamato nel `ConfigureWebJobs`.
* **Versione 2. *x*:** Configurazione di set impostando le proprietà in un oggetto di configurazione che è passare al `JobHost`.

Queste impostazioni specifico dell'associazione sono equivalenti alle impostazioni di [file di progetto host. JSON](../azure-functions/functions-host-json.md) in funzioni di Azure.

È possibile configurare le associazioni seguenti:

* [Trigger di Azure COSMOS DB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Trigger di hub eventi](#event-hubs-trigger-configuration-version-3x)
* Trigger per l'archiviazione code
* [Associazione di SendGrid](#sendgrid-binding-configuration-version-3x)
* [Trigger del Bus di servizio](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configurazione del trigger di Azure COSMOS DB (versione 3. *x*)

In questo esempio viene illustrato come configurare il trigger di Azure Cosmos DB:

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

Per altre informazioni, vedere la [associazione di Azure COSMOS DB](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) articolo.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configurazione di trigger di hub eventi (versione 3. *x*)

In questo esempio viene illustrato come configurare il trigger di hub eventi:

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

Per altre informazioni, vedere la [associazione di hub eventi](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) articolo.

### <a name="queue-storage-trigger-configuration"></a>Configurazione del trigger archiviazione code

Questi esempi mostrano come configurare il trigger di archiviazione di coda:

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

Per altre informazioni, vedere la [associazione di archiviazione della coda](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) articolo.

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

Per altre informazioni, vedere la [riferimento host. JSON v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configurazione dell'associazione SendGrid (versione 3. *x*)

In questo esempio viene illustrato come configurare SendGrid l'associazione di output:

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

Per altre informazioni, vedere la [binding SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) articolo.

### <a name="service-bus-trigger-configuration-version-3x"></a>Configurazione del trigger del Bus di servizio (versione 3. *x*)

In questo esempio viene illustrato come configurare il trigger del Bus di servizio:

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

Per altre informazioni, vedere la [binding del Bus di servizio](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) articolo.

### <a name="configuration-for-other-bindings"></a>Configurazione per altre associazioni

Alcuni tipi di trigger e associazione di definiscono i propri tipi di configurazione personalizzati. Ad esempio, il trigger di File consente di specificare il percorso radice per il monitoraggio, come in questi esempi:

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

Talvolta si desidera specificare un nome di coda, un nome di blob o contenitore o un nome di tabella nel codice anziché hardcoded. Ad esempio, si desidera specificare il nome della coda per l'attributo `QueueTrigger` in una variabile di ambiente o in un file di configurazione.

È possibile farlo mediante il passaggio di un `NameResolver` dell'oggetto per il `JobHostConfiguration` oggetto. Includere segnaposto nei parametri del costruttore dell'attributo di trigger o associazione e il codice `NameResolver` offrirà i valori effettivi da usare in sostituzione di questi segnaposto. Identificare i segnaposto, che li racchiudono con simbolo di percentuale (%) segni, come illustrato di seguito:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Questo codice consente di usare una coda denominata `logqueuetest` nell'ambiente di test e una coda denominata `logqueueprod` nell'ambiente di produzione. Invece di impostare un nome di coda come hardcoded, specificare il nome di una voce nella raccolta `appSettings`.

È un valore predefinito `NameResolver` che ha effetto se non si specifica una personalizzata. Il valore predefinito riceve i valori dalle impostazioni dell'app o dalle variabili di ambiente.

I `NameResolver` classe ottiene il nome della coda da `appSettings`, come illustrato di seguito:

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

Per configurare il sistema di risoluzione, mediante l'inserimento di dipendenza. Questi esempi richiedono l'istruzione `using` seguente:

```cs
using Microsoft.Extensions.DependencyInjection;
```

È possibile aggiungere il sistema di risoluzione chiamando il [ `ConfigureServices` ] metodo di estensione sul [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder), come in questo esempio:

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

Passare il `NameResolver` classe per il `JobHost` dell'oggetto, come illustrato di seguito:

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

Se è necessario eseguire alcune operazioni nella funzione prima di usare un attributo di associazione, ad esempio `Queue`, `Blob`, o `Table`, è possibile usare il `IBinder` interfaccia.

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

La documentazione di funzioni di Azure fornisce informazioni di riferimento su ogni tipo di associazione. Troverai le informazioni seguenti in ogni articolo di riferimento dell'associazione. (In questo esempio è basato su coda di archiviazione).

* [I pacchetti](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Il pacchetto che è necessario installare per includere il supporto per l'associazione in un progetto WebJobs SDK.
* [Esempi](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Esempi di codice. Il C# esempio di libreria della classe si applica a WebJobs SDK. Omettere semplicemente il `FunctionName` attributo.
* [Gli attributi](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Gli attributi da utilizzare per il tipo di associazione.
* [Configurazione](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Spiegazioni delle proprietà di attributo e i parametri del costruttore.
* [Utilizzo](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). È possibile associare ai tipi e informazioni sul funzionamento dell'associazione. Ad esempio: algoritmo di polling, elaborazione di una coda non elaborabile.
  
Per un elenco di articoli di riferimento di associazione, vedere "Associazioni supportate" nel [trigger e associazioni](../azure-functions/functions-triggers-bindings.md#supported-bindings) articolo per funzioni di Azure. In tale elenco, le associazioni di griglia di eventi, Webhook e HTTP sono supportate solo dalle funzioni di Azure, non da WebJobs SDK.

## <a name="disable-attribute"></a>Attributo Disable 

Il [ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) consente di attributo controllare se una funzione possono essere attivati. 

Nell'esempio seguente, se l'impostazione dell'app `Disable_TestJob` ha un valore pari `1` o `True` (maiuscole e minuscole), la funzione non verrà eseguito. In tal caso, il runtime crea un messaggio di log *Function 'Functions.TestJob' is disabled*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando si modificano i valori delle impostazioni app nel portale di Azure, il processo Web viene riavviato per prelevare la nuova impostazione.

L'attributo può essere dichiarato a livello di parametro, metodo o classe. Il nome dell'impostazione può inoltre contenere espressioni di associazione.

## <a name="timeout-attribute"></a>Attributo Timeout

Il [ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) attributo fa sì che una funzione deve essere annullato se non viene completata entro un periodo di tempo specificato. Nell'esempio seguente, la funzione viene eseguita per un giorno senza l'attributo di Timeout. Timeout di fa sì che la funzione deve essere annullato dopo 15 secondi.

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

È possibile applicare l'attributo Timeout a livello di classe o metodo ed è possibile specificare un timeout globale usando `JobHostConfiguration.FunctionTimeout`. I timeout a livello di classe o a livello di metodo di eseguire l'override timeout globali.

## <a name="singleton-attribute"></a>Attributo Singleton

Il [ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) attributo assicura che solo un'istanza di una funzione viene eseguita, anche quando sono presenti più istanze dell'app web host. Ciò è possibile utilizzare [distribuite blocco](#viewing-lease-blobs).

In questo esempio, solo una singola istanza del `ProcessImage` funzione viene eseguita in qualsiasi momento:

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

È possibile usare queste impostazioni per assicurarsi che la funzione viene eseguita come singleton in una singola istanza. Per garantire che solo una singola istanza della funzione è in esecuzione quando l'app web viene scalata orizzontalmente a più istanze, applicare un blocco a livello di listener singleton nella funzione (`[Singleton(Mode = SingletonMode.Listener)]`). Quando viene avviato JobHost, vengono acquisiti blocchi di listener. Se tutte e tre le istanze aumentate vengono avviate nello stesso momento, una sola istanza acquisisce il blocco e viene avviato un solo listener.

### <a name="scope-values"></a>Valori di ambito

È possibile specificare una */valore dell'espressione di ambito* in un singleton. Il valore dell'espressione/garantisce che verranno serializzate tutte le esecuzioni della funzione in un ambito specifico. Che implementa il blocco più granulari in questo modo può attendere un certo grado di parallelismo per la funzione durante la serializzazione di altre chiamate dalle esigenze. Ad esempio, nel codice seguente, l'espressione di ambito associati per il `Region` valore del messaggio in arrivo. Se la coda contiene tre messaggi in aree est, est e Ovest rispettivamente, i messaggi che hanno area orientale sono eseguiti in modalità seriale quando si utilizza il messaggio con area che occidentale viene eseguito in parallelo con quelli nell'area orientale.

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

L'ambito predefinito per un blocco è `SingletonScope.Function`, vale a dire l'ambito del blocco (il percorso di lease di blob) è associato al nome completo di funzione. Per bloccare in funzioni, specificare `SingletonScope.Host` e usare un nome di ID ambito che è lo stesso in tutte le funzioni che non si desidera eseguire simultaneamente. Nell'esempio seguente viene eseguita una sola istanza di `AddItem` o `RemoveItem` alla volta:

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

WebJobs SDK usa i [lease del BLOB di Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) sullo sfondo per implementare il blocco distribuito. Il BLOB del lease usato dal Singleton sono reperibili nel `azure-webjobs-host` contenitore nel `AzureWebJobsStorage` account di archiviazione nel percorso "blocchi". Ad esempio, il percorso del BLOB di lease per il primo esempio `ProcessImage` mostrato sopra potrebbe essere `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Tutti i percorsi includono l'ID JobHost, in questo caso 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Funzioni asincrone

Per informazioni su come le funzioni asincrone di codice, vedere la [documentazione di funzioni di Azure](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Token di annullamento

Per informazioni su come gestire i token di annullamento, vedere la documentazione su [token di annullamento e arresto normale](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens) di Funzioni di Azure.

## <a name="multiple-instances"></a>Più istanze

Se l'app Web viene eseguita in più istanze, WebJob viene eseguito continuativamente in ogni istanza, in ascolto dei trigger e chiamando le funzioni. Le diverse associazioni di trigger sono progettate per condividere in modo efficiente la collaborazione tra le istanze, così che l'aumento delle istanze consenta di gestire un carico maggiore.

Il trigger della coda e blob automaticamente impedire a una funzione di elaborare un messaggio di coda o blob più volte. le funzioni non devono essere idempotenti.

Il trigger del timer garantisce automaticamente che venga eseguita una sola istanza del timer, in modo che non si ottenga più di un'istanza della funzione in esecuzione in un determinato momento pianificato.

Se si desidera garantire che solo un'istanza di una funzione viene eseguita anche quando sono presenti più istanze dell'app web host, è possibile usare la [ `Singleton` ](#singleton-attribute) attributo.

## <a name="filters"></a>Filtri

I filtri funzione (anteprima) offrono un modo per personalizzare la pipeline di esecuzione di WebJobs con la propria logica. I filtri sono simili agli [filtri di ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). È possibile implementarle come attributi dichiarativi che vengono applicati per le funzioni o classi. Per altre informazioni, vedere [Function Filters](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

È consigliabile il framework di registrazione che è stato sviluppato per ASP.NET. Il [iniziare a usare](webjobs-sdk-get-started.md) articolo illustra come usarlo. 

### <a name="log-filtering"></a>Filtro del log

Ogni log creato da un'istanza `ILogger` ha `Category` e `Level` associati. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) è un'enumerazione e il codice integer indica la relativa importanza:

|LogLevel    |Codice|
|------------|---|
|Trace       | 0 |
|Debug       | 1 |
|Informazioni | 2 |
|Avviso     | 3 |
|Tipi di errore       | 4 |
|Critico    | 5 |
|Nessuna        | 6 |

È possibile filtrare in modo indipendente ogni categoria per una determinata [ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel). Ad esempio, si potrebbe voler vedere tutti i log per l'elaborazione del trigger del log ma solo `Error` e superiori per tutto il resto.

#### <a name="version-3x"></a>Versione 3. *x*

Versione 3. *x* del SDK si basa sui filtri incorporati in .NET Core. La classe `LogCategories` consente di definire categorie per funzioni, trigger o utenti specifici. Definisce anche i filtri per gli stati di host specifico, ad esempio `Startup` e `Results`. In questo modo è possibile ottimizzare l'output di registrazione. Se non vengono trovate corrispondenze nelle categorie definite, il filtro resta al valore `Default` quando stabilisce se filtrare il messaggio.

`LogCategories` richiede l'istruzione using seguente:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

L'esempio seguente crea un filtro che, per impostazione predefinita, filtra tutti i log nel `Warning` livello. Il `Function` e `results` categorie (equivalente a `Host.Results` nella versione 2. *x*) vengono filtrati al `Error` livello. Il filtro confronta la categoria corrente con tutti i livelli registrati nell'istanza `LogCategories` e sceglie la corrispondenza più lunga. Ciò significa che il `Debug` a livello di registrazione per `Host.Triggers` corrisponda `Host.Triggers.Queue` o `Host.Triggers.Blob`. In questo modo è possibile controllare categorie più ampie senza bisogno di aggiungerle singolarmente.

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

Nella versione 2. *x* del SDK, si utilizza il `LogCategoryFilter` classe per controllare il filtro. Il `LogCategoryFilter` ha un `Default` proprietà con un valore iniziale pari `Information`, vale a dire che tutti i messaggi nel `Information`, `Warning`, `Error`, o `Critical` livelli vengono registrati, ma i messaggi nel `Debug` o `Trace` livelli vengono filtrati da subito.

Come per gli `LogCategories` nella versione 3. *x*, il `CategoryLevels` proprietà consente di specificare i livelli di log per le categorie specifiche in modo che è possibile ottimizzare l'output di registrazione. Se non vengono trovate corrispondenze nel dizionario `CategoryLevels`, il filtro resta al valore `Default` quando stabilisce se filtrare il messaggio.

L'esempio seguente crea un filtro che per impostazione predefinita filtra tutti i log al livello `Warning`. Il `Function` e `Host.Results` categorie vengono filtrate al `Error` livello. `LogCategoryFilter` confronta la categoria corrente con tutti i valori `CategoryLevels` registrati e sceglie la corrispondenza più lunga. In modo che il `Debug` a livello di registrazione per `Host.Triggers` corrisponderanno `Host.Triggers.Queue` o `Host.Triggers.Blob`. In questo modo è possibile controllare categorie più ampie senza bisogno di aggiungerle singolarmente.

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

Il processo per l'implementazione di dati di telemetria personalizzati per [Application Insights](../azure-monitor/app/app-insights-overview.md) varia a seconda della versione SDK. Per informazioni su come configurare Application Insights, vedere [Aggiungere la registrazione in Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versione 3. *x*

In quanto versione 3. *x* di WebJobs SDK si basa su .NET Core host generico, una factory di dati di telemetria personalizzati non viene più fornito. Ma è possibile aggiungere dati di telemetria personalizzati per la pipeline usando l'inserimento di dipendenze. Gli esempi in questa sezione richiedono le istruzioni `using` seguenti:

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

Quando il valore [`TelemetryConfiguration`] viene costruito, tutti i tipi di registrati di [`ITelemetryInitializer`] sono inclusi. Per altre informazioni, vedere [API di Application Insights per metriche ed eventi personalizzati](../azure-monitor/app/api-custom-events-metrics.md).

Nella versione 3. *x*, non è più necessario scaricare la [ `TelemetryClient` ] quando l'host viene arrestato. Il sistema di inserimento delle dipendenze di .NET Core elimina automaticamente l'`ApplicationInsightsLoggerProvider` registrato, che consente di scaricare [`TelemetryClient`].

#### <a name="version-2x"></a>Versione 2. *x*

Nella versione 2. *x*, il [ `TelemetryClient` ] creato internamente dal provider di Application Insights per WebJobs SDK Usa [ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Quando l'endpoint di Application Insights non è disponibile o limita le richieste in ingresso, il canale [salva le richieste nel file system dell'app Web e le invia di nuovo in un secondo momento](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[`TelemetryClient`] è creato da una classe che implementa `ITelemetryClientFactory`. Per impostazione predefinita, è [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Se si vuole modificare una parte qualsiasi della pipeline di Application Insights, è possibile specificare il proprio `ITelemetryClientFactory` e l'host userà la classe per costruire un [`TelemetryClient`]. Ad esempio, questo codice esegue l'override `DefaultTelemetryClientFactory` per modificare una proprietà di `ServerTelemetryChannel`:

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

Il `SamplingPercentageEstimatorSettings` oggetto consente di configurare [campionamento adattivo](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Ciò significa che in determinati scenari con volumi elevati, Application Insights invia un sottoinsieme selezionato di dati di telemetria al server.

Dopo aver creato la factory di dati di telemetria, si passa nel provider di registrazione di Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Passaggi successivi

Questo articolo ha fornito i frammenti di codice che illustrano come gestire scenari comuni per l'uso di WebJobs SDK. Per trovare esempi completi, vedere [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
