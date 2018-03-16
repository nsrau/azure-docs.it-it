---
title: Correlazione di dati di Application Insights Telemetry di Azure | Microsoft Docs
description: Correlazione di dati di Application Insights Telemetry
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: 5d4abbf8194d633305877275e3dd273352906ad3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlazione di dati di telemetria in Application Insights

Nel mondo dei microservizi, ogni operazione logica richiede un lavoro sui vari componenti del servizio. Ognuno di questi componenti può essere monitorato separatamente tramite [Application Insights](app-insights-overview.md). Il componente app Web comunica con il componente provider di autenticazione per convalidare le credenziali dell'utente e con il componente API per ottenere dati per la visualizzazione. Il componente API può a sua volta eseguire query sui dati di altri servizi, usare componenti del provider di cache e notificare questa richiesta al componente fatturazione. Application Insights supporta la correlazione di dati di telemetria distribuita. Consente di individuare quale componente è responsabile di errori o della riduzione delle prestazioni.

Questo articolo illustra il modello di dati usato da Application Insights per correlare i dati di telemetria inviati da più componenti. Illustra le tecniche di propagazione del contesto e i protocolli. Descrive inoltre l'implementazione dei concetti di correlazione in linguaggi e piattaforme diversi.

## <a name="telemetry-correlation-data-model"></a>Modello di dati per la correlazione di dati di telemetria

Application Insights definisce un [modello di dati](application-insights-data-model.md) per la correlazione di dati di telemetria distribuita. Per associare i dati di telemetria all'operazione logica, ogni elemento di telemetria dispone di un campo di contesto denominato `operation_Id`. Questo identificatore viene condiviso da ogni elemento di telemetria nella traccia distribuita. Se quindi si verifica una perdita di dati di telemetria da un singolo livello, è comunque possibile associare i dati di telemetria segnalati da altri componenti.

Un'operazione logica distribuita consiste in genere in un set di operazioni più piccole, vale a dire richieste elaborate da uno dei componenti. Tali operazioni sono definite dalla [telemetria delle richieste](application-insights-data-model-request-telemetry.md). Ogni telemetria delle richieste è dotata di un proprio `id` che la identifica in modo univoco a livello globale. Tutti i dati di telemetria, ad esempio tracce, eccezioni e così via, associati alla richiesta devono impostare `operation_parentId` sul valore di `id` della richiesta.

Ogni operazione in uscita come richiesta HTTP a un altro componente è rappresentata dalla [telemetria delle dipendenze](application-insights-data-model-dependency-telemetry.md). Anche la telemetria delle dipendenze dispone di un proprio `id` globalmente univoco. La telemetria delle richieste, avviata dalla chiamata di dipendenza, lo usa come `operation_parentId`.

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

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Pagina Stock                |              | STYz               | STYz         |
| dipendenza | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| richiesta    | GET /Home/Stock            | KqKwlrSt9PA = | qJSXU              | STYz         |
| dipendenza | GET /api/stock/value      | bBrf2L7mm2g = | KqKwlrSt9PA =       | STYz         |

Quando la chiamata `GET /api/stock/value` viene effettuata a un servizio esterno, si vuole conoscere l'identità del relativo server. È pertanto possibile impostare il campo `dependency.target` in modo appropriato. Quando il servizio esterno non supporta il monitoraggio, `target` è impostato sul nome host del servizio, ad esempio `stock-prices-api.com`. Se tuttavia il servizio identifica se stesso restituendo un'intestazione HTTP predefinita, `target` contiene l'identità del servizio che consente ad Application Insights di creare la traccia distribuita eseguendo query sui dati di telemetria di quel servizio. 

## <a name="correlation-headers"></a>Intestazioni di correlazione

Microsoft sta lavorando alla proposta RFC per il [protocollo HTTP per la correlazione](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v1.md). Questa proposta definisce due intestazioni:

