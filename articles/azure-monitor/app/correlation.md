---
title: Correlazione di dati di Azure Application Insights Telemetry | Microsoft Docs
description: Correlazione di dati di Application Insights Telemetry
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276127"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlazione di dati di telemetria in Application Insights

Nel mondo dei microservizi, ogni operazione logica richiede un lavoro sui vari componenti del servizio. È possibile monitorare ognuno di questi componenti separatamente utilizzando [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights supporta la correlazione dei dati di telemetria distribuita, usata per rilevare i componenti responsabili degli errori o della riduzione del livello delle prestazioni.

Questo articolo illustra il modello di dati usato da Application Insights per correlare i dati di telemetria inviati da più componenti. Illustra le tecniche di propagazione del contesto e i protocolli. Copre anche l'implementazione di tattiche di correlazione su diversi linguaggi e piattaforme.

## <a name="data-model-for-telemetry-correlation"></a>Modello di dati per la correlazione di dati di telemetria

Application Insights definisce un [modello di dati](../../azure-monitor/app/data-model.md) per la correlazione di dati di telemetria distribuita. Per associare dati di telemetria a un'operazione logica, ogni elemento di telemetria dispone di un campo di contesto denominato `operation_Id`. Questo identificatore viene condiviso da ogni elemento di telemetria nella traccia distribuita. Pertanto, anche se si perdono dati di telemetria da un singolo livello, è comunque possibile associare i dati di telemetria segnalati da altri componenti.

Un'operazione logica distribuita è in genere costituita da un set di operazioni più piccole che sono richieste elaborate da uno dei componenti. Queste operazioni sono definite dalla [telemetria delle richieste](../../azure-monitor/app/data-model-request-telemetry.md). Ogni elemento di telemetria della richiesta ha un proprio `id` che lo identifica in modo univoco e globale. E tutti gli elementi di telemetria (ad esempio tracce ed `operation_parentId` eccezioni) associati `id`alla richiesta devono impostare il sul valore della richiesta .

Ogni operazione in uscita, ad esempio una chiamata HTTP a un altro componente, è rappresentata dalla [telemetria delle dipendenze](../../azure-monitor/app/data-model-dependency-telemetry.md). La telemetria delle dipendenze definisce anche il proprio `id` che è globalmente univoco. La telemetria delle richieste, avviata dalla chiamata di dipendenza, usa questo `id` come `operation_parentId`.

È possibile creare una visualizzazione dell'operazione logica distribuita usando `operation_Id`, `operation_parentId` e `request.id` con `dependency.id`. Questi campi definiscono anche l'ordine della causalità delle chiamate di telemetria.

In un ambiente di microservizi, le tracce dai componenti possono finire in elementi di archiviazione diversi. Ogni componente può avere la propria chiave di strumentazione in Application Insights. Per ottenere dati di telemetria per l'operazione logica, Application Insights esegue una query sui dati da ogni elemento di archiviazione. Quando il numero di elementi di archiviazione è elevato, è necessario un suggerimento su dove cercare successivamente. Per risolvere questo problema, nel modello di dati di Application Insights sono definiti due campi: `request.source` e `dependency.target`. Il primo campo identifica il componente che ha avviato la richiesta di dipendenza. Il secondo campo identifica quale componente ha restituito la risposta della chiamata di dipendenza.

## <a name="example"></a>Esempio

Ecco un esempio. Un'applicazione denominata Stock Prices mostra il prezzo di mercato corrente di un'azione utilizzando un'API esterna denominata Stock. L'applicazione Stock Prices dispone di una pagina denominata `GET /Home/Stock`Stock page che il browser Web client apre utilizzando . L'applicazione esegue una query sull'API Stock utilizzando la chiamata `GET /api/stock/value`HTTP .

È possibile analizzare i dati di telemetria risultanti eseguendo una query:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nei risultati tutti gli elementi di telemetria condividono l'elemento `operation_Id` radice. Quando viene effettuata una chiamata Ajax dalla`qJSXU`pagina, un nuovo ID univoco ( ) viene assegnato `operation_ParentId`ai dati di telemetria delle dipendenze e l'ID di pageView viene utilizzato come . La richiesta server usa quindi l'ID Ajax come `operation_ParentId`.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Pagina Stock                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| richiesta    | GET /Home/Stock            | KqKwlrSt9PA = | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Quando la `GET /api/stock/value` chiamata viene effettuata a un servizio esterno, è necessario `dependency.target` conoscere l'identità di tale server in modo da poter impostare il campo in modo appropriato. Quando il servizio esterno non supporta il monitoraggio, `target` è impostato sul nome host del servizio (ad esempio, `stock-prices-api.com`). Tuttavia, se il servizio si identifica restituendo un'intestazione HTTP predefinita, `target` contiene l'identità del servizio che consente ad Application Insights di creare una traccia distribuita eseguendo query sui dati di telemetria da tale servizio.

## <a name="correlation-headers"></a>Intestazioni di correlazione

Application Insights sta passando a [W3C Trace-Context](https://w3c.github.io/trace-context/), che definisce:

- `traceparent`: esegue l'ID operazione univoco globale e l'identificatore univoco della chiamata.
- `tracestate`: esegue il contesto di traccia tura specifico del sistema.

La versione più recente di Application Insights SDK supporta il protocollo Trace-Context, ma potrebbe essere necessario acconsentonoadevi esplicitamente. (Compatibilità con le versioni precedenti del protocollo di correlazione supportato da Application Insights SDK sarà mantenuta.)

Il protocollo HTTP di [correlazione, denominato anche Request-Id](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), è deprecato. Questo protocollo definisce due intestazioni:

- `Request-Id`: esegue l'ID univoco globale della chiamata.
- `Correlation-Context`: esegue la raccolta di coppie nome-valore delle proprietà di traccia distribuite.

Application Insights definisce anche [l'estensione](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) per il protocollo HTTP di correlazione. Usa le coppie nome-valore `Request-Context` per propagare la raccolta di proprietà usate dal chiamante o dal destinatario della chiamata. Application Insights SDK usa questa `dependency.target` intestazione per impostare i campi e `request.source` .

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app ASP.NET classiche
 
  > [!NOTE]
  >  A `Microsoft.ApplicationInsights.Web` partire `Microsoft.ApplicationInsights.DependencyCollector`da e , non è necessaria alcuna configurazione.

Il supporto W3C Trace-Context viene implementato in modo compatibile con le versioni precedenti. La correlazione dovrebbe funzionare con applicazioni instrumentate con versioni precedenti dell'SDK (senza supporto W3C).

Se si desidera continuare `Request-Id` a utilizzare il protocollo legacy, è possibile disabilitare Trace-Context utilizzando questa configurazione:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se si esegue una versione precedente dell'SDK, è consigliabile aggiornarla o applicare la configurazione seguente per abilitare Trace-Context.
Questa funzionalità è `Microsoft.ApplicationInsights.Web` disponibile `Microsoft.ApplicationInsights.DependencyCollector` nei pacchetti e, a partire dalla versione 2.8.0-beta1.
È disabilitata per impostazione predefinita. Per attivarla, apportare `ApplicationInsights.config`queste modifiche a:

- In `RequestTrackingTelemetryModule`, `EnableW3CHeadersExtraction` aggiungere l'elemento `true`e impostarne il valore su .
- In `DependencyTrackingTelemetryModule`, `EnableW3CHeadersInjection` aggiungere l'elemento `true`e impostarne il valore su .
- Aggiungi `W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers`in . Sarà simile a questo esempio:It will look similar to this example:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app ASP.NET Core

 > [!NOTE]
  > A `Microsoft.ApplicationInsights.AspNetCore` partire dalla versione 2.8.0, non è necessaria alcuna configurazione.
 
Il supporto W3C Trace-Context viene implementato in modo compatibile con le versioni precedenti. La correlazione dovrebbe funzionare con applicazioni instrumentate con versioni precedenti dell'SDK (senza supporto W3C).

Se si desidera continuare `Request-Id` a utilizzare il protocollo legacy, è possibile disabilitare Trace-Context utilizzando questa configurazione:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se si esegue una versione precedente dell'SDK, è consigliabile aggiornarla o applicare la configurazione seguente per abilitare Trace-Context.

Questa funzionalità si trova nella versione 2.5.0-beta1 di `Microsoft.ApplicationInsights.AspNetCore` e nella versione 2.8.0-beta1 di `Microsoft.ApplicationInsights.DependencyCollector`.
È disabilitata per impostazione predefinita. Per abilitarla, impostare `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` su `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app Java

- **Configurazione in ingresso**

  - Per le app Java EE, `<TelemetryModules>` aggiungere quanto segue al tag in ApplicationInsights.xml:For Java EE apps, add the following to the tag in ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Per le app di avvio a molla, aggiungi queste proprietà:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configurazione in uscita**

  Aggiungere il codice seguente al file AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > La modalità di compatibilità con le versioni precedenti è abilitata per impostazione predefinita e il parametro `enableW3CBackCompat` è facoltativo. Usarlo solo per disattivare la compatibilità con le versioni precedenti.
  >
  > È opportuno disattivarla, ad esempio, quando tutti i servizi sono stati aggiornati a versioni più recenti degli SDK che supportano il protocollo W3C. È consigliabile passare a questi SDK più recenti il prima possibile.

> [!IMPORTANT]
> Assicurarsi che le configurazioni in entrata e in uscita siano esattamente le stesse.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Abilitare il supporto della traccia distribuita W3C per le app WebEnable W3C distributed tracing support for Web apps

Questa funzione `Microsoft.ApplicationInsights.JavaScript`è in . È disabilitata per impostazione predefinita. Per abilitarlo, `distributedTracingMode` utilizzare config. AI_AND_W3C viene fornito per garantire la compatibilità con gli eventuali servizi legacy instrumentati da Application Insights.For for compatibility with any legacy services instrumented by Application Insights.

- **Setup npm (ignora se si utilizza l'impostazione del frammento)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Impostazione frammento (ignora se si utilizza npm setup)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing e Application Insights

La [specifica del modello di dati OpenTracing](https://opentracing.io/) e i modelli di dati di Application Insights eseguono il mapping nel modo seguente:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` con `span.kind = server`                  |
| `Dependency`                          | `Span` con `span.kind = client`                  |
| `Id` di `Request` e `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` di tipo `ChildOf` (l'intervallo padre)   |

Per altre informazioni, vedere Modello di dati di telemetria di [Application Insights.For more information,](../../azure-monitor/app/data-model.md)see Application Insights telemetry data model .

Per le definizioni dei concetti di OpenTracing, vedere la [specifica](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing e [le convenzioni semantiche](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlazione di telemetria in OpenCensus PythonTelemetry correlation in OpenCensus Python

OpenCensus Python `OpenTracing` segue le specifiche del modello di dati descritte in precedenza. Supporta inoltre [W3C Trace-Context](https://w3c.github.io/trace-context/) senza richiedere alcuna configurazione.

### <a name="incoming-request-correlation"></a>Correlazione delle richieste in ingresso

OpenCensus Python correla le intestazioni W3C Trace-Context dalle richieste in ingresso agli intervalli generati dalle richieste stesse. OpenCensus farà questo automaticamente con le integrazioni per questi popolari framework di applicazioni web: Flask, Django, e Piramide. È sufficiente popolare le intestazioni W3C Trace-Context con il [formato corretto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e inviarle con la richiesta. Ecco un'applicazione Flask di esempio che illustra questo:Here's a sample Flask application that demonstrates this:

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Questo codice esegue un'applicazione Flask di esempio `8080`nel computer locale, ascoltando la porta . Per correlare il contesto di traccia, si invia una richiesta all'endpoint. In questo esempio, è `curl` possibile utilizzare un comando:In this example, you can use a command:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Esaminando il [formato dell'intestazione Trace-Context](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), è possibile derivare le seguenti informazioni:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se si esamina la voce della richiesta inviata a Monitoraggio di Azure, è possibile visualizzare i campi popolati con le informazioni dell'intestazione di traccia. È possibile trovare questi dati in Log (Analytics) nella risorsa Monitoraggio applicazioni di Azure.You can find this data under Logs (Analytics) in the Azure Monitor Application Insights resource.

![Richiedere dati di telemetria nei log (Analytics)Request telemetry in Logs (Analytics)](./media/opencensus-python/0011-correlation.png)

Il `id` campo è `<trace-id>.<span-id>`nel formato `trace-id` , in cui l'oggetto viene ricavato dall'intestazione di traccia passata nella richiesta e il `span-id` è una matrice a 8 byte generata per questo intervallo.

Il `operation_ParentId` campo è `<trace-id>.<parent-id>`nel formato `trace-id` , `parent-id` dove sia il e il vengono ricavati dall'intestazione di traccia che è stata passata nella richiesta.

### <a name="log-correlation"></a>Correlazione dei registri

OpenCensus Python consente di correlare i log aggiungendo un ID di traccia, un ID span e un flag di campionamento per i record di log. Per aggiungere questi attributi, installare [l'integrazione](https://pypi.org/project/opencensus-ext-logging/)della registrazione OpenCensus . Agli oggetti `LogRecord` Python verranno aggiunti `traceId` `spanId`i `traceSampled`seguenti attributi: , , e . Si noti che questo ha effetto solo per i logger creati dopo l'integrazione.

Ecco un'applicazione di esempio che illustra questo:Here's a sample application that demonstrates this:

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Quando viene eseguito questo codice, nella console vengono stampate le seguenti stampe:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Si noti che `spanId` è presente per il messaggio di log che si trova all'interno dell'intervallo. Si tratta `spanId` della stessa cosa `hello`che appartiene all'intervallo denominato .

È possibile esportare i `AzureLogHandler`dati del registro utilizzando . Per altre informazioni, vedere [questo articolo](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Correlazione di dati di telemetria in .NET

Nel corso del tempo, .NET ha definito diversi modi per correlare i log di telemetria e diagnostica:In time, .NET has defined several ways to correlate telemetry and diagnostics logs:

- `System.Diagnostics.CorrelationManager`consente il rilevamento di [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` ed Event Tracing for Windows (ETW) definiscono il metodo [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger`utilizza [gli ambiti di registro](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) e HTTP creano la propagazione del contesto "corrente".

Ma questi metodi non abilitava il supporto automatico della traccia distribuita. `DiagnosticSource`supporta la correlazione automatica tra macchine. Le librerie `DiagnosticSource` .NET supportano e consentono la propagazione automatica tra computer del contesto di correlazione tramite il trasporto, ad esempio HTTP.

La Guida per `DiagnosticSource` [l'utente](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) di Attività in spiega le nozioni di base delle attività di monitoraggio.

ASP.NET Core 2.0 supporta l'estrazione delle intestazioni HTTP e l'avvio di nuove attività.

`System.Net.Http.HttpClient`, a partire dalla versione 4.1.0, supporta l'inserimento automatico delle intestazioni HTTP di correlazione e il rilevamento delle chiamate HTTP come attività.

È disponibile un nuovo modulo HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), per ASP.NET classici. Questo modulo implementa correlazione di dati di telemetria usando `DiagnosticSource`. Avvia un'attività in base alle intestazioni di richiesta in ingresso. Correla inoltre i dati di telemetria dalle diverse fasi dell'elaborazione delle richieste, anche quando ogni fase dell'elaborazione di Internet Information Services (IIS) viene eseguita in un thread gestito diverso.

Application Insights SDK, a partire dalla versione 2.4.0-beta1, usa `DiagnosticSource` e `Activity` per raccogliere i dati di telemetria e associarli all'attività corrente.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlazione di dati di telemetria in Java SDK

[Application Insights SDK per Java](../../azure-monitor/app/java-get-started.md) versione 2.0.0 o successiva supporta la correlazione automatica dei dati di telemetria. Viene popolato `operation_id` automaticamente per tutti i dati di telemetria (ad esempio tracce, eccezioni ed eventi personalizzati) emessi nell'ambito di una richiesta. Propaga inoltre le intestazioni di correlazione (descritte in precedenza) per le chiamate da servizio a servizio tramite HTTP, se [l'agente Java SDK](../../azure-monitor/app/java-agent.md) è configurato.

> [!NOTE]
> Solo le chiamate effettuate tramite Apache HttpClient sono supportate per la funzionalità di correlazione. Sia Spring RestTemplate che Feign possono essere utilizzati con Apache HttpClient sotto il cofano.

Attualmente, la propagazione automatica del contesto tra le tecnologie di messaggistica (ad esempio Kafka, RabbitMQ e il bus di servizio di Azure) non è supportata. È possibile codificare manualmente tali `trackDependency` scenari `trackRequest` utilizzando i metodi e . In questi metodi, una telemetria delle dipendenze rappresenta un messaggio accodato da un producer. La richiesta rappresenta un messaggio elaborato da un consumer. In questo caso sia `operation_id` che `operation_parentId` devono essere propagati nelle proprietà del messaggio.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Correlazione di telemetria nelle applicazioni Java asincroneTelemetry correlation in asynchronous Java applications

Per informazioni su come correlare i dati di telemetria in un'applicazione di avvio a molla asincrona, vedere [Traccia distribuita in applicazioni Java asincrone](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications). In questo articolo vengono fornite indicazioni per la strumentazione [di ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) di Spring e [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html).


<a name="java-role-name"></a>
## <a name="role-name"></a>Nome del ruolo

È possibile personalizzare la modalità di visualizzazione dei nomi dei componenti nella [mappa dell'applicazione](../../azure-monitor/app/app-map.md). A tale scopo, è `cloud_RoleName` possibile impostare manualmente l'oggetto eseguendo una delle seguenti operazioni:

- Con Application Insights Java SDK 2.5.0 e `cloud_RoleName` versioni `<RoleName>` successive, è possibile specificare il oggetto aggiungendo al file ApplicationInsights.xml:With Application Insights Java SDK 2.5.0 and later, you can specify the by adding to your ApplicationInsights.xml file:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se si usa lo scaraè di primavera con Application Insights Spring Boot Starter, è sufficiente impostare il nome personalizzato per l'applicazione nel file application.properties:

  `spring.application.name=<name-of-app>`

  L'inizio dello scarpone a molla assegna automaticamente `cloudRoleName` al valore immesso per la `spring.application.name` proprietà.

## <a name="next-steps"></a>Passaggi successivi

- Scrivere [dati di telemetria personalizzati.](../../azure-monitor/app/api-custom-events-metrics.md)
- Per scenari di correlazione avanzati in ASP.NET Core e ASP.NET, vedere [Tenere traccia delle operazioni personalizzate](custom-operations-tracking.md).
- Altre informazioni sull'[impostazione di cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) per altri SDK.
- Caricare tutti i componenti del microservizio in Application Insights. Controllare le [piattaforme supportate](../../azure-monitor/app/platforms.md).
- Per informazioni sui tipi di Application Insights, vedere il [modello di dati](../../azure-monitor/app/data-model.md).
- Informazioni su come [estendere e filtrare i dati di telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Vedere le [informazioni di riferimento sulla configurazione di Application Insights](configuration-with-applicationinsights-config.md).
