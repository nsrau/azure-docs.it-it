---
title: 'Metriche basate su log di Azure Application Insights : Documenti Microsoft'
description: Questo articolo elenca le metriche di Azure Application Insights con aggregazioni e dimensioni supportate. I dettagli sulle metriche basate su log includono le istruzioni di query Kusto sottostanti.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664916"
---
# <a name="application-insights-log-based-metrics"></a>Metriche basate sui log di Application Insights

Le metriche basate sui log di Application Insights consentono di analizzare l'integrità delle app monitorate, creare dashboard potenti e configurare avvisi. Esistono due tipi di metriche:

* [Le metriche basate su log dietro](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) la scena vengono convertite in query [Kusto](https://docs.microsoft.com/azure/kusto/query/) da eventi memorizzati.
* [Le metriche standard](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) vengono archiviate come serie temporali preaggregate.

Poiché *le metriche standard* sono preaggregate durante la raccolta, offrono prestazioni migliori in fase di query. Questo li rende una scelta migliore per il dashboarding e in tempo reale di avviso. Le *metriche basate su log* hanno più dimensioni, il che le rende l'opzione superiore per l'analisi dei dati e la diagnostica ad hoc. Utilizzare il [selettore dello spazio dei nomi](metrics-getting-started.md#create-your-first-metric-chart) per passare tra le metriche basate su log e le metriche standard in Esplora [metriche.](metrics-getting-started.md)

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretare e usare le query di questo articolo

Questo articolo elenca le metriche con aggregazioni e dimensioni supportate. I dettagli sulle metriche basate su log includono le istruzioni di query Kusto sottostanti. Per comodità, ogni query usa le impostazioni predefinite per la granularità temporale, il tipo di grafico e talvolta la dimensione di divisione che semplifica l'utilizzo della query in Log Analytics senza alcuna necessità di modifica.

Quando si traccia la stessa metrica in [Esplora metriche](metrics-getting-started.md), non sono presenti impostazioni predefinite: la query viene regolata dinamicamente in base alle impostazioni del grafico:

- **L'intervallo** di tempo selezionato viene convertito in una clausola *timestamp...* aggiuntiva per selezionare solo gli eventi dall'intervallo di tempo selezionato. Ad esempio, un grafico che mostra i dati per le 24 ore più recenti, la query include *il valore in cui timestamp > fa(24 h)*.

- La **granularità temporale** selezionata viene inserita nel riepilogo finale *... per clausola bin(timestamp, [grana temporale]).*

- Tutte le dimensioni **filtro** selezionate vengono convertite in clausole *where* aggiuntive.

- La dimensione **del grafico diviso** selezionata viene convertita in una proprietà di riepilogo aggiuntiva. Ad esempio, se si divide il grafico per *posizione*e si traccia utilizzando una granularità temporale di 5 minuti, la clausola *di riepilogo* viene riepilogata *... per bin(timestamp, 5 m), ubicazione*.

> [!NOTE]
> Se non si ha familiarità con il linguaggio di query Kusto, è possibile iniziare copiando e incollando le istruzioni Kusto nel riquadro della query di Log Analytics senza apportare modifiche. Fare clic su **Esegui** per visualizzare il grafico di base. Quando si inizia a comprendere la sintassi del linguaggio di query, è possibile iniziare ad apportare piccole modifiche e vedere l'impatto delle modifiche. Esplorare i propri dati è un ottimo modo per iniziare a realizzare tutta la potenza di [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) e Monitoraggio [di Azure.](../../azure-monitor/overview.md)

## <a name="availability-metrics"></a>Metriche di disponibilità

Le metriche nella categoria Disponibilità consentono di visualizzare l'integrità dell'applicazione Web osservata dai punti di tutto il mondo. [Configurare i test](../../azure-monitor/app/monitor-web-app-availability.md) di disponibilità per iniziare a usare qualsiasi metrica di questa categoria.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Disponibilità (availabilityResults/availabilityPercentage)
La metrica *Disponibilità* mostra la percentuale di esecuzioni di test Web che non hanno rilevato problemi. Il valore più basso possibile è 0, che indica che tutte le esecuzioni dei test Web non sono riuscite. Il valore 100 indica che tutte le esecuzioni dei test Web hanno superato i criteri di convalida.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|---|---|---|
|Percentuale|Media|Percorso di esecuzione, Nome test|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Durata del test di disponibilità (availabilityResults/duration)

La metrica *Durata test disponibilità* indica il tempo impiegato per l'esecuzione del test Web. Per i [test Web in più passaggi,](../../azure-monitor/app/availability-multistep.md)la metrica riflette il tempo di esecuzione totale di tutti i passaggi.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|---|---|---|
|Millisecondi|Media, Min, Max|Percorso di esecuzione, Nome test, Risultato del test

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Test di disponibilità (availabilityResults/count)

La metrica *Test di disponibilità* riflette il conteggio dei test Web eseguiti da Monitoraggio di Azure.The Availability tests metric reflects the count of the web tests runs by Azure Monitor.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|---|---|---|
|Conteggio|Conteggio|Percorso di esecuzione, Nome test, Risultato del test|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Metriche del browser

Le metriche del browser vengono raccolte da Application Insights JavaScript SDK da browser utente reali. Forniscono informazioni approfondite sull'esperienza degli utenti con l'app Web. Le metriche del browser in genere non vengono campionate, il che significa che forniscono una maggiore precisione dei numeri di utilizzo rispetto alle metriche lato server che potrebbero essere distorte dal campionamento.

> [!NOTE]
> Per raccogliere le metriche del browser, l'applicazione deve essere instrumentata con [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tempo di caricamento della pagina del browser (browserTimings/totalDuration)

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|
|---|---|---|
|Millisecondi|Media, Min, Max|nessuno|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Tempo di elaborazione client (browserTiming/processingDuration)

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|
|---|---|---|
|Millisecondi|Media, Min, Max|nessuno|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Tempo di connessione alla rete del caricamento pagina (browserTimings/networkDuration)

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|
|---|---|---|
|Millisecondi|Media, Min, Max|nessuno|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Ricezione del tempo di risposta (browserTimings/receiveDuration)

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|
|---|---|---|
|Millisecondi|Media, Min, Max|nessuno|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Invia tempo richiesta (browserTimings/sendDuration)

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|
|---|---|---|
|Millisecondi|Media, Min, Max|nessuno|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Metriche di errore

Le metriche in **Errori** mostrano problemi con l'elaborazione delle richieste, delle chiamate di dipendenza e delle eccezioni generate.

### <a name="browser-exceptions-exceptionsbrowser"></a>Eccezioni del browser (eccezioni/browser)

Questa metrica riflette il numero di eccezioni generate dal codice dell'applicazione in esecuzione nel browser. Solo le eccezioni rilevate ```trackException()``` con una chiamata all'API di Application Insights vengono incluse nella metrica.

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|nessuno|La versione basata su log utilizza l'aggregazione SumLog-based version uses **Sum** aggregation|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Errori delle chiamate di dipendenza (dipendenze/non riuscite)Dependency call failures (dependencies/failed)

Numero di chiamate di dipendenza non riuscite.

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|nessuno|La versione basata su log utilizza l'aggregazione SumLog-based version uses **Sum** aggregation|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Eccezioni (eccezioni/conteggio)

Ogni volta che si registra un'eccezione ad Application Insights, viene chiamata al [metodo trackException()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) dell'SDK. La metrica Eccezioni mostra il numero di eccezioni registrate.

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|Nome del ruolo cloud, Istanza del ruolo Cloud, Tipo di dispositivo|La versione basata su log utilizza l'aggregazione SumLog-based version uses **Sum** aggregation|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Richieste non riuscite (richieste/non riuscite)

Numero di richieste di server con rilevamento contrassegnate come *non riuscite.* Per impostazione predefinita, Application Insights SDK contrassegna automaticamente ogni richiesta del server che ha restituito il codice di risposta HTTP 5xx o 4xx come richiesta non riuscita. È possibile personalizzare questa logica modificando la proprietà *di esito* positivo dell'elemento di telemetria della richiesta in un [inizializzatore di telemetria personalizzato.](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|Istanza del ruolo cloud, nome del ruolo Cloud, Traffico reale o sintetico, Prestazioni richiesta, Codice risposta|La versione basata su log utilizza l'aggregazione SumLog-based version uses **Sum** aggregation|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Eccezioni server (eccezioni/server)

Questa metrica mostra il numero di eccezioni del server.

|Unità di misura|Aggregazioni supportate|Dimensioni preaggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|Nome del ruolo cloud, istanza del ruolo Cloud|La versione basata su log utilizza l'aggregazione SumLog-based version uses **Sum** aggregation|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Contatori delle prestazioni

Usare le metriche nella categoria **Contatori delle prestazioni** per accedere [ai contatori delle prestazioni](../../azure-monitor/app/performance-counters.md)del sistema raccolti da Application Insights .

### <a name="available-memory-performancecountersavailablememory"></a>Memoria disponibile (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Frequenza delle eccezioni (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Tempo di esecuzione della richiesta HTTP (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Frequenza richieste HTTP (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Richieste HTTP nella coda dell'applicazione (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU di processo (performanceCounters/processCpuPercentage)

La metrica mostra la quantità di capacità totale del processore utilizzata dal processo che ospita l'app monitorata.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Percentuale|Media, Min, Max|Istanza del ruolo del cloud

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Frequenza di I/O processo (performanceCounters/processIOBytesPerSecond)

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Byte al secondo|Media, Min, Max|Istanza del ruolo del cloud

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Elaborare byte privati (performanceCounters/processPrivateBytes)

Quantità di memoria non condivisa allocata dal processo monitorato per i dati.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Byte|Media, Min, Max|Istanza del ruolo del cloud

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Tempo processore (performanceCounters/processorCpuPercentage)

Utilizzo della CPU da parte di *tutti i* processi in esecuzione sull'istanza del server monitorato.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Percentuale|Media, Min, Max|Istanza del ruolo del cloud

>[!NOTE]
> The processor time metric is not available for the applications hosted in Azure App Services. Utilizzare la metrica [Elabora CPU](#process-cpu-performancecountersprocesscpupercentage) per tenere traccia dell'utilizzo della CPU delle applicazioni Web ospitate in Servizi app.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Metriche del server

### <a name="dependency-calls-dependenciescount"></a>Chiamate di dipendenza (dipendenze/conteggio)

Questa metrica è in relazione al numero di chiamate alle dipendenze.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Durata dipendenza (dipendenze/durata)

Questa metrica si riferisce alla durata delle chiamate alle dipendenze.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Richieste server (richieste/conteggio)

Questa metrica riflette il numero di richieste del server in ingresso ricevute dall'applicazione Web.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Tempo di risposta del server (richieste/durata)

Questa metrica riflette il tempo impiegato dai server per elaborare le richieste in ingresso.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Metriche di utilizzo

### <a name="page-view-load-time-pageviewsduration"></a>Tempo di caricamento della visualizzazione pagina (pageViews/duration)

Questa metrica fa riferimento alla quantità di tempo impiegato per il caricamento degli eventi PageView.This metric refers to the amount of time took for PageView events to load.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Visualizzazioni di pagina (pageViews/count)

Numero di eventi PageView registrati con l'API TrackPageView() Application Insights.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessioni (sessioni/conteggio)

Questa metrica si riferisce al conteggio di ID di sessione distinti.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Tracce (tracce/conteggio)

Numero di istruzioni di traccia registrate con la chiamata API TrackTrace() Application Insights.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Utenti (utenti/conteggio)

Numero di utenti distinti che hanno eseguito l'accesso all'applicazione. L'accuratezza di questa metrica può essere significativamente influenzata dall'utilizzo del filtro e del campionamento dei dati di telemetria.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Utenti, autenticati (utenti/autenticati)

Numero di utenti distinti che hanno eseguito l'autenticazione nell'applicazione.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
