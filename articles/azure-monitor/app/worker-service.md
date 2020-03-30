---
title: Application Insights per le app del servizio di lavoro (app non HTTP)Application Insights for Worker Service apps (non-HTTP apps)
description: Monitoraggio di app non HTTP di .NET Core/.NET Framework con Azure Monitor Application Insights.Monitoring .NET Core/.NET Framework non-HTTP apps with Azure Monitor Application Insights.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 34a64ffa67b1a43a77391e0d50ddf1bfad0f73ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501169"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights per le applicazioni di servizio di lavoro (applicazioni non HTTP)Application Insights for Worker Service applications (non-HTTP applications)

Application Insights rilascia un nuovo `Microsoft.ApplicationInsights.WorkerService`SDK, denominato , più adatto per carichi di lavoro non HTTP come messaggistica, attività in background, applicazioni console e così via. Questi tipi di applicazioni non hanno la nozione di una richiesta HTTP in ingresso come un'applicazione Web ASP.NET/ASP.NET Core tradizionale e pertanto non è supportato l'utilizzo di pacchetti di Application Insights per [ASP.NET](asp-net.md) o ASP.NET applicazioni [Core.](asp-net-core.md)

Il nuovo SDK non esegue alcuna raccolta di telemetria da solo. Al contrario, porta in altri ben noti agenti di raccolta dati di Application Insights come [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) e così via. Questo SDK espone i `IServiceCollection` metodi di estensione per abilitare e configurare la raccolta di telemetria.

## <a name="supported-scenarios"></a>Scenari supportati

[Application Insights SDK per](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) il servizio di lavoro è più adatto per le applicazioni non HTTP, indipendentemente da dove o come vengono eseguite. Se l'applicazione è in esecuzione e dispone di connettività di rete ad Azure, è possibile raccogliere dati di telemetria. Il monitoraggio di Application Insights è supportato ovunque sia supportato .NET Core.Application Insights monitoring is supported everywhere .NET Core is supported. Questo pacchetto può essere utilizzato nel servizio di lavoro [.NET Core 3.0](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)appena introdotto, [attività in background in Asp.Net Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), applicazioni console (.NET Core/ .NET Framework) e così via.

## <a name="prerequisites"></a>Prerequisiti

