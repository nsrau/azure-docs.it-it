---
title: Informazioni dettagliate sulle applicazioni di Azure per applicazioni ASP.NET Core Documenti Microsoft
description: Monitorare le applicazioni Web ASP.NET Core per identificare disponibilità, prestazioni e utilizzo.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: d6a0e507022452f1491e71651ba3bc8db3d1c090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284790"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights per applicazioni ASP.NET Core

In questo articolo viene descritto come abilitare Application Insights per un'applicazione [ASP.NET Core.This](https://docs.microsoft.com/aspnet/core) article describes how to enable Application Insights for an ASP.NET Core application. Dopo aver completato le istruzioni in questo articolo, Application Insights raccoglierà richieste, dipendenze, eccezioni, contatori delle prestazioni, heartbeat e log dall'applicazione ASP.NET Core.When you complete the instructions in this article, Application Insights will collect requests, dependencies, exceptions, performance counters, heartbeats, and logs from your ASP.NET Core application.

L'esempio che verrà utilizzato in questo `netcoreapp2.2`caso è [un'applicazione MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) destinata a . È possibile applicare queste istruzioni a tutte le applicazioni ASP.NET Core.You can apply these instructions to all ASP.NET Core applications.

## <a name="supported-scenarios"></a>Scenari supportati

[Application Insights SDK per ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) può monitorare le applicazioni indipendentemente da dove o come vengono eseguite. Se l'applicazione è in esecuzione e dispone di connettività di rete ad Azure, è possibile raccogliere dati di telemetria. Il monitoraggio di Application Insights è supportato ovunque sia supportato .NET Core.Application Insights monitoring is supported everywhere .NET Core is supported. Coperture di supporto:
* **Sistema operativo**: Windows, Linux o Mac.
* **Metodo**di hosting : In corso o fuori processo.
* **Metodo di distribuzione:** dipendente dal framework o autonomo.
* **Server Web**: IIS (Internet Information Server) o Kestrel.
* **Piattaforma di hosting:** la funzionalità App Web del servizio app di Azure, della macchina virtuale di Azure, di Docker, del servizio Azure Kubernetes (AKS) e così via.
* **Versione di .NET Core Runtime**: 1.XX, 2.XX o 3.XX
* **IDE**: Visual Studio, codice VS o riga di comando.

