---
title: Esplorare i log di traccia .NET in Azure Application Insights con ILogger
description: Esempi dell'utilizzo del provider di Azure Application Insights ILogger con le applicazioni ASP.NET Core e la Console.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: ca842ce46a58dafa87581b77bcbd802191f7fcd1
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511023"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>Per i log di .NET Core ILogger ApplicationInsightsLoggerProvider

ASP.NET Core supporta un'API di registrazione che funziona con tipi diversi di provider di registrazione predefiniti e di terze parti. La registrazione viene eseguita chiamando **log ()** o una variante di tale sul *ILogger* istanze. Questo articolo illustra come usare *ApplicationInsightsLoggerProvider* acquisire i log di ILogger nella console e applicazioni ASP.NET Core. Questo articolo descrive anche come ApplicationInsightsLoggerProvider si integra con altri dati di telemetria di Application Insights.
Per altre informazioni, vedere [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Applicazioni ASP.NET Core

ApplicationInsightsLoggerProvider è abilitato per impostazione predefinita in [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.0-beta3 (e versioni successive) quando si attiva regolare monitoraggio tramite uno standard di Application Insights metodi:
- Chiamando il **UseApplicationInsights** metodo di estensione su IWebHostBuilder 
- Chiamando il **AddApplicationInsightsTelemetry** metodo di estensione su IServiceCollection

I log di ILogger che acquisisce ApplicationInsightsLoggerProvider sono soggetti alla stessa configurazione di eventuali altri dati di telemetria raccolti. Hanno lo stesso set di TelemetryInitializers e TelemetryProcessors, usare la stessa TelemetryChannel e sono correlati e campionati nello stesso modo degli altri dati di telemetria. Se si usa 2.7.0-beta3 versione o versioni successive, non è necessaria acquisire i log di ILogger alcuna azione.

Solo *avviso* o i log di ILogger superiore (da tutte le categorie) vengono inviati ad Application Insights per impostazione predefinita. Tuttavia, è possibile [applicare i filtri per modificare questo comportamento](#control-logging-level). Sono necessari passaggi aggiuntivi per acquisire registri ILogger **Program.cs** oppure **Startup.cs**. (Vedere [log di acquisizione ILogger da Startup.cs e Program.cs nelle applicazioni ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Se si usa una versione precedente del SDK Microsoft.ApplicationInsights.AspNet o si vuole solo usare ApplicationInsightsLoggerProvider senza eventuali altri monitoraggio di Application Insights, usare la procedura seguente:

1. Installare il pacchetto NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modificare **Program.cs** come illustrato di seguito:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

Consente di configurare il codice nel passaggio 2 `ApplicationInsightsLoggerProvider`. Il codice seguente illustra un esempio di classe Controller, che usa `ILogger` per inviare i log. I log vengono acquisiti da Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Acquisire i log di ILogger da Startup.cs e Program.cs nelle App ASP.NET Core

Il nuovo ApplicationInsightsLoggerProvider possibile acquisire log da nelle prime fasi della pipeline di avvio dell'applicazione. Sebbene ApplicationInsightsLoggerProvider viene abilitata automaticamente in Application Insights (partire dalla versione 2.7.0-beta3), non presenta una chiave di strumentazione impostata fino non in un secondo momento nella pipeline. Pertanto, solo i log da **Controller**/ altre classi verranno acquisite. Per ogni log a partire da acquisire **Program.cs** e **Startup.cs** stesso, è necessario abilitare esplicitamente una chiave di strumentazione per ApplicationInsightsLoggerProvider. È inoltre *TelemetryConfiguration* non è completamente configurato quando si accede da **Program.cs** oppure **Startup.cs** stesso. Pertanto, tali log avrà una configurazione minima che non usa InMemoryChannel, nessun campionamento e gli inizializzatori di telemetria standard o processori.

Gli esempi seguenti illustrano questa funzionalità con **Program.cs** e **Startup.cs**.

#### <a name="example-programcs"></a>Esempio Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Esempio Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Eseguire la migrazione dal vecchio ApplicationInsightsLoggerProvider

Versioni del SDK Microsoft.ApplicationInsights.AspNet prima 2.7.0-beta2 supportato un provider di registrazione che è ora obsoleto. Questo provider è stato abilitato tramite il **AddApplicationInsights()** metodo di estensione di ILoggerFactory. È consigliabile eseguire la migrazione al nuovo provider, che prevede due passaggi:

1. Rimuovere il *ILoggerFactory.AddApplicationInsights()* chiamare dalle **Startup.Configure()** metodo per evitare la registrazione di double.
2. Riapplicare tutte le regole di filtro nel codice, perché non essere rispettati dal nuovo provider. Esegue l'overload del *ILoggerFactory.AddApplicationInsights()* impiegato minimo LogLevel o filtro funzioni. Con il nuovo provider, il filtro fa parte del framework di registrazione se stesso. Non avviene dal provider di Application Insights. Pertanto, eventuali filtri che sono disponibili tramite *ILoggerFactory.AddApplicationInsights()* overload deve essere rimosso. E regole di filtro devono essere fornite seguendo la [controllare il livello di registrazione](#control-logging-level) istruzioni. Se si usa *appSettings. JSON* per filtrare la registrazione, continueranno a funzionare con il nuovo provider, perché entrambi usano lo stesso alias, provider *ApplicationInsights*.

È comunque possibile usare il vecchio provider. (Verrà rimosso solo in una modifica di versione principale a 3. *xx*.) Ma è consigliabile eseguire la migrazione al nuovo provider per i motivi seguenti:

- Manca il supporto per il precedente provider [ambiti di log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Del nuovo provider, le proprietà dall'ambito vengono aggiunti automaticamente come proprietà personalizzate ai dati di telemetria raccolti.
- A questo punto è possibile acquisire i log molto più indietro in pipeline di avvio dell'applicazione. I log dal **Program** e **avvio** classi possono ora essere acquisite.
- Con il nuovo provider, il filtro viene eseguito a livello di framework stesso. È possibile filtrare i log per il provider di Application Insights in esattamente come per altri provider, inclusi i provider predefiniti, come Console di Debug e così via. È anche possibile applicare i medesimi filtri per più provider.
- In ASP.NET Core (2.0 e versioni successive), il metodo consigliato per [abilitare i provider di registrazione](https://github.com/aspnet/Announcements/issues/255) consiste nell'usare i metodi di estensione su ILoggingBuilder nelle **Program.cs** stesso.

> [!Note]
> Il nuovo provider è disponibile per le applicazioni destinate a NETSTANDARD2.0 o versione successiva. Se l'applicazione ha come destinazione le versioni precedenti di .NET Core, ad esempio .NET Core 1.1, o se è destinato a .NET Framework, continuare a usare il vecchio provider.

## <a name="console-application"></a>Applicazione console

Il codice seguente illustra un'applicazione console di esempio che è configurata per inviare ILogger tracce in Application Insights.

Pacchetti installati:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Questo esempio Usa il pacchetto autonomo `Microsoft.Extensions.Logging.ApplicationInsights`. Per impostazione predefinita, questa configurazione viene utilizzato il "minimo" TelemetryConfiguration per inviare i dati ad Application Insights. Livello minimo significa che il canale utilizzato InMemoryChannel. Non è disponibile alcun TelemetryInitializers non standard e il campionamento. Questo comportamento può essere sottoposto a override per un'applicazione console, come illustrato nell'esempio seguente.

Installare questo pacchetto aggiuntivo:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

La sezione seguente illustra come sostituire il valore predefinito TelemetryConfiguration utilizzando il **servizi. Configurare<TelemetryConfiguration>()** (metodo). Questo esempio configura `ServerTelemetryChannel` e il campionamento. Aggiunge un ITelemetryInitializer personalizzato di TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Livello di registrazione di controllo

ASP.NET Core *ILogger* infra ha un meccanismo incorporato per applicare [filtro di log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Ciò consente di controllare i log inviati a ogni provider registrati, inclusi il provider di Application Insights. Il filtro può essere eseguito nella configurazione (in genere tramite un *appSettings. JSON* file) o nel codice. Questa funzionalità avviene tramite il framework stesso. Non è specifico del provider di Application Insights.

Negli esempi seguenti si applicano le regole di filtro per ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Creare regole di filtro nella configurazione appSettings. JSON

Per ApplicationInsightsLoggerProvider, è l'alias di provider `ApplicationInsights`. La sezione seguente della *appSettings. JSON* configura i log per *avviso* e versioni successive di tutte le categorie e *errore* e versioni successive di categorie che iniziano con " Microsoft"da inviare al `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Creare regole di filtro nel codice

Il frammento di codice seguente consente di configurare i log per *avviso* e versioni successive di tutte le categorie e per *errore* e versioni successive di categorie che iniziano con "Microsoft" da inviare al `ApplicationInsightsLoggerProvider`. Questa configurazione è uguale a quello nella sezione precedente *appSettings. JSON*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quali sono le versioni precedenti e nuove di ApplicationInsightsLoggerProvider?

[SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) inclusi un ApplicationInsightsLoggerProvider incorporati (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), che è stata abilitata tramite  **ILoggerFactory** i metodi di estensione. Questo provider è contrassegnato come obsoleto da 2.7.0-beta2 versione. Verrà rimosso completamente la modifica di versione principale successiva. Il [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pacchetto stesso non è obsoleto. È necessario per abilitare il monitoraggio delle richieste, dipendenze e così via.

L'alternativa suggerita è il nuovo pacchetto autonomo [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), che contiene un (ApplicationInsightsLoggerProvider migliorata Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) e i metodi di estensione su ILoggerBuilder per abilitarlo.

[SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.0-beta3 assume una dipendenza dal pacchetto nuovo e Abilita acquisizione ILogger automaticamente.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Il motivo per cui vengono visualizzati alcuni log ILogger due volte in Application Insights?

Se si dispone della versione (obsoleta) precedente di ApplicationInsightsLoggerProvider abilitata mediante la chiamata, può verificarsi la duplicazione `AddApplicationInsights` su `ILoggerFactory`. Controllare se le **configura** metodo ha i seguenti elementi e rimuoverlo:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se si verifica registrazione doppia quando esegue il debug da Visual Studio, impostare `EnableDebugLogger` al *false* nel codice che abilita Application Insights, come indicato di seguito. Questa duplicazione e correzione è rilevante solo quando si esegue il debug dell'applicazione.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Ho aggiornato a [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 versione e i log di ILogger vengono acquisiti automaticamente. Come possibile disattivare questa funzionalità completamente?

Vedere le [controllare il livello di registrazione](../../azure-monitor/app/ilogger.md#control-logging-level) sezione per informazioni su come filtrare i log in generale. Disattiva ApplicationInsightsLoggerProvider, usare `LogLevel.None`:

**Nel codice:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**Nel file di configurazione:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Il motivo per cui alcuni log ILogger non hanno le stesse proprietà di altri utenti?

Application Insights acquisisce e invia i log di ILogger usando la stessa TelemetryConfiguration che viene usato per tutti i dati di telemetria. Ma si verifica un'eccezione. Per impostazione predefinita, TelemetryConfiguration non è completamente configurato quando si accede dal **Program.cs** oppure **Startup.cs**. I log da queste posizioni non hanno la configurazione predefinita, in modo che non può essere eseguito tutti TelemetryInitializers e TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Utilizzo del pacchetto autonomo Microsoft.Extensions.Logging.ApplicationInsights e desidera registrare manualmente alcuni dati di telemetria personalizzati aggiuntivi. Modo in cui è necessario farlo?

Quando si usa il pacchetto autonomo `TelemetryClient` non viene inserito nel contenitore di inserimento delle dipendenze, pertanto è necessario creare una nuova istanza della `TelemetryClient` e utilizzano la stessa configurazione l'Usa provider di logger, come illustrato nel codice seguente. Ciò garantisce che la stessa configurazione viene utilizzata per tutti i dati di telemetria personalizzati, nonché dati di telemetria di ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Se si usa il pacchetto Microsoft.ApplicationInsights.AspNetCore per abilitare Application Insights, modificare il codice per ottenere `TelemetryClient` direttamente nel costruttore. Per un esempio, vedere [queste domande frequenti](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Il tipo di dati di telemetria di Application Insights viene generato dai registri di ILogger? O, in cui è possibile visualizzare che i log di ILogger in Application Insights?

ApplicationInsightsLoggerProvider acquisisce i log di ILogger e da esse vengono create TraceTelemetry. Se viene passato un oggetto eccezione per il **log ()** metodo ILogger, *ExceptionTelemetry* viene creata invece TraceTelemetry. Questi elementi di telemetria sono reperibile nelle stesse posizioni come qualsiasi altro TraceTelemetry o ExceptionTelemetry per Application Insights, inclusi portale, analitica o debugger di Visual Studio locale.

Se si preferisce inviare sempre TraceTelemetry, usare questo frammento di codice: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Non è installato il SDK e usare l'estensione App Web di Azure per abilitare Application Insights per le applicazioni ASP.NET Core. Come si usa il nuovo provider? 

L'estensione Application Insights nell'App Web di Azure Usa il vecchio provider. È possibile modificare le regole di filtro nel *appSettings. JSON* file dell'applicazione. Per sfruttare i vantaggi del nuovo provider, utilizzare la strumentazione in fase di compilazione tramite l'aggiunta di una dipendenza NuGet di SDK. Questo articolo verrà aggiornato quando l'estensione passa all'uso di provider di nuovo.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Con il pacchetto autonomo Microsoft.Extensions.Logging.ApplicationInsights e abilitazione di provider di Application Insights chiamando **generatore. AddApplicationInsights("ikey")**. È disponibile un'opzione per ottenere una chiave di strumentazione dalla configurazione?


Modificare Program.cs e appSettings. JSON come segue:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Sezione pertinente da `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Questo codice è obbligatorio solo quando si usa un provider di registrazione autonomo. Per il regolare monitoraggio di Application Insights, la chiave di strumentazione viene caricata automaticamente dal percorso di configurazione *Application Insights: Chiave di strumentazione*. AppSettings. JSON dovrebbe essere simile al seguente:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

* [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [I log di traccia .NET in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
