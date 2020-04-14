---
title: Campionamento della telemetria in Azure Application Insights | Documentazione Microsoft
description: Come tenere sotto controllo il volume della telemetria.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e888e0606b7a9bcd9a7a94c28455d705c5f1bec
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255482"
---
# <a name="sampling-in-application-insights"></a>Campionamento in Application Insights

Il campionamento è una funzionalità di [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). È il modo consigliato per ridurre il traffico di telemetria, i costi dei dati e i costi di archiviazione, preservando un'analisi statisticamente corretta dei dati dell'applicazione. Il campionamento consente inoltre di evitare la limitazione dei dati di telemetria di Application Insights.Sampling also helps you avoid Application Insights throttling your telemetry. Il filtro di campionamento seleziona gli elementi correlati, in modo che sia possibile spostarsi tra gli elementi quando si eseguono indagini diagnostiche.

Quando i conteggi delle metriche vengono presentati nel portale, vengono rinormalizzati per prendere in considerazione il campionamento. In questo modo si riduce al minimo qualsiasi effetto sulle statistiche.

## <a name="brief-summary"></a>Breve riepilogo

* Esistono tre diversi tipi di campionamento: campionamento adattivo, campionamento a frequenza fissa e campionamento di inserimento.
* Il campionamento adattivo è abilitato per impostazione predefinita in tutte le versioni più recenti di Application Insights ASP.NET e ASP.NET Core Software Development Kit (SDK). Viene utilizzato anche da Funzioni di [Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview).
* Il campionamento a frequenza fissa è disponibile nelle versioni recenti degli SDK di Application Insights per ASP.NET, ASP.NET Core, Java (sia l'agente che l'SDK) e Python.
* Il campionamento dell'inserimento funziona nell'endpoint del servizio Application Insights.Ingestion sampling works on the Application Insights service endpoint. Si applica solo quando non sono attivi altri campionamenti. Se l'SDK campiona i dati di telemetria, il campionamento dell'inserimento è disabilitato.
* Per le applicazioni Web, se si registrano eventi personalizzati e si desidera garantire che un `OperationId` set di eventi venga mantenuto o eliminato insieme, gli eventi devono avere lo stesso valore.
* Se si scrivono query di Dati di analisi, è necessario [tener conto del campionamento](../../azure-monitor/log-query/aggregations.md). In particolare, anziché eseguire semplicemente il conteggio dei record, è necessario usare `summarize sum(itemCount)`.
* Alcuni tipi di telemetria, incluse le metriche delle prestazioni e le metriche personalizzate, vengono sempre mantenuti indipendentemente dal fatto che il campionamento sia abilitato o meno.

Nella tabella seguente sono riepilogati i tipi di campionamento disponibili per ogni SDK e il tipo di applicazione:

