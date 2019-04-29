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
ms.openlocfilehash: 615eaa3df7cabad72ac321978eb01d93a7bfa988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901070"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>Per i log di .NET Core ILogger ApplicationInsightsLoggerProvider

ASP.NET Core supporta un'API di registrazione che funziona con tipi diversi di provider di registrazione predefiniti e di terze parti. La registrazione viene eseguita tramite una chiamata di log () o una variante di tale sul `ILogger` istanze. Questo articolo illustra come usare `ApplicationInsightsLoggerProvider` acquisire `ILogger` log console e applicazioni ASP.NET Core. Questo articolo descrive anche come `ApplicationInsightsLoggerProvider` è integrato con altri dati di telemetria di Application Insights.
Per informazioni su altre registrazione in Asp.Net Core, vedere [questo articolo](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Applicazioni ASP.NET Core

A partire [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 versione e versioni successive, `ApplicationInsightsLoggerProvider` è abilitato per impostazione predefinita quando si abilita la regolare monitoraggio di Application Insights utilizzando metodi standard - dalla la chiamata `UseApplicationInsights` metodo di estensione su IWebHostBuilder o `AddApplicationInsightsTelemetry` metodo di estensione su IServiceCollection. `ILogger` i log acquisiti da `ApplicationInsightsLoggerProvider` sono soggetti a stessa configurazione di eventuali altri dati di telemetria raccolti. vale a dire hanno lo stesso set di `TelemetryInitializer`s, `TelemetryProcessor`s, Usa lo stesso `TelemetryChannel`e verrà correlato e campionati in esattamente come tutti i dati di telemetria.  Se si usa questa versione di SDK o versione successiva, quindi è richiesta alcuna azione per acquisire `ILogger` i log.

Per impostazione predefinita, solo `ILogger` log di `Warning` o versioni successive (da tutte le categorie) vengono inviati ad Application Insights. Questo comportamento può essere modificato applicando filtri, come illustrato [qui](#control-logging-level). Inoltre sono necessari passaggi aggiuntivi se `ILogger` registra dal `Program.cs` oppure `Startup.cs` devono essere acquisiti come illustrato [qui](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications).

Se si usa una versione precedente del SDK Microsoft.ApplicationInsights.AspNet, o si vuole solo usare ApplicationInsightsLoggerProvider, senza alcun altro monitoraggio di Application Insights, seguire questa procedura.

1. installare il pacchetto nuget.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. modificare `Program.cs` come indicato di seguito

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
                // Providing an instrumentation key here is required if you are using
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

Il codice sopra riportato configurerà `ApplicationInsightsLoggerProvider`. Seguito è riportato un esempio di classe Controller, che usa `ILogger` per inviare i log, che vengono acquisiti da Application Insights.

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

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>Acquisire i log di ILogger da Startup.cs, Program.cs nelle applicazioni Asp.Net Core

Con il nuovo ApplicationInsightsLoggerProvider, è possibile acquisire log da nelle prime fasi pipeline di avvio dell'applicazione. Persino tuttavia ApplicationInsightsLoggerProvider viene abilitato automaticamente Application Insights (da 2.7.0-beta3 e versioni successive), non dispongono delle impostazione di chiave di strumentazione fino a quando non in un secondo momento nella pipeline, pertanto solo i log dal Controller / altre classi verranno acquisite. Acquisire tutti i log a partire `Program.cs` e `Startup.cs` stesso, uno è necessario abilitare in modo esplicito ApplicationInsightsLoggerProvider con una chiave di strumentazione. È anche importante notare che `TelemetryConfiguration` non completamente set quando registra un elemento da `Program.cs` o `Startup.cs` stesso, pertanto tali log userà una configurazione minima bare, che usa InMemoryChannel, nessun campionamento e nessun dato di telemetria standard gli inizializzatori o processori.

Seguenti vengono illustrati esempi di `Program.cs` e `Startup.cs` mediante questa funzionalità.

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
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
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

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>Eseguire la migrazione dal vecchio ApplicationInsightsLoggerProvider

Versioni del SDK Microsoft.ApplicationInsights.AspNet prima 2.7.0-beta2, supportato un provider di registrazione che è ora obsoleto. Questo provider è stato abilitato con `AddApplicationInsights()` metodo di estensione di `ILoggerFactory`. Questo provider è obsoleto e gli utenti vengono suggeriti per la migrazione al nuovo provider. Migrazione prevede due passaggi.

1. Rimuovere ILoggerFactory.AddApplicationInsights() chiamata da `Startup.Configure()` metodo per evitare la registrazione di double.
2. Riapplicare tutte le regole di filtro nel codice come non essere rispettati dal nuovo provider. Overload di ILoggerFactory.AddApplicationInsights() impiegato minimo LogLevel o filtro funzioni. Con il nuovo provider, il filtro fa parte del framework di registrazione e non è stato eseguito dal provider di Application Insights. Pertanto, tutti i filtri forniti tramite `ILoggerFactory.AddApplicationInsights()` overload deve essere rimosso e le regole di filtro devono essere fornita seguendo [questi](#control-logging-level) istruzioni. Se si usa `appsettings.json` per filtrare la registrazione, continueranno a funzionare con nuovo provider poiché utilizzano entrambi lo stesso Alias - Provider **ApplicationInsights**.

Mentre è ancora possibile utilizzare provider precedente (è ora obsoleta e verrà rimosso solo nella versione principale cambia in 3.xx), la migrazione al provider più recente è consigliabile i motivi seguenti.

1. Provider precedente supportava dei [ambiti](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Del nuovo provider, le proprietà dall'ambito vengono aggiunti automaticamente come proprietà personalizzate ai dati di telemetria raccolti.
2. A questo punto è possibile acquisire i log molto più indietro in pipeline di avvio dell'applicazione. vale a dire A questo punto è possibile acquisire i log dalle classi di programma e avvio.
3. Con il nuovo provider, il filtro viene applicato a livello di framework stesso. Filtro di log al provider di Application Insights può essere eseguito in esatta esattamente come per altri provider, inclusi i provider predefiniti, come Console di Debug e così via. È anche possibile applicare filtri stesso da più provider.
4. Il [consigliato](https://github.com/aspnet/Announcements/issues/255) modo in ASP.NET Core (2.0 o versione successiva) per abilitare i provider di registrazione è utilizzando i metodi di estensione su ILoggingBuilder in `Program.cs` stesso.

> [!Note]
> Il nuovo Provider è disponibile per le applicazioni destinate a `NETSTANDARD2.0` o versione successiva. Se l'applicazione è destinata a versioni precedenti di .NET Core, ad esempio .NET Core 1.1 o se la destinazione è .NET Framework, continuare a usare il vecchio provider.

## <a name="console-application"></a>Applicazione console

Il codice seguente illustra un'applicazione Console di esempio configurata per l'invio `ILogger` tracce in Application Insights.

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
        // Create DI container.
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

Nell'esempio precedente, il pacchetto autonomo `Microsoft.Extensions.Logging.ApplicationInsights` viene usato. Per impostazione predefinita, questa configurazione usa le impostazioni minime di `TelemetryConfiguration` per inviare i dati ad Application Insights. Per impostazioni minime si intende che il canale usato sarà `InMemoryChannel`, senza alcun campionamento e senza oggetti TelemetryInitializer standard. Questo comportamento può essere sostituito per un'applicazione console, come illustrato nell'esempio seguente.

Installare questo pacchetto aggiuntivo:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

La sezione seguente illustra come sostituire il valore predefinito `TelemetryConfiguration` tramite `services.Configure<TelemetryConfiguration>()` (metodo). Questo esempio configura `ServerTelemetryChannel`, il campionamento e aggiunge un oggetto personalizzato `ITelemetryInitializer` per il `TelemetryConfiguration`.

```csharp
    // Create DI container.
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

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Livello di registrazione di controllo

Asp.Net Core `ILogger` infra ha meccanismi integrati per applicare [filtro](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) dei log, che consente agli utenti di controllare i log inviati a ogni provider registrati, tra cui provider di Application Insights. Questo filtro può essere eseguito nella configurazione (in genere utilizzando `appsettings.json` file) o nel codice. Questa funzionalità viene fornita dal framework di se stesso e non è specifica di provider di Application Insights.

Esempi di applicazione delle regole di filtro per ApplicationInsightsLoggerProvider è indicato di seguito.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Creare regole di filtro nella configurazione con appSettings. JSON

Per ApplicationInsightsLoggerProvider, è l'alias di provider `ApplicationInsights`. La sezione mostrato nel seguente `appsettings.json` configura i log `Warning` e versioni successive di tutte le categorie, `Error` e versioni successive dalle categorie che iniziano con "Microsoft" da inviare al `ApplicationInsightsLoggerProvider`.

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

Il codice seguente frammento di codice configura i log `Warning` e versioni successive di tutte le categorie, `Error` e versioni successive di categorie a partire da 'Microsoft' da inviare al `ApplicationInsightsLoggerProvider`. Questa configurazione è quello utilizzato per la configurazione precedente richiede solo pochi `appsettings.json`.

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

*1. Che cos'è il vecchio e nuovo ApplicationInsightsLoggerProvider?*

* [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) fornito con un ApplicationInsightsLoggerProvider incorporati (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), che è stato abilitato tramite ILoggerFactory metodi di estensione. Questo provider è contrassegnato come obsoleto da 2.7.0-beta2 o versione successiva e verrà rimossa completamente dalla modifica di versione principale successiva. [Ciò](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pacchetto stesso non obsoleto ed è necessario per abilitare il monitoraggio delle richieste, dipendenze e così via.

* L'alternativa suggerita è il nuovo pacchetto autonomo [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), contenente un (ApplicationInsightsLoggerProvider migliorata Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) e metodi di estensione su ILoggerBuilder per abilitarlo.

* [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 versione e versioni successive richiederà una dipendenza del pacchetto precedente e abilita `ILogger` acquisire automaticamente.

*2. Vengono visualizzati alcuni `ILogger` i registri vengono visualizzati due volte in Application Insights?*

* Questa duplicazione è possibile se si dispone della versione (obsoleta) precedente di `ApplicationInsightsLoggerProvider` abilitato chiamando `AddApplicationInsights` sul `ILoggerFactory`. Controllare se il `Configure` metodo ha i seguenti elementi e di rimuoverla.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Se la registrazione doppia vengono riscontrati durante il debug da Visual Studio, quindi modificare il codice usato per abilitare Application Insights come indicato di seguito, impostando `EnableDebugLogger` su false. Questo problema di duplicazione e correzione è rilevante solo quando il debug dell'applicazione.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. Ho aggiornato a [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 versione che vengono ora visualizzati che registra da `ILogger` vengono acquisiti automaticamente. Come è possibile disattivare questa funzionalità completamente?*

* Visualizzare [ciò](../../azure-monitor/app/ilogger.md#control-logging-level) sezione per sapere come filtrare i log in generale. Disattiva ApplicationInsightsLoggerProvider usare `LogLevel.None` appositamente.

  Nel codice

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  Nel file di configurazione

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. Vengono visualizzati alcuni `ILogger` log non sono con le stesse proprietà degli altri?*

* Applicazione acquisizioni Insights e invia `ILogger` accede utilizzando lo stesso `TelemetryConfiguration` usato per tutti i dati di telemetria. Si verifica un'eccezione a questa regola. Il valore predefinito `TelemetryConfiguration` non completamente set quando registra un elemento da `Program.cs` oppure `Startup.cs` stesso, in modo che i log da queste posizioni non avrà la configurazione predefinita e di conseguenza non verranno eseguiti tutti i `TelemetryInitializer`s e `TelemetryProcessor`s.

*5. Utilizzo del pacchetto autonomo Microsoft.Extensions.Logging.ApplicationInsights e desidera registrare manualmente alcuni dati di telemetria personalizzati aggiuntivi. Modo in cui è necessario farlo?*

* Quando si usa il pacchetto autonomo `TelemetryClient` non viene inserito al contenitore di inserimento delle dipendenze, in modo che gli utenti dovranno creare una nuova istanza della `TelemetryClient` usando la stessa configurazione utilizzata dal provider di logger, come illustrato di seguito. Ciò garantisce che la stessa configurazione viene usata per tutti i dati di telemetria personalizzati, oltre a quelli acquisiti da ILogger.

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
> Si noti che, se il pacchetto Microsoft.ApplicationInsights.AspNetCore pacchetto viene usato per abilitare Application Insights, quindi l'esempio precedente deve essere modificato per ottenere `TelemetryClient` direttamente nel costruttore. Visualizzare [ciò](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions) , ad esempio completo.


*6. Quale tipo di dati di telemetria di Application Insights viene generato dal `ILogger` registri? o in cui è possibile vedere `ILogger` log in Application Insights?*

* Consente di acquisire ApplicationInsightsLoggerProvider `ILogger` accede e crea `TraceTelemetry` da quest'ultimo. Se un oggetto eccezione viene passato al metodo di log () su ILogger, quindi al posto di `TraceTelemetry`, un `ExceptionTelemetry` viene creato. Questi elementi di telemetria sono reperibili nelle stesse posizioni come qualsiasi altra `TraceTelemetry` o `ExceptionTelemetry` per Application Insights, inclusi portale, analitica o debugger di Visual Studio locale.
Se si vuole inviare sempre `TraceTelemetry`, quindi usare il frammento di codice ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```.

*7. Non è installato SDK, e utilizzo dell'estensione App Web Azure per abilitare Application Insights per le applicazioni Asp.Net Core. Come si usa il nuovo provider?*

* Estensione di Application Insights nell'App Web di Azure Usa il vecchio provider. Regole di filtro possono essere modificata in `appsettings.json` per l'applicazione. Se si desidera sfruttare i vantaggi del nuovo provider, utilizzare la strumentazione in fase di compilazione tramite l'aggiunta delle dipendenze di nuget di SDK. Questo documento verrà aggiornato quando l'estensione passa all'uso di provider di nuovo.

*8. Sono con il pacchetto autonomo Microsoft.Extensions.Logging.ApplicationInsights e l'abilitazione di provider di Application Insights dal generatore di chiamata. AddApplicationInsights("ikey"). È disponibile un'opzione per ottenere una chiave di strumentazione dalla configurazione?*


* Modificare `Program.cs` e `appsettings.json` come illustrato di seguito.

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

Sezione pertinente da `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

Il codice sopra riportato è obbligatorio solo quando si usa il provider di registrazione autonomo. Per il regolare monitoraggio di Application Insights, chiave di strumentazione viene caricata automaticamente dal percorso di configurazione `ApplicationInsights:Instrumentationkey` e `appsettings.json` dovrebbe essere simile di sotto.

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

* [Registrazione in Asp.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [I log di traccia .NET in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
