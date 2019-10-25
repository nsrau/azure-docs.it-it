---
title: Esplorare i log di traccia .NET in Azure Application Insights con ILogger
description: Esempi di uso del provider ILogger di applicazione Azure Insights con ASP.NET Core e applicazioni console.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0e93e2a98d96ca7f436f20e579ab625341024686
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819485"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider per i log ILogger di .NET Core

ASP.NET Core supporta un'API di registrazione che funziona con diversi tipi di provider di registrazione predefiniti e di terze parti. La registrazione viene eseguita chiamando **log ()** o una variante di questa per le istanze *ILogger* . Questo articolo illustra come usare *ApplicationInsightsLoggerProvider* per acquisire i log ILogger nelle applicazioni console e ASP.NET Core. Questo articolo descrive anche il modo in cui ApplicationInsightsLoggerProvider si integra con altri dati di telemetria Application Insights.
Per altre informazioni, vedere [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Applicazioni ASP.NET Core

ApplicationInsightsLoggerProvider è abilitato per impostazione predefinita in [Microsoft. ApplicationInsights. AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.0-beta3 (e versioni successive) quando si attiva il monitoraggio Application Insights regolare tramite uno dei metodi standard:
- Chiamando il metodo di estensione **UseApplicationInsights** in IWebHostBuilder 
- Chiamando il metodo di estensione **AddApplicationInsightsTelemetry** in IServiceCollection

ILogger registra che le acquisizioni di ApplicationInsightsLoggerProvider sono soggette alla stessa configurazione di tutti gli altri dati di telemetria raccolti. Hanno lo stesso set di TelemetryInitializers e TelemetryProcessors, usano lo stesso TelemetryChannel e sono correlati e campionati in modo analogo ad altri dati di telemetria. Se si usa la versione 2.7.0-beta3 o successiva, non è necessaria alcuna azione per acquisire i log di ILogger.

Per impostazione predefinita, vengono inviati solo i registri di ILogger di *avviso* o superiori (da tutte le categorie) a Application Insights. È tuttavia possibile [applicare filtri per modificare questo comportamento](#control-logging-level). Sono necessari passaggi aggiuntivi per acquisire i registri ILogger da **Program.cs** o **Startup.cs**. Vedere [acquisizione dei log ILogger da startup.cs e Program.cs in applicazioni ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).

Se si usa una versione precedente di Microsoft. ApplicationInsights. AspNet SDK oppure si vuole usare semplicemente ApplicationInsightsLoggerProvider senza altri Application Insights monitoraggio, attenersi alla procedura seguente:

1. Installare il pacchetto NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modificare **Program.cs** come mostrato di seguito:

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

Il codice nel passaggio 2 configura `ApplicationInsightsLoggerProvider`. Il codice seguente illustra una classe controller di esempio, che usa `ILogger` per inviare i log. I log vengono acquisiti da Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Acquisire log ILogger da Startup.cs e Program.cs in app ASP.NET Core

> [!NOTE]
> In ASP.NET Core 3,0 e versioni successive non è più possibile inserire `ILogger` in Startup.cs e in Program.cs. Per informazioni dettagliate, vedere https://github.com/aspnet/Announcements/issues/353.

Il nuovo ApplicationInsightsLoggerProvider può acquisire i log dall'inizio della pipeline di avvio dell'applicazione. Sebbene ApplicationInsightsLoggerProvider sia abilitato automaticamente in Application Insights (a partire dalla versione 2.7.0-beta3), non ha una chiave di strumentazione configurata fino a un momento successivo nella pipeline. Verranno acquisiti solo i log delle classi **controller**/altre. Per acquisire ogni log che inizia con **Program.cs** e **Startup.cs** , è necessario abilitare in modo esplicito una chiave di strumentazione per ApplicationInsightsLoggerProvider. Inoltre, *TelemetryConfiguration* non viene configurato completamente quando si esegue la registrazione da **Program.cs** o **Startup.cs** . Quindi, i log avranno una configurazione minima che usa InMemoryChannel, nessun campionamento né inizializzatori o processori di telemetria standard.

Gli esempi seguenti illustrano questa funzionalità con **Program.cs** e **Startup.cs**.

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

#### <a name="example-startupcs"></a>Startup.cs di esempio

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Eseguire la migrazione dalla ApplicationInsightsLoggerProvider precedente

Le versioni dell'SDK Microsoft. ApplicationInsights. AspNet precedenti a 2.7.0-beta2 supportano un provider di registrazione obsoleto. Questo provider è stato abilitato tramite il metodo di estensione **AddApplicationInsights ()** di ILoggerFactory. Si consiglia di eseguire la migrazione al nuovo provider, che prevede due passaggi:

1. Rimuovere la chiamata *ILoggerFactory. AddApplicationInsights ()* dal metodo **Startup. Configure ()** per evitare la doppia registrazione.
2. Riapplicare le regole di filtro nel codice, poiché non verranno rispettate dal nuovo provider. Gli overload di *ILoggerFactory. AddApplicationInsights ()* hanno richiesto LogLevel o funzioni di filtro minime. Con il nuovo provider, il filtro fa parte del Framework di registrazione. Non viene eseguita dal provider Application Insights. Pertanto, è necessario rimuovere tutti i filtri forniti tramite gli overload di *ILoggerFactory. AddApplicationInsights ()* . Le regole di filtro e devono essere fornite seguendo le istruzioni del [livello di registrazione del controllo](#control-logging-level) . Se si usa *appSettings. JSON* per filtrare la registrazione, continuerà a funzionare con il nuovo provider, perché entrambi usano lo stesso alias del provider, *ApplicationInsights*.

È comunque possibile usare il vecchio provider. (Verrà rimosso solo in una modifica di versione principale in 3. *XX*.) ma è consigliabile eseguire la migrazione al nuovo provider per i motivi seguenti:

- Il provider precedente non dispone del supporto per gli [ambiti di log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Nel nuovo provider, le proprietà dall'ambito vengono automaticamente aggiunte come proprietà personalizzate ai dati di telemetria raccolti.
- È ora possibile acquisire i log molto prima nella pipeline di avvio dell'applicazione. È ora possibile acquisire i log delle classi **Program** e **Startup** .
- Con il nuovo provider, il filtro viene eseguito a livello di Framework stesso. È possibile filtrare i log al provider Application Insights come per gli altri provider, inclusi i provider predefiniti come console, debug e così via. È anche possibile applicare gli stessi filtri a più provider.
- In ASP.NET Core (2,0 e versioni successive), il metodo consigliato per [abilitare i provider di registrazione](https://github.com/aspnet/Announcements/issues/255) consiste nell'usare i metodi di estensione in ILoggingBuilder in **Program.cs** stesso.

> [!Note]
> Il nuovo provider è disponibile per le applicazioni destinate a NETSTANDARD 2.0 o versioni successive. Se l'applicazione è destinata a versioni precedenti di .NET Core, ad esempio .NET Core 1,1 o se è destinata alla .NET Framework, continuare a usare il provider precedente.

## <a name="console-application"></a>Applicazione console

> [!NOTE]
> È disponibile un nuovo SDK di Application Insights beta denominato [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , che può essere usato per abilitare Application Insights (ILogger e altri dati di telemetria Application Insights) per qualsiasi applicazione console. È consigliabile usare questo pacchetto e le istruzioni associate da [qui](../../azure-monitor/app/worker-service.md).
L'esempio seguente sarà deprecato dopo il rilascio della versione stabile del nuovo pacchetto.

Il codice seguente illustra un'applicazione console di esempio configurata per l'invio di tracce ILogger a Application Insights.

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

In questo esempio viene usato il pacchetto autonomo `Microsoft.Extensions.Logging.ApplicationInsights`. Per impostazione predefinita, questa configurazione usa il TelemetryConfiguration "minimo" per inviare i dati a Application Insights. Il valore minimo indica che InMemoryChannel è il canale utilizzato. Non sono disponibili campionamenti e TelemetryInitializers standard. Questo comportamento può essere sostituito per un'applicazione console, come illustrato nell'esempio seguente.

Installare questo pacchetto aggiuntivo:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

La sezione seguente illustra come eseguire l'override del TelemetryConfiguration predefinito usando i **servizi. Configurare\<metodo TelemetryConfiguration > ()** . In questo esempio vengono impostati `ServerTelemetryChannel` e il campionamento. Aggiunge un ITelemetryInitializer personalizzato al TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Controllare il livello di registrazione

Il ASP.NET Core *ILogger* infra dispone di un meccanismo incorporato per applicare il [filtro dei log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). In questo modo è possibile controllare i log inviati a ogni provider registrato, incluso il provider di Application Insights. Il filtro può essere eseguito nella configurazione (in genere usando un file *appSettings. JSON* ) o nel codice. Questa funzionalità viene fornita dal Framework stesso. Non è specifico del provider Application Insights.

Negli esempi seguenti vengono applicate le regole di filtro a ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Creare regole di filtro nella configurazione con appSettings. JSON

Per ApplicationInsightsLoggerProvider, l'alias del provider viene `ApplicationInsights`. La sezione seguente di *appSettings. JSON* configura i log per l' *avviso* e il livello superiore da tutte le categorie e dagli *errori* e dalle categorie che iniziano con "Microsoft" da inviare a `ApplicationInsightsLoggerProvider`.

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

Il frammento di codice seguente configura i log per l' *avviso* e il livello superiore da tutte le categorie e per gli *errori* e le versioni precedenti dalle categorie che iniziano con "Microsoft" da inviare a `ApplicationInsightsLoggerProvider`. Questa configurazione è identica a quella della sezione precedente in *appSettings. JSON*.

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

[Microsoft. ApplicationInsights. AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) include un ApplicationInsightsLoggerProvider incorporato (Microsoft. ApplicationInsights. AspNetCore. Logging. ApplicationInsightsLoggerProvider), che è stato abilitato tramite **ILoggerFactory** metodi di estensione. Questo provider è contrassegnato come obsoleto dalla versione 2.7.0-beta2. Verrà rimossa completamente nella successiva modifica della versione principale. Il pacchetto [Microsoft. ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) non è obsoleto. È necessario abilitare il monitoraggio delle richieste, delle dipendenze e così via.

L'alternativa consigliata è il nuovo pacchetto autonomo [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), che contiene un ApplicationInsightsLoggerProvider migliorato ( Microsoft. Extensions. Logging. ApplicationInsights. ApplicationInsightsLoggerProvider) e i metodi di estensione su ILoggerBuilder per abilitarlo.

[Microsoft. ApplicationInsights. AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.0-beta3 acquisisce una dipendenza dal nuovo pacchetto e Abilita automaticamente l'acquisizione di ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Perché alcuni log ILogger sono visualizzati due volte in Application Insights?

La duplicazione può verificarsi se la versione precedente (ora obsoleta) di ApplicationInsightsLoggerProvider è abilitata chiamando `AddApplicationInsights` su `ILoggerFactory`. Controllare se il metodo **Configure** presenta quanto segue e rimuoverlo:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se si verifica una doppia registrazione quando si esegue il debug da Visual Studio, impostare `EnableDebugLogger` su *false* nel codice che Abilita Application Insights, come indicato di seguito. Questa duplicazione e correzione è pertinente solo quando si esegue il debug dell'applicazione.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Ho aggiornato a [Microsoft. ApplicationInsights. AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.7.0-beta3 e i log da ILogger vengono acquisiti automaticamente. Ricerca per categorie disattivare completamente questa funzionalità?

Vedere la sezione [controllo del livello di registrazione](../../azure-monitor/app/ilogger.md#control-logging-level) per vedere come filtrare i log in generale. Per disattivare ApplicationInsightsLoggerProvider, usare `LogLevel.None`:

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

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Perché alcuni log ILogger non hanno le stesse proprietà degli altri?

Application Insights acquisisce e invia i log ILogger usando lo stesso TelemetryConfiguration usato per tutti gli altri dati di telemetria. Ma è presente un'eccezione. Per impostazione predefinita, TelemetryConfiguration non viene configurato completamente quando si esegue la registrazione da **Program.cs** o **Startup.cs**. I log da queste posizioni non avranno la configurazione predefinita, quindi non eseguiranno tutti TelemetryInitializers e TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Sto usando il pacchetto autonomo Microsoft. Extensions. Logging. ApplicationInsights e desidero registrare manualmente alcuni dati di telemetria personalizzati aggiuntivi. Come procedere?

Quando si usa il pacchetto autonomo, `TelemetryClient` non viene inserito nel contenitore DI inserimento delle dipendenze, pertanto è necessario creare una nuova istanza di `TelemetryClient` e usare la stessa configurazione utilizzata dal provider del logger, come illustrato nel codice seguente. In questo modo si garantisce che venga usata la stessa configurazione per tutti i dati di telemetria personalizzati e i dati di telemetria di ILogger.

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
> Se si usa il pacchetto Microsoft. ApplicationInsights. AspNetCore per abilitare Application Insights, modificare questo codice per ottenere `TelemetryClient` direttamente nel costruttore. Per un esempio, vedere le [domande frequenti](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Quali Application Insights tipo di telemetria viene prodotto dai log ILogger? O dove è possibile visualizzare I log di ILogger in Application Insights?

ApplicationInsightsLoggerProvider acquisisce i log ILogger e crea TraceTelemetry da essi. Se un oggetto eccezione viene passato al metodo **log ()** in ILogger, viene creato *ExceptionTelemetry* anziché TraceTelemetry. Questi elementi di telemetria si trovano nello stesso luogo di qualsiasi altro TraceTelemetry o ExceptionTelemetry per Application Insights, tra cui il portale, l'analisi o il debugger locale di Visual Studio.

Se si preferisce inviare sempre TraceTelemetry, usare questo frammento di codice: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>L'SDK non è installato e si usa l'estensione app Web di Azure per abilitare Application Insights per le applicazioni ASP.NET Core. Ricerca per categorie utilizzare il nuovo provider? 

L'estensione Application Insights in app Web di Azure usa il vecchio provider. È possibile modificare le regole di filtro nel file *appSettings. JSON* per l'applicazione. Per sfruttare i vantaggi del nuovo provider, usare la strumentazione in fase di compilazione prendendo una dipendenza NuGet dall'SDK. Questo articolo verrà aggiornato quando l'estensione passa a usare il nuovo provider.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Sto usando il pacchetto autonomo Microsoft. Extensions. Logging. ApplicationInsights e abilitando Application Insights provider chiamando il **generatore. AddApplicationInsights ("iKey")** . È disponibile un'opzione per ottenere una chiave di strumentazione dalla configurazione?


Modificare Program.cs e AppSettings. JSON come indicato di seguito:

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

Questo codice è necessario solo quando si usa un provider di registrazione autonomo. Per il monitoraggio di Application Insights regolari, la chiave di strumentazione viene caricata automaticamente dal percorso di configurazione *ApplicationInsights: Instrumentationkey*. AppSettings. JSON dovrebbe essere simile al seguente:

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

* [Accesso ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Log di traccia .NET in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