- `Request-Id` porta l'ID globalmente univoco della chiamata
- `Correlation-Context` porta la raccolta di coppie nome-valore delle proprietà della traccia distribuita

Lo standard definisce inoltre due schemi di generazione di `Request-Id`: semplice e gerarchico. Con lo schema semplice è presente una chiave di `Id` ben nota definita per la raccolta `Correlation-Context`.

Application Insights definisce l'[estensione](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) per il protocollo HTTP per la correlazione. Usa le coppie nome-valore `Request-Context` per propagare la raccolta di proprietà usate dal chiamante o dal destinatario della chiamata. Application Insights SDK usa questa intestazione per impostare i campi `dependency.target` e `request.source`.

## <a name="open-tracing-and-application-insights"></a>OpenTracing e Application Insights

Ecco come si presentano i modelli di dati di [OpenTracing](http://opentracing.io/) e Application Insights: 

- `request`, `pageView` esegue il mapping a **Span** con `span.kind = server`
- `dependency` esegue il mapping a **Span** con `span.kind = client`
- `id` di una `request` e `dependency` esegue il mapping a **Span.Id**
- `operation_Id` esegue il mapping a **TraceId**
- `operation_ParentId` esegue il mapping a **Reference** di tipo `ChildOf`

Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](application-insights-data-model.md).

Per le definizioni di OpenTracing, vedere a [specifica](https://github.com/opentracing/specification/blob/master/specification.md) e le [convenzioni semantiche](https://github.com/opentracing/specification/blob/master/semantic_conventions.md).


## <a name="telemetry-correlation-in-net"></a>Correlazione di dati di telemetria in .NET

Il linguaggio .NET ha definito nel corso del tempo numerosi modi per correlare i log di diagnostica e di telemetria. `System.Diagnostics.CorrelationManager` consente di tenere traccia di [LogicalOperationStack e ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). `System.Diagnostics.Tracing.EventSource` ed Event Tracing for Windows definiscono il metodo [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx). `ILogger` usa gli [ambiti dei log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). WCF e Http collegano la propagazione del contesto "corrente".

Queste soluzioni non consentono tuttavia il supporto automatico della traccia distribuita. `DiagnosticsSource` rappresenta un modo per supportare la correlazione automatica tra computer. Le librerie .NET supportano DiagnosticsSource e consentono la propagazione automatica tra computer del contesto di correlazione tramite il protocollo di trasporto, ad esempio http.

La [guida alle attività](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) di DiagnosticsSource fornisce le nozioni fondamentali delle attività di monitoraggio. 

ASP.NET Core 2.0 supporta l'estrazione di intestazioni HTTP e l'avvio della nuova attività. 

`System.Net.HttpClient` a partire dalla versione `4.1.0` supporta l'inserimento automatico delle intestazioni Http di correlazione e il monitoraggio della chiamata http come attività.

È disponibile un nuovo modulo HTTP [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) per la versione classica di ASP.NET. Questo modulo implementa correlazione di dati di telemetria usando DiagnosticsSource. Avvia l'attività in base alle intestazioni di richiesta in ingresso. Correla inoltre i dati di telemetria delle diverse fasi di elaborazione della richiesta, anche per i casi in cui ogni fase dell'elaborazione di IIS viene eseguita in un thread di gestione diverso.

La versione iniziale di Application Insights SDK `2.4.0-beta1` usa DiagnosticsSource e la classe Activity per raccogliere i dati di telemetria e associarli all'attività corrente. 

## <a name="next-steps"></a>Passaggi successivi

- [Scrivere dati di telemetria personalizzati](app-insights-api-custom-events-metrics.md)
- Caricare tutti i componenti del microservizio in Application Insights. Controllare le [piattaforme supportate](app-insights-platforms.md).
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](application-insights-data-model.md).
- Informazioni su come [estendere e filtrare i dati di telemetria](app-insights-api-filtering-sampling.md).