Chiave di strumentazione di Application Insights valida. Questa chiave è necessaria per inviare dati di telemetria ad Application Insights.This key is required to send any telemetry to Application Insights. Se è necessario creare una nuova risorsa di Application Insights per ottenere una chiave di strumentazione, vedere [Creare una risorsa di Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

## <a name="using-application-insights-sdk-for-worker-services"></a>Utilizzo di Application Insights SDK per i servizi di lavoroUsing Application Insights SDK for Worker Services

1. Installare il pacchetto [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nell'applicazione.
   Il frammento di codice seguente mostra le modifiche `.csproj` che devono essere aggiunte al file del progetto.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Chiamare `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` il `IServiceCollection`metodo di estensione su , fornendo la chiave di strumentazione. Questo metodo deve essere chiamato all'inizio dell'applicazione. La posizione esatta dipende dal tipo di applicazione.

1. Recuperare `ILogger` un'istanza o `TelemetryClient` un'istanza dal contenitore `serviceProvider.GetRequiredService<TelemetryClient>();` Dependency Injection (DI) chiamando o usando Constructor Injection.Retrieve an instance or instance from the Dependency Injection (DI) container by calling or using Constructor Injection. Questo passaggio attiverà `TelemetryConfiguration` l'impostazione dei moduli di raccolta automatica e.

Istruzioni specifiche per ogni tipo di applicazione sono descritte nelle sezioni seguenti.

## <a name="net-core-30-worker-service-application"></a>Applicazione di servizio di lavoro .NET Core 3.0

L'esempio completo è condiviso [qui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Scaricare e installare [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Creare un nuovo progetto di servizio di lavoro tramite il nuovo modello di progetto di Visual Studio o la riga di comandoCreate a new Worker Service project either by using Visual Studio new project template or command line`dotnet new worker`
3. Installare il pacchetto [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nell'applicazione.

4. Aggiungere `services.AddApplicationInsightsTelemetryWorkerService();` al `CreateHostBuilder()` metodo `Program.cs` nella classe, come in questo esempio:Add to the method in your class, as in this example:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Modificare `Worker.cs` il come da esempio di seguito.

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

6. Impostare la chiave di strumentazione.

    Sebbene sia possibile fornire la `AddApplicationInsightsTelemetryWorkerService`chiave di strumentazione come argomento a , è consigliabile specificare la chiave di strumentazione nella configurazione. Nell'esempio di codice riportato di `appsettings.json`seguito viene illustrato come specificare una chiave di strumentazione in . Assicurarsi `appsettings.json` che venga copiato nella cartella radice dell'applicazione durante la pubblicazione.

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

In alternativa, specificare la chiave di strumentazione in una delle seguenti variabili di ambiente.
`APPINSIGHTS_INSTRUMENTATIONKEY` o `ApplicationInsights:InstrumentationKey`

Ad esempio: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
O`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

In `APPINSIGHTS_INSTRUMENTATIONKEY` genere, specifica la chiave di strumentazione per le applicazioni distribuite in applicazioni Web come processi Web.

> [!NOTE]
> Una chiave di strumentazione specificata `APPINSIGHTS_INSTRUMENTATIONKEY`nel codice supera la variabile di ambiente , che vince su altre opzioni.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET attività in background principali con i servizi ospitati

[In](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) questo documento viene descritto come creare attività di sfondo nell'applicazione ASP.NET Core 2.1/2.2.This document describes how to create backgrounds tasks in ASP.NET Core 2.1/2.2 application.

L'esempio completo è condiviso [qui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Installare il pacchetto Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nell'applicazione.
2. Aggiungere `services.AddApplicationInsightsTelemetryWorkerService();` al `ConfigureServices()` metodo, come in questo esempio:

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

Di seguito è `TimedHostedService` riportato il codice in cui si trova la logica dell'attività in background.

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

3. Impostare la chiave di strumentazione.
   Usare lo `appsettings.json` stesso dall'esempio precedente del servizio di lavoro di .NET Core 3.0.Use the same from the .NET Core 3.0 Worker Service example above.

## <a name="net-corenet-framework-console-application"></a>Applicazione console .NET Core/.NET Framework

Come accennato all'inizio di questo articolo, il nuovo pacchetto può essere usato per abilitare la telemetria di Application Insights anche da una normale applicazione console. Questo pacchetto [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)è destinato a , e quindi può essere usato per le app console in .NET Core 2.0 o versione successiva e .NET Framework 4.7.2 o versione successiva.

L'esempio completo è condiviso [qui](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Installare il pacchetto Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) nell'applicazione.

2. Modificare Program.cs come indicato di seguito.

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
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
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

Anche questa applicazione console `TelemetryConfiguration`utilizza lo stesso valore predefinito e può essere personalizzata nello stesso modo degli esempi della sezione precedente.

## <a name="run-your-application"></a>Eseguire l'applicazione

Eseguire l'applicazione. L'esempio di lavoro da tutto quanto sopra fa una chiamata http ogni secondo per bing.com, e genera anche pochi log utilizzando ILogger. Queste righe vengono `StartOperation` incapsulate all'interno della chiamata `TelemetryClient`di `RequestTelemetry` , utilizzata per creare un'operazione (in questo esempio denominata "operazione"). Application Insights raccoglierà questi log di ILogger (avviso o precedenti per impostazione `RequestTelemetry` predefinita) e le dipendenze e saranno correlati alla relazione con padre-figlio. La correlazione funziona anche attraverso il limite di processo/rete. Ad esempio, se la chiamata è stata effettuata a un altro componente monitorato, sarà correlato anche a questo elemento padre.

Questa operazione `RequestTelemetry` personalizzata di può essere considerata come l'equivalente di una richiesta Web in ingresso in un'applicazione Web tipica. Anche se non è necessario usare un'operazione, si adatta meglio `RequestTelemetry` al modello di dati di correlazione di Application [Insights,](https://docs.microsoft.com/azure/azure-monitor/app/correlation) con l'azione come operazione padre e tutti i dati di telemetria generati all'interno dell'iterazione del ruolo vengono considerati come logicamente appartenenti alla stessa operazione. Questo approccio garantisce inoltre che tutti i dati `operation_id`di telemetria generati (automatico e manuale) avranno lo stesso file . Poiché il `operation_id`campionamento è basato su , l'algoritmo di campionamento mantiene o elimina tutti i dati di telemetria da una singola iterazione.

The following lists the full telemetry automatically collected by Application Insights.

### <a name="live-metrics"></a>Metriche attive

[Le metriche in tempo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) reale possono essere usate per verificare rapidamente se il monitoraggio di Application Insights è configurato correttamente. Anche se potrebbero essere stati alcuni minuti prima che i dati di telemetria inizino a essere visualizzati nel portale e nell'analisi, le metriche dinamiche mostrano l'utilizzo della CPU del processo in esecuzione quasi in tempo reale. Può anche mostrare altri dati di telemetria come richieste, dipendenze, tracce ecc.

### <a name="ilogger-logs"></a>Registri ILogger

I log `ILogger` generati tramite gravità `Warning` o superiore vengono acquisiti automaticamente. Seguire i documenti di ILogger per personalizzare i livelli di log acquisiti da Application Insights.Follow [ILogger docs](ilogger.md#control-logging-level) to customize which log levels are captured by Application Insights.

### <a name="dependencies"></a>Dependencies

La raccolta delle dipendenze è abilitata per impostazione predefinita. [In](asp-net-dependencies.md#automatically-tracked-dependencies) questo articolo vengono illustrate le dipendenze raccolte automaticamente e contengono anche i passaggi per eseguire il rilevamento manuale.

### <a name="eventcounter"></a>EventCounter (Contatore evento)

`EventCounterCollectionModule`è abilitato per impostazione predefinita e raccoglierà un set predefinito di contatori dalle app .NET Core 3.0. L'esercitazione [EventCounter](eventcounters.md) elenca il set predefinito di contatori raccolti. Ha anche le istruzioni per personalizzare l'elenco.

### <a name="manually-tracking-additional-telemetry"></a>Monitoraggio manuale dei dati di telemetria aggiuntiviManually tracking additional telemetry

Mentre l'SDK raccoglie automaticamente i dati di telemetria come spiegato in precedenza, nella maggior parte dei casi l'utente dovrà inviare dati di telemetria aggiuntivi al servizio Application Insights. Il modo consigliato per tenere traccia di `TelemetryClient` dati di telemetria aggiuntivi consiste nell'ottenere un'istanza di da Dependency Injection e quindi chiamare uno dei metodi `TrackXXX()` [API](api-custom-events-metrics.md) supportati. Un altro caso d'uso tipico è il [rilevamento personalizzato delle operazioni](custom-operations-tracking.md). Questo approccio è illustrato negli esempi di Worker precedenti.

## <a name="configure-the-application-insights-sdk"></a>Configurare Application Insights SDK

Il `TelemetryConfiguration` valore predefinito utilizzato dall'SDK del servizio di lavoro è simile alla configurazione automatica utilizzata in `HttpContext`un'applicazione ASP.NET o ASP.NET Core, meno il TelemetryInitializers usato per arricchire i dati di telemetria da .

È possibile personalizzare Application Insights SDK per il servizio di lavoro per modificare la configurazione predefinita. Gli utenti di Application Insights ASP.NET Core SDK potrebbero avere familiarità con la modifica della configurazione tramite ASP.NETCore [core incorporate delle dipendenze injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). WorkerService SDK si basa anche su principi simili. Apportare quasi tutte `ConfigureServices()` le modifiche di `IServiceCollection`configurazione nella sezione chiamando i metodi appropriati su , come descritto di seguito.

> [!NOTE]
> Durante l'utilizzo di questo `TelemetryConfiguration.Active` SDK, la modifica della configurazione tramite modifica non è supportata e le modifiche non verranno riflesse.

### <a name="using-applicationinsightsserviceoptions"></a>Utilizzo di ApplicationInsightsServiceOptionsUsing ApplicationInsightsServiceOptions

È possibile modificare alcune impostazioni `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService`comuni passando a , come in questo esempio:

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

Si `ApplicationInsightsServiceOptions` noti che in `Microsoft.ApplicationInsights.WorkerService` questo SDK `Microsoft.ApplicationInsights.AspNetCore.Extensions` è nello spazio dei nomi anziché nel ASP.NET Core SDK.

Impostazioni di uso comune in`ApplicationInsightsServiceOptions`

|Impostazione | Descrizione | Predefinito
|---------------|-------|-------
|EnableQuickPulseMetricStream (EnableQuickPulseMetricStream) | Attivare/disattivare la funzionalità LiveMetricsEnable/Disable LiveMetrics feature | true
|EnableAdaptiveSampling (CampionamentoAdad) | Abilita/Disabilita campionamento adattivo | true
|EnableHeartbeat | Abilita/Disabilita heartbeat, che periodicamente (impostazione predefinita di 15 min) invia una metrica personalizzata denominata "HeartBeatState" con informazioni sul runtime come la versione .NET, le informazioni sull'ambiente di Azure, se applicabile, e così via. | true
|AddAutoCollectedMetricExtractor | Abilitare/disabilitare l'estrattore AutoCollectedMetrics, ovvero un TelemetryProcessor che invia metriche preaggregate su Richieste/Dipendenze prima che venga eseguito il campionamento. | true

Vedere le [impostazioni `ApplicationInsightsServiceOptions` configurabili in](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) per l'elenco più aggiornato.

### <a name="sampling"></a>campionamento

Application Insights SDK per il servizio Worker supporta sia il campionamento a velocità fissa che il campionamento adattivo. Il campionamento adattivo è abilitato per impostazione predefinita. La configurazione del campionamento per il servizio di lavoro viene eseguita allo stesso modo di [ASP.NET applicazioni principali](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Aggiunta di TelemetryInitializersAdding TelemetryInitializers

Usare [gli inizializzatori](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) di telemetria quando si desidera definire le proprietà inviate con tutti i dati di telemetria.

Aggiungere qualsiasi `TelemetryInitializer` nuovo `DependencyInjection` al contenitore e SDK `TelemetryConfiguration`li aggiungerà automaticamente al file .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Rimozione di TelemetryInitializersRemoving TelemetryInitializers

Gli inizializzatori di telemetria sono presenti per impostazione predefinita. Per rimuovere tutti gli inizializzatori di telemetria `AddApplicationInsightsTelemetryWorkerService()`o specifici, utilizzare il codice di esempio seguente dopo *aver* chiamato .

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

È possibile aggiungere processori `TelemetryConfiguration` di telemetria `AddApplicationInsightsTelemetryProcessor` `IServiceCollection`personalizzati a utilizzando il metodo di estensione in . I processori di telemetria vengono utilizzati in scenari di filtro avanzati per consentire un controllo più diretto sugli elementi inclusi o esclusi dai dati di telemetria inviati al servizio Application Insights.You use telemetry processors in [advanced filtering scenarios](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) to allow more direct control over what's included or excluded from the telemetry you send to the Application Insights service. Utilizzare l'esempio seguente.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurazione o rimozione di TelemetryModules predefinitiConfiguring or removing default TelemetryModules

Application Insights usa i moduli di telemetria per raccogliere automaticamente dati di telemetria su carichi di lavoro specifici senza richiedere il rilevamento manuale.

I seguenti moduli di raccolta automatica sono abilitati per impostazione predefinita. Questi moduli sono responsabili della raccolta automatica dei dati di telemetria. È possibile disabilitarli o configurarli per modificarne il comportamento predefinito.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Per configurare `TelemetryModule`qualsiasi valore `ConfigureTelemetryModule<T>` predefinito, utilizzare il metodo di estensione su `IServiceCollection`, come illustrato nell'esempio seguente.

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

### <a name="configuring-telemetry-channel"></a>Configurazione del canale di telemetriaConfiguring telemetry channel

Il canale `ServerTelemetryChannel`predefinito è . È possibile eseguirne l'override come illustrato nell'esempio seguente.

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

### <a name="disable-telemetry-dynamically"></a>Disabilitare i dati di telemetria in modo dinamico

Se si vuole disabilitare i dati di `TelemetryConfiguration` telemetria in modo condizionale e dinamico, è possibile risolvere l'istanza con ASP.NET contenitore di inserimento delle dipendenze core in qualsiasi punto del codice e impostare `DisableTelemetry` il flag su di esso.

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

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Come è possibile tenere traccia dei dati di telemetria non raccolti automaticamente?

Ottenere un'istanza di `TelemetryClient` utilizzando l'inserimento del costruttore e chiamare il metodo richiesto `TrackXXX()` su di esso. Non è consigliabile `TelemetryClient` creare nuove istanze. Un'istanza singleton di `TelemetryClient` è `DependencyInjection` già registrata `TelemetryConfiguration` nel contenitore, che viene condivisione con il resto dei dati di telemetria. La creazione `TelemetryClient` di una nuova istanza è consigliata solo se è necessaria una configurazione separata dal resto dei dati di telemetria.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>È possibile usare l'IDE di Visual Studio per eseguire l'onboarding di Application Insights a un progetto di servizio di lavoro?

L'onboarding dell'IDE di Visual Studio è attualmente supportato solo per ASP.NET/ASP.NET applicazioni principali. Questo documento verrà aggiornato quando Visual Studio fornisce il supporto per l'onboarding delle applicazioni di servizio di lavoro.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>È possibile abilitare il monitoraggio di Application Insights usando strumenti come Monitoraggio stato?

No. [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [Status Monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) supportano attualmente solo ASP.NET 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se si esegue l'applicazione in Linux, sono supportate tutte le funzionalità?

Sì. Il supporto delle funzionalità per questo SDK è lo stesso in tutte le piattaforme, con le eccezioni seguenti:Feature support for this SDK is the same in all platforms, with the following exceptions:

* I contatori delle prestazioni sono supportati solo in Windows, ad eccezione della CPU/memoria del processo visualizzata in Metriche live.
* Anche `ServerTelemetryChannel` se è abilitato per impostazione predefinita, se l'applicazione è in esecuzione in Linux o MacOS, il canale non crea automaticamente una cartella di archiviazione locale per mantenere temporaneamente la telemetria in caso di problemi di rete. A causa di questa limitazione, i dati di telemetria vengono persi quando si verificano problemi temporanei di rete o del server. Per risolvere questo problema, configurare una cartella locale per il canale:

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

[Applicazione console .NET Core](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Utilizzare questo esempio se si utilizza un'applicazione console scritta in .NET Core (2.0 o versione successiva) o .NET Framework (4.7.2 o versione successiva)Use this sample if you are using a Console Application written in either .NET Core (2.0 or higher) or .NET Framework (4.7.2 or higher)

[Attività in background di ASP .NET Core con HostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Usare questo esempio se si è in Asp.Net Core 2.1/2.2 e la creazione di attività in background in base alle indicazioni ufficiali [qui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[Servizio di lavoro .NET Core 3.0](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Usare questo esempio se si dispone di un'applicazione del servizio di lavoro di .NET Core 3.0 come indicato nelle linee guida ufficiali [qui](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>SDK open source

[Leggere e contribuire al codice](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Passaggi successivi

* [Usa l'API](../../azure-monitor/app/api-custom-events-metrics.md) per inviare eventi e metriche personalizzati per una visualizzazione dettagliata delle prestazioni e dell'utilizzo della tua app.
* [Tenere traccia delle dipendenze aggiuntive non rilevate automaticamente.](../../azure-monitor/app/auto-collect-dependencies.md)
* [Arricchisci o filtra dati di telemetria raccolti automaticamente](../../azure-monitor/app/api-filtering-sampling.md).
* [Inserimento delle dipendenze in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
