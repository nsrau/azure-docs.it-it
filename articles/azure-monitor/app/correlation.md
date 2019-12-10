---
title: Correlazione di dati di Azure Application Insights Telemetry | Microsoft Docs
description: Correlazione di dati di Application Insights Telemetry
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: bc73dfb1c4dc77abe0bd135ecf572fa05ddf6322
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951327"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlazione di dati di telemetria in Application Insights

Nel mondo dei microservizi, ogni operazione logica richiede un lavoro sui vari componenti del servizio. È possibile monitorare separatamente ognuno di questi componenti utilizzando [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights supporta la correlazione dei dati di telemetria distribuita, usata per rilevare i componenti responsabili degli errori o della riduzione del livello delle prestazioni.

Questo articolo illustra il modello di dati usato da Application Insights per correlare i dati di telemetria inviati da più componenti. Illustra le tecniche di propagazione del contesto e i protocolli. Viene inoltre illustrata l'implementazione di tattiche di correlazione su linguaggi e piattaforme diversi.

## <a name="data-model-for-telemetry-correlation"></a>Modello di dati per la correlazione di dati di telemetria

Application Insights definisce un [modello di dati](../../azure-monitor/app/data-model.md) per la correlazione di dati di telemetria distribuita. Per associare i dati di telemetria a un'operazione logica, ogni elemento di telemetria ha un campo di contesto denominato `operation_Id`. Questo identificatore viene condiviso da ogni elemento di telemetria nella traccia distribuita. Quindi, anche se si perdono i dati di telemetria da un singolo livello, è comunque possibile associare i dati di telemetria segnalati da altri componenti.

Un'operazione logica distribuita è in genere costituita da un set di operazioni più piccole che sono richieste elaborate da uno dei componenti. Queste operazioni sono definite dalla [telemetria delle richieste](../../azure-monitor/app/data-model-request-telemetry.md). Ogni elemento di telemetria della richiesta dispone di un proprio `id` che lo identifica in modo univoco e globale. Tutti gli elementi di telemetria (ad esempio tracce ed eccezioni) associati alla richiesta devono impostare il `operation_parentId` sul valore della `id`della richiesta.

Ogni operazione in uscita, ad esempio una chiamata HTTP a un altro componente, è rappresentata dalla [telemetria delle dipendenze](../../azure-monitor/app/data-model-dependency-telemetry.md). I dati di telemetria delle dipendenze definiscono anche la propria `id` univoca a livello globale. La telemetria delle richieste, avviata dalla chiamata di dipendenza, usa questo `id` come `operation_parentId`.

È possibile creare una visualizzazione dell'operazione logica distribuita usando `operation_Id`, `operation_parentId` e `request.id` con `dependency.id`. Questi campi definiscono anche l'ordine della causalità delle chiamate di telemetria.

In un ambiente di microservizi, le tracce dai componenti possono finire in elementi di archiviazione diversi. Ogni componente può avere la propria chiave di strumentazione in Application Insights. Per ottenere la telemetria per l'operazione logica, Application Insights esegue una query dei dati da ogni elemento di archiviazione. Quando il numero di elementi di archiviazione è elevato, è necessario un suggerimento su dove eseguire la ricerca successiva. Per risolvere questo problema, nel modello di dati di Application Insights sono definiti due campi: `request.source` e `dependency.target`. Il primo campo identifica il componente che ha avviato la richiesta di dipendenza. Il secondo campo identifica il componente che ha restituito la risposta della chiamata di dipendenza.

## <a name="example"></a>Esempio

Ecco un esempio. Un'applicazione denominata prezzi azionari Mostra il prezzo di mercato corrente di un'azione usando un'API esterna denominata Stock. L'applicazione dei prezzi azionari dispone di una pagina denominata Stock Page che il browser Web del client si apre usando `GET /Home/Stock`. L'applicazione esegue una query dell'API di inventario usando la chiamata HTTP `GET /api/stock/value`.

È possibile analizzare i dati di telemetria risultanti eseguendo una query:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nei risultati tutti gli elementi di telemetria condividono l'elemento `operation_Id` radice. Quando viene effettuata una chiamata AJAX dalla pagina, viene assegnato un nuovo ID univoco (`qJSXU`) ai dati di telemetria delle dipendenze e l'ID della pagina di visualizzazione viene usato come `operation_ParentId`. La richiesta server usa quindi l'ID Ajax come `operation_ParentId`.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Pagina Stock                |              | STYz               | STYz         |
| dipendenza | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| richiesta    | GET /Home/Stock            | KqKwlrSt9PA = | qJSXU              | STYz         |
| dipendenza | GET /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Quando la chiamata `GET /api/stock/value` viene effettuata a un servizio esterno, è necessario conoscerne l'identità per poter impostare il campo `dependency.target` in modo appropriato. Quando il servizio esterno non supporta il monitoraggio, `target` è impostato sul nome host del servizio (ad esempio, `stock-prices-api.com`). Tuttavia, se il servizio viene identificato restituendo un'intestazione HTTP predefinita, `target` contiene l'identità del servizio che consente Application Insights di compilare una traccia distribuita eseguendo una query sui dati di telemetria di tale servizio.

## <a name="correlation-headers"></a>Intestazioni di correlazione

Application Insights viene eseguita la transizione a [W3C Trace-context](https://w3c.github.io/trace-context/), che definisce:

- `traceparent`: contiene l'ID operazione univoco globale e l'identificatore univoco della chiamata.
- `tracestate`: contiene il contesto di traccia specifico del sistema.

La versione più recente di Application Insights SDK supporta il protocollo di contesto della traccia, ma potrebbe essere necessario acconsentire esplicitamente. La compatibilità con il protocollo di correlazione precedente supportato da Application Insights SDK verrà mantenuta.

Il [protocollo http di correlazione, denominato anche Request-ID](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), verrà deprecato. Questo protocollo definisce due intestazioni:

- `Request-Id`: contiene l'ID univoco globale della chiamata.
- `Correlation-Context`: contiene la raccolta di coppie nome-valore delle proprietà della traccia distribuita.

Application Insights definisce anche l' [estensione](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) per il protocollo http di correlazione. Usa le coppie nome-valore `Request-Context` per propagare la raccolta di proprietà usate dal chiamante o dal destinatario della chiamata. L'SDK Application Insights usa questa intestazione per impostare i campi `dependency.target` e `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app ASP.NET classiche
 
  > [!NOTE]
  >  A partire da `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector`, non è necessaria alcuna configurazione.

Il supporto del contesto di traccia W3C viene implementato in modo compatibile con le versioni precedenti. Si prevede che la correlazione funzioni con le applicazioni instrumentate con le versioni precedenti dell'SDK (senza supporto W3C).

Se si desidera utilizzare il protocollo legacy `Request-Id`, è possibile disabilitare il contesto di traccia utilizzando questa configurazione:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se si esegue una versione precedente dell'SDK, è consigliabile aggiornarla o applicare la configurazione seguente per abilitare il contesto di traccia.
Questa funzionalità è disponibile nei pacchetti `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector`, a partire dalla versione 2.8.0-beta1.
È disabilitata per impostazione predefinita. Per abilitarla, apportare le modifiche seguenti `ApplicationInsights.config`:

- In `RequestTrackingTelemetryModule`aggiungere l'elemento `EnableW3CHeadersExtraction` e impostarne il valore su `true`.
- In `DependencyTrackingTelemetryModule`aggiungere l'elemento `EnableW3CHeadersInjection` e impostarne il valore su `true`.
- Aggiungere `W3COperationCorrelationTelemetryInitializer` in `TelemetryInitializers`. L'aspetto sarà simile a questo esempio:

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app ASP.NET Core

 > [!NOTE]
  > A partire da `Microsoft.ApplicationInsights.AspNetCore` versione 2.8.0, non è necessaria alcuna configurazione.
 
Il supporto del contesto di traccia W3C viene implementato in modo compatibile con le versioni precedenti. Si prevede che la correlazione funzioni con le applicazioni instrumentate con le versioni precedenti dell'SDK (senza supporto W3C).

Se si desidera utilizzare il protocollo legacy `Request-Id`, è possibile disabilitare il contesto di traccia utilizzando questa configurazione:

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Se si esegue una versione precedente dell'SDK, è consigliabile aggiornarla o applicare la configurazione seguente per abilitare il contesto di traccia.

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

  - Per le app Java EE, aggiungere quanto segue al tag `<TelemetryModules>` in ApplicationInsights. XML:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - Per le app Spring boot, aggiungere queste proprietà:

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
> Verificare che le configurazioni in entrata e in uscita siano identiche.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Abilitare il supporto per la traccia distribuita W3C per le app Web

Questa funzionalità è `Microsoft.ApplicationInsights.JavaScript`. È disabilitata per impostazione predefinita. Per abilitarla, usare `distributedTracingMode` config. AI_AND_W3C viene fornito per la compatibilità con le versioni precedenti di tutti i servizi legacy instrumentati da Application Insights.

- **installazione di NPM (ignora se si usa l'installazione del frammento)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Impostazione del frammento (ignora se si usa l'installazione di NPM)**

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

Per altre informazioni, vedere [Application Insights modello di dati di telemetria](../../azure-monitor/app/data-model.md).

Per le definizioni dei concetti di OpenTracing, vedere la [specifica](https://github.com/opentracing/specification/blob/master/specification.md) OpenTracing e le [convenzioni semantiche](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlazione di dati di telemetria in OpenCensus Python

OpenCensus Python segue le specifiche del modello di dati `OpenTracing` descritte in precedenza. Supporta inoltre il [contesto di traccia W3C](https://w3c.github.io/trace-context/) senza richiedere alcuna configurazione.

### <a name="incoming-request-correlation"></a>Correlazione delle richieste in ingresso

OpenCensus Python mette in correlazione le intestazioni del contesto di traccia W3C dalle richieste in ingresso agli intervalli generati dalle richieste stesse. OpenCensus eseguirà questa operazione automaticamente con integrazioni per questi framework di applicazioni Web comuni: Flask, Django e Pyramid. È sufficiente popolare le intestazioni del contesto di traccia W3C con il [formato corretto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) e inviarle con la richiesta. Ecco un'applicazione Flask di esempio che dimostra quanto segue:

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

Questo codice esegue un'applicazione Flask di esempio nel computer locale, ascoltando la porta `8080`. Per correlare il contesto della traccia, inviare una richiesta all'endpoint. In questo esempio è possibile usare un comando `curl`:
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Esaminando il [formato dell'intestazione del contesto di traccia](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), è possibile derivare le informazioni seguenti:

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Se si esamina la voce della richiesta inviata a monitoraggio di Azure, è possibile visualizzare i campi popolati con le informazioni di intestazione della traccia. È possibile trovare questi dati in log (Analytics) nella risorsa di monitoraggio di Azure Application Insights.

![Telemetria delle richieste nei log (analisi)](./media/opencensus-python/0011-correlation.png)

Il campo `id` è nel formato `<trace-id>.<span-id>`, in cui il `trace-id` viene ricavato dall'intestazione di traccia passata nella richiesta e il `span-id` è una matrice a 8 byte generata per questo intervallo.

Il campo `operation_ParentId` è nel formato `<trace-id>.<parent-id>`, in cui sia il `trace-id` che il `parent-id` vengono ricavati dall'intestazione di traccia passata nella richiesta.

### <a name="log-correlation"></a>Correlazione dei registri

OpenCensus Python consente di correlare i log aggiungendo un ID traccia, un ID span e un flag di campionamento ai record di log. Per aggiungere questi attributi, installare OpenCensus [Logging Integration](https://pypi.org/project/opencensus-ext-logging/). Gli attributi seguenti verranno aggiunti agli oggetti Python `LogRecord`: `traceId`, `spanId`e `traceSampled`. Si noti che questa operazione ha effetto solo per i logger creati dopo l'integrazione.

Ecco un'applicazione di esempio che dimostra quanto segue:

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
Quando viene eseguito questo codice, nella console vengono stampate le seguenti opzioni:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Si noti che è presente un `spanId` presente per il messaggio di log compreso nell'intervallo. Si tratta dello stesso `spanId` che appartiene all'intervallo denominato `hello`.

È possibile esportare i dati di log utilizzando `AzureLogHandler`. Per altre informazioni, vedere [questo articolo](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs).

## <a name="telemetry-correlation-in-net"></a>Correlazione di dati di telemetria in .NET

Nel corso del tempo, .NET ha definito diversi modi per correlare i log di diagnostica e di telemetria:

- `System.Diagnostics.CorrelationManager` consente di tenere traccia di [LogicalOperationStack e ActivityID](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx).
- `System.Diagnostics.Tracing.EventSource` ed Event Tracing for Windows (ETW) definiscono il metodo [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger` usa gli [ambiti del log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes).
- Windows Communication Foundation (WCF) e HTTP creano la propagazione del contesto "corrente".

Tuttavia, questi metodi non abilitavano il supporto automatico per la traccia distribuita. `DiagnosticSource` supporta la correlazione automatica tra computer. Le librerie .NET supportano `DiagnosticSource` e consentono la propagazione automatica tra computer del contesto di correlazione tramite il trasporto, ad esempio HTTP.

Il [manuale dell'utente dell'attività](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) in `DiagnosticSource` illustra le nozioni di base delle attività di monitoraggio.

ASP.NET Core 2,0 supporta l'estrazione di intestazioni HTTP e l'avvio di nuove attività.

`System.Net.Http.HttpClient`, a partire dalla versione 4.1.0, supporta l'inserimento automatico delle intestazioni HTTP di correlazione e il rilevamento delle chiamate HTTP come attività.

È disponibile un nuovo modulo HTTP, [Microsoft. AspNet. TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), per ASP.NET classiche. Questo modulo implementa correlazione di dati di telemetria usando `DiagnosticSource`. Avvia un'attività in base alle intestazioni di richiesta in ingresso. Correla inoltre i dati di telemetria delle diverse fasi di elaborazione della richiesta, anche quando ogni fase dell'elaborazione di Internet Information Services (IIS) viene eseguita in un thread gestito diverso.

Application Insights SDK, a partire dalla versione 2.4.0-beta1, usa `DiagnosticSource` e `Activity` per raccogliere i dati di telemetria e associarli all'attività corrente.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlazione di dati di telemetria in Java SDK

[Application Insights SDK per Java](../../azure-monitor/app/java-get-started.md) versione 2.0.0 o successiva supporta la correlazione automatica dei dati di telemetria. Popola automaticamente `operation_id` per tutti i dati di telemetria, ad esempio tracce, eccezioni ed eventi personalizzati, emessi nell'ambito di una richiesta. Propaga anche le intestazioni di correlazione (descritte in precedenza) per le chiamate da servizio a servizio tramite HTTP, se è configurato l' [agente Java SDK](../../azure-monitor/app/java-agent.md) .

> [!NOTE]
> Per la funzionalità di correlazione sono supportate solo le chiamate effettuate tramite Apache HttpClient. Sia Spring RestTemplate che finse possono essere usati con Apache HttpClient dietro le quinte.

Attualmente, la propagazione del contesto automatica tra le tecnologie di messaggistica (ad esempio Kafka, RabbitMQ e il bus di servizio di Azure) non è supportata. È possibile codificare tali scenari manualmente usando i metodi `trackDependency` e `trackRequest`. In questi metodi, una telemetria delle dipendenze rappresenta un messaggio accodato da un producer. La richiesta rappresenta un messaggio elaborato da un consumer. In questo caso sia `operation_id` che `operation_parentId` devono essere propagati nelle proprietà del messaggio.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>Correlazione di dati di telemetria nelle applicazioni Java asincrone

Per informazioni su come correlare i dati di telemetria in un'applicazione Spring boot asincrona, vedere la pagina relativa alla [traccia distribuita nelle applicazioni Java asincrone](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications). Questo articolo fornisce indicazioni per la strumentazione di [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) e [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)di Spring.


<a name="java-role-name"></a>
## <a name="role-name"></a>Nome del ruolo

Potrebbe essere necessario personalizzare il modo in cui i nomi dei componenti vengono visualizzati nella [mappa delle applicazioni](../../azure-monitor/app/app-map.md). A tale scopo, è possibile impostare manualmente il `cloud_RoleName` eseguendo una delle azioni seguenti:

- Con Application Insights SDK Java 2.5.0 e versioni successive, è possibile specificare il `cloud_RoleName` aggiungendo `<RoleName>` al file ApplicationInsights. XML:

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Se si usa Spring boot con Application Insights Spring boot Starter, è sufficiente impostare il nome personalizzato per l'applicazione nel file Application. Properties:

  `spring.application.name=<name-of-app>`

  L'Starter Spring boot assegna automaticamente `cloudRoleName` al valore immesso per la proprietà `spring.application.name`.

## <a name="next-steps"></a>Passaggi successivi

- Scrivere [dati di telemetria personalizzati](../../azure-monitor/app/api-custom-events-metrics.md).
- Per gli scenari di correlazione avanzati in ASP.NET Core e ASP.NET, vedere [tenere traccia delle operazioni personalizzate](custom-operations-tracking.md).
- Altre informazioni sull'[impostazione di cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) per altri SDK.
- Caricare tutti i componenti del microservizio in Application Insights. Controllare le [piattaforme supportate](../../azure-monitor/app/platforms.md).
- Per informazioni sui tipi di Application Insights, vedere il [modello di dati](../../azure-monitor/app/data-model.md).
- Informazioni su come [estendere e filtrare i dati di telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Vedere le [informazioni di riferimento sulla configurazione di Application Insights](configuration-with-applicationinsights-config.md).
