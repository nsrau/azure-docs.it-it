---
title: Correlazione di dati di Application Insights Telemetry di Azure | Microsoft Docs
description: Correlazione di dati di Application Insights Telemetry
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/31/2018
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: 946d0895ff25509a0e35695fe27c783b3c50784b
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999578"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlazione di dati di telemetria in Application Insights

Nel mondo dei microservizi, ogni operazione logica richiede un lavoro sui vari componenti del servizio. Ognuno di questi componenti può essere monitorato separatamente tramite [Application Insights](../../application-insights/app-insights-overview.md). Il componente app Web comunica con il componente provider di autenticazione per convalidare le credenziali dell'utente e con il componente API per ottenere dati per la visualizzazione. Il componente API può a sua volta eseguire query sui dati di altri servizi, usare componenti del provider di cache e notificare questa richiesta al componente fatturazione. Application Insights supporta la correlazione di dati di telemetria distribuita. Consente di individuare quale componente è responsabile di errori o della riduzione delle prestazioni.

Questo articolo illustra il modello di dati usato da Application Insights per correlare i dati di telemetria inviati da più componenti. Illustra le tecniche di propagazione del contesto e i protocolli. Descrive inoltre l'implementazione dei concetti di correlazione in linguaggi e piattaforme diversi.

## <a name="telemetry-correlation-data-model"></a>Modello di dati per la correlazione di dati di telemetria

Application Insights definisce un [modello di dati](../../azure-monitor/app/data-model.md) per la correlazione di dati di telemetria distribuita. Per associare i dati di telemetria all'operazione logica, ogni elemento di telemetria dispone di un campo di contesto denominato `operation_Id`. Questo identificatore viene condiviso da ogni elemento di telemetria nella traccia distribuita. Se quindi si verifica una perdita di dati di telemetria da un singolo livello, è comunque possibile associare i dati di telemetria segnalati da altri componenti.

Un'operazione logica distribuita consiste in genere in un set di operazioni più piccole, vale a dire richieste elaborate da uno dei componenti. Tali operazioni sono definite dalla [telemetria delle richieste](../../azure-monitor/app/data-model-request-telemetry.md). Ogni telemetria delle richieste è dotata di un proprio `id` che la identifica in modo univoco a livello globale. Tutti i dati di telemetria, ad esempio tracce, eccezioni e così via, associati alla richiesta devono impostare `operation_parentId` sul valore di `id` della richiesta.

Ogni operazione in uscita (come ad esempio una richiesta HTTP a un altro componente) è rappresentata dalla [telemetria delle dipendenze](../../azure-monitor/app/data-model-dependency-telemetry.md). Anche la telemetria delle dipendenze dispone di un proprio `id` globalmente univoco. La telemetria delle richieste, avviata dalla chiamata di dipendenza, lo usa come `operation_parentId`.

È possibile creare la visualizzazione dell'operazione logica distribuita usando `operation_Id`, `operation_parentId` e `request.id` con `dependency.id`. Tali campi definiscono anche l'ordine della causalità delle chiamate di telemetria.

in un ambiente di microservizi, le tracce dai componenti possono finire in risorse di archiviazione diverse. Ogni componente può avere la propria chiave di strumentazione in Application Insights. Per ottenere i dati di telemetria per l'operazione logica, è necessario eseguire query sui dati da ogni risorsa di archiviazione. Quando il numero di risorse di archiviazione è enorme, è necessario avere un suggerimento sulla successiva posizione in cui cercare.

Per risolvere questo problema, nel modello di dati di Application Insights sono definiti due campi: `request.source` e `dependency.target`. Il primo campo definisce il componente che ha avviato la richiesta di dipendenza, mentre il secondo identifica quale componente ha restituito la risposta alla chiamata di dipendenza.


## <a name="example"></a>Esempio

Un'applicazione STOCK PRICES mostra il prezzo di mercato di un'azione usando l'API esterna denominata STOCKS API. L'applicazione STOCK PRICE dispone di una pagina `Stock page` aperta dal Web browser client tramite `GET /Home/Stock`. L'applicazione esegue una query a STOCK API mediante una chiamata HTTP `GET /api/stock/value`.

