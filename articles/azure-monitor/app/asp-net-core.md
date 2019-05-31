---
title: Azure Application Insights per ASP.NET Core | Microsoft Docs
description: Monitorare le applicazioni Web ASP.NET Core per identificare disponibilità, prestazioni e utilizzo.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226408"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights per applicazioni ASP.NET Core

Questo articolo descrive come abilitare Application Insights per un [ASP.NET Core](https://docs.microsoft.com/aspnet/core) dell'applicazione. Dopo aver completato le istruzioni riportate in questo articolo, Application Insights inizierà a raccogliere le richieste, dipendenze, eccezioni, i contatori delle prestazioni, heartbeat e i log dall'applicazione ASP.NET Core. L'applicazione di esempio è un' [applicazione MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) targeting `netcoreapp2.2`, ma queste istruzioni sono applicabili a tutte le applicazioni ASP.NET Core.

## <a name="supported-scenarios"></a>Scenari supportati

Il [Application Insights SDK (Software Development Kit) per ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) può monitorare le applicazioni indipendentemente dalla posizione e la modalità viene eseguita l'applicazione. Se l'applicazione è in esecuzione e disponga della connettività di rete in Azure, è possibile raccogliere i dati di telemetria. Ciò significa che il monitoraggio di Application Insights è supportato ovunque che .NET Core è supportato. Questo supporto include, qualsiasi sistema operativo (Windows, Linux, Mac), il metodo di hosting (in-process e out-of-process), metodo di distribuzione (vs dipendente dal framework autonoma), Server Web (IIS, Kestrel), piattaforma di hosting (app Web di Azure, macchine Virtuali di Azure, Docker, Azure Kubernetes Service (AKS) e così via.) o IDE (riga di comando di Visual Studio, Visual Studio Code).

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione ASP.NET Core funzionante. Seguire le [Guida introduttiva a ASP.NET Core getting](https://docs.microsoft.com/aspnet/core/getting-started/) per creare un'applicazione ASP.NET Core, se necessario.
- Una valido Application Insights chiave di strumentazione, che è necessaria per inviare i dati di telemetria al servizio Application Insights. Seguire le [crea una risorsa istruzioni](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) per creare una nuova risorsa di Application Insights, se necessario e ottenere una chiave di strumentazione.

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Abilitare la telemetria di Application Insights lato server (Visual Studio)

1. Aprire il progetto in Visual Studio.

    > [!TIP]
    > Durante un passaggio non obbligatorio, può essere utile impostare il controllo codice sorgente per il progetto in modo che è possibile tenere traccia di tutte le modifiche apportate da Application Insights. Per abilitare sorgente controllo select **File** > **aggiungere al controllo del codice sorgente**.

2. Selezionare **Progetto** > **Aggiungi Application Insights Telemetry**.

3. Selezionare **Attività iniziali**. A seconda della versione di Visual Studio, il testo può variare leggermente. Alcune versioni meno recenti includono invece un pulsante **Inizia gratis**.

4. Selezionare la sottoscrizione e quindi **Risorsa** > **Registra**.

5. Dopo l'aggiunta di Application Insights al progetto, controllare per verificare che si sta utilizzando la versione stabile più recente del SDK. Passare a **Project** > **Gestisci pacchetti NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > Se necessario scegliere **Update**.

     ![Screenshot di gestire schermata del pacchetto NuGet con pacchetto di Application Insights selezionato per l'aggiornamento](./media/asp-net-core/update-nuget-package.png)

6. Se è seguito il suggerimento facoltativo e aggiunto al progetto al controllo del codice sorgente è quindi possibile passare a **View** > **Team Explorer** > **modifiche** e Selezionare ogni singolo file per una visualizzazione differenze delle modifiche apportate dall'aggiunta di Application Insights telemetry.

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>Abilitare la telemetria di Application Insights lato server (senza Visual Studio)

1. Installare il [il pacchetto NuGet di Application Insights SDK per ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). È consigliabile usare sempre la versione stabile più recente. Note sulla versione complete per il SDK è reperibile nella [aprire il repository di GitHub origine](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Il frammento seguente illustra le modifiche da aggiungere al progetto `.csproj` file.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. Aggiungere `services.AddApplicationInsightsTelemetry();` per il `ConfigureServices()` metodo nel `Startup` classe. Esempio completo seguente.

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // code adding other services for your application
            services.AddMvc();
        }
    ```

3. Consente di impostare la chiave di strumentazione.

    Sebbene sia possibile fornire la chiave di strumentazione come argomento a `AddApplicationInsightsTelemetry`, è consigliabile specificare la chiave di strumentazione nella configurazione. Di seguito viene illustrato come specificare una chiave di strumentazione in `appsettings.json`. Assicurarsi che `appsettings.json` viene copiato nella cartella radice dell'applicazione durante la pubblicazione.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    In alternativa, la chiave di strumentazione può anche essere specificata in una delle seguenti variabili di ambiente.

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    Esempio:

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` in genere consente di specificare la chiave di strumentazione delle applicazioni distribuite alle App Web di Azure.

    > [!NOTE]
    > Una chiave di strumentazione specificata nel codice prevale sulla variabile di ambiente `APPINSIGHTS_INSTRUMENTATIONKEY`, quale prevale su altre opzioni.

## <a name="run-your-application"></a>Eseguire l'applicazione

 Eseguire l'applicazione e inviare richieste a questo. I dati di telemetria deve ora avviino ad Application Insights. I seguenti dati di telemetria vengono raccolti automaticamente da Application Insights SDK.

|Le richieste o le dipendenze |Dettagli|
|---------------|-------|
|Requests | Richieste web in ingresso all'applicazione. |
|Http/Https | Le chiamate effettuate con `HttpClient`. |
|SQL | Le chiamate effettuate con `SqlClient`. |
|[Archiviazione di Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chiamate effettuate con il Client di archiviazione di Azure. |
|[SDK del Client dell'hub eventi](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versione 1.1.0 e versioni successive. |
|[SDK Client di ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versione 3.0.0 e versioni successive. |
|Azure Cosmos DB | Automaticamente registrate solo se viene utilizzato HTTP/HTTPS. La modalità TCP non verrà acquisita da Application Insights. |

### <a name="performance-counters"></a>Contatori delle prestazioni

Supporto per [contatori delle prestazioni](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) in ASP.NET Core è limitata al seguente

   * SDK versione 2.4.1 e precedenza raccoglie i contatori delle prestazioni se l'applicazione è in esecuzione in App Web di Azure (Windows)
   * SDK versione 2.7.0-beta3 e precedenza raccoglie i contatori delle prestazioni se l'applicazione è in esecuzione in Windows e come destinazione `NETSTANDARD2.0` o versione successiva.
   * Per le applicazioni destinate a .NET Framework, i contatori delle prestazioni sono supportati in tutte le versioni del SDK.
   * Questo articolo verrà aggiornato quando viene aggiunto il supporto di contatore delle prestazioni in Linux.

### <a name="ilogger-logs"></a>Log di ILogger

[I log di ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) livello di gravità `Warning` o versioni successive vengono automaticamente acquisite dal SDK versione 2.7.0-beta3 o versione successiva.

### <a name="live-metrics"></a>Metriche in tempo reale

Potrebbero occorrere alcuni minuti prima che i dati di telemetria prima di venire visualizzati nel portale. Per verificare rapidamente se tutto funziona, è consigliabile usare [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), durante la creazione di richieste per l'applicazione in esecuzione.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Abilitare la telemetria lato Client per le applicazioni Web

I passaggi precedenti sono sufficienti per iniziare a raccogliere i dati di telemetria lato server. Se l'applicazione include i componenti lato client, quindi attenersi alla procedura seguente per iniziare a raccogliere [telemetria relativa all'utilizzo](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) da tale posizione.

1. In `_ViewImports.cshtml`, aggiungere injection:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. Nelle `_Layout.cshtml`, inserire HtmlHelper alla fine della `<head>` sezione ma prima di qualsiasi altro script. I dati di telemetria JavaScript personalizzato di che desidera ottenere un report dalla pagina cui deve essere inserito dopo questo frammento di codice:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Il `.cshtml` sono nomi di file citati in precedenza da un modello di applicazione MVC predefinita. In definitiva, attivare correttamente il monitoraggio lato client per l'applicazione è necessario il frammento JavaScript siano presenti nel `<head>` sezione di ogni pagina dell'applicazione che si desidera monitorare. Per questo modello di applicazione aggiungendo il frammento di codice Javascript `_Layout.cshtml` verrà in modo efficace portare a termine questo obiettivo. Se il progetto non ha questo file specificato è comunque possibile aggiungere [monitoraggio lato client](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). Semplicemente necessario a una delle due aggiungere il codice JavaScript a un file equivalente che controlla il `<head>` di tutte le pagine all'interno di app o in alternativa è possibile aggiungere il frammento di codice singoli più pagine Sebbene questa sia difficile da mantenere e non è in genere è consigliabile.

## <a name="configuring-application-insights-sdk"></a>Configurazione di Application Insights SDK

Application Insights SDK per ASP.NET Core possono essere personalizzati per modificare la configurazione predefinita. Gli utenti di Application Insights SDK di ASP.NET potrebbero essere familiari con l'uso di configurazione `ApplicationInsights.config`, o modificando `TelemetryConfiguration.Active`. Per ASP.NET Core, configurazione viene eseguita in modo diverso. SDK di ASP.NET Core viene aggiunto all'applicazione e configurato mediante incorporato di ASP.NET Core [inserimento delle dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Quasi tutte le modifiche alla configurazione vengono eseguite nel `ConfigureServices()` metodo di `Startup.cs` classe, salvo diversa indicazione. Seguire le sezioni seguenti per altre informazioni.

> [!NOTE]
>  La modifica della configurazione modificando `TelemetryConfiguration.Active` non è consigliata nelle applicazioni ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Configurazione mediante ApplicationInsightsServiceOptions

È possibile modificare alcune impostazioni comuni passando `ApplicationInsightsServiceOptions` a `AddApplicationInsightsTelemetry`. Di seguito è illustrato un esempio.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

L'elenco esatto di impostazioni configurabili `ApplicationInsightsServiceOptions` reperibili [qui](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>campionamento

Application Insights SDK per ASP.NET Core supporta FixedRate e il campionamento adattivo. Il campionamento adattivo è abilitato per impostazione predefinita. Seguire nostri [indicazioni sul campionamento adattivo](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications), per imparare a configurare il campionamento per le applicazioni ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Aggiunta di TelemetryInitializers

[Gli inizializzatori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) vengono usate quando si desidera definire le proprietà globali che vengono inviate con tutti i dati di telemetria.

Per aggiungere un nuovo `TelemetryInitializer`, aggiungerlo nel contenitore DependencyInjection, come illustrato di seguito. `TelemetryInitializer`aggiunta al contenitore DependencyInjection verrà prelevato dal SDK automaticamente.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Rimozione TelemetryInitializers

Per rimuovere tutti o TelemetryInitializers specifico, che sono presenti per impostazione predefinita, usare il seguente codice di esempio **dopo aver** chiamata `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>Aggiungere i processori di telemetria

Processori di telemetria personalizzati possono essere aggiunti al `TelemetryConfiguration` usando il metodo di estensione `AddApplicationInsightsTelemetryProcessor` sul `IServiceCollection`. Processori di telemetria vengono usati [scenari di filtro avanzate](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) la possibilità di controllo più diretto su ciò che viene incluso o escluso dai dati di telemetria inviati al servizio Application Insights. Usare l'esempio seguente.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurazione o la rimozione predefinito TelemetryModules

Application Insights Usa moduli di telemetria come modo [raccogliere automaticamente informazioni utili](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) sui carichi di lavoro specifici senza la necessità di configurazioni aggiuntive.

I seguenti moduli di raccolta automatica sono abilitati per impostazione predefinita e sono responsabili per la raccolta automatica di dati di telemetria. Possono essere disabilitati e configurati per modificare il comportamento predefinito.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Per configurare qualsiasi impostazione predefinita `TelemetryModule`, usare il metodo di estensione `ConfigureTelemetryModule<T>` su `IServiceCollection` come illustrato nell'esempio seguente.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Configurazione di canale di telemetria

Il canale predefinito utilizzato è il `ServerTelemetryChannel`. È possibile eseguirne l'override dall'esempio riportato di seguito.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>Si vuole tenere traccia di dati di telemetria aggiuntivi diversi da dati di telemetria raccolti automaticamente. Come devo fare?

Ottenere un'istanza di `TelemetryClient` utilizzando l'inserimento del costruttore e chiamare richiesti `TrackXXX()` metodo su di esso. Non è consigliabile creare nuovi `TelemetryClient` nell'applicazione ASP.NET Core, come istanza singleton di istanze `TelemetryClient` è già registrato nel contenitore di inserimento delle dipendenze, condivide `TelemetryConfiguration` con il resto dei dati di telemetria. Creazione di un nuovo `TelemetryClient` istanza è consigliata solo se deve disporre di una configurazione separata dal resto dei dati di telemetria. Nell'esempio seguente viene illustrato come tenere traccia dei dati di telemetria aggiuntivi da un controller.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Fare riferimento a [metriche personalizzate di Application Insights API reference](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) per una descrizione dei dati personalizzate, creazione di report in Application Insights.

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alcuni modelli di Visual Studio consente di metodo di estensione UseApplicationInsights() su IWebHostBuilder per abilitare Application Insights. È questo utilizzo è ancora valida?

L'abilitazione di Application Insights con questo metodo è valido e viene usato in Visual Studio di onboarding e anche le estensioni di App Web di Azure. Tuttavia, è consigliabile usare `services.AddApplicationInsightsTelemetry()` poiché fornisce gli overload per controllare alcune attività di configurazione. Entrambi metodi eseguono internamente la stessa operazione, pertanto, se è presente alcuna configurazione personalizzata da applicare, chiamando entrambi sono validi.

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Distribuito l'applicazione ASP.NET Core App Web di Azure. È consigliabile comunque abilitare l'estensione Application Insights da app Web?

Se è installato il SDK in fase di compilazione come illustrato in questo articolo, non è necessario abilitare l'estensione Application Insights dal portale del servizio App. Anche se è installata l'estensione, verrà nuovamente disattivato quando rileva che il SDK è già stato aggiunto all'applicazione. Abilitazione di Application Insights dall'estensione libera è dall'installazione e l'aggiornamento del SDK. Tuttavia, l'abilitazione di Application Insights in base a questo articolo è più flessibile per i motivi seguenti.
   * Application Insights Telemetry continueranno a funzionare:
       * Tutti i sistemi operativi: Windows, Linux e Mac.
       * Tutto pubblicare - modalità indipendente o dipendente dal Framework.
       * Tutti i framework di destinazione, tra cui la versione completa di .NET Framework.
       * Tutte le opzioni di Hosting - APP Web di Azure, macchine virtuali, Linux, contenitori, servizio contenitore di AZURE, non di Azure.
   * I dati di telemetria possono essere visualizzati in locale, durante il debug da Visual Studio.
   * Consente l'uso di dati di telemetria personalizzati aggiuntivi rilevamento `TrackXXX()` API.
   * È necessario il controllo completo sulla configurazione.

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>È possibile abilitare il monitoraggio di Application Insights usando strumenti come Status Monitor?

 No. [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e la relativa sostituzione imminente [v2 Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) attualmente supporta ASP.NET 4.x solo.

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>Ho un'applicazione ASP.NET Core 2.0. Application Insights non è abilitata automaticamente senza intervento dell'utente?

`Microsoft.AspNetCore.All` metapacchetto 2.0 incluso Application Insights SDK (versione 2.1.0) e se si esegue l'applicazione nel debugger di Visual Studio, Visual Studio Abilita Application Insights e Mostra i dati di telemetria in locale nell'IDE stesso. I dati di telemetria non è stato inviato al servizio Application Insights, a meno che non è specificata in modo esplicito una chiave di strumentazione. È consigliabile seguire le istruzioni in questo articolo per abilitare Application Insights, anche per 2.0 le app.

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>Eseguire l'applicazione in Linux. Tutte le funzionalità supportate in Linux nonché?

* Sì. Supporto della funzionalità per il SDK è lo stesso in tutte le piattaforme, con le eccezioni seguenti:

    * I contatori delle prestazioni non sono ancora supportati in Non-Windows.
    * Anche se `ServerTelemetryChannel` è abilitato per impostazione predefinita, se l'applicazione è in esecuzione in Linux o MacOS, il canale non crea automaticamente una cartella di archiviazione locale per mantenere temporaneamente i dati di telemetria, se sono presenti problemi di rete. Questa limitazione fa in modo che i dati di telemetria andrà persa se sono presenti problemi di server o temporanee della rete. La soluzione alternativa per risolvere questo problema è l'utente deve configurare una cartella locale per il canale, come illustrato di seguito.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>SDK open source
[Leggere e contribuire al codice](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- Video esterno dettagliato sulla [configurazione da zero di Application Insights con .NET Core e Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t).
- External video dettagliata sulla [configurazione di Application Insights con .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) da zero.

## <a name="next-steps"></a>Passaggi successivi
* [Esplorare i flussi degli utenti](../../azure-monitor/app/usage-flows.md) per identificare in che modo gli utenti si sposteranno nell'app.
* [Configurare la raccolta di snapshot](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) per visualizzare lo stato del codice sorgente e delle variabili nel momento in cui viene generata un'eccezione.
* [Utilizzare l'API](../../azure-monitor/app/api-custom-events-metrics.md) per inviare gli eventi e le metriche per una visualizzazione più dettagliata dell'utilizzo e delle prestazioni dell'app.
* Usare [test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) per controllare costantemente l'app da ogni parte del mondo.
