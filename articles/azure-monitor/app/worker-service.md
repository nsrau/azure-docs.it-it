---
title: Application Insights per le app del servizio Worker (app non HTTP) | Microsoft Docs
description: Monitoraggio di app .NET Core/. NET Framework non HTTP con Application Insights.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: cithomas
ms.openlocfilehash: 2185f5b0c4148e643e90741235054fd06fdbb151
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174608"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights per le applicazioni del servizio Worker (applicazioni non HTTP)

Application Insights sta rilasciando un nuovo SDK, denominato `Microsoft.ApplicationInsights.WorkerService`, ideale per carichi di lavoro non HTTP come la messaggistica, le attività in background, le applicazioni console e così via. Questi tipi di applicazioni non hanno la nozione di richiesta HTTP in ingresso come un'applicazione Web ASP.NET/ASP.NET Core tradizionale e pertanto l'uso di pacchetti di Application Insights per [ASP.NET](asp-net.md) o le applicazioni [ASP.NET Core](asp-net-core.md) non è supportato.

Il nuovo SDK non esegue alcuna raccolta di dati di telemetria. Viene invece riportata in altri agenti di raccolta automatici Application Insights come [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) e così via. Questo SDK espone i metodi di estensione su `IServiceCollection` per abilitare e configurare la raccolta di dati di telemetria.

## <a name="supported-scenarios"></a>Scenari supportati

Il [Application Insights SDK per il servizio Worker](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) è ideale per le applicazioni non http, indipendentemente da dove o come vengono eseguite. Se l'applicazione è in esecuzione e ha la connettività di rete ad Azure, è possibile raccogliere i dati di telemetria. Il monitoraggio Application Insights è supportato ovunque sia supportato .NET Core. Questo pacchetto può essere usato nel servizio ruolo di [lavoro .net core 3,0](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)appena introdotto, [attività in background in ASP.NET Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), app console (.NET Core/.NET Framework) e così via.

## <a name="prerequisites"></a>Prerequisiti