È possibile analizzare i dati di telemetria risultanti eseguendo una query:

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Nella visualizzazione dei risultati, tutti gli elementi di telemetria condividono l'elemento `operation_Id` radice. Quando la chiamata ajax viene eseguita dalla pagina, viene assegnato un nuovo ID univoco `qJSXU` alla telemetria delle dipendenze e l'ID di pageView viene usato come `operation_ParentId`. La richiesta del server usa a sua volta l'ID ajax come `operation_ParentId` e così via.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Pagina Stock                |              | STYz               | STYz         |
| dipendenza | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| richiesta    | GET /Home/Stock            | KqKwlrSt9PA = | qJSXU              | STYz         |
| dipendenza | GET /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Quando la chiamata `GET /api/stock/value` viene effettuata a un servizio esterno, si vuole conoscere l'identità del relativo server. È pertanto possibile impostare il campo `dependency.target` in modo appropriato. Quando il servizio esterno non supporta il monitoraggio, `target` è impostato sul nome host del servizio, ad esempio `stock-prices-api.com`. Se tuttavia il servizio identifica se stesso restituendo un'intestazione HTTP predefinita, `target` contiene l'identità del servizio che consente ad Application Insights di creare la traccia distribuita eseguendo query sui dati di telemetria di quel servizio. 

## <a name="correlation-headers"></a>Intestazioni di correlazione

