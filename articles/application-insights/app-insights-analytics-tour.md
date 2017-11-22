---
title: Presentazione di Analisi in Azure Application Insights | Microsoft Docs
description: Brevi esempi di tutte le principali query in Analisi, lo strumento di ricerca avanzato di Application Insights.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/06/2017
ms.author: mbullwin
ms.openlocfilehash: a33fedd765acde666eef280ba7dfa72536bf1bd2
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Presentazione dello strumento Analisi in Application Insights
L'[analisi](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Log Analytics.

* **[Guardare il video introduttivo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Eseguire la versione di test di Analisi sui dati simulati](https://analytics.applicationinsights.io/demo)** se l'app non invia ancora i dati ad Application Insights.
* Il **[foglio informativo sugli utenti SQL](https://aka.ms/sql-analytics)** traduce i linguaggi più comuni.

Di seguito sono descritte alcune query di base utili per iniziare.

## <a name="connect-to-your-application-insights-data"></a>Connettersi ai dati di Application Insights
Aprire Analisi dal [pannello Panoramica](app-insights-dashboards.md) dell'app in Application Insights:

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsioquerylanguagequerylanguagetakeoperatorhtml-show-me-n-rows"></a>[Take](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html): visualizzare n righe
I punti dati che registrano le operazioni degli utenti (in genere richieste HTTP ricevute dall'app Web) vengono archiviati in una tabella denominata `requests`. Ogni riga è un punto dati di telemetria ricevuto da Application Insights SDK nell'applicazione.

Si inizierà ora a esaminare alcune righe di esempio della tabella:

![results](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Inserire il cursore in un punto nell'istruzione prima fare clic su Vai. È possibile suddividere un'istruzione su più righe, ma non inserire righe vuote in un'istruzione. Le righe vuote sono un modo pratico per mantenere più query separate nella finestra.
>
>

Scegliere le colonne, trascinarle, creare gruppi in base alle colonne e applicare filtri:

![Fare clic sulla selezione di colonna in alto a destra dei risultati](./media/app-insights-analytics-tour/030.png)

Espandere un elemento per visualizzare i dettagli:

![Scegliere Tabella e usare Configura colonne](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Fare clic sull'intestazione di una colonna per riordinare i risultati disponibili nel Web browser. Tuttavia, tenere presente che per un set di risultati di grandi dimensioni, il numero di righe scaricate nel browser è limitato. Questa modalità di ordinamento si limita a ordinare il set di risultati restituiti e non sempre illustra gli elementi effettivi massimi o minimi. Per ordinarli in modo affidabile, usare l'operatore `top` o `sort`.
>
>

## <a name="query-across-applications"></a>Eseguire una query tra applicazioni
Se si vogliono combinare dati da più applicazioni di Application Insights, usare la parola chiave **app** per specificare l'applicazione con il nome della tabella.  Questa query combina le richieste da due diverse applicazioni usando il comando **union**.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsioquerylanguagequerylanguagetopoperatorhtml-and-sorthttpsdocsloganalyticsioquerylanguagequerylanguagesortoperatorhtml"></a>[Top](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) e [sort](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)
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

Le intestazioni di colonna nella visualizzazione tabella possono essere usate anche per ordinare i risultati sullo schermo. Naturalmente, se è stato usato `take` o `top` per recuperare solo parte di una tabella, fare clic sull'intestazione di colonna per riordinare solo i record recuperati.

## <a name="wherehttpsdocsloganalyticsioquerylanguagequerylanguagewhereoperatorhtml-filtering-on-a-condition"></a>[Where](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html): filtrare in base a una condizione

Di seguito sono illustrate solo le richieste che hanno restituito un particolare codice di risultato:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

L'operatore `where` accetta un'espressione booleana. Tenere presente i punti chiave seguenti:

* `and`, `or`: operatori booleani
* `==`, `<>`, `!=`: uguale a e non uguale a
* `=~`, `!~`: stringa senza distinzione tra maiuscole e minuscole uguale a e non uguale a. Sono disponibili numerosi altri operatori di confronto delle stringhe.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Individuare le richieste non riuscite

Convertire un valore di stringa in un numero intero per usare il confronto basato sul criterio "maggiore di":

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Tempo

Per impostazione predefinita, le query sono limitate alle ultime 24 ore. Questo intervallo, però, può essere modificato:

![](./media/app-insights-analytics-tour/change-time-range.png)

Eseguire l'override dell'intervallo di tempo scrivendo una query che includa `timestamp` in una clausola where. ad esempio:

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

La funzionalità di intervallo di tempo è equivalente a una clausola 'where' inserita dopo ogni menzione di una delle tabelle di origine.

`ago(3d)` significa 'tre giorni fa'. Le altre unità di tempo includono ore (`2h`, `2.5h`), minuti (`25m`) e secondi (`10s`).

Altri esempi:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[Riferimento su date e ore](https://docs.loganalytics.io/concepts/concepts_datatypes_datetime.html).


## <a name="projecthttpsdocsloganalyticsioquerylanguagequerylanguageprojectoperatorhtml-select-rename-and-compute-columns"></a>[Project](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html): selezionare, rinominare e calcolare le colonne
Usare [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) per selezionare solo le colonne desiderate:

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

* I nomi di colonna possono includere spazi o simboli se sono racchiusi tra parentesi quadre, ad esempio: `['...']` o `["..."]`
* `%` è il consueto operatore modulo.
* `1d` (la cifra uno seguita da "d") è il valore letterale di un intervallo di tempo che indica un giorno. Ecco altri valori letterali di intervallo di tempo: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) arrotonda un valore per difetto al multiplo più vicino del valore di base specificato. `floor(aTime, 1s)` arrotonda un'ora per difetto al secondo più vicino.

Le espressioni possono includere tutti gli operatori consueti (`+`, `-`, ...) ed è disponibile una gamma di funzioni utili.

## <a name="extend"></a>Extend
Per aggiungere colonne a quelle già esistenti, usare [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Se si vogliono mantenere tutte le colonne esistenti, [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html) è meno dettagliato di [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html).

### <a name="convert-to-local-time"></a>Convertire nell'ora locale

I timestamp sono sempre espressi in formato UTC. Pertanto, se ci si trova sulla costa del Pacifico ed è inverno, potrebbe essere utile quanto riportato di seguito:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizehttpsdocsloganalyticsioquerylanguagequerylanguagesummarizeoperatorhtml-aggregate-groups-of-rows"></a>[Summarize](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html): aggregare gruppi di righe
`Summarize` applica una *funzione di aggregazione* specificata a gruppi di righe.

Ad esempio, il tempo che l'app Web impiega per rispondere a una richiesta viene indicato nel campo `duration`. Verrà visualizzato il tempo medio di risposta a tutte le richieste:

![](./media/app-insights-analytics-tour/410.png)

È anche possibile separare il risultato in richieste con nomi diversi:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` raccoglie i punti dati del flusso in gruppi che la clausola `by` valuta in ugual misura. Ogni valore nell'espressione `by`, ovvero ogni nome di operazione univoco nell'esempio precedente, restituisce una riga nella tabella dei risultati.

È anche possibile raggruppare i risultati per ora del giorno:

![](./media/app-insights-analytics-tour/430.png)

Si noti che si sta usando la funzione `bin`, detta anche `floor`. Se si usasse solo `by timestamp`, ogni riga di input finirebbe in un piccolo gruppo distinto. Per i valori scalari continui, come le ore o i numeri, è necessario suddividere l'intervallo continuo in un numero gestibile di valori discreti. Il modo più semplice per eseguire questa operazione è usare `bin`, che è in realtà la nota funzione `floor` per l'arrotondamento per difetto.

È possibile usare la stessa tecnica per ridurre gli intervalli di stringhe:

![](./media/app-insights-analytics-tour/440.png)

Si noti che è possibile usare `name=` per impostare il nome di una colonna di risultati, nelle espressioni di aggregazione o nella clausola by.

## <a name="counting-sampled-data"></a>Conteggio dei dati campionati
`sum(itemCount)` è l'aggregazione consigliata per contare gli eventi. In molti casi, itemCount==1, quindi la funzione somma semplicemente il numero di righe nel gruppo. Ma, quando nell'operazione è previsto un [campionamento](app-insights-sampling.md), solo una frazione degli eventi originali viene conservata come punti dati in Application Insights, in modo che siano presenti eventi in numero pari a `itemCount` per ogni punto dati visualizzato.

Ad esempio, se il campionamento elimina il 75% degli eventi originali, allora itemCount==4 nei record mantenuti; ciò significa che per ogni record mantenuto erano presenti quattro record originali.

Il campionamento adattivo causa un valore itemCount più elevato durante i periodi in cui l'applicazione viene utilizzata di frequente.

Il riepilogo di itemCount offre quindi una stima valida del numero di eventi originale.

![](./media/app-insights-analytics-tour/510.png)

È disponibile anche un'aggregazione `count()` , oltre a un'operazione di conteggio, per i casi in cui si vuole effettivamente contare il numero di righe in un gruppo.

Esiste un intervallo di [funzioni di aggregazione](https://docs.loganalytics.io/learn/tutorials/aggregations.html).

## <a name="charting-the-results"></a>Disegnare i risultati
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

Per impostazione predefinita, i risultati vengono visualizzati sotto forma di tabella:

![](./media/app-insights-analytics-tour/225.png)

È possibile ottenere un risultato migliore della visualizzazione tabella. Si osservino i risultati nella visualizzazione grafico con l'opzione delle barre verticali:

![Fare clic su Grafico, quindi scegliere Grafico a barre verticali e assegnare gli assi x e y](./media/app-insights-analytics-tour/230.png)

Si noti che anche se i risultati non sono stati ordinati in base all'ora (come è possibile osservare nella visualizzazione tabella), la visualizzazione grafico mostra sempre i dati data/ora nell'ordine corretto.


## <a name="timecharts"></a>Grafici di tempo
Visualizzare il numero di eventi presenti in ogni ora:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Selezionare l'opzione di visualizzazione grafico:

![timechart](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Serie multiple
La presenza di più espressioni nella clausola `summarize` determina la creazione di più colonne.

La presenza di più espressioni nella clausola `by` determina la creazione di più righe, una per ogni combinazione di valori.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tabella delle richieste in base a ora e località](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Segmentare un grafico in base alle dimensioni
Se si crea un grafico per una tabella contenente una colonna di stringhe e una colonna numerica, la stringa può essere usata per suddividere i dati numerici in serie separate di punti. Se è presente più di una colonna di stringhe, è possibile scegliere la colonna da usare come discriminatore.

![Segmentare un grafico di analisi](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Frequenza di mancati recapiti

Convertire un valore booleano in una stringa per usarlo come discriminatore:

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Visualizzare più metriche
Se si crea un grafico per una tabella contenente più di una colonna numerica, oltre al timestamp, è possibile visualizzare qualsiasi combinazione di colonne.

![Segmentare un grafico di analisi](./media/app-insights-analytics-tour/110.png)

È necessario selezionare **Don't Split** (Non dividere) prima di poter selezionare più colonne numeriche. Non è possibile dividere in base a una colonna di stringhe e allo stesso tempo visualizzare più di una colonna numerica.

## <a name="daily-average-cycle"></a>Ciclo della media giornaliera
Come varia l'uso nella giornata media?

Conteggiare le richieste per un modulo di un giorno, suddivise in ore:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Grafico a linee delle ore di un giorno medio](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Si noti che attualmente è necessario convertire le durate in valori datetime per visualizzarli in un grafico a linee.
>
>

## <a name="compare-multiple-daily-series"></a>Confrontare più serie giornaliere
Come varia l'uso nelle ore del giorno nei diversi paesi?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![Suddivisione in base a client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Tracciare una distribuzione
Quante sessioni esistono di lunghezze diverse?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

L'ultima riga serve per convertire in formato datetime. Attualmente l'asse x di un grafico viene visualizzato come valore scalare solo se è un valore datetime.

La clausola `where` esclude le sessioni monofase (sessionDuration==0) e imposta la lunghezza dell'asse x.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsioquerylanguagequerylanguagepercentilesaggfunctionhtml"></a>[Percentili](https://docs.loganalytics.io/queryLanguage/query_language_percentiles_aggfunction.html)
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

Viene anche rimosso il limite superiore nella clausola where in modo da ottenere dati corretti che includono tutte le sessioni con più di una richiesta:

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

## <a name="join"></a>Join
È possibile accedere a più tabelle, incluse le richieste e le eccezioni.

Per trovare le eccezioni correlate a una richiesta che ha restituito una risposta di errore, è possibile creare un join delle tabelle in `session_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


È buona norma usare `project` per selezionare solo le colonne necessarie prima di eseguire il join.
Nelle stesse clausole, si rinomina la colonna timestamp.

## <a name="lethttpsdocsloganalyticsioquerylanguagequerylanguageletstatementhtml-assign-a-result-to-a-variable"></a>[Let](https://docs.loganalytics.io/queryLanguage/query_language_letstatement.html): assegnare un risultato a una variabile

Usare `let` per separare le parti dell'espressione precedente. I risultati rimangono invariati:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> Nel client Analisi non inserire righe vuote tra le parti della query. Verificare che vengano eseguiti tutti gli elementi.
>

Usare `toscalar` per convertire una cella di tabella in un valore:

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Funzioni

Usare *Let* per definire una funzione:

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Accesso a oggetti annidati
È facile accedere agli oggetti annidati. Nel flusso exceptions, ad esempio, sono visibili oggetti strutturati come questo:

![risultato](./media/app-insights-analytics-tour/520.png)

È possibile renderlo flat scegliendo le proprietà che interessano:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

È necessario eseguire il cast del risultato per il tipo appropriato.


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

Per verificare se una dimensione personalizzata è di tipo specifico:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

## <a name="dashboards"></a>Dashboard
È possibile aggiungere i risultati a un dashboard in modo da riunire tutti i grafici e le tabelle più importanti.

* [Dashoboard condiviso di Azure](app-insights-dashboards.md#share-dashboards): fare clic sull'icona di aggiunta. Per poterlo fare, è necessario disporre di un dashboard condiviso. Nel portale di Azure, aprire o creare un dashboard e fare clic su Condividi.
* [Dashboard di Power BI](app-insights-export-power-bi.md): fare clic su Esporta, Power BI Query (Query Power BI). Un vantaggio di questa alternativa consiste nel fatto che è possibile visualizzare la query insieme ad altri risultati da un'ampia gamma di origini.

## <a name="combine-with-imported-data"></a>Combinare con dati importati

I report di Analisi hanno un ottimo aspetto nel dashboard, tuttavia a volte si desidera convertire i dati in un formato più semplice. Si supponga, ad esempio, che gli utenti autenticati siano identificati nei dati di telemetria da un alias. Si desidera visualizzarne i nomi reali nei risultati. A tale scopo, è necessario un file CSV con il mapping dagli alias ai nomi reali.

È possibile importare un file di dati e usarlo come qualsiasi tabella standard (richieste, eccezioni e così via). Eseguire una query sulla propria tabella oppure aggiungerla ad altre tabelle. Ad esempio, se si dispone di una tabella denominata usermap contenente le colonne `realName` e `userId`, è possibile usarla per convertire il campo `user_AuthenticatedId` nei dati di telemetria della richiesta:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Per importare in una tabella, nel pannello Schema, in **Altre origini dati**, seguire le istruzioni per aggiungere una nuova origine dati, caricando un campione dei dati. È quindi possibile usare questa definizione per caricare le tabelle.

La funzionalità di importazione è attualmente in anteprima e sarà inizialmente visualizzato un collegamento "Contattaci" in "Altre origini dati." Usare questo collegamento per iscriversi al programma di anteprima. Il collegamento verrà puoi sostituito da un pulsante "Aggiungi nuova origine dati".


## <a name="tables"></a>Tabelle
Il flusso di dati di telemetria ricevuto dall'app è accessibile tramite più tabelle. Lo schema delle proprietà disponibili per ogni tabella è situato nella parte sinistra della finestra.

### <a name="requests-table"></a>Tabella di richieste
Contare le richieste HTTP all'applicazione Web e il segmento in base al nome della pagina:

![Richieste di conteggio segmentate per nome](./media/app-insights-analytics-tour/analytics-count-requests.png)

Trovare le richieste con il maggior numero di esiti negativi:

![Richieste di conteggio segmentate per nome](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Tabella di eventi personalizzati
Se si usa [Trackevent()](app-insights-api-custom-events-metrics.md#trackevent) per inviare eventi personalizzati, è possibile leggerli da questa tabella.

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
Se si usa [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) per inviare i valori metrici, i risultati sono disponibili nel flusso **customMetrics**. Ad esempio:  

![Metriche personalizzate in Application Insights - Analisi](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> In [Esplora metriche](app-insights-metrics-explorer.md) tutte le misurazioni personalizzate associate a qualsiasi tipo di telemetria vengono visualizzate insieme nel pannello delle metriche, con le metriche inviate usando `TrackMetric()`. In Analisi, invece, le misure personalizzate sono ancora associate al tipo di telemetria su cui sono state rilevate, come eventi, richieste e così via, mentre le metriche inviate da TrackMetric vengono visualizzate all'interno del proprio flusso.
>
>

### <a name="performance-counters-table"></a>Tabella dei contatori delle prestazioni
[I contatori delle prestazioni](app-insights-performance-counters.md) mostrano le metriche base del sistema per l'applicazione, ad esempio CPU, memoria e uso della rete. È possibile configurare l'SDK per inviare contatori aggiuntivi, tra cui contatori personalizzati.

Lo schema **performanceCounters** espone `category`, il nome `counter` e il nome `instance` per ogni contatore delle prestazioni. I nomi delle istanze di contatore sono applicabili solo ad alcuni contatori delle prestazioni e indicano in genere il nome del processo a cui il conteggio si riferisce. Nei dati di telemetria per ogni applicazione verranno visualizzati solo i contatori per l'applicazione specifica. Ad esempio, per visualizzare quali contatori sono disponibili:

![Contatori delle prestazioni in Application Insights - Analisi](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Per ottenere un grafico della memoria disponibile nel periodo selezionato:

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

### <a name="availability-results-table"></a>Tabella dei risultati di disponibilità
`availabilityResults` mostra i risultati dei [test Web](app-insights-monitor-web-app-availability.md). Ogni esecuzione di test da ogni posizione di test viene segnalata separatamente.

![Tempistiche di caricamento delle pagine in Analisi](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabella delle dipendenze
Contiene i risultati delle chiamate che l'applicazione esegue sul database e sull'API REST e le chiamate a TrackDependency(). Include inoltre le chiamate AJAX effettuate dal browser.

Chiamate AJAX dal browser:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Chiamate di dipendenza dal server:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

I risultati della dipendenza lato server mostrano sempre `success==False` se l'agente Application Insights non è installato. Tuttavia, gli altri dati sono corretti.

### <a name="traces-table"></a>Tabella delle tracce
Contiene i dati di telemetria inviati dall'app tramite TrackTrace() o [altri framework di registrazione](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Video 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Query avanzate:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>Passaggi successivi
* [Informazioni di riferimento sul linguaggio di Analisi](app-insights-analytics-reference.md)
* Il [foglio informativo sugli utenti SQL](https://aka.ms/sql-analytics) traduce i linguaggi più comuni.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