Chiave di strumentazione Application Insights valida. Questa chiave è necessaria per inviare i dati di telemetria a Application Insights. Se è necessario creare una nuova risorsa Application Insights per ottenere una chiave di strumentazione, vedere [creare una risorsa di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Uso di Application Insights SDK per i servizi del ruolo di lavoro

1. Installare il pacchetto [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nell'applicazione.
   Il frammento di codice seguente mostra le modifiche che devono essere aggiunte al file `.csproj` del progetto.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.8.0" />
    </ItemGroup>
```

1. Chiamare il metodo di estensione `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` su `IServiceCollection`, specificando la chiave di strumentazione. Questo metodo deve essere chiamato all'inizio dell'applicazione. Il percorso esatto dipende dal tipo di applicazione.

1. Recuperare un'istanza `ILogger` o `TelemetryClient` dal contenitore di inserimento delle dipendenze chiamando `serviceProvider.GetRequiredService<TelemetryClient>();` o usando l'inserimento del costruttore. Questo passaggio attiverà la configurazione dei moduli `TelemetryConfiguration` e della raccolta automatica.

Nelle sezioni seguenti vengono descritte le istruzioni specifiche per ogni tipo di applicazione.

## <a name="net-core-30-worker-service-application"></a>Applicazione del servizio ruolo di lavoro .NET Core 3,0

L'esempio completo è condiviso [qui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Scaricare e installare [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Creare un nuovo progetto di servizio di lavoro usando il nuovo modello di progetto o la riga di comando di Visual Studio `dotnet new worker`
3. Installare il pacchetto [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nell'applicazione.

4. Aggiungere `services.AddApplicationInsightsTelemetryWorkerService();` al metodo `CreateHostBuilder()` nella classe `Program.cs`, come nell'esempio seguente:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Modificare il `Worker.cs` come nell'esempio riportato di seguito.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Configurare la chiave di strumentazione.

    Sebbene sia possibile fornire la chiave di strumentazione come argomento a `AddApplicationInsightsTelemetryWorkerService`, è consigliabile specificare la chiave di strumentazione nella configurazione. Nell'esempio di codice seguente viene illustrato come specificare una chiave di strumentazione in `appsettings.json`. Assicurarsi che `appsettings.json` venga copiato nella cartella radice dell'applicazione durante la pubblicazione.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

In alternativa, specificare la chiave di strumentazione in una delle variabili di ambiente seguenti.
`APPINSIGHTS_INSTRUMENTATIONKEY` o `ApplicationInsights:InstrumentationKey`

Ad esempio: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
O `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

In genere, `APPINSIGHTS_INSTRUMENTATIONKEY` specifica la chiave di strumentazione per le applicazioni distribuite nelle app Web come processi Web.

> [!NOTE]
> Una chiave di strumentazione specificata nel codice prevale sulla variabile di ambiente `APPINSIGHTS_INSTRUMENTATIONKEY`, che prevale su altre opzioni.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Core attività in background con servizi ospitati
In [questo](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) documento viene descritto come creare attività di background nell'applicazione ASP.NET Core 2.1/2.2.

L'esempio completo è condiviso [qui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Installare il pacchetto Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nell'applicazione.
2. Aggiungere `services.AddApplicationInsightsTelemetryWorkerService();` al metodo `ConfigureServices()`, come nell'esempio seguente:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Di seguito è riportato il codice per `TimedHostedService` in cui risiede la logica dell'attività in background.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Configurare la chiave di strumentazione.
   Usare lo stesso `appsettings.json` dall'esempio di servizio di lavoro .NET Core 3,0 precedente.

## <a name="net-corenet-framework-console-application"></a>Applicazione console .NET Core/. NET Framework

Come indicato all'inizio di questo articolo, è possibile usare il nuovo pacchetto per abilitare Application Insights Telemetry anche da un'applicazione console normale. Questo pacchetto è destinato a [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)e può quindi essere usato per le app console in .net core 2,0 o versione successiva e .NET Framework 4.7.2 o versione successiva.

L'esempio completo è condiviso [qui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Installare il pacchetto Microsoft. ApplicationInsights. WorkerService (https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nell'applicazione.

2. Modificare Program.cs come riportato di seguito.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key must be specified here.
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Questa applicazione console usa anche lo stesso valore predefinito `TelemetryConfiguration` e può essere personalizzato nello stesso modo degli esempi nella sezione precedente.

## <a name="run-your-application"></a>Eseguire l'applicazione

Eseguire l'applicazione. I ruoli di lavoro di esempio di tutti gli esempi precedenti effettuano una chiamata http ogni secondo a bing.com e generano anche alcuni log con ILogger. Queste righe vengono incapsulate all'interno della chiamata `StartOperation` di `TelemetryClient`, che viene usata per creare un'operazione (in questo esempio `RequestTelemetry` denominato "Operation"). Application Insights raccoglierà i log ILogger (avviso o superiore per impostazione predefinita) e le dipendenze e verranno correlati alla relazione `RequestTelemetry` con la relazione padre-figlio. La correlazione funziona anche tra processo/limite di rete. Ad esempio, se la chiamata è stata effettuata a un altro componente monitorato, sarà correlato anche a questo elemento padre.

Questa operazione personalizzata di `RequestTelemetry` può essere considerata come l'equivalente di una richiesta Web in ingresso in una tipica applicazione Web. Sebbene non sia necessario usare un'operazione, si adatta al modello di dati di [correlazione Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/correlation) , con `RequestTelemetry` che funge da operazione padre e ogni telemetria generata all'interno dell'iterazione di lavoro considerata come appartenente logicamente per la stessa operazione. Questo approccio assicura anche che tutti i dati di telemetria generati (automatici e manuali) avranno lo stesso `operation_id`. Poiché il campionamento è basato su `operation_id`, l'algoritmo di campionamento mantiene o Elimina tutti i dati di telemetria da un'unica iterazione.

Di seguito sono elencati i dati di telemetria completi raccolti automaticamente da Application Insights.

### <a name="live-metrics"></a>Metriche attive

È possibile usare le metriche in tempo [reale](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) per verificare rapidamente se Application Insights il monitoraggio è configurato correttamente. Sebbene potrebbero essere necessari alcuni minuti prima che i dati di telemetria inizino a essere visualizzati nel portale e nelle analisi, le metriche attive indicheranno l'utilizzo della CPU del processo in esecuzione quasi in tempo reale. Può anche visualizzare altri dati di telemetria, ad esempio richieste, dipendenze, tracce e così via.

### <a name="ilogger-logs"></a>Log ILogger

I log emessi tramite `ILogger` di gravità `Warning` o superiore vengono acquisiti automaticamente. Seguire i [documenti di ILogger](ilogger.md#control-logging-level) per personalizzare i livelli di log acquisiti da Application Insights.

### <a name="dependencies"></a>Dependencies

La raccolta delle dipendenze è abilitata per impostazione predefinita. [Questo](asp-net-dependencies.md#automatically-tracked-dependencies) articolo illustra le dipendenze che vengono raccolte automaticamente e contiene anche i passaggi per eseguire il rilevamento manuale.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` è abilitato per impostazione predefinita e verrà raccolto un set predefinito di contatori dalle app .NET Core 3,0. L'esercitazione [EventCounter](eventcounters.md) elenca il set predefinito di contatori raccolti. Sono inoltre disponibili istruzioni per la personalizzazione dell'elenco.

### <a name="manually-tracking-additional-telemetry"></a>Rilevamento manuale di dati di telemetria aggiuntivi

Mentre l'SDK raccoglie automaticamente i dati di telemetria come spiegato in precedenza, nella maggior parte dei casi l'utente dovrà inviare dati di telemetria aggiuntivi al servizio Application Insights. Il modo consigliato per tenere traccia dei dati di telemetria aggiuntivi è ottenere un'istanza di `TelemetryClient` dall'inserimento delle dipendenze e quindi chiamare uno dei metodi [API](api-custom-events-metrics.md) `TrackXXX()` supportati. Un altro caso d'uso tipico è il [rilevamento personalizzato delle operazioni](custom-operations-tracking.md). Questo approccio viene illustrato negli esempi di lavoro precedenti.

## <a name="configure-the-application-insights-sdk"></a>Configurare il Application Insights SDK

Il valore predefinito `TelemetryConfiguration` usato dall'SDK del servizio Worker è simile alla configurazione automatica usata in un'applicazione ASP.NET o ASP.NET Core, meno il TelemetryInitializers usato per arricchire i dati di telemetria da `HttpContext`.

Per modificare la configurazione predefinita, è possibile personalizzare il servizio Application Insights SDK per il ruolo di lavoro. Gli utenti del Application Insights ASP.NET Core SDK potrebbero avere familiarità con la modifica della configurazione tramite ASP.NET Core [inserimento di dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)predefinito. WorkerService SDK si basa anche su principi simili. Apportare quasi tutte le modifiche alla configurazione nella sezione `ConfigureServices()` chiamando i metodi appropriati su `IServiceCollection`, come descritto di seguito.

> [!NOTE]
> Quando si usa questo SDK, la modifica della configurazione modificando `TelemetryConfiguration.Active` non è supportata e le modifiche non vengono riflesse.

### <a name="using-applicationinsightsserviceoptions"></a>Uso di ApplicationInsightsServiceOptions

È possibile modificare alcune impostazioni comuni passando `ApplicationInsightsServiceOptions` al `AddApplicationInsightsTelemetryWorkerService`, come nell'esempio seguente:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Si noti che `ApplicationInsightsServiceOptions` in questo SDK si trova nello spazio dei nomi `Microsoft.ApplicationInsights.WorkerService` anziché `Microsoft.ApplicationInsights.AspNetCore.Extensions` nell'SDK ASP.NET Core.

Impostazioni usate di frequente in `ApplicationInsightsServiceOptions`

|Impostazione | Descrizione | Predefinito
|---------------|-------|-------
|EnableQuickPulseMetricStream | Abilita/Disabilita la funzionalità LiveMetrics | true
|EnableAdaptiveSampling | Abilita/Disabilita il campionamento adattivo | true
|EnableHeartbeat | Abilita/Disabilita la funzionalità heartbeat, che periodicamente (15 minuti per impostazione predefinita) Invia una metrica personalizzata denominata ' HeartBeatState ' con informazioni sul runtime come la versione .NET, le informazioni sull'ambiente di Azure, se applicabile e così via. | true
|AddAutoCollectedMetricExtractor | Abilita/Disabilita AutoCollectedMetrics Extractor, ovvero un TelemetryProcessor che invia metriche pre-aggregate relative a richieste/dipendenze prima che venga eseguita il campionamento. | true

Per l'elenco più aggiornato, vedere le [impostazioni configurabili in `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) .

### <a name="sampling"></a>campionamento

Il Application Insights SDK per il servizio Worker supporta sia il campionamento a frequenza fissa che quello adattivo. Il campionamento adattivo è abilitato per impostazione predefinita. La configurazione del campionamento per il servizio worker viene eseguita nello stesso modo delle [applicazioni ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Aggiunta di TelemetryInitializers

Usare gli [inizializzatori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) quando si desidera definire le proprietà che vengono inviate con tutti i dati di telemetria.

Aggiungere nuovi `TelemetryInitializer` al contenitore `DependencyInjection` e l'SDK li aggiungerà automaticamente al `TelemetryConfiguration`.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Rimozione di TelemetryInitializers

Gli inizializzatori di telemetria sono presenti per impostazione predefinita. Per rimuovere tutti gli inizializzatori di telemetria o specifici, usare il codice di esempio seguente *dopo* avere chiamato `AddApplicationInsightsTelemetryWorkerService()`.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Aggiunta di processori di telemetria

È possibile aggiungere processori di telemetria personalizzati a `TelemetryConfiguration` usando il metodo di estensione `AddApplicationInsightsTelemetryProcessor` in `IServiceCollection`. I processori di telemetria vengono usati in [scenari di filtro avanzati](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) per consentire un controllo più diretto sugli elementi inclusi o esclusi dai dati di telemetria inviati al servizio Application Insights. Usare l'esempio seguente.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurazione o rimozione di TelemetryModules predefiniti

Application Insights USA moduli di telemetria per raccogliere automaticamente i dati di telemetria relativi a carichi di lavoro specifici senza richiedere il rilevamento manuale.

I moduli di raccolta automatica seguenti sono abilitati per impostazione predefinita. Questi moduli sono responsabili della raccolta automatica dei dati di telemetria. È possibile disabilitarle o configurarle per modificarne il comportamento predefinito.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Per configurare qualsiasi valore predefinito `TelemetryModule`, usare il metodo di estensione `ConfigureTelemetryModule<T>` in `IServiceCollection`, come illustrato nell'esempio seguente.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Configurazione del canale di telemetria

Il canale predefinito è `ServerTelemetryChannel`. È possibile eseguirne l'override come illustrato nell'esempio riportato di seguito.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Disabilitare la telemetria in modo dinamico

Se si vuole disabilitare la telemetria in modo condizionale e dinamico, è possibile risolvere l'istanza `TelemetryConfiguration` con ASP.NET Core contenitore di inserimento delle dipendenze in qualsiasi punto del codice e impostare il flag `DisableTelemetry`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Come è possibile tenere traccia dei dati di telemetria che non vengono raccolti automaticamente?

Ottenere un'istanza di `TelemetryClient` usando l'inserimento del costruttore e chiamare il metodo `TrackXXX()` richiesto. Non è consigliabile creare nuove istanze di @no__t 0. Un'istanza singleton di `TelemetryClient` è già registrata nel contenitore `DependencyInjection`, che condivide `TelemetryConfiguration` con il resto dei dati di telemetria. La creazione di una nuova istanza di `TelemetryClient` è consigliata solo se è necessaria una configurazione separata dal resto dei dati di telemetria.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>È possibile usare l'IDE di Visual Studio per caricare Application Insights in un progetto di servizio del ruolo di lavoro?

Il caricamento dell'IDE di Visual Studio è attualmente supportato solo per le applicazioni ASP.NET/ASP.NET Core. Questo documento verrà aggiornato quando Visual Studio fornisce il supporto per le applicazioni del servizio di lavoro di onboarding.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>È possibile abilitare il monitoraggio Application Insights usando strumenti come Status Monitor?

No. [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [Status Monitor V2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) attualmente supportano solo ASP.NET 4. x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eseguo l'applicazione in Linux, tutte le funzionalità sono supportate?

Sì. Il supporto delle funzionalità per questo SDK è lo stesso in tutte le piattaforme, con le eccezioni seguenti:

* I contatori delle prestazioni sono supportati solo in Windows, ad eccezione della CPU/memoria del processo visualizzata in metriche attive.
* Anche se `ServerTelemetryChannel` è abilitato per impostazione predefinita, se l'applicazione è in esecuzione in Linux o MacOS, il canale non crea automaticamente una cartella di archiviazione locale per conservare temporaneamente la telemetria in caso di problemi di rete. A causa di questa limitazione, i dati di telemetria vengono persi in caso di problemi di rete o del server temporanei. Per risolvere questo problema, configurare una cartella locale per il canale:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Applicazioni di esempio

[Applicazione console .NET Core](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Usare questo esempio se si usa un'applicazione console scritta in .NET Core (2,0 o versione successiva) o .NET Framework (4.7.2 o versione successiva)

[Attività in background ASP .NET Core con HostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Usare questo esempio se ci si trova in Asp.Net Core 2.1/2.2 e si creano attività in background in base alle linee guida ufficiali [qui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[Servizio ruolo di lavoro .NET Core 3,0](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Usare questo esempio se si dispone di un'applicazione di servizio di lavoro .NET Core 3,0 in base a una guida ufficiale [qui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>SDK open source

[Leggere e contribuire al codice](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Passaggi successivi

* [Usare l'API](../../azure-monitor/app/api-custom-events-metrics.md) per inviare i propri eventi e metriche per una visualizzazione dettagliata delle prestazioni e dell'utilizzo dell'app.
* [Tenere traccia delle dipendenze aggiuntive non rilevate automaticamente](../../azure-monitor/app/auto-collect-dependencies.md).
* [Arricchire o filtrare i dati di telemetria raccolti automaticamente](../../azure-monitor/app/api-filtering-sampling.md).
* [Inserimento delle dipendenze in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
