---
title: Correlazione di dati di Azure Application Insights Telemetry | Microsoft Docs
description: Correlazione di dati di Application Insights Telemetry
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: bcb62b311426c7582202ced3714d01c429118ab5
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268414"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlazione di dati di telemetria in Application Insights

Nel mondo dei microservizi, ogni operazione logica richiede un lavoro sui vari componenti del servizio. Ognuno di questi componenti può essere monitorato separatamente da [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Il componente app Web comunica con il componente provider di autenticazione per convalidare le credenziali dell'utente e con il componente API per ottenere dati per la visualizzazione. Il componente API può eseguire query sui dati di altri servizi e usare componenti del provider di cache per notificare questa richiesta al componente fatturazione. Application Insights supporta la correlazione dei dati di telemetria distribuita, usata per rilevare i componenti responsabili degli errori o della riduzione del livello delle prestazioni.

Questo articolo illustra il modello di dati usato da Application Insights per correlare i dati di telemetria inviati da più componenti. Illustra le tecniche di propagazione del contesto e i protocolli. Descrive anche l'implementazione dei concetti di correlazione in linguaggi e piattaforme diversi.

## <a name="data-model-for-telemetry-correlation"></a>Modello di dati per la correlazione di dati di telemetria

Application Insights definisce un [modello di dati](../../azure-monitor/app/data-model.md) per la correlazione di dati di telemetria distribuita. Per associare i dati di telemetria all'operazione logica, ogni elemento di telemetria dispone di un campo di contesto denominato `operation_Id`. Questo identificatore viene condiviso da ogni elemento di telemetria nella traccia distribuita. Se quindi si verifica una perdita di dati di telemetria da un singolo livello, è comunque possibile associare i dati di telemetria segnalati da altri componenti.

Un'operazione logica distribuita consiste in genere in un set di operazioni più piccole, vale a dire richieste elaborate da uno dei componenti. Queste operazioni sono definite dalla [telemetria delle richieste](../../azure-monitor/app/data-model-request-telemetry.md). Ogni telemetria delle richieste è dotata di un proprio `id` che la identifica in modo univoco e a livello globale. Tutti gli elementi della telemetria (ad esempio, tracce ed eccezioni) associati alla richiesta devono impostare `operation_parentId` sul valore di `id` della richiesta.

Ogni operazione in uscita, ad esempio una chiamata HTTP a un altro componente, è rappresentata dalla [telemetria delle dipendenze](../../azure-monitor/app/data-model-dependency-telemetry.md). Anche la telemetria delle dipendenze dispone di un proprio `id` globalmente univoco. La telemetria delle richieste, avviata dalla chiamata di dipendenza, usa questo `id` come `operation_parentId`.

È possibile creare una visualizzazione dell'operazione logica distribuita usando `operation_Id`, `operation_parentId` e `request.id` con `dependency.id`. Questi campi definiscono anche l'ordine della causalità delle chiamate di telemetria.

In un ambiente di microservizi, le tracce dai componenti possono finire in elementi di archiviazione diversi. Ogni componente può avere la propria chiave di strumentazione in Application Insights. Per ottenere i dati di telemetria per l'operazione logica, è necessario eseguire query sui dati da ogni elemento di archiviazione. Quando il numero di elementi di archiviazione è molto elevato, sarà necessario un hint per sapere dove cercare dopo. Per risolvere questo problema, nel modello di dati di Application Insights sono definiti due campi: `request.source` e `dependency.target`. Il primo campo definisce il componente che ha avviato la richiesta di dipendenza, mentre il secondo identifica quale componente ha restituito la risposta alla chiamata di dipendenza.

## <a name="example"></a>Esempio

Un'applicazione denominata Stock Prices mostra il prezzo di mercato di un'azione, usando un'API esterna denominata `Stock`. L'applicazione Stock Prices ha una pagina denominata `Stock page` che viene aperta dal Web browser client con `GET /Home/Stock`. L'applicazione esegue una query sull'API `Stock` usando una chiamata HTTP `GET /api/stock/value`.

È possibile analizzare i dati di telemetria risultanti eseguendo una query:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nei risultati tutti gli elementi di telemetria condividono l'elemento `operation_Id` radice. Quando viene eseguita una chiamata Ajax dalla pagina, viene assegnato un nuovo ID univoco (`qJSXU`) alla telemetria delle dipendenze e l'ID di pageView viene usato come `operation_ParentId`. La richiesta server usa quindi l'ID Ajax come `operation_ParentId`.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Pagina Stock                |              | STYz               | STYz         |
| dipendenza | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| richiesta    | GET /Home/Stock            | KqKwlrSt9PA = | qJSXU              | STYz         |
| dipendenza | GET /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Quando la chiamata `GET /api/stock/value` viene effettuata a un servizio esterno, si vuole conoscere l'identità del relativo server per poter impostare correttamente il campo `dependency.target`. Quando il servizio esterno non supporta il monitoraggio, `target` è impostato sul nome host del servizio (ad esempio, `stock-prices-api.com`). Se tuttavia il servizio identifica se stesso restituendo un'intestazione HTTP predefinita, `target` contiene l'identità del servizio che consente ad Application Insights di creare una traccia distribuita eseguendo query sui dati di telemetria di quel servizio.

## <a name="correlation-headers"></a>Intestazioni di correlazione

Microsoft sta lavorando a una proposta RFC per il [protocollo HTTP per la correlazione](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Questa proposta definisce due intestazioni:

- `Request-Id`: contiene l'ID globalmente univoco della chiamata.
- `Correlation-Context`: contiene la raccolta di coppie nome-valore delle proprietà della traccia distribuita.

Lo standard definisce anche due schemi per la generazione di `Request-Id`: semplice e gerarchico. Con lo schema semplice, una chiave di `Id` nota viene definita per la raccolta `Correlation-Context`.

Application Insights definisce l'[estensione](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) per il protocollo HTTP per la correlazione. Usa le coppie nome-valore `Request-Context` per propagare la raccolta di proprietà usate dal chiamante o dal destinatario della chiamata. Application Insights SDK usa questa intestazione per impostare i campi `dependency.target` e `request.source`.

### <a name="w3c-distributed-tracing"></a>Analisi distribuita W3C

Microsoft sta eseguendo la transizione al [formato di analisi distribuita W3C](https://w3c.github.io/trace-context/), che definisce quanto segue:

- `traceparent`: contiene l'ID operazione globalmente univoco e un identificatore univoco della chiamata.
- `tracestate`: contiene il contesto specifico del sistema di analisi.

#### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app ASP.NET classiche

Questa funzionalità è disponibile nei pacchetti `Microsoft.ApplicationInsights.Web` e `Microsoft.ApplicationInsights.DependencyCollector` a partire dalla versione 2.8.0-beta1.
È disabilitata per impostazione predefinita. Per abilitarla, modificare `ApplicationInsights.config`:

- In `RequestTrackingTelemetryModule` aggiungere l'elemento `EnableW3CHeadersExtraction` con il valore impostato su `true`.
- In `DependencyTrackingTelemetryModule` aggiungere l'elemento `EnableW3CHeadersInjection` con il valore impostato su `true`.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app ASP.NET Core

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

#### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app Java

- **Configurazione in ingresso**

  - Per le app Java EE, aggiungere il codice seguente al tag `<TelemetryModules>` all'interno di ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Per le app Spring Boot, aggiungere le proprietà seguenti:

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
> Assicurarsi che entrambe le configurazioni in ingresso e in uscita siano identiche.

## <a name="opentracing-and-application-insights"></a>OpenTracing e Application Insights

La [specifica del modello di dati OpenTracing](https://opentracing.io/) e i modelli di dati di Application Insights eseguono il mapping nel modo seguente:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` con `span.kind = server`                  |
| `Dependency`                          | `Span` con `span.kind = client`                  |
| `Id` di `Request` e `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` di tipo `ChildOf` (l'intervallo padre)   |

Per altre informazioni, vedere [Modello di dati di Application Insights Telemetry](../../azure-monitor/app/data-model.md). 

Per le definizioni dei concetti di OpenTracing, vedere la [specifica](https://github.com/opentracing/specification/blob/master/specification.md) e le [convenzioni semantiche](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Correlazione di dati di telemetria in .NET

Il linguaggio .NET ha definito nel corso del tempo diversi modi per correlare i log di diagnostica e di telemetria:

- `System.Diagnostics.CorrelationManager` consente il monitoraggio di [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` ed Event Tracing for Windows (ETW) definiscono il metodo [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger` usa gli [ambiti dei log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) e HTTP creano la propagazione del contesto "corrente".

Queste soluzioni non consentono tuttavia il supporto automatico della traccia distribuita. `DiagnosticSource` rappresenta un modo per supportare la correlazione automatica tra computer. Le librerie .NET supportano "DiagnosticSource" e consentono la propagazione automatica tra computer del contesto di correlazione tramite il protocollo di trasporto, ad esempio HTTP.

La [guida alle attività](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) di `DiagnosticSource` fornisce le nozioni fondamentali delle attività di monitoraggio.

ASP.NET Core 2.0 supporta l'estrazione di intestazioni HTTP e l'avvio di una nuova attività.

`System.Net.HttpClient`, a partire dalla versione 4.1.0, supporta l'inserimento automatico delle intestazioni HTTP di correlazione e il monitoraggio della chiamata HTTP come attività.

È disponibile un nuovo modulo HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/), per la versione classica di ASP.NET. Questo modulo implementa correlazione di dati di telemetria usando `DiagnosticSource`. Avvia un'attività in base alle intestazioni di richiesta in ingresso. Correla anche i dati di telemetria dalle diverse fasi dell'elaborazione delle richieste, anche quando ogni fase dell'elaborazione di Internet Information Services (IIS) viene eseguita in un thread gestito diverso.

Application Insights SDK, a partire dalla versione 2.4.0-beta1, usa `DiagnosticSource` e `Activity` per raccogliere i dati di telemetria e associarli all'attività corrente.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlazione di dati di telemetria in Java SDK

[Application Insights SDK per Java](../../azure-monitor/app/java-get-started.md) supporta la correlazione automatica dei dati di telemetria a partire dalla versione 2.0.0. Popola automaticamente `operation_id` per tutti i dati di telemetria (ad esempio, tracce, eccezioni ed eventi personalizzati) rilasciati nell'ambito di una richiesta. Gestisce anche la propagazione delle intestazioni di correlazione (descritte in precedenza) per le chiamate da servizio a servizio tramite HTTP se l'[agente Java SDK](../../azure-monitor/app/java-agent.md) è configurato.

> [!NOTE]
> Per la funzionalità di correlazione sono supportate solo le chiamate effettuate tramite Apache HTTPClient. Se si usa Spring RestTemplate o Feign, entrambi possono essere usati con Apache HTTPClient in background.

La propagazione automatica del contesto attraverso tecnologie di messaggistica (ad esempio, Kafka, RabbitMQ o il bus di servizio di Azure) non è attualmente supportata. È tuttavia possibile scrivere manualmente il codice per tali scenari usando le API `trackDependency` e `trackRequest`. In queste API i dati di telemetria di una dipendenza rappresentano un messaggio che viene accodato da un producer e la richiesta rappresenta un messaggio che viene elaborato da un consumer. In questo caso sia `operation_id` che `operation_parentId` devono essere propagati nelle proprietà del messaggio.

<a name="java-role-name"></a>
## <a name="role-name"></a>Nome del ruolo

Potrebbe a volte essere necessario personalizzare il modo in cui i nomi dei componenti vengono visualizzati nella [mappa delle applicazioni](../../azure-monitor/app/app-map.md). A questo scopo, è possibile impostare manualmente `cloud_RoleName` in uno dei modi seguenti:

- Se si usa Spring Boot con l'utilità di avvio Spring Boot di Application Insights, l'unica modifica necessaria consiste nell'impostare il nome personalizzato per l'applicazione nel file application.properties.

  `spring.application.name=<name-of-app>`

  L'utilità di avvio Spring Boot assegna automaticamente `cloudRoleName` al valore immesso per la proprietà `spring.application.name`.

- Se si usa `WebRequestTrackingFilter`, `WebAppNameContextInitializer` imposta automaticamente il nome dell'applicazione. Aggiungere il testo seguente al file di configurazione (ApplicationInsights.xml):

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Se si usa la classe contesto cloud:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Passaggi successivi

- Scrivere [dati di telemetria personalizzati](../../azure-monitor/app/api-custom-events-metrics.md).
- Altre informazioni sull'[impostazione di cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloudrolename) per altri SDK.
- Caricare tutti i componenti del microservizio in Application Insights. Controllare le [piattaforme supportate](../../azure-monitor/app/platforms.md).
- Per informazioni sui tipi di Application Insights, vedere il [modello di dati](../../azure-monitor/app/data-model.md).
- Informazioni su come [estendere e filtrare i dati di telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- Vedere le [informazioni di riferimento sulla configurazione di Application Insights](configuration-with-applicationinsights-config.md).
