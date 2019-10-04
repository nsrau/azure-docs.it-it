---
title: Metriche basate su log di applicazione Azure Insights | Microsoft Docs
description: Questo articolo elenca applicazione Azure metriche di Insights con le aggregazioni e le dimensioni supportate. Le informazioni dettagliate sulle metriche basate su log includono le istruzioni di query kusto sottostanti.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 903fd2309949036b62fb4975596fb645c021d06d
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535045"
---
# <a name="application-insights-log-based-metrics"></a>Metriche basate su log Application Insights

Application Insights metriche basate su log consentono di analizzare l'integrità delle app monitorate, creare dashboard potenti e configurare gli avvisi. Esistono due tipi di metriche:

* Le [metriche basate su log](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) dietro la scena vengono convertite in [query kusto](https://docs.microsoft.com/azure/kusto/query/) da eventi archiviati.
* Le [metriche standard](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) vengono archiviate come serie temporali pre-aggregate.

Poiché le *metriche standard* sono pre-aggregate durante la raccolta, hanno prestazioni migliori in fase di query. Questo li rende una scelta migliore per i dashboard e per gli avvisi in tempo reale. Le *metriche basate su log* hanno più dimensioni, in modo da renderle l'opzione superiore per l'analisi dei dati e la diagnostica ad hoc. Usare il [selettore dello spazio dei nomi](metrics-getting-started.md#create-your-first-metric-chart) per passare tra la metrica basata su log e la metrica standard in [Esplora metriche](metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretare e usare le query di questo articolo

Questo articolo elenca le metriche con le aggregazioni e le dimensioni supportate. Le informazioni dettagliate sulle metriche basate su log includono le istruzioni di query kusto sottostanti. Per praticità, ogni query utilizza le impostazioni predefinite per la granularità temporale, il tipo di grafico e talvolta la suddivisione della dimensione che semplifica l'utilizzo della query in Log Analytics senza alcuna necessità di modifica.

Quando si traccia la stessa metrica in [Esplora metriche](metrics-getting-started.md), non sono disponibili valori predefiniti. la query viene modificata in modo dinamico in base alle impostazioni del grafico:

- L' **intervallo di tempo** selezionato viene convertito in un'altra clausola *where timestamp...* per selezionare solo gli eventi dall'intervallo di tempo selezionato. Ad esempio, un grafico che mostra i dati per le ultime 24 ore, la query include *| where timestamp > ago (24 h)* .

- La **granularità temporale** selezionata viene inserita nel riepilogo finale *... per bin (timestamp, [Time Grain])* .

- Tutte le dimensioni di **filtro** selezionate vengono convertite in clausole *where* aggiuntive.

- La dimensione selezionata del **grafico suddiviso** viene convertita in una proprietà riepilogativa aggiuntiva. Se, ad esempio, si suddivide il grafico in base alla *posizione*e si esegue il tracciato utilizzando una granularità di 5 minuti, la clausola *riepilogativa* viene riepilogata *... per bin (timestamp, 5 m), location*.

> [!NOTE]
> Se non si ha familiarità con il linguaggio di query kusto, si inizia copiando e incollando istruzioni kusto nel riquadro Query Log Analytics senza apportare alcuna modifica. Fare clic su **Esegui** per visualizzare il grafico di base. Quando si inizia a comprendere la sintassi del linguaggio di query, è possibile iniziare a apportare piccole modifiche e vedere l'effetto della modifica. L'esplorazione dei propri dati è un ottimo modo per iniziare a realizzare tutta la potenza di [log Analytics](../../azure-monitor/log-query/get-started-portal.md) e [monitoraggio di Azure](../../azure-monitor/overview.md).

## <a name="availability-metrics"></a>Metriche di disponibilità

Le metriche nella categoria di disponibilità consentono di visualizzare l'integrità dell'applicazione Web come osservato dai punti di tutto il mondo. [Configurare i test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) per iniziare a usare qualsiasi metrica da questa categoria.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Disponibilità (availabilityResults/availabilityPercentage)
La metrica di *disponibilità* Mostra la percentuale delle esecuzioni dei test Web che non hanno rilevato alcun problema. Il valore più basso possibile è 0, che indica che tutte le esecuzioni dei test Web non sono riuscite. Il valore 100 indica che tutte le esecuzioni dei test Web hanno superato i criteri di convalida.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|---|---|---|
|Percentuale|Average|Percorso di esecuzione, nome del test|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Durata del test di disponibilità (availabilityResults/Duration)

La metrica *durata test di disponibilità* indica il tempo impiegato per l'esecuzione del test Web. Per i [test Web](../../azure-monitor/app/availability-multistep.md)in più passaggi, la metrica riflette il tempo di esecuzione totale di tutti i passaggi.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|---|---|---|
|Millisecondi|Media, min, max|Percorso di esecuzione, nome del test, risultato del test

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Test di disponibilità (availabilityResults/count)

La metrica dei *test di disponibilità* riflette il conteggio delle esecuzioni dei test Web da parte di monitoraggio di Azure.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|---|---|---|
|Conteggio|Conteggio|Percorso di esecuzione, nome del test, risultato del test|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Metriche del browser

Le metriche del browser vengono raccolte dal Application Insights JavaScript SDK dai browser degli utenti finali reali. Forniscono informazioni eccezionali sull'esperienza degli utenti con l'app Web. Le metriche del browser non vengono in genere campionate, il che significa che forniscono una maggiore precisione dei numeri di utilizzo rispetto alle metriche lato server, che potrebbero essere inclinate in base al campionamento.

> [!NOTE]
> Per raccogliere le metriche del browser, l'applicazione deve essere instrumentata con [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tempo di caricamento pagina del browser (browserTimings/totalDuration)

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|
|---|---|---|
|Millisecondi|Media, min, max|Nessuna|

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

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|
|---|---|---|
|Millisecondi|Media, min, max|Nessuna|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Tempo di connessione alla rete di caricamento della pagina (browserTimings/networkDuration)

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|
|---|---|---|
|Millisecondi|Media, min, max|Nessuna|

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

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|
|---|---|---|
|Millisecondi|Media, min, max|Nessuna|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Tempo di invio richiesta (browserTimings/sendDuration)

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|
|---|---|---|
|Millisecondi|Media, min, max|Nessuna|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Metriche errori

Le metriche negli **errori** presentano problemi relativi all'elaborazione delle richieste, alle chiamate alle dipendenze e alle eccezioni generate.

### <a name="browser-exceptions-exceptionsbrowser"></a>Eccezioni del browser (eccezioni/browser)

Questa metrica riflette il numero di eccezioni generate dal codice dell'applicazione in esecuzione nel browser. Nella metrica vengono incluse solo le eccezioni rilevate con una ```trackException()``` chiamata API Application Insights.

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|Nessuna|Per la versione basata su log viene utilizzata l'aggregazione **Sum**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Errori delle chiamate di dipendenza (dipendenze/non riuscite)

Numero di chiamate di dipendenza non riuscite.

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|Nessuna|Per la versione basata su log viene utilizzata l'aggregazione **Sum**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Eccezioni (eccezioni/conteggio)

Ogni volta che si registra un'eccezione a Application Insights, viene rilevata una chiamata al [Metodo trackexception ()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) dell'SDK. La metrica eccezioni Mostra il numero di eccezioni registrate.

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|Nome del ruolo Cloud, istanza del ruolo Cloud, tipo di dispositivo|Per la versione basata su log viene utilizzata l'aggregazione **Sum**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Richieste non riuscite (richieste/non riuscite)

Conteggio delle richieste del server rilevate contrassegnate come *non riuscite*. Per impostazione predefinita, il Application Insights SDK contrassegna automaticamente ogni richiesta del server che ha restituito il codice di risposta HTTP 5xx o 4xx come richiesta non riuscita. È possibile personalizzare questa logica modificando la proprietà *Success* dell'elemento telemetria della richiesta in un inizializzatore di telemetria [personalizzato](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer).

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|Istanza del ruolo Cloud, nome del ruolo Cloud, traffico reale o sintetico, prestazioni delle richieste, codice di risposta|Per la versione basata su log viene utilizzata l'aggregazione **Sum**|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Eccezioni del server (eccezioni/server)

Questa metrica indica il numero di eccezioni del server.

|Unità di misura|Aggregazioni supportate|Dimensioni pre-aggregate|Note|
|---|---|---|---|
|Conteggio|Conteggio|Nome del ruolo Cloud, istanza del ruolo Cloud|Per la versione basata su log viene utilizzata l'aggregazione **Sum**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Contatori delle prestazioni

Usare le metriche nella categoria **contatori delle prestazioni** per accedere ai [contatori delle prestazioni di sistema raccolti da Application Insights](../../azure-monitor/app/performance-counters.md).

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

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Frequenza delle richieste HTTP (performanceCounters/requestsPerSecond)

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

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU processo (performanceCounters/processCpuPercentage)

La metrica Mostra la quantità di capacità totale del processore utilizzata dal processo che ospita l'app monitorata.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Percentuale|Media, min, max|Istanza del ruolo cloud

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Velocità IO processo (performanceCounters/processIOBytesPerSecond)

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Byte al secondo|Media, min, max|Istanza del ruolo cloud

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Byte privati processo (performanceCounters/processPrivateBytes)

Quantità di memoria non condivisa allocata dal processo monitorato per i dati.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Byte|Media, min, max|Istanza del ruolo cloud

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Tempo processore (performanceCounters/processorCpuPercentage)

Utilizzo della CPU da parte di *tutti* i processi in esecuzione nell'istanza del server monitorata.

|Unità di misura|Aggregazioni supportate|Dimensioni supportate|
|---|---|---|
|Percentuale|Media, min, max|Istanza del ruolo cloud

>[!NOTE]
> La metrica del tempo del processore non è disponibile per le applicazioni ospitate nei servizi app Azure. Usare la metrica [Process CPU](#process-cpu-performancecountersprocesscpupercentage) per tenere traccia dell'utilizzo della CPU delle applicazioni Web ospitate nei servizi app.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Metriche del server

### <a name="dependency-calls-dependenciescount"></a>Chiamate di dipendenza (dipendenze/conteggio)

Questa metrica è correlata al numero di chiamate alle dipendenze.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Durata delle dipendenze (dipendenze/durata)

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

Questa metrica riflette il numero di richieste server in ingresso ricevute dall'applicazione Web.

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

### <a name="page-view-load-time-pageviewsduration"></a>Tempo di caricamento della visualizzazione pagina (pagine visualizzate/durata)

Questa metrica si riferisce alla quantità di tempo impiegato per il caricamento degli eventi di visualizzazione pagina.

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

### <a name="page-views-pageviewscount"></a>Visualizzazioni pagina (pagine di visualizzazione/conteggio)

Il numero di eventi di visualizzazione della pagina registrati con l'API Application Insights TrackPageView ().

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessioni (sessioni/conteggio)

Questa metrica si riferisce al numero di ID di sessione distinti.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Tracce (tracce/conteggio)

Numero di istruzioni di traccia registrate con la chiamata API TrackTrace () Application Insights.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Utenti (utenti/conteggi)

Numero di utenti distinti che hanno eseguito l'accesso all'applicazione. L'accuratezza della metrica potrebbe avere un notevole effetto sull'uso del campionamento e del filtro di telemetria.

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
