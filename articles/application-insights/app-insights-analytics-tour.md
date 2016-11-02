<properties 
    pageTitle="Demo sull'analisi in Application Insights | Microsoft Azure" 
    description="Brevi esempi di tutte le principali query in Analisi, lo strumento di ricerca avanzato di Application Insights." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="awills"/>


 

# <a name="a-tour-of-analytics-in-application-insights"></a>Presentazione dello strumento Analisi in Application Insights


L'[analisi](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analisi.


* **[Guardare il video introduttivo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Eseguire la versione di test di Analisi sui dati simulati](https://analytics.applicationinsights.io/demo)** se l'app non invia ancora i dati ad Application Insights.


Di seguito sono descritte alcune query di base utili per iniziare.

## <a name="connect-to-your-application-insights-data"></a>Connettersi ai dati di Application Insights

Aprire Analisi dal [pannello Panoramica](app-insights-dashboards.md) dell'app in Application Insights:

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-insights-analytics-tour/001.png)

    
## <a name="[take](app-insights-analytics-reference.md#take-operator):-show-me-n-rows"></a>[Take](app-insights-analytics-reference.md#take-operator): visualizzare n righe

I punti dati che registrano le operazioni degli utenti (in genere richieste HTTP ricevute dall'app Web) vengono archiviati in una tabella denominata `requests`. Ogni riga è un punto dati di telemetria ricevuto da Application Insights SDK nell'applicazione.

Si inizierà ora a esaminare alcune righe di esempio della tabella:

![results](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] Inserire il cursore in un punto nell'istruzione prima fare clic su Vai. È possibile suddividere un'istruzione su più righe, ma non inserire righe vuote in un'istruzione. Le righe vuote sono un modo pratico per mantenere più query separate nella finestra.


Scegliere le colonne e modificarne le posizioni:

![Fare clic sulla selezione di colonna in alto a destra dei risultati](./media/app-insights-analytics-tour/030.png)


Espandere un elemento per visualizzare i dettagli:
 
![Scegliere Tabella e utsare Configura colonne](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] Fare clic sull'intestazione di una colonna per riordinare i risultati disponibili nel Web browser. Tuttavia, tenere presente che per un set di risultati di grandi dimensioni, il numero di righe scaricate nel browser è limitato. Tenere presente che questa modalità di ordinamento non sempre illustra gli elementi effettivi massimi o minimi. A tale scopo, usare l'operatore `top` o `sort`. 

## <a name="[top](app-insights-analytics-reference.md#top-operator)-and-[sort](app-insights-analytics-reference.md#sort-operator)"></a>[Top](app-insights-analytics-reference.md#top-operator) e [sort](app-insights-analytics-reference.md#sort-operator)

`take` è utile per ottenere un rapido esempio di un risultato, ma mostra le righe della tabella senza un ordine particolare. Per ottenere una visualizzazione ordinata, usare `top` per un campione o `sort` per l'intera tabella.

Mostrare le prime n righe, ordinate in base a una colonna specifica:

```AIQL

    requests | top 10 by timestamp desc 
```

* *Sintassi:* la maggior parte degli operatori ha parametri di tipo parola chiave, ad esempio `by`.
* `desc` = ordine decrescente, `asc` = ordine crescente.

![](./media/app-insights-analytics-tour/260.png)

`top...` è più efficiente di `sort ... | take...`. Si sarebbe potuto scrivere:

```AIQL

    requests | sort by timestamp desc | take 10
```

Il risultato sarebbe stato lo stesso, ma l'esecuzione sarebbe risultata più lenta. Sarebbe stato anche possibile scrivere `order`, che è un alias di `sort`.

Le intestazioni di colonna nella visualizzazione tabella possono essere usate anche per ordinare i risultati sullo schermo. Naturalmente, se è stato usato `take` o `top` per recuperare solo parte di una tabella, verranno riordinati solo i record recuperati.


## <a name="[project](app-insights-analytics-reference.md#project-operator):-select,-rename-and-compute-columns"></a>[Project](app-insights-analytics-reference.md#project-operator): selezionare, rinominare e calcolare le colonne

Usare [`project`](app-insights-analytics-reference.md#project-operator) per selezionare solo le colonne desiderate:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)


È inoltre possibile rinominare le colonne e definire nuove colonne:

```AIQL

    requests 
  	| top 10 by timestamp desc 
  	| project  
            name, 
            response = resultCode,
            timestamp, 
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![risultato](./media/app-insights-analytics-tour/270.png)

* I [nomi di colonna](app-insights-analytics-reference.md#names) possono includere spazi o simboli se sono racchiusi tra parentesi quadre, ad esempio: `['...']` o `["..."]`
* `%` è il consueto operatore modulo. 
* `1d` (la cifra uno seguita da "d") è il valore letterale di un intervallo di tempo che indica un giorno. Ecco altri valori letterali di intervallo di tempo: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) arrotonda un valore per difetto al multiplo più vicino del valore di base specificato. `floor(aTime, 1s)` arrotonda un'ora per difetto al secondo più vicino.

Le [espressioni](app-insights-analytics-reference.md#scalars) possono includere tutti gli operatori consueti (`+`, `-`, ...) ed è disponibile una gamma di funzioni utili.

    

## <a name="[extend](app-insights-analytics-reference.md#extend-operator):-compute-columns"></a>[Extend](app-insights-analytics-reference.md#extend-operator): calcolare le colonne

Per aggiungere colonne a quelle già esistenti, usare [`extend`](app-insights-analytics-reference.md#extend-operator):

```AIQL

    requests 
  	| top 10 by timestamp desc
  	| extend timeOfDay = floor(timestamp % 1d, 1s)
```

Se si vogliono mantenere tutte le colonne esistenti, [`extend`](app-insights-analytics-reference.md#extend-operator) è meno dettagliato di [`project`](app-insights-analytics-reference.md#project-operator).


## <a name="[summarize](app-insights-analytics-reference.md#summarize-operator):-aggregate-groups-of-rows"></a>[Summarize](app-insights-analytics-reference.md#summarize-operator): aggregare gruppi di righe

`Summarize` applica una *funzione di aggregazione* specificata a gruppi di righe. 

Ad esempio, il tempo che l'app Web impiega per rispondere a una richiesta viene indicato nel campo `duration`. Verrà visualizzato il tempo medio di risposta a tutte le richieste:

![](./media/app-insights-analytics-tour/410.png)

È anche possibile separare il risultato in richieste con nomi diversi:


![](./media/app-insights-analytics-tour/420.png)

`Summarize` raccoglie i punti dati del flusso in gruppi che la clausola `by` valuta in ugual misura. Ogni valore nell'espressione `by` , ovvero ogni nome di operazione nell'esempio precedente, restituisce una riga nella tabella dei risultati. 

È anche possibile raggruppare i risultati per ora del giorno:

![](./media/app-insights-analytics-tour/430.png)

Si noti che si sta usando la funzione `bin`, detta anche `floor`. Se si usasse solo `by timestamp`, ogni riga di input finirebbe in un piccolo gruppo distinto. Per i valori scalari continui, come le ore o i numeri, è necessario suddividere l'intervallo continuo in un numero gestibile di valori discreti. Il modo più semplice per eseguire questa operazione è usare `bin`, che è in realtà la nota funzione `floor` per l'arrotondamento per difetto.

È possibile usare la stessa tecnica per ridurre gli intervalli di stringhe:


![](./media/app-insights-analytics-tour/440.png)

Si noti che è possibile usare `name=` per impostare il nome di una colonna di risultati, nelle espressioni di aggregazione o nella clausola by.

## <a name="counting-sampled-data"></a>Conteggio dei dati campionati

`sum(itemCount)` è l'aggregazione consigliata per contare gli eventi. In molti casi, itemCount==1, quindi la funzione somma semplicemente il numero di righe nel gruppo. Ma, quando nell'operazione è previsto un [campionamento](app-insights-sampling.md), solo una frazione degli eventi originali verrà conservata come punto dati in Application Insights, in modo che siano presenti eventi in numero pari a `itemCount` per ogni punto dati visualizzato. 

Ad esempio, se il campionamento elimina il 75% degli eventi originali, allora itemCount==4 nei record mantenuti; ciò significa che per ogni record mantenuto erano presenti quattro record originali. 

Il campionamento adattivo causa un valore itemCount più elevato durante i periodi in cui l'applicazione viene utilizzata di frequente.

Il riepilogo di itemCount offre quindi una stima valida del numero di eventi originale.


![](./media/app-insights-analytics-tour/510.png)

È disponibile anche un'aggregazione `count()` , oltre a un'operazione di conteggio, per i casi in cui si vuole effettivamente contare il numero di righe in un gruppo.


Esiste un intervallo di [funzioni di aggregazione](app-insights-analytics-reference.md#aggregations).


## <a name="charting-the-results"></a>Disegnare i risultati


```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

Per impostazione predefinita, i risultati vengono visualizzati sotto forma di tabella:

![](./media/app-insights-analytics-tour/225.png)


È possibile ottenere un risultato migliore della visualizzazione tabella. Si osservino i risultati nella visualizzazione grafico con l'opzione delle barre verticali:

![Fare clic su Grafico, quindi scegliere Grafico a barre verticali e assegnare gli assi x e y](./media/app-insights-analytics-tour/230.png)

Si noti che anche se i risultati non sono stati ordinati in base all'ora (come è possibile osservare nella visualizzazione tabella), la visualizzazione grafico mostra sempre i dati data/ora nell'ordine corretto.


## <a name="[where](app-insights-analytics-reference.md#where-operator):-filtering-on-a-condition"></a>[Where](app-insights-analytics-reference.md#where-operator): filtrare in base a una condizione

Se Application Insights è configurato per il monitoraggio sia del lato [client](app-insights-javascript.md) che del lato server dell'app, alcuni dati di telemetria del database provengono dai browser.

Si osservino solo le eccezioni riportate dai browser:

```AIQL

    exceptions 
  	| where device_Id == "browser" 
  	|  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-insights-analytics-tour/250.png)

L'operatore `where` accetta un'espressione booleana. Tenere presente i punti chiave seguenti:

 * `and`, `or`: operatori booleani
 * `==`, `<>`: uguale a e non uguale a
 * `=~`, `!=`: stringa senza distinzione tra maiuscole e minuscole uguale a e non uguale a. Sono disponibili numerosi altri operatori di confronto delle stringhe.

Informazioni dettagliate sulle [espressioni scalari](app-insights-analytics-reference.md#scalars).

### <a name="filtering-events"></a>Filtro degli eventi

Individuare le richieste non riuscite:

```AIQL

    requests 
  	| where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` è di tipo stringa, quindi per un confronto numerico è necessario [eseguire il cast](app-insights-analytics-reference.md#casts) .

Riepilogare le diverse risposte:

```AIQL

    requests
  	| where isnotempty(resultCode) and toint(resultCode) >= 400
  	| summarize count() 
      by resultCode
```

## <a name="timecharts"></a>Grafici di tempo

Visualizzare il numero di eventi presenti in ogni giorno:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Selezionare l'opzione di visualizzazione grafico:

![timechart](./media/app-insights-analytics-tour/080.png)

L'asse x per i grafici a linee deve essere di tipo DateTime. 

## <a name="multiple-series"></a>Serie multiple 

Usare più valori in una clausola `summarize by` per creare una riga distinta per ogni combinazione di valori:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

Per visualizzare più righe in un grafico, fare clic su **Dividi** e selezionare una colonna.

![](./media/app-insights-analytics-tour/100.png)



## <a name="daily-average-cycle"></a>Ciclo della media giornaliera

Come varia l'uso nella giornata media?

Conteggiare le richieste per un modulo di un giorno, suddivise in ore:

```AIQL

    requests
  	| extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
  	| summarize event_count=count() by hour
```

![Grafico a linee delle ore di un giorno medio](./media/app-insights-analytics-tour/120.png)

>[AZURE.NOTE] Si noti che è necessario convertire le durate in valori data/ora per visualizzarli in un grafico.


## <a name="compare-multiple-daily-series"></a>Confrontare più serie giornaliere

Come varia l'uso nelle ore del giorno nei diversi stati?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Suddividere il grafico per stato:

![Suddividere per client_StateOrProvince](./media/app-insights-analytics-tour/130.png)


## <a name="plot-a-distribution"></a>Tracciare una distribuzione

Quante sessioni esistono di lunghezze diverse?

```AIQL

    requests 
  	| where isnotnull(session_Id) and isnotempty(session_Id) 
  	| summarize min(timestamp), max(timestamp) 
      by session_Id 
  	| extend sessionDuration = max_timestamp - min_timestamp 
  	| where sessionDuration > 1s and sessionDuration < 3m 
  	| summarize count() by floor(sessionDuration, 3s) 
  	| project d = sessionDuration + datetime("2016-01-01"), count_
```

L'ultima riga è necessaria per la conversione in datetime: l'asse x di un grafico a linee può essere solo un valore datetime.

La clausola `where` esclude le sessioni monofase (sessionDuration==0) e imposta la lunghezza dell'asse x.


![](./media/app-insights-analytics-tour/290.png)



## <a name="[percentiles](app-insights-analytics-reference.md#percentiles)"></a>[Percentili](app-insights-analytics-reference.md#percentiles)

Quali intervalli di durate coprono le diverse percentuali delle sessioni?

Usare la query precedente, ma sostituire l'ultima riga:

```AIQL

    requests 
  	| where isnotnull(session_Id) and isnotempty(session_Id) 
  	| summarize min(timestamp), max(timestamp) 
      by session_Id 
  	| extend sesh = max_timestamp - min_timestamp 
  	| where sesh > 1s
  	| summarize count() by floor(sesh, 3s) 
  	| summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Viene inoltre rimosso il limite superiore nella clausola where in modo da ottenere dati corretti che includono tutte le sessioni con più di una richiesta:

![risultato](./media/app-insights-analytics-tour/180.png)

È possibile osservare che:

* Il 5% delle sessioni ha una durata inferiore a 3 minuti e 34 secondi; 
* Il 50% delle sessioni dura meno di 36 minuti;
* Il 5% delle sessioni dura più di 7 giorni

Per ottenere una suddivisione separata per ogni paese, è sufficiente visualizzare la colonna client_CountryOrRegion separatamente attraverso entrambi gli operatori summarize:

```AIQL

    requests 
  	| where isnotnull(session_Id) and isnotempty(session_Id) 
  	| summarize min(timestamp), max(timestamp) 
      by session_Id, client_CountryOrRegion
  	| extend sesh = max_timestamp - min_timestamp 
  	| where sesh > 1s
  	| summarize count() by floor(sesh, 3s), client_CountryOrRegion
  	| summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)


## <a name="[join](app-insights-analytics-reference.md#join)"></a>[Join](app-insights-analytics-reference.md#join)

È possibile accedere a più tabelle, incluse le richieste e le eccezioni.

Per trovare le eccezioni correlate a una richiesta che ha restituito una risposta di errore, è possibile creare un join delle tabelle in `session_Id`:

```AIQL

    requests 
  	| where toint(responseCode) >= 500 
  	| join (exceptions) on operation_Id 
  	| take 30
```


È buona norma usare `project` per selezionare solo le colonne necessarie prima di eseguire il join.
Nelle stesse clausole, si rinomina la colonna timestamp.



## <a name="[let](app-insights-analytics-reference.md#let-clause):-assign-a-result-to-a-variable"></a>[Let](app-insights-analytics-reference.md#let-clause): assegnare un risultato a una variabile

Usare [let](./app-insights-analytics-reference.md#let-statements) per separare le parti dell'espressione precedente. I risultati rimangono invariati:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
  	| join (exceptions) on session_Id 
  	| take 30
```

> Suggerimento: nel client Analisi non inserire righe vuote tra le parti di questo oggetto. Verificare che vengano eseguiti tutti gli elementi.


## <a name="accessing-nested-objects"></a>Accesso a oggetti annidati

È facile accedere agli oggetti annidati. Nel flusso exceptions, ad esempio, sono visibili oggetti strutturati come questo:

![risultato](./media/app-insights-analytics-tour/520.png)

È possibile renderlo flat scegliendo le proprietà che interessano:

```AIQL

    exceptions | take 10
  	| extend method1 = tostring(details[0].parsedStack[1].method)
```

Si noti che è necessario usare un [cast](app-insights-analytics-reference.md#casts) nel tipo appropriato.

## <a name="custom-properties-and-measurements"></a>Proprietà e misure personalizzate

Se l'applicazione associa agli eventi [dimensioni (proprietà) e misure personalizzate](app-insights-api-custom-events-metrics.md#properties), queste saranno visibili negli oggetti `customDimensions` e `customMeasurements`.


Ad esempio, se l'applicazione include:

```C#

    var dimensions = new Dictionary<string, string> 
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Per estrarre questi valori in Dati di analisi:

```AIQL

    customEvents
  	| extend p1 = customDimensions.p1, 
      m1 = todouble(customMeasurements.m1) // cast to expected type

``` 

## <a name="tables"></a>Tables

Il flusso di dati di telemetria ricevuto dall'app è accessibile tramite più tabelle. Lo schema delle proprietà disponibili per ogni tabella è situato nella parte sinistra della finestra.

### <a name="requests-table"></a>Tabella di richieste

Contare le richieste HTTP all'applicazione Web e il segmento in base al nome della pagina:

![Richieste di conteggio segmentate per nome](./media/app-insights-analytics-tour/analytics-count-requests.png)

Trovare le richieste con il maggior numero di esiti negativi:

![Richieste di conteggio segmentate per nome](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Tabella di eventi personalizzati

Se si usa [Trackevent()](app-insights-api-custom-events-metrics.md#track-event) per inviare eventi personalizzati, è possibile leggerli da questa tabella. 

Esaminiamo un esempio in cui il codice dell'app contiene le righe seguenti:

```C#

    telemetry.TrackEvent("Query", 
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Visualizzare la frequenza di questi eventi:
 
![Frequenza di visualizzazione degli eventi personalizzati](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Estrarre le misure e le dimensioni degli eventi:

![Frequenza di visualizzazione degli eventi personalizzati](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Tabella delle metriche personalizzate

Se si usa [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) per inviare i valori metrici, i risultati sono disponibili nel flusso **customMetrics**. Ad esempio:  

![Metriche personalizzate in Application Insights - Analisi](./media/app-insights-analytics-tour/analytics-custom-metrics.png)


> [AZURE.NOTE] In [Esplora metriche](app-insights-metrics-explorer.md) tutte le misurazioni personalizzate associate a qualsiasi tipo di telemetria vengono visualizzate insieme nel pannello delle metriche, con le metriche inviate usando `TrackMetric()`. In Analisi, invece, le misure personalizzate sono ancora associate al tipo di telemetria su cui sono state rilevate, come eventi, richieste e così via, mentre le metriche inviate da TrackMetric vengono visualizzate all'interno del proprio flusso.

### <a name="performance-counters-table"></a>Tabella dei contatori delle prestazioni

[I contatori delle prestazioni](app-insights-web-monitor-performance.md#system-performance-counters) mostrano le metriche base del sistema per l'applicazione, ad esempio CPU, memoria e uso della rete. È possibile configurare l'SDK per inviare contatori aggiuntivi, tra cui contatori personalizzati.

Lo schema **performanceCounters** espone `category`, il nome `counter` e il nome `instance` per ogni contatore delle prestazioni. I nomi delle istanze di contatore sono applicabili solo ad alcuni contatori delle prestazioni e indicano in genere il nome del processo a cui il conteggio si riferisce. Nei dati di telemetria per ogni applicazione verranno visualizzati solo i contatori per l'applicazione specifica. Ad esempio, per visualizzare quali contatori sono disponibili: 

![Contatori delle prestazioni in Application Insights - Analisi](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Per ottenere un grafico della memoria disponibile nel periodo recente: 

![Timechart delle metriche in Application Insights - Analisi](./media/app-insights-analytics-tour/analytics-available-memory.png)


Come altri dati di telemetria, **performanceCounters** contiene anche una colonna `cloud_RoleInstance` che indica l'identità del computer host in cui è in esecuzione l'app. Ad esempio, per confrontare le prestazioni dell'applicazione su computer diversi: 


![Prestazioni segmentate per istanze del ruolo in Application Insights - Analisi](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Tabelle delle eccezioni

[Le eccezioni segnalate dall'app](app-insights-asp-net-exceptions.md) sono disponibili in questa tabella. 

Per trovare la richiesta HTTP che l'app gestiva al momento del rilevamento dell'eccezione, creare un join in operation_Id:

![Creare un join delle eccezioni con le richieste in operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)


### <a name="browser-timings-table"></a>Tabella delle tempistiche del browser

`browserTimings` mostra i dati di caricamento della pagina raccolti nel browser degli utenti.

[Configurare l'app per la telemetria sul lato client](app-insights-javascript.md) per visualizzare queste metriche. 

Lo schema include [metriche che indicano la lunghezza delle varie fasi del processo di caricamento della pagina](app-insights-javascript.md#page-load-performance). Le metriche non indicano il tempo trascorso dagli utenti a leggere una pagina.  

Le metriche mostrano la popolarità delle diverse pagine e i tempi di caricamento di ogni pagina:

![Tempistiche di caricamento delle pagine in Analisi](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availbility-results-table"></a>Tabella dei risultati di disponibilità

`availabilityResults` mostra i risultati dei [test Web](app-insights-monitor-web-app-availability.md). Ogni esecuzione di test da ogni posizione di test viene segnalata separatamente. 


![Tempistiche di caricamento delle pagine in Analisi](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabella delle dipendenze

Contiene i risultati delle chiamate che l'applicazione esegue sul database e sull'API REST e le chiamate a TrackDependency().

### <a name="traces-table"></a>Tabella delle tracce

Contiene i dati di telemetria inviati dall'app tramite TrackTrace() o [altri framework di registrazione](app-insights-asp-net-trace-logs.md).

## <a name="try-it!"></a>Prova

* **[Eseguire la versione di test di Analisi sui dati simulati](https://analytics.applicationinsights.io/demo)** se l'app non invia ancora i dati ad Application Insights.


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]





<!--HONumber=Oct16_HO2-->


