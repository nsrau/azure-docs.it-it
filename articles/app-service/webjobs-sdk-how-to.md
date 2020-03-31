---
title: Come utilizzare l'SDK di WebJobs
description: Altre informazioni su come scrivere codice per WebJobs SDK. Creare processi di elaborazione in background basati su eventi che accedono ai dati in Azure e nei servizi di terze parti.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: c606f6e60b1c906a0d5c29992287d126aaa37b7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602933"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Come usare Azure WebJobs SDK per l'elaborazione in background guidata dagli eventi

Questo articolo fornisce indicazioni su come usare Azure WebJobs SDK. Per iniziare subito a usare WebJobs, vedere [Introduzione a Azure WebJobs SDK per l'elaborazione in background basata](webjobs-sdk-get-started.md)su eventi. 

## <a name="webjobs-sdk-versions"></a>Versioni di WebJobs SDK

Queste sono le differenze principali tra la versione 3. *x* e la versione 2. *x* dell'SDK di WebJobs:

* Versione 3. *x* aggiunge il supporto per .NET Core.
* Nella versione 3. *x*, è necessario installare in modo esplicito l'estensione di associazione di archiviazione richiesta da WebJobs SDK. Nella versione 2. *x*, i binding di archiviazione sono stati inclusi nell'SDK.
* Strumenti di Visual Studio per .NET Core (3.* x*) i progetti sono diversi dagli strumenti per .NET Framework (2.* x*) progetti. Per altre informazioni, vedere [Sviluppare e distribuire WebJobs con Visual Studio - Servizio app di Azure](webjobs-dotnet-deploy-vs.md).

Quando possibile, vengono forniti esempi per entrambe le versioni 3.When possible, examples are provided for both version 3. *x* e la versione 2. *x*.

> [!NOTE]
> [Funzioni di Azure](../azure-functions/functions-overview.md) si basa su WebJobs SDK e in questo articolo vengono forniti collegamenti alla documentazione di Funzioni di Azure per alcuni argomenti. Notate queste differenze tra Funzioni e WebJobs SDK:
> * Funzioni di Azure versione 2.Azure Functions version 2. *x* corrisponde a WebJobs SDK versione 3. *x*e Funzioni di Azure 1. *x* corrisponde a WebJobs SDK 2. *x*. I repository di codice sorgente utilizzano la numerazione WebJobs SDK.
> * Il codice di esempio per le librerie di classi di Funzioni `FunctionName` di Azure in Cè è simile al codice SDK di WebJobs, ad eccezione del fatto che non è necessario un attributo in un progetto WebJobs SDK.Sample code for Azure Functions C' class libraries is like WebJobs SDK code, except you don't need a attribute in a WebJobs SDK project.
> * Alcuni tipi di associazione sono supportati solo in Funzioni, ad esempio HTTP (Webhook) e Griglia di eventi (basata su HTTP).
>
> Per altre informazioni, vedere [Confrontare WebJobs SDK e Funzioni di Azure](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host WebJobs

L'host è un contenitore di runtime per le funzioni.  Ascolta i trigger e chiama le funzioni. Nella versione 3. *x*, l'host `IHost`è un'implementazione di . Nella versione 2. *x*, si `JobHost` utilizza l'oggetto. L'utente crea un'istanza host nel codice e scrive il codice per personalizzarne il comportamento.

Si tratta di una differenza fondamentale tra l'uso diretto di WebJobs SDK e l'utilizzo indiretto tramite Funzioni di Azure.This is a key difference between using the WebJobs SDK directly and using it indirectly through Azure Functions. In Funzioni di Azure il servizio controlla l'host e non è possibile personalizzare l'host scrivendo codice. Funzioni di Azure consente di personalizzare il comportamento dell'host tramite le impostazioni del file host.json. Queste impostazioni sono stringhe, non codice, e questo limita i tipi di personalizzazioni che è possibile eseguire.

### <a name="host-connection-strings"></a>Stringhe di connessione dell'host

WebJobs SDK cerca le stringhe di connessione di Archiviazione di Azure e del bus di servizio di Azure nel file local.settings.json quando si esegue in locale o nell'ambiente del processo Web quando si esegue in Azure.The WebJobs SDK looks for Azure Storage and Azure Service Bus connection strings in the local.settings.json file when you run locally or in the environment of the WebJob when you run in Azure. Per impostazione predefinita, è `AzureWebJobsStorage` necessaria un'impostazione della stringa di connessione di archiviazione denominata.  

Versione 2. *x* dell'SDK consente di usare nomi personalizzati per queste stringhe di connessione o di archiviarli altrove. È possibile impostare i [`JobHostConfiguration`]nomi nel codice utilizzando il , come illustrato di seguito:

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

Perché la versione 3. *x* utilizza le API di configurazione predefinite di .NET Core, non esiste alcuna API per modificare i nomi delle stringhe di connessione.

### <a name="host-development-settings"></a>Impostazioni di sviluppo dell'host

È possibile eseguire l'host in modalità di sviluppo per rendere più efficiente lo sviluppo locale. Di seguito sono riportate alcune delle impostazioni che vengono modificate quando si esegue in modalità di sviluppo:

| Proprietà | Impostazione di sviluppo |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` per massimizzare l'output del log. |
| `Queues.MaxPollingInterval`  | Un valore basso per garantire che i metodi di coda vengano attivati immediatamente.  |
| `Singleton.ListenerLockPeriod` | 15 secondi per rendere più rapido lo sviluppo iterativo. |

Il processo per l'abilitazione della modalità di sviluppo dipende dalla versione dell'SDK. 

#### <a name="version-3x"></a>Versione 3. *x (in* questo modo

Versione 3. *x* usa lo standard api ASP.NET Core. Chiamare [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) il metodo [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) sull'istanza. Passare una `development`stringa denominata , come in questo esempio:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versione 2. *x (in* questo modo

La classe `JobHostConfiguration` ha un metodo `UseDevelopmentSettings` che abilita la modalità di sviluppo.  L'esempio seguente mostra come usare le impostazioni di sviluppo. Per `config.IsDevelopment` restituire `true` quando viene eseguito localmente, `AzureWebJobsEnv` impostare `Development`una variabile di ambiente locale denominata con il valore .

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

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Gestione delle connessioni simultanee (versione 2.* x*)

Nella versione 3. *x*, il limite di connessione viene impostato per impostazione predefinita su connessioni infinite. Se per qualche motivo è necessario modificare [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) questo limite, è possibile utilizzare la proprietà della [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) classe.

Nella versione 2. *x*, è possibile controllare il numero di connessioni simultanee a un host utilizzando l'API [ServicePointManager.DefaultConnectionLimit.](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) In 2. *x*, è necessario aumentare questo valore dal valore predefinito di 2 prima di avviare l'host WebJobs.

Tutte le richieste HTTP in uscita `HttpClient` effettuate `ServicePointManager`da una funzione tramite flow attraverso . Dopo aver raggiunto `DefaultConnectionLimit`il `ServicePointManager` valore impostato in , avvia la coda delle richieste prima di inviarle. Si supponga che il valore `DefaultConnectionLimit` sia impostato su 2 e che il codice effettui 1000 richieste HTTP. All'inizio sono consentite solo due richieste al sistema operativo. Le altre 998 vengono inserite nella coda finché ci sarà spazio. Ciò `HttpClient` significa che il tuo potrebbe sorpassare perché sembra aver effettuato la richiesta, ma la richiesta non è mai stata inviata dal sistema operativo al server di destinazione. Pertanto si potrebbe osservare un comportamento apparentemente senza senso: `HttpClient` locale richiede 10 secondi per completare una richiesta, ma il servizio restituisce ogni richiesta in 200 ms. 

Il valore predefinito per `Int32.MaxValue`ASP.NET applicazioni è ed è probabile che funzioni correttamente per I processi Web in esecuzione in un piano di servizio app di base o superiore. I processi Web in genere richiedono l'impostazione Always On e sono supportati solo dai piani di servizio app di base e superiori.

Se WebJob è in esecuzione in un piano di servizio app Gratuito o Condiviso, l'applicazione è limitata dalla sandbox del servizio app, che al momento ha un [limite di connessioni pari a 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Con un limite di `ServicePointManager`connessione non associato in , è più probabile che venga raggiunta la soglia di connessione sandbox e che il sito venga arrestato. In tal caso, l'impostazione di `DefaultConnectionLimit` su un valore inferiore, ad esempio 50 o 100, può impedire che si verifichi questa situazione e allo stesso tempo consentire una velocità effettiva sufficiente.

L'impostazione deve essere configurata prima che venga effettuata qualsiasi richiesta HTTP. Per questo motivo, l'host WebJobs non deve modificare automaticamente l'impostazione. Potrebbero esserci richieste HTTP che si verificano prima dell'avvio dell'host, che potrebbero causare un comportamento imprevisto. L'approccio migliore consiste nell'impostare immediatamente il valore nel `Main` metodo prima dell'inizializzazione, `JobHost`come illustrato di seguito:

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

Le funzioni devono essere metodi pubblici e [`NoAutomaticTrigger`](#manual-triggers) devono avere un attributo trigger o l'attributo.

### <a name="automatic-triggers"></a>Trigger automatici

I trigger automatici chiamano una funzione in risposta a un evento. Si consideri questo esempio di una funzione attivata da un messaggio aggiunto all'archiviazione delle code di Azure.Consider this example of a function that's triggered by a message added to Azure Queue storage. Risponde leggendo un BLOB dall'archiviazione BLOB di Azure:It responds by reading a blob from Azure Blob storage:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

L'attributo `QueueTrigger` indica al runtime di chiamare la `myqueue-items` funzione ogni volta che viene visualizzato un messaggio della coda nella coda. L'attributo `Blob` indica al runtime di usare il messaggio della coda per leggere un BLOB nel contenitore *sample-workitems.* Il contenuto del messaggio della coda, passato `myQueueItem` alla funzione nel parametro, è il nome del BLOB.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Trigger manuali

Per attivare una funzione `NoAutomaticTrigger` manualmente, utilizzare l'attributo, come illustrato di seguito:To trigger a function manually, use the attribute, as shown here:

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

Il processo per l'attivazione manuale della funzione dipende dalla versione SDK.

#### <a name="version-3x"></a>Versione 3. *x (in* questo modo

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

#### <a name="version-2x"></a>Versione 2. *x (in* questo modo

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Associazioni di input e output

Le associazioni di input offrono una modalità dichiarativa per rendere disponibili nel codice i dati dei servizi di Azure o di terze parti. Le associazioni di output garantiscono un modo per aggiornare i dati. L'articolo [Introduzione](webjobs-sdk-get-started.md) mostra un esempio di ciascuno di essi.

È possibile usare un valore restituito del metodo per un'associazione di output applicando l'attributo al valore restituito del metodo. Vedere l'esempio in [Utilizzo del valore restituito della funzione di Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipi di associazioni

Il processo di installazione e gestione dei tipi di associazione varia a seconda che si utilizzi la versione 3. *x* o versione 2. *x* dell'SDK. È possibile trovare il pacchetto da installare per un particolare tipo di associazione nella sezione "Pacchetti" [dell'articolo](#binding-reference-information)di riferimento sulle funzioni di Azure di tale tipo di associazione. Un'eccezione è il trigger e l'associazione file (per il file system locale), che non è supportato da Funzioni di Azure.An exception is the Files trigger and binding (for the local file system), which's not supported by Azure Functions.

#### <a name="version-3x"></a>Versione 3. *x (in* questo modo

Nella versione 3. *x*, le associazioni di `Microsoft.Azure.WebJobs.Extensions.Storage` archiviazione sono incluse nel pacchetto. Chiamare `AddAzureStorage` il metodo `ConfigureWebJobs` di estensione nel metodo, come illustrato di seguito:Call the extension method in the method, as shown here:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Per usare altri tipi di trigger e associazioni, installare il pacchetto NuGet che li contiene e chiamare il metodo di estensione `Add<binding>` implementato nell'estensione. Ad esempio, se si vuole usare un'associazione DB Cosmos di Azure, installare e chiamare , in questo modo:For example, if you want to use an Azure Cosmos DB binding, install `Microsoft.Azure.WebJobs.Extensions.CosmosDB` and call `AddCosmosDB`, like this:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Per usare il trigger Timer o l'associazione File, che fanno parte dei servizi di base, chiamare rispettivamente i metodi di estensione `AddTimers` o `AddFiles`.

#### <a name="version-2x"></a>Versione 2. *x (in* questo modo

Questi tipi di trigger e associazione sono inclusi nella versione 2.These trigger and binding types are included in version 2. *x* del `Microsoft.Azure.WebJobs` pacchetto:

* Archiviazione BLOB
* Archiviazione code
* Archiviazione tabelle

Per usare altri tipi di trigger e associazioni, installare il pacchetto NuGet che li contiene e chiamare un metodo `Use<binding>` nell'oggetto `JobHostConfiguration`. Ad esempio, se si desidera utilizzare `Microsoft.Azure.WebJobs.Extensions` un `UseTimers` trigger `Main` Timer, installare e chiamare il metodo, come illustrato di seguito:For example, if you want to use a Timer trigger, install and call in the method, as shown here:

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

WebJobs consente di [`ExecutionContext`]eseguire l'associazione a un file . Con questa associazione, [`ExecutionContext`] è possibile accedere a come parametro nella firma della funzione. Ad esempio, il codice seguente usa l'oggetto di contesto per accedere all'ID di chiamata, che può essere usato per correlare tutti i log prodotti da una determinata chiamata di funzione.  

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

Il processo di [`ExecutionContext`] associazione a dipende dalla versione dell'SDK.

#### <a name="version-3x"></a>Versione 3. *x (in* questo modo

Chiamare `AddExecutionContextBinding` il metodo `ConfigureWebJobs` di estensione nel metodo, come illustrato di seguito:Call the extension method in the method, as shown here:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versione 2. *x (in* questo modo

Il pacchetto `Microsoft.Azure.WebJobs.Extensions` citato in precedenza offre anche un tipo di associazione speciale che è possibile registrare chiamando il metodo `UseCore`. Questa associazione consente [`ExecutionContext`] di definire un parametro nella firma della funzione, che è abilitata in questo modo:This binding lets you define an parameter in your function signature, which is enabled like this:

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

* **Versione 3. *x*:** imposta `Add<Binding>` la configurazione `ConfigureWebJobs`quando il metodo viene chiamato in .
* **Versione 2. *x*:** consente di impostare la configurazione impostando `JobHost`le proprietà in un oggetto di configurazione passato a .

Queste impostazioni specifiche dell'associazione sono equivalenti alle impostazioni nel file di progetto host.json in Funzioni di Azure.These binding-specific settings are equivalent to settings in the [host.json project file](../azure-functions/functions-host-json.md) in Azure Functions.

È possibile configurare i binding seguenti:You can configure the following bindings:

* [Trigger CosmosDB di AzureAzure CosmosDB trigger](#azure-cosmosdb-trigger-configuration-version-3x)
* [Trigger per Hub eventi](#event-hubs-trigger-configuration-version-3x)
* [Trigger per l'archiviazione code](#queue-storage-trigger-configuration)
* [Associazione SendGrid](#sendgrid-binding-configuration-version-3x)
* [Trigger di bus di servizio](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configurazione trigger CosmosDB di Azure (versione 3.Azure CosmosDB trigger configuration (version 3.* x*)

Questo esempio mostra come configurare il trigger del database Cosmos di Azure:This example shows how to configure the Azure Cosmos DB trigger:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Per altre informazioni, vedere l'articolo [sull'associazione CosmosDB di Azure.For](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) more details, see the Azure CosmosDB binding article.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configurazione trigger hub eventi (versione 3.* x*)

In questo esempio viene illustrato come configurare il trigger Hub eventi:This example shows how to configure the Event Hubs trigger:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Per altre informazioni, vedere l'articolo [sull'associazione hub eventi.](../azure-functions/functions-bindings-event-hubs-output.md#hostjson-settings)

### <a name="queue-storage-trigger-configuration"></a>Configurazione del trigger di archiviazione delle code

In questi esempi viene illustrato come configurare il trigger di archiviazione della coda:These examples show how to configure the Queue storage trigger:

#### <a name="version-3x"></a>Versione 3. *x (in* questo modo

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Per altre informazioni, vedere l'articolo [Associazione di archiviazione della coda.](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)

#### <a name="version-2x"></a>Versione 2. *x (in* questo modo

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

Per ulteriori dettagli, vedere il [riferimento host.json v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configurazione dell'associazione SendGrid (versione 3.* x*)

In questo esempio viene illustrato come configurare l'associazione di output SendGrid:This example shows how to configure the SendGrid output binding:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Per altre informazioni, vedere l'articolo [sull'associazione SendGrid.For](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) more details, see the SendGrid binding article.

### <a name="service-bus-trigger-configuration-version-3x"></a>Configurazione del trigger del bus di servizio (versione 3.Service Bus trigger configuration (version 3.* x*)

In questo esempio viene illustrato come configurare il trigger del bus di servizio:This example shows how to configure the Service Bus trigger:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Per altre informazioni, vedere l'articolo [Sull'associazione](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings) al bus di servizio.

### <a name="configuration-for-other-bindings"></a>Configurazione per altre associazioni

Alcuni tipi di trigger e associazione definiscono i propri tipi di configurazione personalizzati. Ad esempio, il trigger File consente di specificare il percorso radice da monitorare, come in questi esempi:

#### <a name="version-3x"></a>Versione 3. *x (in* questo modo

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versione 2. *x (in* questo modo

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

In alcuni momenti si vuole specificare un nome di coda, un nome blob o un contenitore o un nome di tabella nel codice anziché codificarlo a livello di codice. Ad esempio, si desidera specificare il nome della coda per l'attributo `QueueTrigger` in una variabile di ambiente o in un file di configurazione.

È possibile farlo passando `NameResolver` un oggetto `JobHostConfiguration` all'oggetto. Includere segnaposto nei parametri del costruttore dell'attributo di trigger o associazione e il codice `NameResolver` offrirà i valori effettivi da usare in sostituzione di questi segnaposto. I segnaposto vengono identificati racchiudendoli con percentuale (%) come mostrato qui:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Questo codice consente di usare una coda denominata `logqueuetest` nell'ambiente di test e una coda denominata `logqueueprod` nell'ambiente di produzione. Invece di impostare un nome di coda come hardcoded, specificare il nome di una voce nella raccolta `appSettings`.

C'è un `NameResolver` valore predefinito che ha effetto se non si fornisce uno personalizzato. Il valore predefinito riceve i valori dalle impostazioni dell'app o dalle variabili di ambiente.

La `NameResolver` classe ottiene il `appSettings`nome della coda da , come illustrato di seguito:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Versione 3. *x (in* questo modo

Configurare il sistema di risoluzione utilizzando l'inserimento di dipendenze. Questi esempi richiedono l'istruzione `using` seguente:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Per aggiungere il sistema [`ConfigureServices`] di [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)risoluzione chiamando il metodo di estensione su , come in questo esempio:

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

#### <a name="version-2x"></a>Versione 2. *x (in* questo modo

Passa `NameResolver` la tua `JobHost` classe all'oggetto, come mostrato qui:

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

Se è necessario eseguire alcune operazioni nella funzione prima `Queue` `Blob`di `Table`utilizzare un attributo di associazione come , , o , è possibile utilizzare l'interfaccia `IBinder` .

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

La documentazione di Funzioni di Azure fornisce informazioni di riferimento su ogni tipo di associazione. In ogni articolo di riferimento sull'associazione sono disponibili le informazioni seguenti. Questo esempio è basato sulla coda di archiviazione.

* [Pacchetti](../azure-functions/functions-bindings-storage-queue.md). Pacchetto da installare per includere il supporto per l'associazione in un progetto WebJobs SDK.
* [Esempi](../azure-functions/functions-bindings-storage-queue-trigger.md). Esempi di codice. L'esempio di libreria di classi C 'net si applica a WebJobs SDK. Basta omettere `FunctionName` l'attributo.
* [Attributi](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Attributi da utilizzare per il tipo di associazione.
* [Configurazione](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Spiegazioni delle proprietà dell'attributo e dei parametri del costruttore.
* [Utilizzo](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). I tipi a cui è possibile eseguire l'associazione e le informazioni sul funzionamento dell'associazione. Ad esempio: algoritmo di polling, elaborazione di una coda non elaborabile.
  
Per un elenco di articoli di riferimento sull'associazione, vedere "Associazioni supportate" nell'articolo Trigger e binding per Funzioni di Azure.For a list of binding reference articles, see "Supported bindings" in the [Triggers and bindings](../azure-functions/functions-triggers-bindings.md#supported-bindings) article for Azure Functions. In tale elenco, le associazioni HTTP, Webhook e Griglia di eventi sono supportate solo da Funzioni di Azure, non da WebJobs SDK.

## <a name="disable-attribute"></a>Attributo Disable 

L'attributo [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) consente di controllare se una funzione può essere attivata. 

Nell'esempio seguente, se `Disable_TestJob` l'impostazione `1` `True` dell'app ha un valore di o (senza distinzione tra maiuscole e minuscole), la funzione non verrà eseguita. In tal caso, il runtime crea un messaggio di log *Function 'Functions.TestJob' is disabled*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando si modificano i valori di impostazione dell'app nel portale di Azure, WebJob viene riavviato per selezionare la nuova impostazione.

L'attributo può essere dichiarato a livello di parametro, metodo o classe. Il nome dell'impostazione può inoltre contenere espressioni di associazione.

## <a name="timeout-attribute"></a>Attributo Timeout

L'attributo [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) determina l'annullamento di una funzione se non viene completata entro un periodo di tempo specificato. Nell'esempio seguente, la funzione verrebbe eseguita per un giorno senza l'attributo Timeout. Timeout fa sì che la funzione venga annullata dopo 15 secondi.

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

È possibile applicare l'attributo Timeout a livello di classe o `JobHostConfiguration.FunctionTimeout`di metodo e specificare un timeout globale utilizzando . I timeout a livello di classe o di metodo sostituiscono i timeout globali.

## <a name="singleton-attribute"></a>Attributo Singleton

L'attributo [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) garantisce che venga eseguita una sola istanza di una funzione, anche quando sono presenti più istanze dell'app Web host. Questa operazione viene eseguita utilizzando il [blocco distribuito](#viewing-lease-blobs).

In questo esempio, viene eseguita una sola istanza della `ProcessImage` funzione in un determinato momento:

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

È possibile usare queste impostazioni per assicurarsi che la funzione viene eseguita come singleton in una singola istanza. Per garantire che solo una singola istanza della funzione sia in esecuzione quando l'app Web viene ridimensionata a più istanze, applicare un blocco singleton a livello di listener sulla funzione ( ).`[Singleton(Mode = SingletonMode.Listener)]` I blocchi del listener vengono acquisiti all'avvio di JobHost. Se tutte e tre le istanze aumentate vengono avviate nello stesso momento, una sola istanza acquisisce il blocco e viene avviato un solo listener.

### <a name="scope-values"></a>Valori di ambito

È possibile specificare *un'espressione/valore* di ambito in un singleton. L'espressione/valore assicura che tutte le esecuzioni della funzione in un ambito specifico verranno serializzate. L'implementazione di un blocco più granulare in questo modo può consentire un certo livello di parallelismo per la funzione durante la serializzazione di altre chiamate in base alle proprie esigenze. Nel codice seguente, ad esempio, l'espressione `Region` di ambito viene associata al valore del messaggio in arrivo. Quando la coda contiene tre messaggi nelle aree Est, Est e Ovest, i messaggi con area orientale vengono eseguiti in modo seriale mentre il messaggio con l'area ovest viene eseguito in parallelo con quelli dell'area est.

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

L'ambito predefinito per `SingletonScope.Function`un blocco è , ovvero l'ambito del blocco (il percorso di lease del BLOB) è legato al nome completo della funzione. Per bloccare le `SingletonScope.Host` funzioni, specificare e usare un nome ID ambito uguale a tutte le funzioni che non si desidera eseguire contemporaneamente. Nell'esempio seguente viene eseguita una sola istanza di `AddItem` o `RemoveItem` alla volta:

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

WebJobs SDK usa i [lease del BLOB di Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) sullo sfondo per implementare il blocco distribuito. I BLOB di lease usati da Singleton sono disponibili nel `azure-webjobs-host` contenitore nell'account `AzureWebJobsStorage` di archiviazione nel percorso "locks". Ad esempio, il percorso del BLOB di lease per il primo esempio `ProcessImage` mostrato sopra potrebbe essere `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Tutti i percorsi includono l'ID JobHost, in questo caso 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Funzioni asincrone

Per informazioni su come codificare le funzioni asincrone, vedere la [documentazione](../azure-functions/functions-dotnet-class-library.md#async)relativa alle funzioni di Azure.For information about how to code async functions, see the Azure Functions documentation .

## <a name="cancellation-tokens"></a>Token di annullamento

Per informazioni su come gestire i token di annullamento, vedere la documentazione su [token di annullamento e arresto normale](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens) di Funzioni di Azure.

## <a name="multiple-instances"></a>Istanze multiple

Se l'app Web viene eseguita in più istanze, WebJob viene eseguito continuativamente in ogni istanza, in ascolto dei trigger e chiamando le funzioni. Le diverse associazioni di trigger sono progettate per condividere in modo efficiente la collaborazione tra le istanze, così che l'aumento delle istanze consenta di gestire un carico maggiore.

Mentre alcuni trigger possono comportare l'elaborazione doppia, i trigger di archiviazione di code e BLOB impediscono automaticamente a una funzione di elaborare più di una volta un messaggio della coda o un BLOB. Per altre informazioni, vedere [Progettazione di input identici](../azure-functions/functions-idempotent.md) nella documentazione di Funzioni di Azure.For more information, see Designing for identical input in the Azure Functions documentation.

Il trigger del timer garantisce automaticamente che venga eseguita una sola istanza del timer, in modo che non si ottenga più di un'istanza della funzione in esecuzione in un determinato momento pianificato.

Se si desidera assicurarsi che venga eseguita una sola istanza di una funzione anche [`Singleton`](#singleton-attribute) quando sono presenti più istanze dell'app Web host, è possibile utilizzare l'attributo .

## <a name="filters"></a>Filtri

I filtri funzione (anteprima) offrono un modo per personalizzare la pipeline di esecuzione di WebJobs con la propria logica. I filtri sono simili [ai filtri ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). È possibile implementarli come attributi dichiarativi applicati alle funzioni o alle classi. Per altre informazioni, vedere [Function Filters](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

È consigliabile il framework di registrazione sviluppato per ASP.NET. Nell'articolo [Introduzione](webjobs-sdk-get-started.md) viene illustrato come utilizzarlo. 

### <a name="log-filtering"></a>Filtro del log

Ogni log creato da un'istanza `ILogger` ha `Category` e `Level` associati. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)è un'enumerazione e il codice integer indica un'importanza relativa:Is an enumeration, and the integer code indicates relative importance:

|LogLevel    |Codice|
|------------|---|
|Trace       | 0 |
|Debug       | 1 |
|Informazioni | 2 |
|Avviso     | 3 |
|Errore       | 4 |
|Critico    | 5 |
|nessuno        | 6 |

È possibile filtrare in [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)modo indipendente ogni categoria in base a un particolare oggetto . Ad esempio, si potrebbe voler vedere tutti i log per l'elaborazione del trigger del log ma solo `Error` e superiori per tutto il resto.

#### <a name="version-3x"></a>Versione 3. *x (in* questo modo

Versione 3. *x* dell'SDK si basa sul filtro incorporato in .NET Core. La classe `LogCategories` consente di definire categorie per funzioni, trigger o utenti specifici. Definisce inoltre i filtri per `Startup` stati `Results`host specifici, like e . In questo modo è possibile ottimizzare l'output di registrazione. Se non vengono trovate corrispondenze nelle categorie definite, il filtro resta al valore `Default` quando stabilisce se filtrare il messaggio.

`LogCategories` richiede l'istruzione using seguente:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Nell'esempio `Warning` seguente viene creato un filtro che, per impostazione predefinita, filtra tutti i log a livello di . Le `Function` `results` categorie e `Host.Results` (equivalente alla versione 2.* x*) vengono `Error` filtrati a livello. Il filtro confronta la categoria corrente con tutti i livelli registrati nell'istanza `LogCategories` e sceglie la corrispondenza più lunga. Ciò significa `Debug` che `Host.Triggers` il `Host.Triggers.Queue` `Host.Triggers.Blob`livello registrato per le corrispondenze o . In questo modo è possibile controllare categorie più ampie senza bisogno di aggiungerle singolarmente.

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

#### <a name="version-2x"></a>Versione 2. *x (in* questo modo

Nella versione 2. *x* dell'SDK, si `LogCategoryFilter` utilizza la classe per controllare il filtro. La `LogCategoryFilter` proprietà `Default` dispone di una `Information`proprietà con un `Information`valore `Warning` `Error`iniziale `Critical` di , ovvero tutti `Debug` i `Trace` messaggi a livello , , o vengono registrati, ma tutti i messaggi ai livelli o vengono filtrati.

Come `LogCategories` nella versione 3. *x*, `CategoryLevels` la proprietà consente di specificare i livelli di log per categorie specifiche in modo da ottimizzare l'output di registrazione. Se non vengono trovate corrispondenze nel dizionario `CategoryLevels`, il filtro resta al valore `Default` quando stabilisce se filtrare il messaggio.

L'esempio seguente crea un filtro che per impostazione predefinita filtra tutti i log al livello `Warning`. Le `Function` `Host.Results` categorie e vengono `Error` filtrate a livello. `LogCategoryFilter` confronta la categoria corrente con tutti i valori `CategoryLevels` registrati e sceglie la corrispondenza più lunga. Quindi `Debug` il livello `Host.Triggers` registrato `Host.Triggers.Queue` `Host.Triggers.Blob`per corrisponderà o . In questo modo è possibile controllare categorie più ampie senza bisogno di aggiungerle singolarmente.

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

Il processo di implementazione dei dati di telemetria personalizzati per [Application Insights](../azure-monitor/app/app-insights-overview.md) dipende dalla versione dell'SDK. Per informazioni su come configurare Application Insights, vedere [Aggiungere la registrazione in Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versione 3. *x (in* questo modo

Perché la versione 3. *x* dell'SDK WebJobs si basa sull'host generico .NET Core, non viene più fornita una factory di telemetria personalizzata. È tuttavia possibile aggiungere dati di telemetria personalizzati alla pipeline usando l'inserimento delle dipendenze. Gli esempi in questa sezione richiedono le istruzioni `using` seguenti:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

La seguente implementazione personalizzata [`ITelemetryInitializer`] [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) di consente [`TelemetryConfiguration`]di aggiungere il proprio al valore predefinito .

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Chiamare [`ConfigureServices`] il generatore per [`ITelemetryInitializer`] aggiungere il valore personalizzato alla pipeline.

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Quando [`TelemetryConfiguration`] viene costruito, vengono [`ITelemetryInitializer`] inclusi tutti i tipi registrati di. Per altre informazioni, vedere [API di Application Insights per gli eventi e le metriche personalizzati.](../azure-monitor/app/api-custom-events-metrics.md)

Nella versione 3. *x*, non è più [`TelemetryClient`] necessario scaricare il quando l'host si arresta. Il sistema di inserimento delle dipendenze `ApplicationInsightsLoggerProvider`.NET Core elimina [`TelemetryClient`]automaticamente l'oggetto registrato, che scarica il file .

#### <a name="version-2x"></a>Versione 2. *x (in* questo modo

Nella versione 2. *x*, [`TelemetryClient`] la proprietà creata internamente dal provider [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)Application Insights per WebJobs SDK utilizza . Quando l'endpoint di Application Insights non è disponibile o limita le richieste in ingresso, il canale [salva le richieste nel file system dell'app Web e le invia di nuovo in un secondo momento](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

L'oggetto [`TelemetryClient`] viene creato `ITelemetryClientFactory`da una classe che implementa . Per impostazione predefinita, si tratta del [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)file .

Se si desidera modificare qualsiasi parte della pipeline di `ITelemetryClientFactory`Application Insights, è possibile fornire [`TelemetryClient`]un oggetto personalizzato e l'host utilizzerà la classe per costruire un oggetto . Ad esempio, questo `DefaultTelemetryClientFactory` codice esegue l'override per modificare una proprietà di `ServerTelemetryChannel`:

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

L'oggetto `SamplingPercentageEstimatorSettings` configura il [campionamento adattivo.](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) Ciò significa che in alcuni scenari con volumi elevati, Application Insights invia un subset selezionato di dati di telemetria al server.

Dopo aver creato la factory di telemetria, passarla al provider di registrazione di Application Insights:After you create the telemetry factory, you pass-in to the Application Insights logging provider:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> Passaggi successivi

In questo articolo sono stati forniti frammenti di codice che illustrano come gestire scenari comuni per l'utilizzo di WebJobs SDK. Per trovare esempi completi, vedere [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

['ExecutionContext']: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetryInitializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
