---
title: Esplorare i log di traccia di .NET con ILogger - Azure Application InsightsExplore .NET trace logs with ILogger - Azure Application Insights
description: Esempi di utilizzo del provider di ILogger di Azure Application Insights con ASP.NET applicazioni di base e console.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f40c1c1a8ee7f20c769a62e9746da43face4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276377"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider per i log iLogger di .NET Core

ASP.NET Core supporta un'API di registrazione che funziona con diversi tipi di provider di registrazione incorporati e di terze parti. La registrazione viene eseguita chiamando **Log()** o una variante di esso su istanze *ILogger.* Questo articolo illustra come usare ApplicationInsightsLoggerProvider per acquisire i log ILogger nelle applicazioni console e ASP.NET Core.This article demonstrates how to use *ApplicationInsightsLoggerProvider* to capture ILogger logs in console and ASP.NET Core applications. Questo articolo descrive anche come ApplicationInsightsLoggerProvider si integra con altri dati di telemetria di Application Insights.This article also describes how ApplicationInsightsLoggerProvider integrates with other Application Insights telemetry.
Per altre informazioni, vedere [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>applicazioni ASP.NET Core

ApplicationInsightsLoggerProvider è abilitato per impostazione predefinita in [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.1 (e successive) quando si attiva il monitoraggio regolare di Application Insights tramite uno dei metodi seguenti:

- Chiamando il **useApplicationInsights** metodo di estensione su IWebHostBuilder (ora obsoleto)
- Chiamando il metodo di estensione **AddApplicationInsightsTelemetry** su IServiceCollection

ILogger registra che le acquisizioni ApplicationInsightsLoggerProvider sono soggette alla stessa configurazione di qualsiasi altro livello di telemetria raccolto. Hanno lo stesso set di TelemetryInitializers e TelemetryProcessors, usano lo stesso TelemetryChannel e sono correlati e campionati allo stesso modo di altri dati di telemetria. Se si utilizza la versione 2.7.1 o successiva, non è necessaria alcuna azione per acquisire i registri ILogger.

Solo i log ILogger *di avviso* o superiori (da tutte le [categorie](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)) vengono inviati ad Application Insights per impostazione predefinita. È tuttavia possibile [applicare filtri per modificare questo comportamento.](#control-logging-level) Sono necessari passaggi aggiuntivi per acquisire i log di ILogger da **Program.cs** o **Startup.cs**. Consultate [Acquisire log di ILogger da Startup.cs e Program.cs nelle applicazioni ASP.NET Core.](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)

Se si utilizza una versione precedente di Microsoft.ApplicationInsights.AspNet SDK o si desidera utilizzare solo ApplicationInsightsLoggerProvider senza altri monitoraggio di Application Insights, utilizzare la procedura seguente:

1. Installare il pacchetto NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modificare Program.cs come illustrato di seguito:Modify **Program.cs** as shown here:

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

Il codice nel passaggio 2 configura `ApplicationInsightsLoggerProvider`. Il codice seguente mostra un esempio `ILogger` di classe Controller, che usa per inviare i log. I log vengono acquisiti da Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Acquisire log di ILogger da Startup.cs e Program.cs nelle app ASP.NET CoreCapture ILogger logs from Startup.cs and Program.cs in ASP.NET Core apps

> [!NOTE]
> In ASP.NET Core 3.0 e versioni successive, `ILogger` non è più possibile iniettare Startup.cs e Program.cs. Per informazioni dettagliate, vedere https://github.com/aspnet/Announcements/issues/353.

Il nuovo ApplicationInsightsLoggerProvider è in grado di acquisire i log nelle prime fasi della pipeline di avvio dell'applicazione. Anche se ApplicationInsightsLoggerProvider viene abilitato automaticamente in Application Insights (a partire dalla versione 2.7.1), non dispone di una chiave di strumentazione impostata fino a un secondo momento nella pipeline. Pertanto, verranno acquisiti solo i log da **Controller**/altre classi. Per acquisire ogni log che inizia con **Program.cs** e **Startup.cs** stesso, è necessario abilitare in modo esplicito una chiave di strumentazione per ApplicationInsightsLoggerProvider.To capture every log starting with Program.cs and Startup.cs itself, you must explicitly enable an instrumentation key for ApplicationInsightsLoggerProvider. Inoltre, *TelemetryConfiguration* non è completamente impostato quando si accede da **Program.cs** o **Startup.cs** stesso. Pertanto, tali log avranno una configurazione minima che utilizza InMemoryChannel, nessun campionamento e nessun inizializzatori o processori di telemetria standard.

Negli esempi seguenti viene illustrata questa funzionalità con **Program.cs** e **Startup.cs**.

#### <a name="example-programcs"></a>Program.cs di esempio

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

#### <a name="example-startupcs"></a>Esempio di Startup.cs

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Eseguire la migrazione dal provider di applicazioni applicationInsightsLoggerProvider precedenteMigrate from the old ApplicationInsightsLoggerProvider

Le versioni di Microsoft.ApplicationInsights.AspNet SDK precedenti alla 2.7.1 supportavano un provider di registrazione obsoleto. Questo provider è stato abilitato tramite il metodo di estensione **AddApplicationInsights()** di ILoggerFactory. È consigliabile eseguire la migrazione al nuovo provider, che prevede due passaggi:We recommend that you migrate to the new provider, which involves two steps:

1. Rimuovere la chiamata *ILoggerFactory.AddApplicationInsights()* dal metodo **Startup.Configure()** per evitare la doppia registrazione.
2. Riapplicare le regole di filtro nel codice, perché non verranno rispettate dal nuovo provider. Gli overload di *ILoggerFactory.AddApplicationInsights()* hanno richiesto funzioni di filtro o LogLevel minime. Con il nuovo provider, il filtro fa parte del framework di registrazione stesso. Non viene eseguita dal provider di Application Insights.It's not done by the Application Insights provider. Pertanto, tutti i filtri forniti tramite gli overload *ILoggerFactory.AddApplicationInsights()* devono essere rimossi. E le regole di filtro devono essere fornite seguendo le istruzioni del livello di [registrazione di controllo.](#control-logging-level) Se si utilizza *appsettings.json* per filtrare la registrazione, continuerà a funzionare con il nuovo provider, perché entrambi utilizzano lo stesso alias del provider, *ApplicationInsights*.

È comunque possibile utilizzare il provider precedente. (Sarà rimosso solo in una versione principale cambia a 3. *xx*.) Ma è consigliabile eseguire la migrazione al nuovo provider per i motivi seguenti:

- Il provider precedente non supporta gli ambiti di [log.](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes) Nel nuovo provider, le proprietà dell'ambito vengono aggiunte automaticamente come proprietà personalizzate ai dati di telemetria raccolti.
- I log possono ora essere acquisiti molto prima nella pipeline di avvio dell'applicazione. È ora possibile acquisire i log delle classi **Program** e **Startup.**
- Con il nuovo provider, il filtro viene eseguito a livello di provider stesso. È possibile filtrare i log nel provider di Application Insights come per altri provider, inclusi i provider incorporati come Console, Debug e così via. È inoltre possibile applicare gli stessi filtri a più provider.
- In ASP.NET Core (2.0 e versioni successive), il modo consigliato per [abilitare i provider](https://github.com/aspnet/Announcements/issues/255) di registrazione consiste nell'usare i metodi di estensione in ILoggingBuilder nel **Program.cs** stesso.

> [!Note]
> Il nuovo provider è disponibile per le applicazioni destinate a NETSTANDARD2.0 o versione successiva. Se l'applicazione è destinata a versioni precedenti di .NET Core, ad esempio .NET Core 1.1, o se è destinata a .NET Framework, continuare a usare il provider precedente.

## <a name="console-application"></a>Applicazione console

> [!NOTE]
> È disponibile un nuovo Application Insights SDK denominato [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) che può essere usato per abilitare Application Insights (ILogger e altri dati di telemetria di Application Insights) per tutte le applicazioni console. Si consiglia di utilizzare questo pacchetto e le istruzioni associate da [qui](../../azure-monitor/app/worker-service.md).

Il codice seguente mostra un'applicazione console di esempio configurata per inviare le tracce di ILogger ad Application Insights.The following code shows a sample console application that's configured to send ILogger traces to Application Insights.

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

In questo esempio viene `Microsoft.Extensions.Logging.ApplicationInsights`utilizzato il pacchetto autonomo . Per impostazione predefinita, questa configurazione usa la configurazione TelemetryConfiguration "minima" per l'invio di dati ad Application Insights.By default, this configuration uses the "bare minimum" TelemetryConfiguration for sending data to Application Insights. Minimo minimo significa che InMemoryChannel è il canale utilizzato. Non è disponibile alcun campionamento e non è disponibile TelemetryInitializers standard. Questo comportamento può essere sottoposto a override per un'applicazione console, come illustrato nell'esempio seguente.

Installare questo pacchetto aggiuntivo:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

Nella sezione seguente viene illustrato come eseguire l'override della configurazione di telemetria predefinita usando i **servizi. Configurare\<il metodo TelemetryConfiguration>().** Questo esempio `ServerTelemetryChannel` imposta e campiona. Aggiunge un oggetto personalizzato ITelemetryInitializer alla TelemetryConfiguration.It adds a custom ITelemetryInitializer to the TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Controllare il livello di registrazioneControl logging level

L'infrastruttura *di ilogger* di ASP.NET Core dispone di un meccanismo incorporato per applicare il [filtro dei log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). In questo modo è possibile controllare i log inviati a ogni provider registrato, incluso il provider di Application Insights. Il filtro può essere eseguito nella configurazione (in genere tramite un file *appsettings.json)* o nel codice. Questa struttura è fornita dal framework stesso. Non è specifico del provider di Application Insights.

Gli esempi seguenti applicano le regole di filtro a ApplicationInsightsLoggerProvider.The following examples apply filter rules to ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Creare regole di filtro nella configurazione con appsettings.jsonCreate filter rules in configuration with appsettings.json

Per ApplicationInsightsLoggerProvider, l'alias del provider è `ApplicationInsights`. La sezione seguente di *appsettings.json* indica ai provider di registrazione in genere di registrare a livello *Warning* e superiore. Viene quindi eseguito l'override di `ApplicationInsightsLoggerProvider` per registrare le categorie che iniziano con "Microsoft" al livello *Error* e oltre.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Creare regole di filtro nel codiceCreate filter rules in code

Il frammento di codice seguente configura i log per *Avviso* e versioni successive da tutte `ApplicationInsightsLoggerProvider`le categorie e per *Error* e versioni successive da categorie che iniziano con "Microsoft" da inviare a . Questa configurazione è la stessa della sezione precedente in *appsettings.json*.

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

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) include va in modo che ApplicationInsightsLoggerProvider incorporato (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider) sia abilitato tramite i metodi di estensione **ILoggerFactory.** Questo provider è contrassegnato come obsoleto dalla versione 2.7.1.This provider is marked obsolete from version 2.7.1. Verrà rimosso completamente nella successiva modifica della versione principale. Il pacchetto [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) non è obsoleto. È necessario per abilitare il monitoraggio di richieste, dipendenze e così via.

L'alternativa consigliata è il nuovo pacchetto autonomo [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), che contiene un ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) migliorato e metodi di estensione in ILoggerBuilder per abilitarlo.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.1 accetta una dipendenza dal nuovo pacchetto e abilita automaticamente l'acquisizione di ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Perché alcuni log di ILogger vengono visualizzati due volte in Application Insights?

La duplicazione può verificarsi se è abilitata la versione precedente (ora obsoleta) di ApplicationInsightsLoggerProvider chiamando `AddApplicationInsights` su `ILoggerFactory`. Verificare se il metodo **Configure** dispone di quanto segue e rimuoverlo:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se si verifica la doppia registrazione quando `EnableDebugLogger` si esegue il debug da Visual Studio, impostare *su false* nel codice che abilita Application Insights, come indicato di seguito. Questa duplicazione e correzione è rilevante solo quando si esegue il debug dell'applicazione.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Ho aggiornato [a Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.1 e i log di ILogger vengono acquisiti automaticamente. Come faccio a disattivare completamente questa funzione?

Vedere la sezione Controllare il livello di [registrazione](../../azure-monitor/app/ilogger.md#control-logging-level) per vedere come filtrare i log in generale. Per disattivare ApplicationInsightsLoggerProvider, `LogLevel.None`usare:

**Nel codice:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**In configurazione:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Perché alcuni registri ILogger non hanno le stesse proprietà di altri?

Application Insights acquisisce e invia i log ILogger usando la stessa TelemetryConfiguration usata per ogni altro livello di telemetria. Ma c'è un'eccezione. Per impostazione predefinita, TelemetryConfiguration non è completamente impostato quando si accede da **Program.cs** o **Startup.cs**. I log di queste posizioni non avranno la configurazione predefinita, pertanto non eseguiranno tutti i TelemetryInitializers e TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Sto usando il pacchetto autonomo Microsoft.Extensions.Logging.ApplicationInsights, e voglio registrare manualmente alcuni dati di telemetria personalizzati aggiuntivi. Come dovrei farlo?

Quando si utilizza il `TelemetryClient` pacchetto autonomo, non viene inserito nel contenitore DI, pertanto è necessario creare una nuova istanza di `TelemetryClient` e utilizzare la stessa configurazione utilizzata dal provider del logger, come illustrato nel codice seguente. In questo modo si garantisce che la stessa configurazione venga usata per tutti i dati di telemetria personalizzati e per i dati di telemetria di ILogger.This ensures that the same configuration is used for all custom telemetry as well as telemetry from ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Se si utilizza il pacchetto Microsoft.ApplicationInsights.AspNetCore per abilitare `TelemetryClient` Application Insights, modificare questo codice per ottenere direttamente nel costruttore. Per un esempio, vedere [questa sezione DOMANDE frequenti](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Quale tipo di telemetria di Application Insights viene prodotto dai log di ILogger? In caso' di posizione in cui è possibile visualizzare i log di ILogger in Application Insights?

ApplicationInsightsLoggerProvider acquisisce i log di ILogger e crea TraceTelemetry da essi. Se un oggetto Exception viene passato al metodo **Log()** in ILogger, viene creato *ExceptionTelemetry* anziché TraceTelemetry. Questi elementi di telemetria possono essere trovati nelle stesse posizioni di qualsiasi altro TraceTelemetry o ExceptionTelemetry per Application Insights, tra cui portale, analisi o debugger locale di Visual Studio.These telemetry items can be found in same locations as any other TraceTelemetry or ExceptionTelemetry for Application Insights, including portal, analytics, or Visual Studio local debugger.

Se si preferisce inviare sempre TraceTelemetry, usare questo frammento di codice:If you prefer to always send TraceTelemetry, use this snippet:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Non è installato l'SDK e si usa l'estensione App Web di Azure per abilitare Application Insights per le applicazioni ASP.NET Core. Come si usa il nuovo provider? 

L'estensione Application Insights in App Web di Azure usa il nuovo provider. È possibile modificare le regole di filtro nel file *appsettings.json* per l'applicazione.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Sto usando il pacchetto autonomo Microsoft.Extensions.Logging.ApplicationInsights e abilitando il provider di Application Insights chiamando **il generatore. AddApplicationInsights("ikey")**. È disponibile un'opzione per ottenere una chiave di strumentazione dalla configurazione?


Modificare Program.cs e appsettings.json come segue:

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

Questo codice è necessario solo quando si utilizza un provider di registrazione autonomo. Per il normale monitoraggio di Application Insights, la chiave di strumentazione viene caricata automaticamente dal percorso di configurazione *ApplicationInsights: Instrumentationkey*. Appsettings.json dovrebbe essere simile al seguente:Appsettings.json should look like this:

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
* [Log di traccia .NET in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