| Application Insights SDK | Campionamento adattivo supportato | Campionamento a velocità fissa supportato | Campionamento dell'inserimento supportato |
|-|-|-|-|
| ASP.NET | [Sì (attivata per impostazione predefinita)](#configuring-adaptive-sampling-for-aspnet-applications) | [Sì](#configuring-fixed-rate-sampling-for-aspnet-applications) | Solo se non sono attivi altri campionamenti |
| ASP.NET Core | [Sì (attivata per impostazione predefinita)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Sì](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Solo se non sono attivi altri campionamenti |
| Funzioni di Azure | [Sì (attivata per impostazione predefinita)](#configuring-adaptive-sampling-for-azure-functions) | No | Solo se non sono attivi altri campionamenti |
| Java | No | [Sì](#configuring-fixed-rate-sampling-for-java-applications) | Solo se non sono attivi altri campionamenti |
| Python | No | [Sì](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Solo se non sono attivi altri campionamenti |
| Tutti gli altri | No | No | [Sì](#ingestion-sampling) |

> [!NOTE]
> Le informazioni nella maggior parte di questa pagina si applicano alle versioni correnti degli SDK di Application Insights. Per informazioni sulle versioni precedenti degli SDK, [vedere la sezione seguente.](#older-sdk-versions)

## <a name="types-of-sampling"></a>Tipi di campionamento

Esistono tre diversi metodi di campionamento:

* **Il campionamento adattivo** regola automaticamente il volume di dati di telemetria inviati dall'SDK nell'app ASP.NET/ASP.NET Core e da Funzioni di Azure.Adaptive sampling automatically adjusts the volume of telemetry sent from the SDK in your ASP.NET/ASP.NET Core app, and from Azure Functions. Questo è il campionamento predefinito quando si usa l'SDK di ASP.NET o ASP.NET Core. Il campionamento adattivo è attualmente disponibile solo per i dati di telemetria sul lato server e per ASP.NET funzioni di Azure.Adaptive sampling is currently only available for a server-side telemetry, and for Azure Functions.

* **Il campionamento** a frequenza fissa riduce il volume dei dati di telemetria inviati sia dal ASP.NET che dal server ASP.NET Core o Java e dai browser degli utenti. È necessario impostare la frequenza. Il client e il server sincronizzeranno il rispettivo campionamento in modo che nella ricerca sia possibile spostarsi tra le visualizzazioni pagina e le richieste correlate.

* **Il campionamento dell'inserimento** si verifica nell'endpoint del servizio Application Insights.Ingestion sampling happens at the Application Insights service endpoint. Rimuove alcuni dati di telemetria provenienti dall'app, a una velocità di campionamento impostata. Non riduce il traffico di telemetria inviato dall'app, ma consente all'utente di rispettare la quota mensile. Il vantaggio principale del campionamento di inserimento è che puoi impostare la frequenza di campionamento senza ridistribuire l'app. Il campionamento dell'inserimento funziona in modo uniforme per tutti i server e i client, ma non si applica quando sono in funzione altri tipi di campionamento.

> [!IMPORTANT]
> Se sono in funzione metodi di campionamento adattivi o a tasso fisso, il campionamento dell'inserimento è disabilitato.

## <a name="adaptive-sampling"></a>Campionamento adattivo

Il campionamento adattivo riguarda il volume dei dati di telemetria inviati dall'app del server Web all'endpoint del servizio Application Insights.

> [!TIP]
> Il campionamento adattivo è abilitato per impostazione predefinita quando si usa l'SDK di ASP.NET o l'SDK di ASP.NET Core ed è abilitato anche per impostazione predefinita per Funzioni di Azure.Adaptive sampling is enabled by default when you use the ASP.NET SDK or the ASP.NET Core SDK, and is also enabled by default for Azure Functions.

Il volume viene regolato automaticamente per mantenere entro una velocità `MaxTelemetryItemsPerSecond`massima di traffico specificata ed è controllato tramite l'impostazione . Se l'applicazione produce una quantità bassa di dati di telemetria, ad esempio durante il debug o `MaxTelemetryItemsPerSecond`a causa di un utilizzo ridotto, gli elementi non verranno eliminati dal processore di campionamento finché il volume è inferiore a . Con l'aumentare del volume di telemetria, la frequenza di campionamento viene regolata in modo da raggiungere il volume di destinazione. La rettifica viene ricalcolata a intervalli regolari e si basa su una media mobile della frequenza di trasmissione in uscita.

Per ottenere il volume di destinazione, alcuni dei dati di telemetria generati vengono eliminati. Tuttavia, come in altri tipi di campionamento, l'algoritmo consente di mantenere gli elementi di telemetria correlati. Ad esempio, quando si controllano i dati di telemetria nella ricerca, sarà possibile trovare la richiesta correlata a una particolare eccezione.

I conteggi di metrica, ad esempio la frequenza delle richieste e delle eccezioni, vengono adattati per compensare la frequenza di campionamento, in modo che mostrino i valori corretti in Esplora metriche.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configurazione del campionamento adattivo per applicazioni ASP.NET

> [!NOTE]
> Questa sezione si applica alle applicazioni ASP.NET, non alle applicazioni ASP.NET Core.This section applies to ASP.NET applications, not to ASP.NET Core applications. [Informazioni sulla configurazione del campionamento adattivo per le applicazioni ASP.NET Core più avanti in questo documento.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

In [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md), è possibile regolare diversi parametri nel `AdaptiveSamplingTelemetryProcessor` nodo. Le cifre indicate sono i valori predefiniti:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Frequenza di destinazione che l'algoritmo adattivo deve raggiungere **su ogni host server**. Se l'app Web viene eseguita su più host, ridurre questo valore per non superare la frequenza di destinazione del traffico nel portale di Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervallo in base al quale viene rivalutata la frequenza corrente dei dati di telemetria. La valutazione viene eseguita come media mobile. Potrebbe essere necessario ridurre questo intervallo se la telemetria è responsabile di burst improvvisi.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Quando il valore percentuale di campionamento cambia, quando viene consentito di abbassare nuovamente la percentuale di campionamento per acquisire meno dati?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Quando il valore percentuale di campionamento cambia, quanto tempo dopo è consentito aumentare nuovamente la percentuale di campionamento per acquisire più dati?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Poiché la percentuale di campionamento varia, qual è il valore minimo che è consentito impostare?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Poiché la percentuale di campionamento varia, qual è il valore massimo che è consentito impostare?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Nel calcolo della media mobile, specifica il peso che deve essere assegnato al valore più recente. Usare un valore uguale o inferiore a 1. I valori più bassi rendono l'algoritmo meno reattivo alle modifiche improvvise.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Quantità di dati di telemetria da campionare quando l'app è appena stata avviata. Non ridurre questo valore durante il debug.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Elenco delimitato da punti e virgola di tipi che non si desidera essere soggetti a campionamento. I tipi `Dependency`riconosciuti sono: `Event`, , `Exception`, `PageView`, , `Request`. `Trace` Tutti i dati di telemetria dei tipi specificati vengono trasmessi; i tipi non specificati verranno campionati.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Elenco delimitato da punti e virgola di tipi che si desidera sottoporre al campionamento. I tipi `Dependency`riconosciuti sono: `Event`, , `Exception`, `PageView`, , `Request`. `Trace` I tipi specificati verranno campionati; tutti i dati di telemetria degli altri tipi saranno sempre trasmessi.

**Per disattivare** il campionamento adattivo, rimuovere i `AdaptiveSamplingTelemetryProcessor` nodi da `ApplicationInsights.config`.

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: Configurare il campionamento adattivo nel codiceAlternative: Configure adaptive sampling in code

Anziché impostare il `.config` parametro di campionamento nel file, è possibile impostare questi valori a livello di codice.

1. Rimuovere tutti `AdaptiveSamplingTelemetryProcessor` i nodi `.config` dal file.
2. Usare il frammento di codice seguente per configurare il campionamento adattivo:Use the following snippet to configure adaptive sampling:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Informazioni sui processori di telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

È anche possibile regolare la frequenza di campionamento per ogni tipo di telemetria singolarmente o escludere determinati tipi dall'essere campionati affatto:You can also adjust the sampling rate for each telemetry type individually, or can even exclude certain types from being sampled at all:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configurazione del campionamento adattivo per applicazioni ASP.NET CoreConfiguring adaptive sampling for ASP.NET Core applications

Non esiste `ApplicationInsights.config` per ASP.NET applicazioni Core, pertanto tutta la configurazione viene eseguita tramite codice.
Il campionamento adattivo è abilitato per impostazione predefinita in tutte le applicazioni ASP.NET Core, ma può essere disabilitato o è possibile personalizzarne il comportamento.

#### <a name="turning-off-adaptive-sampling"></a>Disattivazione del campionamento adattivo

La funzionalità di campionamento predefinita può essere disabilitata `ApplicationInsightsServiceOptions` durante `Startup.cs` l'aggiunta del servizio Application Insights nel metodo `ConfigureServices`, utilizzando il file:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

Il codice precedente disabiliterà il campionamento adattivo. Seguire la procedura descritta di seguito per aggiungere al campionamento più opzioni di personalizzazione.

#### <a name="configure-sampling-settings"></a>Configurare le impostazioni di campionamento

Usare i metodi di estensione di `TelemetryProcessorChainBuilder`, come illustrato di seguito, per personalizzare il comportamento della funzionalità di campionamento.

> [!IMPORTANT]
> Se si utilizza questo metodo per configurare `aiOptions.EnableAdaptiveSampling` il `false` campionamento, assicurarsi di impostare la proprietà su quando si chiama `AddApplicationInsightsTelemetry()`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Configurazione del campionamento adattivo per funzioni di AzureConfiguring adaptive sampling for Azure Functions

Seguire le istruzioni di questa pagina per configurare il campionamento adattivo per le app in esecuzione in Funzioni di Azure.Follow instructions from [this page](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) to configure adaptive sampling for apps running in Azure Functions.

## <a name="fixed-rate-sampling"></a>Campionamento a velocità fissa

Il campionamento a frequenza fissa riduce il traffico inviato dal server Web e dai browser Web. A differenza del campionamento adattivo, riduce i dati di telemetria a una frequenza fissa definita dall'utente. Il campionamento a frequenza fissa è disponibile per le applicazioni ASP.NET, ASP.NET Core, Java e Python.

Come altre tecniche di campionamento, questo mantiene anche gli elementi correlati. Sincronizza inoltre il campionamento client e server in modo che gli elementi correlati vengano mantenuti, ad esempio quando si esamina una visualizzazione di pagina in Ricerca, è possibile trovare le richieste server correlate. 

In Esplora metriche, frequenze quali il numero di richieste ed eccezioni vengono moltiplicate per un fattore in modo da compensare la frequenza di campionamento ed essere quindi corretti.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Configurazione del campionamento a frequenza fissa per applicazioni ASP.NET

1. **Disabilitare il campionamento adattivo**: In [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md), rimuovere o impostare come commento il `AdaptiveSamplingTelemetryProcessor` nodo.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Abilitare il modulo di campionamento a frequenza fissa.** Aggiungi questo [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)frammento di codice a :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      In alternativa, anziché impostare `ApplicationInsights.config` il parametro di campionamento nel file, è possibile impostare questi valori a livello di codice:Alternatively, instead of setting the sampling parameter in the file, you can programmatically set these values:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([Informazioni sui processori di telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Configurazione del campionamento a frequenza fissa per le applicazioni ASP.NET CoreConfiguring fixed-rate sampling for ASP.NET Core applications

1. **Disattiva campionamento adattivo**: `ConfigureServices` Le modifiche `ApplicationInsightsServiceOptions`possono essere apportate nel metodo, utilizzando :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Abilitare il modulo di campionamento a frequenza fissa.** Le modifiche possono `Configure` essere apportate nel metodo come illustrato nel frammento di codice seguente:Changes can be made in the method as shown in the below snippet:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Configurazione del campionamento a frequenza fissa per le applicazioni JavaConfiguring fixed-rate sampling for Java applications

Per impostazione predefinita, nessun campionamento è abilitato nell'agente Java e nell'SDK. Attualmente supporta solo il campionamento a tasso fisso. Il campionamento adattivo non è supportato in Java.

#### <a name="configuring-java-agent"></a>Configurazione dell'agente Java

1. Scaricare [applicationinsights-agent-3.0.0-PREVIEW.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

1. Per abilitare il `ApplicationInsights.json` campionamento, aggiungere quanto segue al file:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>Configurazione di Java SDK

1. Scaricare e configurare l'applicazione Web con l'ultimo SDK Java di [Application Insights.](../../azure-monitor/app/java-get-started.md)

2. **Abilitare il modulo** di campionamento a `ApplicationInsights.xml` frequenza fissa aggiungendo il frammento di codice seguente al file:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. È possibile includere o escludere tipi specifici di dati `Processor` di `FixedRateSamplingTelemetryProcessor`telemetria dal campionamento usando i tag seguenti all'interno del tag:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

I tipi di telemetria che possono `Dependency`essere `Event` `Exception`inclusi `PageView` `Request`o `Trace`esclusi dal campionamento sono: , , , , , e .

> [!NOTE]
> Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero.  Il campionamento attualmente non supporta altri valori.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Configurazione del campionamento a frequenza fissa per le applicazioni OpenCensus Python

Instrumentare l'applicazione con gli [esportatori OpenCensus di Azure Monitor OpenCensus](../../azure-monitor/app/opencensus-python.md)più recenti.

> [!NOTE]
> Il campionamento a frequenza fissa non è disponibile per l'esportatore di metriche. Ciò significa che le metriche personalizzate sono gli unici tipi di telemetria in cui il campionamento NON può essere configurato. L'esefattore di metriche invierà tutti i dati di telemetria di cui tiene traccia.

#### <a name="fixed-rate-sampling-for-tracing"></a>Campionamento a frequenza fissa per la tracciatura ####
È possibile specificare un `sampler` come parte della configurazione `Tracer`. Se non viene fornito alcun `ProbabilitySampler` campionatore esplicito, verrà utilizzato per impostazione predefinita. L'utilizzo `ProbabilitySampler` di una frequenza di 1/10000 per impostazione predefinita, ovvero una richiesta su 10000 verrà inviata ad Application Insights. Se si desidera specificare una frequenza di campionamento, vedere di seguito.

Per specificare la frequenza `Tracer` di campionamento, assicurarsi di specificare un campionatore con una frequenza di campionamento compresa tra 0,0 e 1,0 inclusi. A sampling rate of 1.0 represents 100%, meaning all of your requests will be sent as telemetry to Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Campionamento a frequenza fissa per i log ####
È possibile configurare il `AzureLogHandler` campionamento `logging_sampling_rate` a frequenza fissa per modificando l'argomento facoltativo. Se non viene fornito alcun argomento, verrà utilizzata una frequenza di campionamento pari a 1,0. A sampling rate of 1.0 represents 100%, meaning all of your requests will be sent as telemetry to Application Insights.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Configurazione del campionamento a frequenza fissa per le pagine Web con JavaScript

Le pagine Web basate su JavaScript possono essere configurate per l'utilizzo di Application Insights. La telemetria viene inviata dall'applicazione client in esecuzione all'interno del browser dell'utente e le pagine possono essere ospitate da qualsiasi server.

Quando si [configurano le pagine Web basate su JavaScript per Application Insights](javascript.md), modificare il frammento di codice JavaScript ottenuto dal portale di Application Insights.

> [!TIP]
> In ASP.NET applicazioni con JavaScript incluso, lo snippet in genere va in `_Layout.cshtml`.

Inserire una riga simile a `samplingPercentage: 10,` prima della chiave di strumentazione:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Come percentuale di campionamento, sceglierne una vicina a 100/N dove N è un numero intero. Il campionamento attualmente non supporta altri valori.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Coordinamento del campionamento lato server e lato client

L'SDK JavaScript lato client partecipa al campionamento a frequenza fissa insieme all'SDK sul lato server. Le pagine instrumentate invieranno solo dati di telemetria sul lato client dallo stesso utente per il quale l'SDK sul lato server ha preso la decisione di includere nel campionamento. Questa logica è progettata per mantenere l'integrità delle sessioni utente tra le applicazioni lato client e lato server. Di conseguenza, da qualsiasi elemento di telemetria specifico in Application Insights è possibile trovare tutti gli altri elementi di telemetria per l'utente o la sessione e in Ricerca, è possibile spostarsi tra le visualizzazioni di pagina correlate e le richieste.

Se i dati di telemetria lato client e lato server non mostrano campioni coordinati:If your client and server-side telemetry don't show coordinated samples:

* Verificare di aver abilitato il campionamento sia sul server che sul client.
* Controllare di avere impostato la stessa percentuale di campionamento sia nel client che nel server.
* Assicurarsi che la versione dell'SDK sia 2.0 o successiva.

## <a name="ingestion-sampling"></a>Campionamento per inserimento

Il campionamento dell'inserimento opera nel punto in cui i dati di telemetria dal server Web, dai browser e dai dispositivi raggiungono l'endpoint del servizio Application Insights.Ingestion sampling operates at the point where the telemetry from your web server, browsers, and devices reaches the Application Insights service endpoint. Anche se non riduce il traffico dei dati di telemetria inviato dall'app, riduce la quantità di dati elaborati, conservati e addebitati da Application Insights.

Usare questo tipo di campionamento se l'app spesso supera la quota mensile e non si ha la possibilità di usare uno dei tipi di campionamento basati sull'SDK. 

Impostare la frequenza di campionamento nella pagina Utilizzo e costi stimati:

![Nel pannello Panoramica dell'applicazione fare clic su Impostazioni, Quota, Esempi, quindi selezionare una frequenza di campionamento e fare clic su Aggiorna.From the application's Overview blade, click Settings, Quota, Samples, then select a sampling rate, and click Update.](./media/sampling/data-sampling.png)

Come in altri tipi di campionamento, l'algoritmo consente di mantenere gli elementi di telemetria correlati. Ad esempio, quando si controllano i dati di telemetria nella ricerca, sarà possibile trovare la richiesta correlata a una particolare eccezione. I conteggi di metrica, ad esempio la frequenza delle richieste e delle eccezioni, vengono mantenuti correttamente.

I punti dati che vengono rimossi dal campionamento non sono disponibili in alcuna funzionalità di Application Insights, ad esempio nell' [esportazione continua](../../azure-monitor/app/export-telemetry.md).

Il campionamento dell'ingestione non funziona mentre è in funzione il campionamento adattivo o a frequenza fissa. Il campionamento adattivo è abilitato per impostazione predefinita quando viene usato l'SDK di ASP.NET o ASP.NET Core SDK oppure quando Application Insights è abilitato nel [servizio app di Azure](azure-web-apps.md) o tramite Monitoraggio stato. Quando i dati di telemetria vengono ricevuti dall'endpoint del servizio Application Insights, esaminai i dati di telemetria e se la frequenza di campionamento viene segnalata come inferiore al 100% (che indica che i dati di telemetria vengono campionati), la frequenza di campionamento di inserimento impostata viene ignorata.

> [!WARNING]
> Il valore visualizzato nel riquadro del portale indica il valore impostato per il campionamento dell'inserimento. Non rappresenta la frequenza di campionamento effettiva se è in funzione qualsiasi tipo di campionamento SDK (campionamento adattivo o a frequenza fissa).

## <a name="when-to-use-sampling"></a>Quando usare il campionamentoWhen to use sampling

In generale, per la maggior parte delle applicazioni di piccole e medie dimensioni non è necessario il campionamento. Le informazioni di diagnostica più utili e le statistiche più accurate si ottengono raccogliendo dati su tutte le attività utente. 

I vantaggi principali del campionamento sono:

* Il servizio Application Insights elimina i punti dati ("throttles") quando l'app invia una frequenza di dati molto elevata in un breve intervallo di tempo. Il campionamento riduce la probabilità che l'applicazione visualizzi la limitazione delle richieste.
* Non superare la [quota](pricing.md) di punti dati per il proprio piano tariffario. 
* Ridurre il traffico di rete dalla raccolta di telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Quale tipo di campionamento è opportuno usare?

**Usare il campionamento per inserimento se:**

* Spesso si usa la quota mensile di dati di telemetria.
* Si ricevono troppi dati di telemetria dai browser Web degli utenti.
* Si usa una versione dell'SDK che non supporta il campionamento, ad esempio le versioni di ASP.NET precedenti alla 2.

**Usare il campionamento a frequenza fissa se:**

* Si desidera eseguire il campionamento sincronizzato tra client e server in modo che, quando si analizzano gli eventi nella [ricerca,](../../azure-monitor/app/diagnostic-search.md)sia possibile spostarsi tra gli eventi correlati sul client e sul server, ad esempio le visualizzazioni di pagina e le richieste HTTP.
* Se è certi della percentuale di campionamento appropriata per l'app. Deve essere abbastanza elevata da ottenere metriche accurate, ma al di sotto della frequenza che fa superare la quota di prezzo e le soglie di limitazione.

**Usare il campionamento adattivo:**

Se le condizioni per l'uso di altre forme di campionamento non sono valide per uno scenario specifico, è consigliabile adottare il campionamento adattativo. Questa impostazione è abilitata per impostazione predefinita nell'SDK di ASP.NET/ASP.NET Core. Non ridurrà il traffico fino a quando non sarà raggiunto un certo tasso minimo, quindi i siti a basso uso probabilmente non saranno campionati affatto.

## <a name="knowing-whether-sampling-is-in-operation"></a>Sapere se il campionamento è in funzione

Per individuare la frequenza di campionamento effettiva indipendentemente dal punto in cui è stata applicata, usare una [query di Analisi](../../azure-monitor/app/analytics.md) simile alla seguente:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Se si `RetainedPercentage` nota che per qualsiasi tipo è minore di 100, viene campionato il tipo di telemetria.

> [!IMPORTANT]
> Application Insights non campiona la sessione, le metriche (incluse le metriche personalizzate) o i tipi di telemetria del contatore delle prestazioni in una delle tecniche di campionamento. Questi tipi sono sempre esclusi dal campionamento poiché una riduzione della precisione può essere altamente indesiderabile per questi tipi di telemetria.

## <a name="how-sampling-works"></a>Come funziona il campionamento

L'algoritmo di campionamento decide quali elementi di telemetria eliminare e quali mantenere. Questo è vero se il campionamento viene eseguito dall'SDK o nel servizio Application Insights. La decisione sul campionamento si basa su alcune regole che hanno lo scopo di lasciare intatti tutti i punti dati correlati, mantenendo in Application Insights un'esperienza di diagnostica sfruttabile e affidabile anche con un set di dati ridotto. Ad esempio, se l'app ha una richiesta non riuscita inclusa in un esempio, gli elementi di telemetria aggiuntivi (ad esempio le eccezioni e le tracce registrate per questa richiesta) verranno mantenuti. Il campionamento li mantiene o li scende tutti insieme. Di conseguenza, quando si osservano i dettagli della richiesta in Application Insights, è sempre possibile visualizzare la richiesta con gli elementi di telemetria associati.

La decisione di campionamento si basa sull'ID operazione della richiesta, il che significa che tutti gli elementi di telemetria appartenenti a una particolare operazione vengono mantenuti o eliminati. Per gli elementi di telemetria che non dispongono di un ID operazione impostato (ad esempio elementi di telemetria segnalati da thread asincroni senza contesto HTTP) il campionamento acquisisce semplicemente una percentuale di elementi di telemetria di ogni tipo.

Quando la telemetria viene ripresentata all'utente, il servizio Application Insights modifica le metriche in base alla stessa percentuale di campionamento usata in fase di raccolta, per compensare i punti dati mancanti. Quindi, quando osservano la telemetria in Application Insights, gli utenti visualizzano approssimazioni statisticamente corrette molto vicine ai numeri reali.

La precisione dell'approssimazione dipende in gran parte dalla percentuale di campionamento configurata. La precisione è anche maggiore per le applicazioni che gestiscono un volume elevato di richieste generalmente simili da una grande quantità di utenti. Per le applicazioni che non gestiscono un carico di lavoro significativo, invece, il campionamento non è necessario perché queste applicazioni in genere riescono a inviare tutti i dati di telemetria senza superare la quota e senza causare perdite di dati dovute alla limitazione. 

## <a name="frequently-asked-questions"></a>Domande frequenti

*Qual è il comportamento di campionamento predefinito negli SDK di ASP.NET e ASP.NET Core?*

* Se si usa una delle versioni più recenti dell'SDK precedente, il campionamento adattivo è abilitato per impostazione predefinita con cinque elementi di telemetria al secondo.
  Sono presenti `AdaptiveSamplingTelemetryProcessor` due nodi aggiunti per `Event` impostazione predefinita e uno `Event` include il tipo nel campionamento, mentre l'altro esclude il tipo dal campionamento. Questa configurazione significa che l'SDK tenterà di `Event` limitare gli elementi di telemetria a cinque `Events` elementi di telemetria di tipi e cinque elementi di telemetria di tutti gli altri tipi combinati, garantendo in tal modo che vengono campionati separatamente da altri tipi di telemetria. Gli eventi vengono in genere usati per i dati di telemetria aziendali e molto probabilmente non dovrebbero essere influenzati dai volumi di telemetria di diagnostica.
  
  Di seguito viene `ApplicationInsights.config` illustrato il file predefinito generato. In ASP.NET Core, lo stesso comportamento predefinito è abilitato nel codice. Utilizzare gli [esempi nella sezione precedente di questa pagina](#configuring-adaptive-sampling-for-aspnet-core-applications) per modificare questo comportamento predefinito.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*È possibile campionare i dati di telemetria più di una volta?*

* No. SamplingTelemetryProcessors ignorano gli elementi dalle considerazioni di campionamento se l'elemento è già campionato. Lo stesso vale anche per il campionamento di inserimento, che non verrà applicato al campionamento agli elementi già campionati nell'SDK stesso.

*Perché il campionamento non è una semplice "raccolta di percentuale X di ogni tipo di telemetria"?*

* Anche se questo approccio di campionamento fornirebbe un alto livello di precisione nelle approssimazioni metriche, interromperebbe la capacità di correlare i dati di diagnostica per utente, sessione e richiesta, che è fondamentale per la diagnostica. Di conseguenza, il campionamento funziona meglio con criteri come "raccogliere tutti gli elementi di telemetria per X percento degli utenti dell'app" o "raccogliere tutti i dati di telemetria per X percentuale di richieste di app". Per gli elementi di telemetria non associati alle richieste (ad esempio l'elaborazione asincrona in background), il fallback consiste nel "raccogliere la percentuale X di tutti gli elementi per ogni tipo di telemetria". 

*La percentuale di campionamento può variare nel tempo?*

* Sì, il campionamento adattivo modifica gradualmente la percentuale di campionamento, in base al volume attualmente osservato della telemetria.

*Se si usa il campionamento a frequenza fissa, come stabilire quale sarà la percentuale di campionamento ideale per l'app?*

* Una modalità è quella di iniziare con il campionamento adattivo, scoprire quale frequenza è impostata (vedere la domanda precedente) e quindi cambiarla a campionamento a frequenza fissa usando quella frequenza. 
  
    In caso contrario, è necessario usare l'intuito. Analizzare l'uso della telemetria corrente in Application Insights, osservare le possibili limitazioni in corso e stimare il volume della telemetria raccolta. Questi tre input, insieme al piano tariffario selezionato, suggeriscono di quanto ridurre il volume dei dati di telemetria raccolti. Tuttavia, un aumento nel numero degli utenti o qualsiasi altra migrazione nel volume di telemetria potrebbe invalidare la stima.

*Cosa succede se configuro la percentuale di campionamento in modo che sia troppo bassa?*

* Percentuali di campionamento eccessivamente basse causano un campionamento eccessivamente aggressivo e riducono l'accuratezza delle approssimazioni quando Application Insights tenta di compensare la visualizzazione dei dati per la riduzione del volume di dati. Anche l'esperienza diagnostica potrebbe essere influenzata negativamente, poiché alcune delle richieste raramente non riuscite o lente possono essere campionate.

*Cosa succede se configuro la percentuale di campionamento in modo che sia troppo alta?*

* La configurazione di una percentuale di campionamento troppo elevata (non abbastanza aggressiva) comporta una riduzione insufficiente del volume dei dati di telemetria raccolti. Potrebbe tuttavia verificarsi una perdita dei dati di telemetria correlata alla limitazione e il costo per l'uso di Application Insights potrebbe essere superiore al previsto per l'applicazione di tariffe aggiuntive.

*Su quali piattaforme è possibile usare il campionamento?*

* Se nell'SDK non è impostato il campionamento, si verifica automaticamente il campionamento per inserimento per i dati di telemetria superiori a un determinato volume. Questa configurazione funzionerebbe, ad esempio, se si utilizza una versione precedente di ASP.NET SDK o Java SDK.
* Se si usa l'ASP.NET corrente o ASP.NET Core SDK (ospitati in Azure o nel proprio server), si ottiene il campionamento adattivo per impostazione predefinita, ma è possibile passare alla velocità fissa come descritto in precedenza. Con il campionamento a frequenza fissa, l'SDK del browser si sincronizza automaticamente con gli eventi correlati al campione. 
* Se si utilizza l'agente Java corrente, è possibile `ApplicationInsights.json` `ApplicationInsights.xml`configurare (per Java SDK, configure ) per attivare il campionamento a frequenza fissa. Il campionamento viene disattivato per impostazione predefinita. Con il campionamento a frequenza fissa, l'SDK del browser e il server si sincronizzano automaticamente con gli eventi correlati di esempio.

*Esistono alcuni eventi rari che si vuole visualizzare sempre. Come è possibile passarli al modulo di campionamento?*

* Il modo migliore per ottenere questo risultato consiste nello `SamplingPercentage` scrivere un [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)personalizzato, che imposta il valore su 100 sull'elemento di telemetria che si desidera mantenere, come illustrato di seguito. Poiché è garantito che gli inizializzatori vengano eseguiti prima dei processori di telemetria (incluso il campionamento), ciò garantisce che tutte le tecniche di campionamento ignorino questo elemento da eventuali considerazioni di campionamento. Gli inizializzatori di telemetria personalizzati sono disponibili nell'SDK di ASP.NET, nell'SDK di ASP.NET Core, in JavaScript SDK e in Java SDK. Ad esempio, è possibile configurare un inizializzatore di telemetria usando l'SDK di ASP.NET:For example, you can configure a telemetry initializer using the ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Versioni precedenti dell'SDK

Il campionamento adattivo è disponibile per Application Insights SDK per ASP.NET v2.0.0-beta3 e versioni successive, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 e versioni successive e abilitato per impostazione predefinita.

Il campionamento a frequenza fissa è una funzionalità dell'SDK in ASP.NET versioni da 2.0.0 e Java SDK versione 2.0.1 e successive.

Prima della versione v2.5.0-beta2 dell'SDK ASP.NET e della versione 2.2.0-beta3 di ASP.NET Core SDK, la decisione di campionamento si basava sull'hash dell'ID utente per le applicazioni che definiscono "utente" (ovvero, la maggior parte delle applicazioni Web tipiche). Per i tipi di applicazioni che non definiscono gli utenti (ad esempio i servizi Web) la decisione di campionamento è basata sull'ID operazione della richiesta. Le versioni recenti del ASP.NET e degli SDK di ASP.NET Core utilizzano l'ID operazione per la decisione di campionamento.

## <a name="next-steps"></a>Passaggi successivi

* [applicazione di filtri](../../azure-monitor/app/api-filtering-sampling.md) può garantire un controllo più rigoroso sui dati inviati dall'SDK.
* Leggere l'articolo di rete per sviluppatori [Ottimizzare la telemetria con Application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