> [!NOTE]
> Se si usa ASP.NET Core 3.X insieme a Application Insights, usare la versione [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) o successiva. Questa è l'unica versione che supporta ASP.NET Core 3.X.

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione ASP.NET Core funzionante. Se è necessario creare un'applicazione ASP.NET Core, seguire questa [esercitazione ASP.NET Core.](https://docs.microsoft.com/aspnet/core/getting-started/)
- Chiave di strumentazione di Application Insights valida. Questa chiave è necessaria per inviare dati di telemetria ad Application Insights.This key is required to send any telemetry to Application Insights. Se è necessario creare una nuova risorsa di Application Insights per ottenere una chiave di strumentazione, vedere [Creare una risorsa di Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Abilitare la telemetria lato server di Application Insights (Visual Studio)Enable Application Insights server-side telemetry (Visual Studio)

1. Aprire il progetto in Visual Studio.

    > [!TIP]
    > Se lo si desidera, è possibile impostare il controllo del codice sorgente per il progetto in modo da poter tenere traccia di tutte le modifiche apportate da Application Insights. Per abilitare il controllo del codice sorgente, selezionare **Aggiungi file** > **al controllo del codice sorgente**.

2. Selezionare **Report Aggiungi** > **dati di telemetria di Application Insights**.

3. Selezionare **Inizia**. Il testo di questa selezione può variare a seconda della versione di Visual Studio.This selection's text might vary, depending on your version of Visual Studio. Alcune versioni precedenti utilizzano invece un pulsante **Start Free.**

4. Selezionare la propria sottoscrizione. Selezionare quindi**Registro** **risorse** > .

5. Dopo aver aggiunto Application Insights al progetto, verificare di utilizzare la versione stabile più recente dell'SDK. Passare a **Project** > **Manage NuGet Packages** > **Microsoft.ApplicationInsights.AspNetCore**. Se necessario, scegliere **Aggiorna**.

     ![Screenshot che mostra dove selezionare il pacchetto di Application Insights per l'aggiornamento](./media/asp-net-core/update-nuget-package.png)

6. Se è stato seguito il suggerimento facoltativo e il progetto è stato aggiunto al controllo del codice sorgente, passare a **View** > **Team Explorer** > **Changes**. Selezionare quindi ogni file per visualizzare una visualizzazione diff delle modifiche apportate dai dati di telemetria di Application Insights.Then select each file to see a diff view of the changes made by Application Insights telemetry.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Abilitare i dati di telemetria sul lato server di Application Insights (senza Visual Studio)Enable Application Insights server-side telemetry (no Visual Studio)

1. Installare il [pacchetto NuGet](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)di Application Insights SDK per ASP.NET Core . Si consiglia di utilizzare sempre l'ultima versione stabile. Trova le note sulla versione complete per l'SDK nel [repository Open-source GitHub](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Nell'esempio di codice seguente vengono illustrate le `.csproj` modifiche da aggiungere al file del progetto.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Aggiungere `services.AddApplicationInsightsTelemetry();` al `ConfigureServices()` metodo `Startup` nella classe, come in questo esempio:Add to the method in your class, as in this example:

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

3. Impostare la chiave di strumentazione.

    Sebbene sia possibile fornire la `AddApplicationInsightsTelemetry`chiave di strumentazione come argomento a , è consigliabile specificare la chiave di strumentazione nella configurazione. Nell'esempio di codice riportato di `appsettings.json`seguito viene illustrato come specificare una chiave di strumentazione in . Assicurarsi `appsettings.json` che venga copiato nella cartella radice dell'applicazione durante la pubblicazione.

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

    In `APPINSIGHTS_INSTRUMENTATIONKEY` genere, specifica la chiave di strumentazione per le applicazioni distribuite in App Web di Azure.Typically, specifies the instrumentation key for applications deployed to Azure Web Apps.

    > [!NOTE]
    > Una chiave di strumentazione specificata `APPINSIGHTS_INSTRUMENTATIONKEY`nel codice supera la variabile di ambiente , che vince su altre opzioni.

## <a name="run-your-application"></a>Eseguire l'applicazione

Eseguire l'applicazione e apportare richieste ad essa. I dati di telemetria dovrebbero ora passare a Application Insights.Telemetry should now flow to Application Insights. Application Insights SDK raccoglie automaticamente le richieste Web in arrivo all'applicazione, insieme ai dati di telemetria seguenti.

### <a name="live-metrics"></a>Metriche attive

[Le metriche in tempo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) reale possono essere usate per verificare rapidamente se il monitoraggio di Application Insights è configurato correttamente. Anche se potrebbero essere stati alcuni minuti prima che i dati di telemetria inizino a essere visualizzati nel portale e nell'analisi, le metriche dinamiche mostrano l'utilizzo della CPU del processo in esecuzione quasi in tempo reale. Può anche mostrare altri dati di telemetria come richieste, dipendenze, tracce e così via.

### <a name="ilogger-logs"></a>Registri ILogger

I log `ILogger` generati tramite gravità `Warning` o superiore vengono acquisiti automaticamente. Seguire i documenti di ILogger per personalizzare i livelli di log acquisiti da Application Insights.Follow [ILogger docs](ilogger.md#control-logging-level) to customize which log levels are captured by Application Insights.

### <a name="dependencies"></a>Dependencies

La raccolta delle dipendenze è abilitata per impostazione predefinita. [In](asp-net-dependencies.md#automatically-tracked-dependencies) questo articolo vengono illustrate le dipendenze raccolte automaticamente e contengono anche i passaggi per eseguire il rilevamento manuale.

### <a name="performance-counters"></a>Contatori delle prestazioni

Il supporto per [i contatori delle prestazioni](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) in ASP.NET Core è limitato:Support for performance counters in ASP.NET Core is limited:

* SDK versioni 2.4.1 e successive raccolgono i contatori delle prestazioni se l'applicazione è in esecuzione in App Web di Azure (Windows).
* SDK versioni 2.7.1 e successive raccolgono i contatori `NETSTANDARD2.0` delle prestazioni se l'applicazione è in esecuzione in Windows e di destinazione o versioni successive.
* Per le applicazioni destinate a .NET Framework, tutte le versioni di SDK supportano i contatori delle prestazioni.
* SDK versioni 2.8.0 e successive supportano il contatore cpu/memoria in Linux. Nessun altro contatore è supportato in Linux. Il modo consigliato per ottenere i contatori di sistema in Linux (e in altri ambienti non Windows) consiste [nell'utilizzare EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter (Contatore evento)

`EventCounterCollectionModule`è abilitato per impostazione predefinita e raccoglierà un set predefinito di contatori dalle app .NET Core 3.X. L'esercitazione [EventCounter](eventcounters.md) elenca il set predefinito di contatori raccolti. Ha anche le istruzioni per personalizzare l'elenco.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Abilitare la telemetria lato client per le applicazioni WebEnable client-side telemetry for web applications

I passaggi precedenti sono sufficienti per iniziare a raccogliere dati di telemetria sul lato server. Se l'applicazione dispone di componenti sul lato client, seguire i passaggi successivi per iniziare a raccogliere dati [di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)di utilizzo.

1. In `_ViewImports.cshtml`, aggiungere l'iniezione:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. In `_Layout.cshtml`, `HtmlHelper` inserire alla `<head>` fine della sezione, ma prima di qualsiasi altro script. Se si desidera segnalare dati di telemetria JavaScript personalizzati dalla pagina, inserirla dopo questo frammento di codice:If you want to report any custom JavaScript telemetry from the page, inject it after this snippet:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
In alternativa, `FullScript` è `ScriptBody` possibile utilizzare il è disponibile a partire da SDK v2.14. Utilizzare questa opzione se `<script>` è necessario controllare il tag per impostare un criterio di sicurezza del contenuto:Use this if you need to control the tag to set a Content Security Policy:

    ```cshtml
        <script> // apply custom changes to this script tag.
            @Html.Raw(JavaScriptSnippet.ScriptBody)
        </script>
    ```

I `.cshtml` nomi di file a cui si fa riferimento in precedenza provengono da un modello di applicazione MVC predefinito. In definitiva, se si desidera abilitare correttamente il monitoraggio lato client `<head>` per l'applicazione, lo snippet JavaScript deve essere visualizzato nella sezione di ogni pagina dell'applicazione che si desidera monitorare. È possibile raggiungere questo obiettivo per questo modello `_Layout.cshtml`di applicazione aggiungendo lo snippet JavaScript a . 

Se il progetto non `_Layout.cshtml`include , è comunque possibile aggiungere il [monitoraggio lato client.](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring) Puoi farlo aggiungendo lo snippet JavaScript a un `<head>` file equivalente che controlla la di tutte le pagine all'interno dell'app. In alternativa, è possibile aggiungere il frammento di codice a più pagine, ma questa soluzione è difficile da gestire e in genere non è consigliabile.

## <a name="configure-the-application-insights-sdk"></a>Configurare Application Insights SDK

È possibile personalizzare Application Insights SDK per ASP.NET Core per modificare la configurazione predefinita. Gli utenti di Application Insights ASP.NET SDK potrebbero `ApplicationInsights.config` avere `TelemetryConfiguration.Active`familiarità con la modifica della configurazione utilizzando o modificando . Modificare la configurazione in modo diverso per ASP.NET Core. Aggiungere l'ASP.NET Core SDK all'applicazione e configurarlo utilizzando ASP.NET Core built-in [dependency injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Apportare quasi tutte `ConfigureServices()` le modifiche `Startup.cs` di configurazione nel metodo della classe, a meno che non si venga indirizzati diversamente. Le sezioni seguenti offrono ulteriori informazioni.

> [!NOTE]
> Nelle applicazioni ASP.NET Core, `TelemetryConfiguration.Active` la modifica della configurazione tramite modifica non è supportata.

### <a name="using-applicationinsightsserviceoptions"></a>Utilizzo di ApplicationInsightsServiceOptionsUsing ApplicationInsightsServiceOptions

È possibile modificare alcune impostazioni `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`comuni passando a , come in questo esempio:

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
|EnableEventCounterCollectionModule (Modulo di Abilitazionedei)   | Abilita/Disabilita`EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Abilita/Disabilita`DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Abilita/Disabilita`AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Abilita/Disabilita`AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream (EnableQuickPulseMetricStream) | Attivare/disattivare la funzionalità LiveMetricsEnable/Disable LiveMetrics feature | true
|EnableAdaptiveSampling (CampionamentoAdad) | Abilita/Disabilita campionamento adattivo | true
|EnableHeartbeat | Abilita/Disabilita heartbeat, che periodicamente (impostazione predefinita di 15 min) invia una metrica personalizzata denominata "HeartBeatState" con informazioni sul runtime come la versione .NET, le informazioni sull'ambiente di Azure, se applicabile, e così via. | true
|AddAutoCollectedMetricExtractor | Abilitare/disabilitare l'estrattore AutoCollectedMetrics, ovvero un TelemetryProcessor che invia metriche preaggregate su Richieste/Dipendenze prima che venga eseguito il campionamento. | true
|RequestCollectionOptions.TrackExceptions | Abilitare/disabilitare la segnalazione del rilevamento delle eccezioni non gestite da parte del modulo di raccolta delle richieste. | false in NETSTANDARD2.0 (perché le eccezioni vengono rilevate con ApplicationInsightsLoggerProvider), true in caso contrario.

Vedere le [impostazioni `ApplicationInsightsServiceOptions` configurabili in](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) per l'elenco più aggiornato.

### <a name="sampling"></a>campionamento

Application Insights SDK per ASP.NET Core supporta sia il campionamento a velocità fissa che il campionamento adattivo. Il campionamento adattivo è abilitato per impostazione predefinita. 

Per ulteriori informazioni, consultate [Configurare il campionamento adattivo per applicazioni ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Aggiunta di TelemetryInitializersAdding TelemetryInitializers

Usare [gli inizializzatori](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) di telemetria quando si desidera definire le proprietà globali inviate con tutti i dati di telemetria.

Aggiungere qualsiasi `TelemetryInitializer` nuovo `DependencyInjection` al contenitore come illustrato nel codice seguente. L'SDK preleva `TelemetryInitializer` automaticamente qualsiasi elemento `DependencyInjection` aggiunto al contenitore.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Rimozione di TelemetryInitializersRemoving TelemetryInitializers

Gli inizializzatori di telemetria sono presenti per impostazione predefinita. Per rimuovere tutti gli inizializzatori di telemetria `AddApplicationInsightsTelemetry()`o specifici, utilizzare il codice di esempio seguente dopo *aver* chiamato .

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

È possibile aggiungere processori `TelemetryConfiguration` di telemetria `AddApplicationInsightsTelemetryProcessor` `IServiceCollection`personalizzati a utilizzando il metodo di estensione in . I processori di telemetria vengono utilizzati in scenari di [filtro avanzati.](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) Utilizzare l'esempio seguente.

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configurazione o rimozione di TelemetryModules predefinitiConfiguring or removing default TelemetryModules

Application Insights usa i moduli di telemetria per raccogliere automaticamente dati di telemetria utili su carichi di lavoro specifici senza richiedere il rilevamento manuale da parte dell'utente.

I seguenti moduli di raccolta automatica sono abilitati per impostazione predefinita. Questi moduli sono responsabili della raccolta automatica dei dati di telemetria. È possibile disabilitarli o configurarli per modificarne il comportamento predefinito.

* `RequestTrackingTelemetryModule`- Raccoglie RequestTelemetry dalle richieste web in arrivo.- Collects RequestTelemetry from incoming web requests.
* `DependencyTrackingTelemetryModule`- Raccoglie DependencyTelemetry dalle chiamate http in uscita e le chiamate SQL.- Collects DependencyTelemetry from outgoing http calls and sql calls.
* `PerformanceCollectorModule`- Raccoglie PerformanceCounters di Windows.- Collects Windows PerformanceCounters.
* `QuickPulseTelemetryModule`- Raccoglie i dati di telemetria per la visualizzazione nel portale Live Metrics.- Collects telemetry for showing in Live Metrics portal.
* `AppServicesHeartbeatTelemetryModule`- Raccoglie battiti cardiaci (che vengono inviati come metriche personalizzate), sull'ambiente del servizio app di Azure in cui è ospitata l'applicazione.- Collects heart beats (which are sent as custom metrics), about Azure App Service environment where application is hosted.
* `AzureInstanceMetadataTelemetryModule`- Raccoglie battiti cardiaci (che vengono inviati come metriche personalizzate), sull'ambiente VM di Azure in cui è ospitata l'applicazione.- Collects heart beats (which are sent as custom metrics), about Azure VM environment where application is hosted.
* `EventCounterCollectionModule`- Raccoglie [EventCounters.- Collects EventCounters.](eventcounters.md) Questo modulo è una nuova funzionalità ed è disponibile in SDK versione 2.8.0 e successive.

Per configurare `TelemetryModule`qualsiasi valore `ConfigureTelemetryModule<T>` predefinito, utilizzare il metodo di estensione su `IServiceCollection`, come illustrato nell'esempio seguente.

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

A partire dalla versione 2.12.2, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) contiene un'opzione semplice per disabilitare uno qualsiasi dei moduli predefiniti.

### <a name="configuring-a-telemetry-channel"></a>Configurazione di un canale di telemetriaConfiguring a telemetry channel

Il canale `ServerTelemetryChannel`predefinito è . È possibile eseguirne l'override come illustrato nell'esempio seguente.

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

### <a name="disable-telemetry-dynamically"></a>Disabilitare i dati di telemetria in modo dinamico

Se si vuole disabilitare i dati di `TelemetryConfiguration` telemetria in modo condizionale e dinamico, è possibile risolvere l'istanza con ASP.NET contenitore di inserimento delle dipendenze core in qualsiasi punto del codice e impostare `DisableTelemetry` il flag su di esso.

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

Quanto sopra non impedisce ai moduli di raccolta automatica di raccogliere dati di telemetria. Solo l'invio di dati di telemetria ad Application Insights viene disabilitato con l'approccio precedente. Se un particolare modulo di raccolta automatica non è desiderato, è consigliabile rimuovere il modulo di [telemetria](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-application-insights-support-aspnet-core-3x"></a>Application Insights supporta ASP.NET Core 3.X?

Sì. Eseguire l'aggiornamento a [Application Insights SDK per ASP.NET versione principale](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.8.0 o successiva. Le versioni precedenti dell'SDK non supportano ASP.NET Core 3.X.

Inoltre, se si utilizzano istruzioni basate su Visual Studio da [qui](#enable-application-insights-server-side-telemetry-visual-studio), aggiornare alla versione più recente di Visual Studio 2019 (16.3.0) per eseguire l'onboarding. Le versioni precedenti di Visual Studio non supportano l'onboarding automatico per ASP.NET app Core 3.X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Come è possibile tenere traccia dei dati di telemetria non raccolti automaticamente?

Ottenere un'istanza di `TelemetryClient` utilizzando l'inserimento del costruttore e chiamare il metodo richiesto `TrackXXX()` su di esso. Non è consigliabile `TelemetryClient` creare nuove istanze in un'applicazione ASP.NET Core.We don't recommend creating new instances in an ASP.NET Core application. Un'istanza singleton di `TelemetryClient` è `DependencyInjection` già registrata `TelemetryConfiguration` nel contenitore, che viene condivisione con il resto dei dati di telemetria. La creazione `TelemetryClient` di una nuova istanza è consigliata solo se è necessaria una configurazione separata dal resto dei dati di telemetria.

Nell'esempio seguente viene illustrato come tenere traccia di dati di telemetria aggiuntivi da un controller.

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

Per altre informazioni sulla creazione di report sui dati personalizzati in Application Insights, vedere Informazioni di [riferimento sull'API delle metriche personalizzate di Application Insights.For more](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)information about custom data reporting in Application Insights, see Application Insights custom metrics API reference .

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alcuni modelli di Visual Studio hanno usato il metodo di estensione UseApplicationInsights() in IWebHostBuilder per abilitare Application Insights. Questo utilizzo è ancora valido?

Mentre il `UseApplicationInsights()` metodo di estensione è ancora supportato, è contrassegnato come obsoleto in Application Insights SDK versione 2.8.0 e versioni successive. Verrà rimosso nella prossima versione principale dell'SDK. Il modo consigliato per abilitare `AddApplicationInsightsTelemetry()` i dati di telemetria di Application Insights consiste nell'usare gli overload per controllare alcune configurazioni. Inoltre, in ASP.NET applicazioni Core `services.AddApplicationInsightsTelemetry()` 3.X, è l'unico modo per abilitare le informazioni dettagliate sulle applicazioni.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Sto distribuendo l'applicazione ASP.NET Core in applicazioni Web. È comunque necessario abilitare l'estensione Application Insights dalle app Web?

Se l'SDK viene installato in fase di compilazione, come illustrato in questo articolo, non è necessario abilitare [l'estensione Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) dal portale del servizio app. Anche se l'estensione è installata, verrà eseguito il back off quando rileva che l'SDK è già stato aggiunto all'applicazione. Se si abilita Application Insights dall'estensione, non è necessario installare e aggiornare l'SDK. Tuttavia, se si abilita Application Insights seguendo le istruzioni in questo articolo, si ha maggiore flessibilità perché:But if you enable Application Insights by following instructions in this article, you have more flexibility because:

   * I dati di telemetria di Application Insights continueranno a funzionare in:Application Insights telemetry will continue to work in:
       * Tutti i sistemi operativi, inclusi Windows, Linux e Mac.
       * Tutte le modalità di pubblicazione, incluse le modalità di pubblicazione autonome o dipendenti dal framework.
       * Tutti i framework di destinazione, incluso .NET Framework completo.
       * Tutte le opzioni di hosting, tra cui app Web, macchine virtuali, Linux, contenitori, servizio Azure Kubernetes e hosting non Azure.All hosting options, including Web Apps, VMs, Linux, containers, Azure Kubernetes Service, and non-Azure hosting.
       * Tutte le versioni di .NET Core, incluse le versioni di anteprima.
   * È possibile visualizzare la telemetria in locale durante il debug da Visual Studio.You can see telemetry locally when you're debugging from Visual Studio.
   * È possibile tenere traccia di `TrackXXX()` dati di telemetria personalizzati aggiuntivi usando l'API.
   * Si dispone del controllo completo sulla configurazione.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>È possibile abilitare il monitoraggio di Application Insights usando strumenti come Monitoraggio stato?

No. [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [Status Monitor v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) supportano attualmente solo ASP.NET 4.x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights è abilitato automaticamente per l'applicazione ASP.NET Core 2.0?

Il `Microsoft.AspNetCore.All` metapacchetto 2.0 includeva Application Insights SDK (versione 2.1.0). Se si esegue l'applicazione nel debugger di Visual Studio, Visual Studio abilita Application Insights e mostra la telemetria in locale nell'IDE stesso. La telemetria non è stata inviata al servizio Application Insights a meno che non sia stata specificata una chiave di strumentazione. È consigliabile seguire le istruzioni in questo articolo per abilitare Application Insights, anche per le app 2.0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se si esegue l'applicazione in Linux, sono supportate tutte le funzionalità?

Sì. Il supporto delle funzionalità per l'SDK è lo stesso in tutte le piattaforme, con le eccezioni seguenti:

* L'SDK raccoglie i contatori di eventi in Linux perché [i contatori delle prestazioni](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) sono supportati solo in Windows.The SDK collects Event [Counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) on Linux because Performance Counters are only supported in Windows. La maggior parte delle metriche sono le stesse.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Questo SDK è supportato per le nuove applicazioni modello del servizio di lavoro .NET Core 3.X?

Questo SDK `HttpContext`richiede , e pertanto non funziona in tutte le applicazioni non HTTP, incluse le applicazioni del servizio di lavoro .NET Core 3.X. Fare riferimento a [questo](worker-service.md) documento per abilitare le informazioni dettagliate sulle applicazioni in tali applicazioni, utilizzando il Microsoft.ApplicationInsights.WorkerService SDK appena rilasciato.

## <a name="open-source-sdk"></a>SDK open source

[Leggere e contribuire al codice](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="video"></a>Video

- Dai un'occhiata a questo video aggiuntivo esterno per [configurare Application Insights con .NET Core e Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) da zero.
- Dai un'occhiata a questo video aggiuntivo esterno per [configurare Application Insights con .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) da zero.

## <a name="next-steps"></a>Passaggi successivi

* [Esplora i flussi degli utenti](../../azure-monitor/app/usage-flows.md) per capire come gli utenti si spostano nella tua app.
* [Configurare una raccolta snapshot](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) per visualizzare lo stato del codice sorgente e le variabili nel momento in cui viene generata un'eccezione.
* [Usa l'API](../../azure-monitor/app/api-custom-events-metrics.md) per inviare eventi e metriche personalizzati per una visualizzazione dettagliata delle prestazioni e dell'utilizzo della tua app.
* Usare [test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) per controllare costantemente l'app da ogni parte del mondo.
* [Inserimento delle dipendenze nel ASP.NET CoreDependency Injection in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
