---
title: Esplorare i log di traccia .NET in Azure Application Insights con ILogger
description: Esempi d'uso dell'implementazione di ILogger di Azure Application Insights con applicazioni console e ASP.NET Core.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 4c385d2af0d9e4e213cd690b3c30d8719588220d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58399506"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core supporta un'API di registrazione che funziona con un'ampia gamma di provider di registrazione predefiniti e di terze parti. Questo articolo illustra come gestire la registrazione con l'implementazione di ILogger di Application Insights nelle applicazioni console e ASP.NET Core. Per altre informazioni sula registrazione basata su ILogger, vedere [questo articolo](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="console-application"></a>Applicazione console

Di seguito è illustrata un'applicazione console di esempio configurata per inviare tracce di ILogger ad Application Insights.

Pacchetti installati:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
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
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
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

## <a name="aspnet-core-application"></a>Applicazione ASP.NET Core

Di seguito è illustrata un'applicazione ASP.NET Core di esempio configurata per inviare tracce di ILogger ad Application Insights. È possibile seguire questo esempio per inviare tracce di ILogger da Program.cs, Startup.cs o qualsiasi altra logica contoller/applicazione.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(logging =>
        {
            logging.AddApplicationInsights("ikeyhere");

            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);

            // Additional filtering For category starting in "Microsoft",
            // only Warning or above will be sent to Application Insights.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

        // The following will be picked up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
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

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
            _logger.LogInformation("An example of a Information trace..");
            _logger.LogWarning("An example of a Warning trace..");
            _logger.LogTrace("An example of a Trace level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

In entrambi gli esempi precedenti viene usato il pacchetto autonomo Microsoft.Extensions.Logging.ApplicationInsights. Per impostazione predefinita, questa configurazione usa le impostazioni minime di `TelemetryConfiguration` per inviare i dati ad Application Insights. Per impostazioni minime si intende che il canale usato sarà `InMemoryChannel`, senza alcun campionamento e senza oggetti TelemetryInitializer standard. Questo comportamento può essere sostituito per un'applicazione console, come illustrato nell'esempio seguente.

Installare questo pacchetto aggiuntivo:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

La sezione seguente illustra come sostituire l'oggetto `TelemetryConfiguration` predefinito. Questo esempio configura `ServerTelemetryChannel`, il campionamento e un oggetto `ITelemetryInitializer` personalizzato.

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

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
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

Mentre l'approccio precedente può essere utilizzato anche un'applicazione ASP.NET Core, un approccio più comune, è possibile combinare applicazione normale monitoraggio (richieste, dipendenze e così via) con l'acquisizione di ILogger, come illustrato di seguito.

Installare questo pacchetto aggiuntivo:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

Aggiungere quanto segue al metodo `ConfigureServices`. Questo codice consente il normale monitoraggio dell'applicazione con la configurazione predefinita (ServerTelemetryChannel, LiveMetrics, richiesta/dipendenze, correlazione e così via.)

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

In questo esempio la configurazione usata da `ApplicationInsightsLoggerProvider` è uguale a quella usata per il normale monitoraggio dell'applicazione. Di conseguenza, entrambe le tracce di `ILogger` e altri dati di telemetria (richieste, dipendenze e così via) eseguiranno lo stesso set di oggetti `TelemetryInitializers`, `TelemetryProcessors` e `TelemetryChannel`. La correlazione e il campionamento/mancato campionamento avverranno nello stesso modo.

C'è tuttavia un'eccezione a questo comportamento. L'oggetto `TelemetryConfiguration` predefinito non è completamente configurato quando si esegue la registrazione da `Program.cs` o `Startup.cs`, quindi questi log non avranno la configurazione predefinita. Tuttavia, ogni altro log (ad esempio i log di controller, modelli e così via) condividerà questa configurazione.

## <a name="control-logging-level"></a>Livello di registrazione di controllo

Oltre a filtrare i log nel codice come illustrato negli esempi precedenti, è anche possibile controllare il livello di registrazione di Application Insights consente di acquisire, modificando il `appsettings.json`. Il [registrazione documentazione nozioni fondamentali su ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) viene illustrato come ottenere questo risultato. In particolare per Application Insights, è il nome dell'alias di provider `ApplicationInsights`, come illustrato nel seguente esempio che consentono di configurare `ApplicationInsights` acquisire solo i log di `Warning` e versioni successive di tutte le categorie.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## <a name="frequently-asked-questions"></a>Domande frequenti

*Vengono visualizzati che alcuni log ILogger vengono visualizzati due volte in Application Insights?*

* Ciò è possibile se si dispone della versione (obsoleta) precedente di `ApplicationInsightsLoggerProvider` abilitato chiamando `AddApplicationInsights` sul `ILoggerFactory`. Controllare se il `Configure` metodo ha i seguenti elementi e di rimuoverla.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Se si verificano double registrazione durante il debug da Visual Studio, quindi modificare il codice usato per abilitare Application Insights come indicato di seguito, impostando `EnableDebugLogger` su false. Ciò è rilevante solo durante il debug dell'applicazione.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```



## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Registrazione basata su ILogger](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [Log di traccia .NET](../../azure-monitor/app/asp-net-trace-logs.md)
