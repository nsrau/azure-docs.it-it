---
title: Applicazione Azure Insights per le applicazioni ASP.NET Core | Microsoft Docs
description: Monitorare le applicazioni Web ASP.NET Core per identificare disponibilità, prestazioni e utilizzo.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 770cc3f34487f837631a98c6f652d8746ae2c2df
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321395"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights per le applicazioni ASP.NET Core

Questo articolo descrive come abilitare Application Insights per un'applicazione [ASP.NET Core](/aspnet/core) . Al termine delle istruzioni riportate in questo articolo, Application Insights raccoglierà richieste, dipendenze, eccezioni, contatori delle prestazioni, heartbeat e log dall'applicazione ASP.NET Core.

L'esempio che verrà usato qui è un' [applicazione MVC](/aspnet/core/tutorials/first-mvc-app) destinata a `netcoreapp3.0` . È possibile applicare queste istruzioni a tutte ASP.NET Core applicazioni. Se si usa il [servizio Worker](/aspnet/core/fundamentals/host/hosted-services#worker-service-template), usare le istruzioni riportate [qui](./worker-service.md).

## <a name="supported-scenarios"></a>Scenari supportati

Il [Application Insights SDK per ASP.NET Core è in grado di](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) monitorare le applicazioni, indipendentemente da dove o come vengono eseguite. Se l'applicazione è in esecuzione e ha la connettività di rete ad Azure, è possibile raccogliere i dati di telemetria. Il monitoraggio Application Insights è supportato ovunque sia supportato .NET Core. Supporto tecnico:
* **Sistema operativo**: Windows, Linux o Mac.
* **Metodo di hosting**: interno o esterno al processo.
* **Metodo di distribuzione**: dipendente dal framework o autonomo.
* **Server Web**: IIS (Internet Information Server) o Kestrel.
* **Piattaforma di hosting**: la funzionalità App Web del Servizio app di Azure, Macchina virtuale di Azure, Docker, servizio Azure Kubernetes e così via.
* **Versione runtime di .NET Core**: 1. XX, 2. XX o 3. XX
* **IDE**: Visual Studio, vs code o riga di comando.

> [!NOTE]
> ASP.NET Core 3. X richiede [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) o versione successiva.

## <a name="prerequisites"></a>Prerequisiti

- Applicazione ASP.NET Core funzionante. Se è necessario creare un'applicazione ASP.NET Core, seguire questa [esercitazione ASP.NET Core](/aspnet/core/getting-started/).
- Chiave di strumentazione Application Insights valida. Questa chiave è necessaria per inviare i dati di telemetria a Application Insights. Se è necessario creare una nuova risorsa Application Insights per ottenere una chiave di strumentazione, vedere [creare una risorsa di Application Insights](./create-new-resource.md).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Abilitare la telemetria lato server Application Insights (Visual Studio)

1. Aprire il progetto in Visual Studio.

    > [!TIP]
    > Se lo si desidera, è possibile configurare il controllo del codice sorgente per il progetto in modo che sia possibile tenere traccia di tutte le modifiche apportate Application Insights. Per abilitare il controllo del codice sorgente, selezionare **file**  >  **Aggiungi al controllo del codice sorgente**.

2. Selezionare **progetto**  >  **Aggiungi Application Insights Telemetry**.

3. Selezionare **Attività iniziali**. Il testo di questa selezione può variare a seconda della versione di Visual Studio. Alcune versioni precedenti usano invece un pulsante **Start Free** .

4. Selezionare la propria sottoscrizione. Selezionare quindi **Resource**  >  **Registro**risorse.

5. Dopo aver aggiunto Application Insights al progetto, verificare che si stia usando la versione stabile più recente dell'SDK. Passare a **progetto**  >  **Gestisci pacchetti NuGet**  >  **Microsoft. ApplicationInsights. AspNetCore**. Se necessario, scegliere **Aggiorna**.

     ![Screenshot che mostra dove selezionare il pacchetto di Application Insights per l'aggiornamento](./media/asp-net-core/update-nuget-package.png)

6. Se è stato seguito il suggerimento facoltativo e il progetto è stato aggiunto al controllo del codice sorgente, passare a **Visualizza**  >  **Team Explorer**  >  **modifiche**. Selezionare quindi ogni file per visualizzare una visualizzazione diff delle modifiche apportate da Application Insights telemetria.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Abilitare la telemetria lato server di Application Insights (senza Visual Studio)

1. Installare il [pacchetto NuGet di Application Insights SDK per ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). È consigliabile usare sempre la versione stabile più recente. Trovare le note sulla versione complete per l'SDK nel [repository GitHub Open Source](https://github.com/Microsoft/ApplicationInsights-dotnet/releases).

    Nell'esempio di codice riportato di seguito vengono illustrate le modifiche da aggiungere al file del progetto `.csproj` .

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Aggiungere `services.AddApplicationInsightsTelemetry();` al `ConfigureServices()` metodo nella `Startup` classe, come in questo esempio:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Configurare la chiave di strumentazione.

    Sebbene sia possibile fornire la chiave di strumentazione come argomento a `AddApplicationInsightsTelemetry` , è consigliabile specificare la chiave di strumentazione nella configurazione. Nell'esempio di codice seguente viene illustrato come specificare una chiave di strumentazione in `appsettings.json` . Assicurarsi che `appsettings.json` venga copiato nella cartella radice dell'applicazione durante la pubblicazione.

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

    In alternativa, specificare la chiave di strumentazione in una delle seguenti variabili di ambiente:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Ad esempio:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY`viene in genere usato nelle [app Web di Azure](./azure-web-apps.md?tabs=net), ma può essere usato anche in tutte le posizioni in cui questo SDK è supportato. Se si sta eseguendo il monitoraggio di app Web senza codice, questo formato è obbligatorio se non si usano stringhe di connessione.

    Anziché impostare le chiavi di strumentazione, è ora possibile utilizzare anche le [stringhe di connessione](./sdk-connection-string.md?tabs=net).

    > [!NOTE]
    > Una chiave di strumentazione specificata nel codice prevale sulla variabile di ambiente `APPINSIGHTS_INSTRUMENTATIONKEY` , che prevale su altre opzioni.

## <a name="run-your-application"></a>Eseguire l'applicazione

Eseguire l'applicazione e creare richieste. I dati di telemetria dovrebbero ora fluire Application Insights. Il Application Insights SDK raccoglie automaticamente le richieste Web in ingresso per l'applicazione, insieme ai dati di telemetria seguenti.

### <a name="live-metrics"></a>Metriche attive    

È possibile usare le metriche in tempo [reale](./live-stream.md) per verificare rapidamente se Application Insights il monitoraggio è configurato correttamente. Sebbene potrebbero essere necessari alcuni minuti prima che i dati di telemetria inizino a essere visualizzati nel portale e nelle analisi, le metriche attive indicheranno l'utilizzo della CPU del processo in esecuzione quasi in tempo reale. Può anche visualizzare altri dati di telemetria, ad esempio richieste, dipendenze, tracce e così via.

### <a name="ilogger-logs"></a>Log ILogger

I log emessi tramite un livello `ILogger` di gravità `Warning` o maggiore vengono acquisiti automaticamente. Seguire i [documenti di ILogger](ilogger.md#control-logging-level) per personalizzare i livelli di log acquisiti da Application Insights.

### <a name="dependencies"></a>Dependencies

La raccolta delle dipendenze è abilitata per impostazione predefinita. [Questo](asp-net-dependencies.md#automatically-tracked-dependencies) articolo illustra le dipendenze che vengono raccolte automaticamente e contiene anche i passaggi per eseguire il rilevamento manuale.

### <a name="performance-counters"></a>Contatori delle prestazioni

Il supporto per i [contatori delle prestazioni](./web-monitor-performance.md) in ASP.NET Core è limitato:

* Le versioni di SDK 2.4.1 e successive raccolgono i contatori delle prestazioni se l'applicazione è in esecuzione in App Web di Azure (Windows).
* Le versioni di SDK 2.7.1 e successive raccolgono contatori delle prestazioni se l'applicazione è in esecuzione in Windows e ha come destinazione `NETSTANDARD2.0` o versioni successive.
* Per le applicazioni destinate a .NET Framework, tutte le versioni di SDK supportano i contatori delle prestazioni.
* Le versioni di SDK 2.8.0 e successive supportano il contatore della cpu/memoria in Linux. Linux non supporta nessun altro contatore. Per ottenere i contatori di sistema in Linux (e in altri ambienti non Windows), usare [Contatore eventi](#eventcounter)

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`è abilitato per impostazione predefinita e raccoglie un set predefinito di contatori dalle app .NET Core 3. X. L'esercitazione [EventCounter](eventcounters.md) elenca il set predefinito di contatori raccolti. Sono inoltre disponibili istruzioni per la personalizzazione dell'elenco.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Abilitare la telemetria sul lato client per le applicazioni Web

I passaggi precedenti sono sufficienti per iniziare a raccogliere i dati di telemetria sul lato server. Se l'applicazione ha componenti lato client, seguire i passaggi successivi per iniziare a raccogliere i [dati di telemetria sull'utilizzo](./usage-overview.md).

1. In `_ViewImports.cshtml` aggiungere injection:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. In `_Layout.cshtml` inserire `HtmlHelper` alla fine della `<head>` sezione ma prima di qualsiasi altro script. Se si desidera segnalare i dati di telemetria JavaScript personalizzati dalla pagina, inserire il frammento di codice seguente:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
In alternativa, per usare `FullScript` `ScriptBody` è disponibile a partire da SDK v 2.14. Usare questa impostazione se è necessario controllare il `<script>` tag per impostare i criteri di sicurezza del contenuto:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

I `.cshtml` nomi file a cui si fa riferimento in precedenza si trovano in un modello di applicazione MVC predefinito. Infine, se si desidera abilitare correttamente il monitoraggio lato client per l'applicazione, il frammento di codice JavaScript deve essere visualizzato nella `<head>` sezione di ogni pagina dell'applicazione che si desidera monitorare. È possibile portare a termine questo obiettivo per questo modello di applicazione aggiungendo il frammento JavaScript a `_Layout.cshtml` . 

Se il progetto non include `_Layout.cshtml` , è comunque possibile aggiungere il [monitoraggio lato client](./website-monitoring.md). A tale scopo, aggiungere il frammento di codice JavaScript a un file equivalente che controlla il `<head>` di tutte le pagine all'interno dell'app. In alternativa, è possibile aggiungere il frammento di codice a più pagine, ma questa soluzione è difficile da gestire e in genere non è consigliata.

## <a name="configure-the-application-insights-sdk"></a>Configurare il Application Insights SDK

Per modificare la configurazione predefinita, è possibile personalizzare l'SDK Application Insights per ASP.NET Core. Gli utenti di Application Insights SDK ASP.NET possono avere familiarità con la modifica della configurazione tramite `ApplicationInsights.config` o modificando `TelemetryConfiguration.Active` . Modificare la configurazione in modo diverso per ASP.NET Core. Aggiungere la ASP.NET Core SDK all'applicazione e configurarla utilizzando ASP.NET Core [inserimento delle dipendenze](/aspnet/core/fundamentals/dependency-injection)predefinito. Apportare quasi tutte le modifiche alla configurazione nel `ConfigureServices()` metodo della `Startup.cs` classe, a meno che non si sia diretti diversamente. Nelle sezioni seguenti sono disponibili ulteriori informazioni.

> [!NOTE]
> Nelle applicazioni ASP.NET Core la modifica della configurazione mediante la modifica `TelemetryConfiguration.Active` non è supportata.

### <a name="using-applicationinsightsserviceoptions"></a>Uso di ApplicationInsightsServiceOptions

È possibile modificare alcune impostazioni comuni passando `ApplicationInsightsServiceOptions` a `AddApplicationInsightsTelemetry` , come nell'esempio seguente:

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

Elenco completo delle impostazioni in`ApplicationInsightsServiceOptions`

|Impostazione | Descrizione | Predefinito
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Abilita/Disabilita`PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Abilita/Disabilita`RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Abilita/Disabilita`EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Abilita/Disabilita`DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Abilita/Disabilita`AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Abilita/Disabilita`AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Abilita/Disabilita la funzionalità LiveMetrics | true
|EnableAdaptiveSampling | Abilita/Disabilita il campionamento adattivo | true
|EnableHeartbeat | Abilita/Disabilita la funzionalità heartbeat, che periodicamente (15 minuti per impostazione predefinita) Invia una metrica personalizzata denominata ' HeartbeatState ' con informazioni sul runtime come la versione .NET, le informazioni sull'ambiente di Azure, se applicabile e così via. | true
|AddAutoCollectedMetricExtractor | Abilita/Disabilita AutoCollectedMetrics Extractor, ovvero un TelemetryProcessor che invia metriche pre-aggregate relative a richieste/dipendenze prima che venga eseguita il campionamento. | true
|RequestCollectionOptions.TrackExceptions | Abilitare/disabilitare la creazione di report per il rilevamento delle eccezioni non gestite da parte del modulo di raccolta della richiesta. | false in NETSTANDARD 2.0 (poiché le eccezioni vengono rilevate con ApplicationInsightsLoggerProvider); in caso contrario, true.

Per l'elenco più aggiornato, vedere le [impostazioni configurabili in `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) .

### <a name="sampling"></a>campionamento

Il Application Insights SDK per ASP.NET Core supporta sia il campionamento a frequenza fissa che quello adattivo. Il campionamento adattivo è abilitato per impostazione predefinita. 

Per altre informazioni, vedere [configurare il campionamento adattivo per ASP.NET Core applicazioni](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Aggiunta di TelemetryInitializers

Usare gli [inizializzatori di telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) quando si vogliono arricchire i dati di telemetria con ulteriori informazioni.

Aggiungere qualsiasi nuovo `TelemetryInitializer` al `DependencyInjection` contenitore come illustrato nel codice seguente. L'SDK preleva automaticamente eventuali `TelemetryInitializer` aggiunte al `DependencyInjection` contenitore.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Rimozione di TelemetryInitializers

Gli inizializzatori di telemetria sono presenti per impostazione predefinita. Per rimuovere tutti gli inizializzatori di telemetria o specifici, usare il codice di esempio seguente *dopo* la chiamata a `AddApplicationInsightsTelemetry()` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

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

È possibile aggiungere processori di telemetria personalizzati a `TelemetryConfiguration` usando il metodo di estensione `AddApplicationInsightsTelemetryProcessor` in `IServiceCollection` . I processori di telemetria vengono usati in [scenari di filtro avanzati](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer). Usare l'esempio seguente.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurazione o rimozione di TelemetryModules predefiniti

Application Insights USA moduli di telemetria per raccogliere automaticamente dati di telemetria utili su carichi di lavoro specifici senza richiedere il rilevamento manuale da un utente.

I moduli di raccolta automatica seguenti sono abilitati per impostazione predefinita. Questi moduli sono responsabili della raccolta automatica dei dati di telemetria. È possibile disabilitarle o configurarle per modificarne il comportamento predefinito.

* `RequestTrackingTelemetryModule`: Raccoglie RequestTelemetry dalle richieste Web in ingresso.
* `DependencyTrackingTelemetryModule`: Raccoglie [DependencyTelemetry](./asp-net-dependencies.md) dalle chiamate http in uscita e dalle chiamate SQL.
* `PerformanceCollectorModule`-Raccoglie Windows PerformanceCounters.
* `QuickPulseTelemetryModule`-Raccoglie i dati di telemetria per la visualizzazione nel portale di metriche attive.
* `AppServicesHeartbeatTelemetryModule`-Raccoglie i battimenti cardiaci, che vengono inviati come metriche personalizzate, sull'ambiente app Azure servizio in cui è ospitata l'applicazione.
* `AzureInstanceMetadataTelemetryModule`-Raccoglie i battimenti cardiaci, che vengono inviati come metriche personalizzate, sull'ambiente di macchine virtuali di Azure in cui è ospitata l'applicazione.
* `EventCounterCollectionModule`-Raccoglie [EventCounters.](eventcounters.md) Questo modulo è una nuova funzionalità ed è disponibile nella versione SDK 2.8.0 e versioni successive.

Per configurare qualsiasi impostazione predefinita `TelemetryModule` , usare il metodo di estensione `ConfigureTelemetryModule<T>` su `IServiceCollection` , come illustrato nell'esempio seguente.

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

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

A partire dalla versione 2.12.2, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) contiene un'opzione semplice per disabilitare i moduli predefiniti.

### <a name="configuring-a-telemetry-channel"></a>Configurazione di un canale di telemetria

Il [canale di telemetria](./telemetry-channels.md) predefinito è `ServerTelemetryChannel` . È possibile eseguirne l'override come illustrato nell'esempio riportato di seguito.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Disabilitare la telemetria in modo dinamico

Se si vuole disabilitare la telemetria in modo condizionale e dinamico, è possibile risolvere `TelemetryConfiguration` l'istanza con ASP.NET Core contenitore di inserimento delle dipendenze in qualsiasi punto del codice e impostare `DisableTelemetry` il flag su di essa.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Il precedente non impedisce ai moduli di raccolta automatica di raccogliere dati di telemetria. Solo l'invio di dati di telemetria a Application Insights viene disabilitato con l'approccio precedente. Se non si desidera un particolare modulo di raccolta automatica, è preferibile [rimuovere il modulo di telemetria](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-application-insights-support-aspnet-core-3x"></a>Application Insights supporta ASP.NET Core 3. X?

Sì. Eseguire l'aggiornamento a [Application Insights SDK per ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versione 2.8.0 o successiva. Le versioni precedenti dell'SDK non supportano ASP.NET Core 3. X.

Inoltre, se si usano le istruzioni basate su Visual Studio da [qui](#enable-application-insights-server-side-telemetry-visual-studio), eseguire l'aggiornamento alla versione più recente di visual studio 2019 (16.3.0) per l'onboarding. Le versioni precedenti di Visual Studio non supportano l'onboarding automatico per le app ASP.NET Core 3. X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Come è possibile tenere traccia dei dati di telemetria che non vengono raccolti automaticamente?

Ottenere un'istanza di `TelemetryClient` usando l'inserimento del costruttore e chiamare il `TrackXXX()` metodo richiesto. Non è consigliabile creare nuove `TelemetryClient` istanze in un'applicazione ASP.NET Core. Un'istanza singleton di `TelemetryClient` è già registrata nel `DependencyInjection` contenitore, che condivide `TelemetryConfiguration` con il resto dei dati di telemetria. La creazione di una nuova `TelemetryClient` istanza è consigliata solo se è necessaria una configurazione separata dal resto dei dati di telemetria.

L'esempio seguente illustra come tenere traccia dei dati di telemetria aggiuntivi da un controller.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Per altre informazioni sulla creazione di report di dati personalizzati in Application Insights, vedere informazioni di riferimento sulle API per le [metriche personalizzate Application Insights](./api-custom-events-metrics.md). Un approccio simile può essere usato per inviare metriche personalizzate a Application Insights usando l' [API getmetric](./get-metric.md).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alcuni modelli di Visual Studio hanno usato il metodo di estensione UseApplicationInsights () in IWebHostBuilder per abilitare Application Insights. Questo utilizzo è ancora valido?

Anche se il metodo `UseApplicationInsights()` di estensione è ancora supportato, è contrassegnato come obsoleto in Application Insights SDK versione 2.8.0 e versioni successive. Verrà rimossa nella prossima versione principale dell'SDK. Il modo consigliato per abilitare la telemetria Application Insights consiste nell'usare `AddApplicationInsightsTelemetry()` perché fornisce overload per controllare alcune configurazioni. Inoltre, nelle app ASP.NET Core 3. X `services.AddApplicationInsightsTelemetry()` è l'unico modo per abilitare Application Insights.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Si distribuisce l'applicazione ASP.NET Core nelle app Web. È comunque possibile abilitare l'estensione Application Insights dalle app Web?

Se l'SDK viene installato in fase di compilazione, come illustrato in questo articolo, non è necessario abilitare l' [estensione Application Insights](./azure-web-apps.md) dal portale del servizio app. Anche se l'estensione è installata, verrà riattivata quando viene rilevato che l'SDK è già stato aggiunto all'applicazione. Se si Abilita Application Insights dall'estensione, non è necessario installare e aggiornare l'SDK. Tuttavia, se si Abilita Application Insights seguendo le istruzioni riportate in questo articolo, si ha maggiore flessibilità perché:

   * Application Insights la telemetria continuerà a funzionare in:
       * Tutti i sistemi operativi, tra cui Windows, Linux e Mac.
       * Tutte le modalità di pubblicazione, inclusi i dipendenti indipendenti o del Framework.
       * Tutti i Framework di destinazione, inclusa la .NET Framework completa.
       * Tutte le opzioni di hosting, tra cui app Web, macchine virtuali, Linux, contenitori, servizio Azure Kubernetes e hosting non di Azure.
       * Tutte le versioni di .NET Core, incluse le versioni di anteprima.
   * È possibile visualizzare i dati di telemetria localmente durante il debug da Visual Studio.
   * È possibile tenere traccia dei dati di telemetria personalizzati aggiuntivi tramite l' `TrackXXX()` API.
   * Si ha il controllo completo sulla configurazione.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>È possibile abilitare il monitoraggio Application Insights usando strumenti come Status Monitor?

No. [Status Monitor](./monitor-performance-live-website-now.md) e [Status Monitor V2](./status-monitor-v2-overview.md) attualmente supportano solo ASP.NET 4. x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights è abilitato automaticamente per l'applicazione ASP.NET Core 2,0?

Il `Microsoft.AspNetCore.All` metapacchetto 2,0 includeva SDK Application Insights (versione 2.1.0). Se si esegue l'applicazione nel debugger di Visual Studio, Visual Studio Abilita Application Insights e Visualizza i dati di telemetria localmente nell'IDE stesso. La telemetria non è stata inviata al servizio Application Insights a meno che non sia stata specificata una chiave di strumentazione. È consigliabile seguire le istruzioni riportate in questo articolo per abilitare Application Insights, anche per le app 2,0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eseguo l'applicazione in Linux, tutte le funzionalità sono supportate?

Sì. Il supporto delle funzionalità per l'SDK è lo stesso in tutte le piattaforme, con le eccezioni seguenti:

* L'SDK raccoglie i [contatori degli eventi](./eventcounters.md) in Linux perché i [contatori delle prestazioni](./performance-counters.md) sono supportati solo in Windows. La maggior parte delle metriche è la stessa.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Questo SDK è supportato per le nuove applicazioni modello di servizio del ruolo di lavoro .NET Core 3. X?

Questo SDK richiede `HttpContext` e pertanto non funziona in alcuna applicazione non http, incluse le applicazioni del servizio di lavoro .NET Core 3. X. Fare riferimento a [questo](worker-service.md) documento per abilitare Application Insights in tali applicazioni, usando il nuovo SDK Microsoft. ApplicationInsights. WorkerService.

## <a name="open-source-sdk"></a>SDK open source

[Leggere e contribuire al codice](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare i flussi utente](./usage-flows.md) per comprendere in che modo gli utenti si spostano nell'app.
* [Configurare una raccolta di snapshot](./snapshot-debugger.md) per visualizzare lo stato del codice sorgente e delle variabili nel momento in cui viene generata un'eccezione.
* [Usare l'API](./api-custom-events-metrics.md) per inviare i propri eventi e metriche per una visualizzazione dettagliata delle prestazioni e dell'utilizzo dell'app.
* Usare [test di disponibilità](./monitor-web-app-availability.md) per controllare costantemente l'app da ogni parte del mondo.
* [Inserimento di dipendenze in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection)

