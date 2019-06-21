---
title: Azure Application Insights per applicazioni ASP.NET Core | Microsoft Docs
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
ms.openlocfilehash: 7fe5a4f5a5d1d254918f1b4f997acfb9cf67a75b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272449"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights per applicazioni ASP.NET Core

Questo articolo descrive come abilitare Application Insights per un [ASP.NET Core](https://docs.microsoft.com/aspnet/core) dell'applicazione. Dopo aver completato le istruzioni riportate in questo articolo, Application Insights raccoglierà le richieste, dipendenze, eccezioni, i contatori delle prestazioni, heartbeat e i log dall'applicazione ASP.NET Core. 

L'esempio verrà usato in questo caso è un' [applicazione MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) che fa riferimento a `netcoreapp2.2`. È possibile applicare queste istruzioni per tutte le applicazioni ASP.NET Core.

## <a name="supported-scenarios"></a>Scenari supportati

Il [Application Insights SDK per ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) può monitorare le applicazioni indipendentemente da dove o come vengono eseguiti. Se l'applicazione è in esecuzione e disponga della connettività di rete in Azure, è possibile raccogliere i dati di telemetria. Monitoraggio di Application Insights è supportato ovunque .NET Core è supportato. Supporto include:
* **Sistema operativo**: Windows, Linux o Mac.
* **Metodo di hosting**: Il processo o fuori del processo. 
* **Metodo di distribuzione**: Framework autonoma o dipendenti.
* **Server Web**: IIS (Internet Information Server) o Kestrel. 
* **Piattaforma di hosting**: La funzionalità App Web del servizio App di Azure, macchine Virtuali di Azure, Docker, Azure Kubernetes Service (AKS) e così via.
* **IDE**: Visual Studio, Visual Studio Code o della riga di comando.

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione ASP.NET Core. Se è necessario creare un'applicazione ASP.NET Core, seguire questo [esercitazione ASP.NET Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Una chiave di strumentazione di Application Insights valida. Questa chiave è necessaria per inviare i dati di telemetria ad Application Insights. Se è necessario creare una nuova risorsa di Application Insights per ottenere una chiave, vedere strumentazione [creare una risorsa di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Abilitare la telemetria di Application Insights lato server (Visual Studio)

1. Aprire il progetto in Visual Studio.

    > [!TIP]
    > Se si desidera, è possibile impostare il controllo codice sorgente per il progetto in modo che è possibile tenere traccia di tutte le modifiche apportate da Application Insights. Per abilitare il controllo del codice sorgente, selezionare **File** > **aggiungere al controllo del codice sorgente**.

2. Selezionare **Progetto** > **Aggiungi Application Insights Telemetry**.

3. Selezionare **Attività iniziali**. Testo della selezione, questo può variare a seconda della versione di Visual Studio. Alcune versioni precedenti usano un **inizia gratis** pulsante invece.

4. Selezionare la propria sottoscrizione. Quindi selezionare **Resource** > **registrare**.

5. Dopo l'aggiunta di Application Insights al progetto, selezionare per confermare che si sta usando la versione stabile più recente del SDK. Passare a **Project** > **Gestisci pacchetti NuGet** > **Microsoft.ApplicationInsights.AspNetCore**. Se è necessario, scegliere **Update**.

     ![Screenshot che illustra come selezionare il pacchetto di Application Insights per l'aggiornamento](./media/asp-net-core/update-nuget-package.png)

6. Se è seguito il suggerimento facoltativo e aggiunto al progetto al controllo del codice sorgente, andare al **View** > **Team Explorer** > **modifiche**. Selezionare quindi ogni file per visualizzare una visualizzazione differenze delle modifiche apportate da Application Insights telemetry.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Abilitare la telemetria di Application Insights lato server (non Visual Studio)

1. Installare il [il pacchetto NuGet di Application Insights SDK per ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). È consigliabile usare sempre la versione stabile più recente. Note sulla versione complete per il SDK disponibili nel [repository GitHub open source](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Esempio di codice seguente mostra le modifiche da aggiungere al progetto `.csproj` file.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. Aggiungere `services.AddApplicationInsightsTelemetry();` per il `ConfigureServices()` metodo nella `Startup` (classe), come nel seguente esempio:

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

3. Consente di impostare la chiave di strumentazione.

    Sebbene sia possibile fornire la chiave di strumentazione come argomento a `AddApplicationInsightsTelemetry`, è consigliabile specificare la chiave di strumentazione nella configurazione. Esempio di codice seguente viene illustrato come specificare una chiave di strumentazione in `appsettings.json`. Assicurarsi che `appsettings.json` viene copiato nella cartella radice dell'applicazione durante la pubblicazione.

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

    In alternativa, specificare la chiave di strumentazione in una delle variabili di ambiente seguenti:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Ad esempio:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    In genere, `APPINSIGHTS_INSTRUMENTATIONKEY` specifica la chiave di strumentazione delle applicazioni distribuite alle App Web.

    > [!NOTE]
    > Una chiave di strumentazione specificata nel codice prevale sulla variabile di ambiente `APPINSIGHTS_INSTRUMENTATIONKEY`, quale prevale su altre opzioni.

## <a name="run-your-application"></a>Eseguire l'applicazione

Eseguire l'applicazione e inviare richieste a questo. I dati di telemetria ora devono essere trasmessi ad Application Insights. Application Insights SDK raccoglie automaticamente i seguenti dati di telemetria.

|Le richieste o le dipendenze |Dettagli|
|---------------|-------|
|Requests | Richieste web in ingresso all'applicazione. |
|HTTP o HTTPS | Le chiamate effettuate con `HttpClient`. |
|SQL | Le chiamate effettuate con `SqlClient`. |
|[Archiviazione di Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chiamate effettuate con il client di archiviazione di Azure. |
|[SDK client di hub eventi](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versione 1.1.0 e versioni successive. |
|[ServiceBus client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versione 3.0.0 e versioni successive. |
|Azure Cosmos DB | Automaticamente registrate solo se viene utilizzato HTTP/HTTPS. Application Insights non acquisisce la modalità TCP. |

### <a name="performance-counters"></a>Contatori delle prestazioni

Supporto per [contatori delle prestazioni](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) in ASP.NET Core è limitato:

   * Versioni del SDK 2.4.1 e in un secondo momento Raccogli contatori se l'applicazione è in esecuzione nelle App Web (Windows).
   * Contatori 2.7.0-beta3 versioni SDK e versioni successive delle prestazioni raccogliere se l'applicazione è in esecuzione in Windows e le destinazioni `NETSTANDARD2.0` o versione successiva.
   * Per le applicazioni destinate a .NET Framework, tutte le versioni del SDK supportano i contatori delle prestazioni.
 
Questo articolo verrà aggiornato quando viene aggiunto il supporto di contatore delle prestazioni in Linux.

### <a name="ilogger-logs"></a>Log di ILogger

[I log di ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger) livello di gravità `Warning` o versioni successive, vengono acquisiti automaticamente nel SDK versioni 2.7.0-beta3 e versioni successive.

### <a name="live-metrics"></a>Metriche in tempo reale

Potrebbe richiedere alcuni minuti prima dell'avvio di dati di telemetria disponibili nel portale. Per verificare rapidamente che tutto funzioni correttamente, è consigliabile usare [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) quando si effettua richieste per l'applicazione in esecuzione.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Abilitare la telemetria lato client per le applicazioni web

I passaggi precedenti sono sufficienti per aiutarti a iniziare a raccogliere i dati di telemetria lato server. Se l'applicazione include i componenti lato client, attenersi alla procedura seguente per iniziare a raccogliere [telemetria relativa all'utilizzo](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. In `_ViewImports.cshtml`, aggiungere injection:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. Nelle `_Layout.cshtml`, inserire `HtmlHelper` alla fine del `<head>` sezione ma prima di qualsiasi altro script. Se si desidera segnalare i dati di telemetria JavaScript personalizzato dalla pagina, viene inserita dopo questo frammento di codice:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

Il `.cshtml` sono nomi di file citati in precedenza da un modello di applicazione MVC predefinita. Infine, se si desidera attivare correttamente il monitoraggio lato client per l'applicazione, il frammento JavaScript deve comparire nel `<head>` sezione di ogni pagina dell'applicazione che si desidera monitorare. È possibile raggiungere questo obiettivo di questo modello di applicazione aggiungendo il frammento di codice JavaScript `_Layout.cshtml`. 

Se il progetto non include `_Layout.cshtml`, è comunque possibile aggiungere [monitoraggio lato client](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring). È possibile farlo aggiungendo il frammento di codice JavaScript in un file equivalente che controlla il `<head>` di tutte le pagine all'interno dell'app. Oppure è possibile aggiungere il frammento di codice su più pagine, ma questa soluzione è difficile da gestire e a livello generale non è consigliabile.

## <a name="configure-the-application-insights-sdk"></a>Configurare Application Insights SDK

È possibile personalizzare Application Insights SDK per ASP.NET Core modificare la configurazione predefinita. Gli utenti di ASP.NET SDK di Application Insights possono avere familiari con la modifica di configurazione usando `ApplicationInsights.config` o modificando `TelemetryConfiguration.Active`. Modificare una configurazione in modo diverso per ASP.NET Core. Aggiungere il SDK di ASP.NET Core all'applicazione e può essere configurato mediante incorporato di ASP.NET Core [inserimento delle dipendenze](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). Apportare quasi tutte le modifiche alla configurazione nel `ConfigureServices()` metodo di `Startup.cs` classe, a meno che non si viene indirizzati in caso contrario. Le sezioni seguenti offrono altre informazioni.

> [!NOTE]
> Nelle applicazioni ASP.NET Core, la modifica di configurazione modificando `TelemetryConfiguration.Active` non è consigliato.

### <a name="using-applicationinsightsserviceoptions"></a>Usando ApplicationInsightsServiceOptions

È possibile modificare alcune impostazioni comuni passando `ApplicationInsightsServiceOptions` a `AddApplicationInsightsTelemetry`, come in questo esempio:

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

Per altre informazioni, vedere la [impostazioni configurabili `ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>campionamento

Application Insights SDK per ASP.NET Core supporta sia a frequenza fissa e il campionamento adattivo. Il campionamento adattivo è abilitato per impostazione predefinita. 

Per altre informazioni, vedere [configurare il campionamento adattivo per le applicazioni ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Aggiunta di TelemetryInitializers

Uso [inizializzatori di telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) quando si desidera definire le proprietà globali che vengono inviate con tutti i dati di telemetria.

Aggiungi una nuova `TelemetryInitializer` per il `DependencyInjection` contenitore come illustrato nel codice seguente. il SDK rileva automaticamente qualsiasi `TelemetryInitializer` che viene aggiunto al `DependencyInjection` contenitore.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Rimozione TelemetryInitializers

Gli inizializzatori di telemetria sono presenti per impostazione predefinita. Per rimuovere tutti o gli inizializzatori di telemetria specifici, usare il seguente codice di esempio *dopo aver* chiamare `AddApplicationInsightsTelemetry()`.

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

È possibile aggiungere processori di telemetria personalizzati per `TelemetryConfiguration` usando il metodo di estensione `AddApplicationInsightsTelemetryProcessor` sul `IServiceCollection`. Si utilizzano processori di telemetria in [scenari di filtro avanzate](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor) la possibilità di controllo più diretto su ciò che è incluso o escluso dai dati di telemetria inviati al servizio Application Insights. Usare l'esempio seguente.

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

Application Insights Usa i moduli di telemetria al [raccogliere automaticamente informazioni utili](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies) sui carichi di lavoro specifici senza richiedere un'ulteriore configurazione.

I seguenti moduli di raccolta automatica sono abilitati per impostazione predefinita. Questi moduli sono responsabili per la raccolta automatica di dati di telemetria. È possibile disabilitare o configurarle per modificare il comportamento predefinito.

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Per configurare qualsiasi impostazione predefinita `TelemetryModule`, usare il metodo di estensione `ConfigureTelemetryModule<T>` su `IServiceCollection`, come illustrato nell'esempio seguente.

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

### <a name="configuring-a-telemetry-channel"></a>Configurare un canale di telemetria

Il canale predefinito è `ServerTelemetryChannel`. È possibile eseguirne l'override come illustrato nell'esempio seguente.

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

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Come è possibile tenere traccia i dati di telemetria che vengono automaticamente raccolti?

Ottenere un'istanza di `TelemetryClient` utilizzando l'inserimento del costruttore e chiamare richiesti `TrackXXX()` metodo su di esso. Non è consigliabile creare nuovi `TelemetryClient` istanze in un'applicazione ASP.NET Core. Un'istanza singleton della `TelemetryClient` è già registrato nella `DependencyInjection` contenitore che condivide `TelemetryConfiguration` con il resto dei dati di telemetria. Creazione di un nuovo `TelemetryClient` istanza è consigliata solo se necessita di una configurazione che è separata dal resto dei dati di telemetria. 

Nell'esempio seguente viene illustrato come tenere traccia dei dati di telemetria aggiuntivi da un controller.

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

Per altre informazioni sulla creazione di report in Application Insights di dati personalizzati, vedere [metriche personalizzate di Application Insights API reference](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Alcuni modelli di Visual Studio usato il metodo di estensione UseApplicationInsights() su IWebHostBuilder per abilitare Application Insights. È questo utilizzo è ancora valida?

Sì, l'abilitazione di Application Insights con questo metodo è valido. Questa tecnica viene usata nel processo di onboarding di Visual Studio e nelle estensioni di App Web. Tuttavia, è consigliabile usare `services.AddApplicationInsightsTelemetry()` perché fornisce gli overload per controllare alcune attività di configurazione. Entrambi i metodi di eseguono la stessa operazione internamente, pertanto se non è necessario applicare la configurazione personalizzata, è possibile chiamare uno dei due metodi.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Distribuito l'applicazione ASP.NET Core in App Web. È consigliabile comunque abilitare l'estensione Application Insights da app Web?

Se è installato il SDK in fase di compilazione come illustrato in questo articolo, è necessario abilitare l'estensione Application Insights dal portale del servizio App. Anche se è installata l'estensione, verrà nuovamente disattivato quando rileva che il SDK è già stato aggiunto all'applicazione. Se si abilita Application Insights dall'estensione, non è necessario installare e aggiornare il SDK. Ma se si abilita Application Insights seguendo le istruzioni in questo articolo, è disponibile una maggiore flessibilità in quanto:

   * I dati di telemetria di Application Insights continueranno a funzionare:
       * Tutti i sistemi operativi, inclusi Windows, Linux e Mac.
       * Tutte le modalità di pubblicazione, tra cui indipendente o dipendente da framework.
       * Tutti i framework di destinazione, tra cui la versione completa di .NET Framework.
       * Tutte le opzioni di hosting, tra cui App Web, macchine virtuali, Linux, contenitori, Azure Kubernetes Service e non Azure che ospita.
   * Quando esegue il debug da Visual Studio, è possibile visualizzare i dati di telemetria in locale.
   * È possibile tenere traccia di dati di telemetria personalizzati aggiuntivi utilizzando la `TrackXXX()` API.
   * È necessario il controllo completo sulla configurazione.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>È possibile abilitare il monitoraggio di Application Insights usando strumenti come Status Monitor?

No. [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e [v2 Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) attualmente supportati da ASP.NET 4.x solo.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights è automaticamente abilitata per l'applicazione ASP.NET Core 2.0?

Il `Microsoft.AspNetCore.All` metapacchetto 2.0 incluso Application Insights SDK (versione 2.1.0). Se si esegue l'applicazione nel debugger di Visual Studio, Visual Studio Abilita Application Insights e Mostra i dati di telemetria in locale nell'IDE stesso. I dati di telemetria non è stato inviato al servizio Application Insights a meno che non è stata specificata una chiave di strumentazione. È consigliabile seguire le istruzioni in questo articolo per abilitare Application Insights, anche per 2.0 le app.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se l'applicazione viene eseguito in Linux, tutte le funzionalità sono supportate?

Sì. Supporto della funzionalità per il SDK è identico in tutte le piattaforme, con le eccezioni seguenti:

* I contatori delle prestazioni sono supportati solo in Windows.
* Anche se `ServerTelemetryChannel` è abilitato per impostazione predefinita, se l'applicazione è in esecuzione in Linux o MacOS, il canale non crea automaticamente una cartella di archiviazione locale per mantenere temporaneamente i dati di telemetria, se sono presenti problemi di rete. A causa di questa limitazione, i dati di telemetria vengono persi quando si verificano problemi di rete o del server temporanei. Per risolvere questo problema, configurare una cartella locale per il canale:

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

## <a name="open-source-sdk"></a>SDK open source

[Leggere e contribuire al codice](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="video"></a>Video

- Guardare il video dettagliato esterno al [Configura Application Insights con .NET Core e Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) da zero.
- Guardare il video dettagliato esterno al [Configura Application Insights con .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) da zero.

## <a name="next-steps"></a>Passaggi successivi

* [Esplorare i flussi utente](../../azure-monitor/app/usage-flows.md) per comprendere gli spostamenti degli utenti tramite l'app.
* [Configurare un insieme di snapshot](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) per visualizzare lo stato del codice sorgente e le variabili nel momento in cui viene generata un'eccezione.
* [Usare l'API](../../azure-monitor/app/api-custom-events-metrics.md) per l'invio di eventi e metriche per una visualizzazione dettagliata delle prestazioni e l'utilizzo dell'app.
* Usare [test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) per controllare costantemente l'app da ogni parte del mondo.