Microsoft sta lavorando a una proposta RFC per il [protocollo HTTP per la correlazione](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Questa proposta definisce due intestazioni:

- `Request-Id` porta l'ID globalmente univoco della chiamata
- `Correlation-Context` porta la raccolta di coppie nome-valore delle proprietà della traccia distribuita

Lo standard definisce inoltre due schemi di generazione di `Request-Id`: semplice e gerarchico. Con lo schema semplice è presente una chiave di `Id` ben nota definita per la raccolta `Correlation-Context`.

Application Insights definisce l'[estensione](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) per il protocollo HTTP per la correlazione. Usa le coppie nome-valore `Request-Context` per propagare la raccolta di proprietà usate dal chiamante o dal destinatario della chiamata. Application Insights SDK usa questa intestazione per impostare i campi `dependency.target` e `request.source`.

### <a name="w3c-distributed-tracing"></a>Analisi distribuita W3C

Microsoft sta eseguendo la transizione a [formato di analisi distribuita W3C](https://w3c.github.io/trace-context/). che definisce quanto segue:
- `traceparent`: contiene l'ID operazione globalmente univoco e un identificatore univoco della chiamata
- `tracestate`: contiene il contesto specifico del sistema di analisi.

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-classic-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app ASP.NET classiche

Questa funzionalità è disponibile nei pacchetti Microsoft.ApplicationInsights.Web e Microsoft.ApplicationInsights.DependencyCollector a partire dalla versione 2.8.0-beta1.
È **disattivata** per impostazione predefinita; per abilitarla, modificare `ApplicationInsights.config`:

* in `RequestTrackingTelemetryModule` aggiungere l'elemento `EnableW3CHeadersExtraction` con il valore impostato su `true`
* in `DependencyTrackingTelemetryModule` aggiungere l'elemento `EnableW3CHeadersInjection` con il valore impostato su `true`

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Abilitare il supporto di analisi distribuita W3C per le app ASP.NET Core

Questa funzionalità è presente in Microsoft.ApplicationInsights.AspNetCore con la versione 2.5.0-beta1 e in Microsoft.ApplicationInsights.DependencyCollector versione 2.8.0-beta1.
È **disattivata** per impostazione predefinita; per abilitarla, impostare `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` su `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

## <a name="open-tracing-and-application-insights"></a>OpenTracing e Application Insights

La [specifica del modello di dati OpenTracing](https://opentracing.io/) e i modelli di dati di Application Insights eseguono il mapping nel modo seguente:

| Application Insights                  | OpenTracing                                      |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` con `span.kind = server`                  |
| `Dependency`                          | `Span` con `span.kind = client`                  |
| `Id` di `Request` e `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` di tipo `ChildOf` (l'intervallo padre)   |

Per altre informazioni sul modello di dati di Application Insights, vedere il [modello di dati](../../azure-monitor/app/data-model.md). 

Per le definizioni dei concetti di OpenTracing, vedere la [specifica](https://github.com/opentracing/specification/blob/master/specification.md) e le [convenzioni semantiche](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).

## <a name="telemetry-correlation-in-net"></a>Correlazione di dati di telemetria in .NET

Il linguaggio .NET ha definito nel corso del tempo numerosi modi per correlare i log di diagnostica e di telemetria. `System.Diagnostics.CorrelationManager` consente di tenere traccia di [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` ed Event Tracing for Windows definiscono il metodo [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` usa gli [ambiti dei log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF e Http collegano la propagazione del contesto "corrente".

Queste soluzioni non consentono tuttavia il supporto automatico della traccia distribuita. `DiagnosticsSource` rappresenta un modo per supportare la correlazione automatica tra computer. Le librerie .NET supportano DiagnosticsSource e consentono la propagazione automatica tra computer del contesto di correlazione tramite il protocollo di trasporto, ad esempio http.

La [guida alle attività](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) di DiagnosticsSource fornisce le nozioni fondamentali delle attività di monitoraggio. 

ASP.NET Core 2.0 supporta l'estrazione di intestazioni HTTP e l'avvio della nuova attività. 

`System.Net.HttpClient` a partire dalla versione `4.1.0` supporta l'inserimento automatico delle intestazioni Http di correlazione e il monitoraggio della chiamata http come attività.

È disponibile un nuovo modulo HTTP [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) per la versione classica di ASP.NET. Questo modulo implementa correlazione di dati di telemetria usando DiagnosticsSource. Avvia l'attività in base alle intestazioni di richiesta in ingresso. Correla inoltre i dati di telemetria delle diverse fasi di elaborazione della richiesta, anche per i casi in cui ogni fase dell'elaborazione di IIS viene eseguita in un thread di gestione diverso.

La versione iniziale di Application Insights SDK `2.4.0-beta1` usa DiagnosticsSource e la classe Activity per raccogliere i dati di telemetria e associarli all'attività corrente. 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlazione di dati di telemetria in Java SDK
[Application Insights Java SDK](../../azure-monitor/app/java-get-started.md) supporta la correlazione automatica dei dati di telemetria a partire dalla versione `2.0.0`. Popola automaticamente `operation_id` per tutti i dati di telemetria (tracce, eccezioni, eventi personalizzati e così via) rilasciati nell'ambito di una richiesta. Gestisce anche la propagazione delle intestazioni di correlazione (descritte sopra) per le chiamate da servizio a servizio tramite HTTP se l'[agente Java SDK](../../azure-monitor/app/java-agent.md) è configurato. Nota: per la funzionalità di correlazione sono supportate solo le chiamate effettuate tramite il client HTTP Apache. Se si usa Spring Rest Template o Feign, entrambi possono essere usati con il client HTTP Apache in background.

La propagazione automatica del contesto attraverso tecnologie di messaggistica (ad esempio, Kafka, RabbitMQ, bus di servizio di Azure) non è attualmente supportata. È tuttavia possibile scrivere manualmente il codice per tali scenari usando le API `trackDependency` e `trackRequest`, dove i dati di telemetria di una dipendenza rappresentano un messaggio che viene accodato da un producer e la richiesta rappresenta un messaggio che viene elaborato da un consumer. In questo caso sia `operation_id` che `operation_parentId` devono essere propagati nelle proprietà del messaggio.

<a name="java-role-name"></a>
## <a name="role-name"></a>Nome ruolo

Potrebbe a volte essere necessario personalizzare il modo in cui i nomi dei componenti vengono visualizzati nella [mappa delle applicazioni](../../azure-monitor/app/app-map.md). A questo scopo, è possibile impostare manualmente `cloud_RoleName` in uno dei modi seguenti:

Se si usa Spring Boot con l'utilità di avvio Spring Boot di Application Insights, l'unica modifica necessaria consiste nell'impostare il nome personalizzato per l'applicazione nel file application.properties.

`spring.application.name=<name-of-app>`

L'utilità di avvio Spring Boot assegnerà automaticamente cloudRoleName al valore immesso per la proprietà spring.application.name.

Se si usa `WebRequestTrackingFilter`, `WebAppNameContextInitializer` imposterà automaticamente il nome dell'applicazione. Aggiungere il testo seguente al file di configurazione (ApplicationInsights.xml):
```XML
<ContextInitializers>
  <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
</ContextInitializers>
```

Tramite la classe contesto cloud:

```Java
telemetryClient.getContext().getCloud().setRole("My Component Name");
```

## <a name="next-steps"></a>Passaggi successivi

- [Scrivere dati di telemetria personalizzati](../../azure-monitor/app/api-custom-events-metrics.md)
- [Altre informazioni su](../../azure-monitor/app/app-map.md#set-cloudrolename) impostazione di cloud_RoleName per altri SDK.
- Caricare tutti i componenti del microservizio in Application Insights. Controllare le [piattaforme supportate](../../azure-monitor/app/platforms.md).
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](../../azure-monitor/app/data-model.md).
- Informazioni su come [estendere e filtrare i dati di telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- [Application Insights config reference](configuration-with-applicationinsights-config.md) (Informazioni di riferimento sulla configurazione di Application Insights)

