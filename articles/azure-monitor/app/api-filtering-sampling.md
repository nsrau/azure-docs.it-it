---
title: Filtro e pre-elaborazione in Application Insights SDK | Microsoft Docs
description: Scrivere processori di telemetria e inizializzatori di telemetria per l'SDK per filtrare o aggiungere proprietà ai dati prima di inviare i dati di telemetria al portale di Application Insights.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-javascript
ms.openlocfilehash: eec3cf44eb516ce20db564e1bed32e5741bfd02a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87366756"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Filtrare e pre-elaborare i dati di telemetria in Application Insights SDK

È possibile scrivere e configurare i plug-in per il Application Insights SDK per personalizzare il modo in cui i dati di telemetria possono essere arricchiti ed elaborati prima di essere inviati al servizio Application Insights.

* [campionamento](sampling.md) riduce il volume della telemetria senza effetti sulle statistiche. Consente di raggruppare i punti dati correlati in modo che sia possibile spostarsi tra di essi quando si diagnostica un problema. Nel portale i conteggi totali vengono moltiplicati per compensare il campionamento.
* L'applicazione di filtri con processori di telemetria consente di filtrare i dati di telemetria nell'SDK prima che vengano inviati al server. È possibile, ad esempio, ridurre il volume della telemetria escludendo le richieste dei robot. Il filtro è un approccio più semplice per ridurre il traffico rispetto al campionamento. Consente un maggiore controllo su ciò che viene trasmesso, ma influiscono sulle statistiche. Ad esempio, è possibile filtrare tutte le richieste riuscite.
* Gli [inizializzatori di telemetria aggiungono o modificano le proprietà](#add-properties) ai dati di telemetria inviati dall'app, inclusi i dati di telemetria dei moduli standard. Ad esempio, è possibile aggiungere i valori calcolati o i numeri di versione in base ai quali filtrare i dati nel portale.
* [L'API SDK](./api-custom-events-metrics.md) viene usata per inviare metriche ed eventi personalizzati.

Prima di iniziare:

* Installare l'SDK appropriato per l'applicazione: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [non http/Worker per .NET/.NET Core](worker-service.md)o [JavaScript](javascript.md).

<a name="filtering"></a>

## <a name="filtering"></a>Filtro

Questa tecnica offre il controllo diretto sugli elementi inclusi o esclusi dal flusso di dati di telemetria. È possibile usare il filtro per eliminare gli elementi di telemetria dall'invio a Application Insights. È possibile utilizzare i filtri in combinazione con il campionamento o separatamente.

Per filtrare i dati di telemetria, scrivere un processore di telemetria e registrarlo con `TelemetryConfiguration` . Tutti i dati di telemetria passano attraverso il processore. È possibile scegliere di rilasciarlo dal flusso o assegnarlo al processore successivo nella catena. Sono inclusi i dati di telemetria dei moduli standard, ad esempio l'agente di raccolta delle richieste HTTP e l'agente di raccolta delle dipendenze e i dati di telemetria rilevati. Ad esempio, è possibile filtrare i dati di telemetria sulle richieste provenienti da robot o chiamate di dipendenza riuscite.

> [!WARNING]
> Il filtraggio dei dati di telemetria inviati dall'SDK usando i processori può alterare le statistiche visualizzate nel portale e rendere difficile seguire gli elementi correlati.
>
> In alternativa, valutare la possibilità di usare il [campionamento](./sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Creare un processore di telemetria (C#)

1. Per creare un filtro, implementare `ITelemetryProcessor` .

    I processori di telemetria costruiscono una catena di elaborazione. Quando si crea un'istanza di un processore di telemetria, viene fornito un riferimento al processore successivo nella catena. Quando un punto dati di telemetria viene passato al metodo Process, esegue le operazioni e quindi chiama (o non chiama) il processore di telemetria successivo nella catena.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Aggiungere il processore.

**App** ASP.NET

Inserire il frammento di codice ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

È possibile passare i valori della stringa dal file .config fornendo proprietà denominate come pubbliche nella classe.

> [!WARNING]
> Prestare attenzione a fare corrispondere il nome del tipo e i nomi delle proprietà nel file. config ai nomi di classe e di proprietà nel codice. Se il file. config fa riferimento a un tipo o a una proprietà inesistente, l'SDK potrebbe non riuscire a inviare i dati di telemetria in modo invisibile.
>

In alternativa , è possibile inizializzare il filtro nel codice. In una classe di inizializzazione appropriata, ad esempio AppStart in `Global.asax.cs` , inserire il processore nella catena:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

I client di telemetria creati dopo questo punto useranno i processori.

**App del servizio ASP.NET Core/Worker**

> [!NOTE]
> L'aggiunta di un processore usando `ApplicationInsights.config` o `TelemetryConfiguration.Active` non è valida per ASP.NET Core applicazioni o se si usa Microsoft. ApplicationInsights. WorkerService SDK.

Per le app scritte usando [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) o [WorkerService](worker-service.md#adding-telemetry-processors), l'aggiunta di un nuovo processore di telemetria viene eseguita usando il `AddApplicationInsightsTelemetryProcessor` metodo di estensione su `IServiceCollection` , come illustrato. Questo metodo viene chiamato nel `ConfigureServices` metodo della `Startup.cs` classe.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Filtri di esempio

#### <a name="synthetic-requests"></a>Richieste sintetiche

Filtrare i robot e i test Web. Anche se Esplora metriche offre la possibilità di filtrare le origini sintetiche, questa opzione riduce il traffico e le dimensioni di inserimento filtrando gli stessi nell'SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Autenticazione non riuscita

Filtrare le richieste con una risposta "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrare le chiamate di dipendenza remote rapide

Se si desidera diagnosticare solo le chiamate lente, filtrare quelle più veloci.

> [!NOTE]
> Questo filtro distorcerà le statistiche visualizzate nel portale.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnosticare i problemi di dipendenza

[Questo blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descrive un progetto per diagnosticare i problemi di dipendenza con l'invio automatico di ping regolari alle dipendenze.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Applicazioni Web JavaScript

**Filtrare usando ITelemetryInitializer**

1. Creare una funzione di callback dell'inizializzatore di telemetria. La funzione di callback accetta `ITelemetryItem` come parametro, ovvero l'evento che viene elaborato. La restituzione `false` da questo callback comporta il filtraggio dell'elemento di telemetria.

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Aggiungere il callback dell'inizializzatore di telemetria:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Aggiungi/modifica proprietà: ITelemetryInitializer

Usare gli inizializzatori di telemetria per migliorare la telemetria con informazioni aggiuntive o per eseguire l'override delle proprietà di telemetria impostate dai moduli di telemetria standard.

Application Insights per un pacchetto Web, ad esempio, raccoglie dati di telemetria sulle richieste HTTP. Per impostazione predefinita, contrassegna come non riuscita qualsiasi richiesta con un codice di risposta >= 400. Tuttavia, se si desidera considerare 400 come operazione riuscita, è possibile fornire un inizializzatore di telemetria che imposta la proprietà Success.

Se si fornisce un inizializzatore di telemetria, viene chiamato ogni volta che viene chiamato uno qualsiasi dei metodi Track * (). Sono inclusi `Track()` i metodi chiamati dai moduli di telemetria standard. Per convenzione, questi moduli non impostano alcuna proprietà già impostata da un inizializzatore. Gli inizializzatori di telemetria vengono chiamati prima di chiamare i processori di telemetria. Quindi, eventuali arricchimenti eseguiti dagli inizializzatori sono visibili ai processori.

**Definire l'inizializzatore**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**App ASP.NET: caricare l'inizializzatore**

In ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

In alternativa, è possibile creare un'istanza dell'inizializzatore nel codice, ad esempio in Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

Vedere altri [esempi](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole).

**App del servizio ASP.NET Core/Worker: caricare l'inizializzatore**

> [!NOTE]
> L'aggiunta di un inizializzatore tramite `ApplicationInsights.config` o `TelemetryConfiguration.Active` non è valida per ASP.NET Core applicazioni o se si usa Microsoft. ApplicationInsights. WorkerService SDK.

Per le app scritte usando [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) o [WorkerService](worker-service.md#adding-telemetryinitializers), l'aggiunta di un nuovo inizializzatore di telemetria viene eseguita aggiungendola al contenitore di inserimento delle dipendenze, come illustrato. Questa operazione viene eseguita nel `Startup.ConfigureServices` metodo.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>Inizializzatori di telemetria JavaScript
*JavaScript*

Inserire un inizializzatore di telemetria immediatamente dopo il codice di inizializzazione ottenuto dal portale:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Per un riepilogo delle proprietà non personalizzate disponibili nell'elemento di telemetria, vedere [Application Insights esportare il modello di dati](./export-data-model.md).

È possibile aggiungere tutti gli inizializzatori desiderati. Vengono chiamati nell'ordine in cui sono stati aggiunti.

### <a name="opencensus-python-telemetry-processors"></a>Processori di telemetria Python OpenCensus

I processori di telemetria in OpenCensus Python sono semplicemente funzioni di callback chiamate per elaborare i dati di telemetria prima di essere esportati. La funzione di callback deve accettare un tipo di dati [Envelope](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) come parametro. Per filtrare i dati di telemetria dall'esportazione, verificare che la funzione di callback venga restituita `False` . È possibile visualizzare lo schema per i tipi di dati di monitoraggio di Azure nelle buste [su GitHub](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

> [!NOTE]
> È possibile modificare modificando `cloud_RoleName` l' `ai.cloud.role` attributo nel `tags` campo.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Puoi aggiungere tutti i processori che preferisci. Vengono chiamati nell'ordine in cui sono stati aggiunti. Se un processore genera un'eccezione, non ha alcun effetto sui processori seguenti.

### <a name="example-telemetryinitializers"></a>TelemetryInitializers di esempio

#### <a name="add-a-custom-property"></a>Aggiungere una proprietà personalizzata

L'inizializzatore di esempio seguente aggiunge una proprietà personalizzata a tutti i dati di telemetria rilevati.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>Aggiungere un nome di ruolo Cloud

L'inizializzatore di esempio seguente imposta il nome del ruolo Cloud su tutti i dati di telemetria rilevati.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>Aggiungere informazioni da HttpContext

L'inizializzatore di esempio seguente legge i dati da [`HttpContext`](/aspnet/core/fundamentals/http-context?view=aspnetcore-3.1) e li aggiunge a un' `RequestTelemetry` istanza di. `IHttpContextAccessor`Viene fornito automaticamente tramite l'inserimento di dipendenze del costruttore.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

Qual è la differenza tra processori di telemetria e inizializzatori di telemetria?

* Esistono alcune sovrapposizioni in ciò che è possibile fare con loro. Entrambi possono essere usati per aggiungere o modificare le proprietà dei dati di telemetria, anche se si consiglia di usare gli inizializzatori a tale scopo.
* Gli inizializzatori di telemetria vengono sempre eseguiti prima di processori di telemetria.
* Gli inizializzatori di telemetria possono essere chiamati più di una volta. Per convenzione, non impostano alcuna proprietà già impostata.
* I processori di telemetria consentono di sostituire completamente o eliminare un elemento di telemetria.
* Tutti gli inizializzatori di telemetria registrati sono sicuramente chiamati per ogni elemento di telemetria. Per i processori di telemetria, SDK garantisce la chiamata del primo processore di telemetria. Se il resto dei processori viene chiamato o meno viene deciso dai precedenti processori di telemetria.
* Usare gli inizializzatori di telemetria per arricchire la telemetria con proprietà aggiuntive o sostituirne una esistente. Usare un processore di telemetria per filtrare la telemetria.

## <a name="troubleshoot-applicationinsightsconfig"></a>Risoluzione dei problemi ApplicationInsights.config

* Verificare che il nome di tipo completo e il nome di assembly siano corretti.
* Verificare che il file applicationinsights.config si trovi nella directory di output e includa le ultime modifiche apportate.

## <a name="reference-docs"></a>Documentazione di riferimento

* [Panoramica delle API](./api-custom-events-metrics.md)
* [Riferimento ASP.NET](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>Codice SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Passaggi successivi
* [Cercare eventi e log](./diagnostic-search.md)
* [campionamento](./sampling.md)
* [Risoluzione dei problemi](../faq.md)

