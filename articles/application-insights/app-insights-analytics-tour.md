<properties 
	pageTitle="Presentazione dello strumento Analisi in Application Insights" 
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
	ms.date="05/04/2016" 
	ms.author="awills"/>


 
# Presentazione dello strumento Analisi in Application Insights


[Analytics](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analisi.


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]
 
Di seguito sono descritte alcune query di base utili per iniziare.

## Connettersi ai dati di Application Insights

Aprire Analisi dal [pannello Panoramica](app-insights-dashboards.md) dell'app in Application Insights:

![In portal.azure.com, aprire la risorsa di Application Insights e selezionare Analytics.](./media/app-insights-analytics-tour/001.png)

	
## [Take](app-insights-analytics-reference.md#take-operator): visualizzare n righe

I punti dati che registrano le operazioni degli utenti (in genere richieste HTTP ricevute dall'app Web) vengono archiviati in una tabella denominata `requests`. Ogni riga è un punto dati di telemetria ricevuto da Application Insights SDK nell'applicazione.

Si inizierà ora a esaminare alcune righe di esempio della tabella:

![results](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] Inserire il cursore in un punto nell'istruzione prima fare clic su Vai. È possibile suddividere un'istruzione su più righe, ma non inserire righe vuote in un'istruzione. Le righe vuote sono un modo pratico per mantenere più query separate nella finestra.


Scegliere le colonne e modificarne le posizioni:

![Fare clic sulla selezione di colonna in alto a destra dei risultati](./media/app-insights-analytics-tour/030.png)


Espandere un elemento per visualizzare i dettagli:
 
![Scegliere Tabella e utsare Configura colonne](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] Fare clic sull'intestazione di una colonna per riordinare i risultati disponibili nel Web browser. Tuttavia, tenere presente che per un set di risultati di grandi dimensioni, il numero di righe scaricate nel browser è limitato. Tenere presente che questa modalità di ordinamento non sempre illustra gli elementi effettivi massimi o minimi. A tale scopo, usare l'operatore `top` o `sort`.

## [Top](app-insights-analytics-reference.md#top-operator) e [sort](app-insights-analytics-reference.md#sort-operator)

`take` è utile per ottenere un rapido esempio di un risultato, ma mostra le righe della tabella senza un ordine particolare. Per ottenere una visualizzazione ordinata, usare `top` per un esempio o `sort` per l'intera tabella.

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


## [Project](app-insights-analytics-reference.md#project-operator): selezionare, rinominare e calcolare le colonne

Usare [`project`](app-insights-analytics-reference.md#project-operator) per selezionare solo le colonne volute:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)


È inoltre possibile rinominare le colonne e definire nuove colonne:

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![risultato](./media/app-insights-analytics-tour/270.png)

Nell'espressione scalare:

* `%` è il consueto operatore modulo. 
* `1d` (la cifra uno seguita da "d") è il valore letterale di un intervallo di tempo che indica un giorno. Ecco altri valori letterali di intervallo di tempo: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) arrotonda un valore per difetto al multiplo più vicino del valore di base specificato. `floor(aTime, 1s)` arrotonda un'ora per difetto al secondo più vicino.

Le [espressioni](app-insights-analytics-reference.md#scalars) possono includere tutti gli operatori consueti (`+`, `-`, ...) ed è disponibile una gamma di funzioni utili.

    

## [Extend](app-insights-analytics-reference.md#extend-operator): calcolare le colonne

Per aggiungere colonne a quelle esistenti, usare [`extend`](app-insights-analytics-reference.md#extend-operator):

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Se si vogliono mantenere tutte le colonne esistenti, [`extend`](app-insights-analytics-reference.md#extend-operator) è meno dettagliato di [`project`](app-insights-analytics-reference.md#project-operator).


## Accesso a oggetti annidati

È facile accedere agli oggetti annidati. Nel flusso exceptions, ad esempio, sono visibili oggetti strutturati come questo:

![risultato](./media/app-insights-analytics-tour/520.png)

È possibile renderlo flat scegliendo le proprietà che interessano:

```AIQL

    exceptions | take 10
    | extend method1 = details[0].parsedStack[1].method
```

## Proprietà e misure personalizzate

Se l'applicazione associa a eventi [dimensioni (proprietà) personalizzate e misure personalizzate](app-insights-api-custom-events-metrics.md#properties), queste saranno visibili negli oggetti `customDimensions` e `customMeasurements`.


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
      m1 = todouble(customMeasurements.m1) // cast numerics

``` 

> [AZURE.NOTE] In [Esplora metriche](app-insights-metrics-explorer.md) tutte le misurazioni personalizzate associate a qualsiasi tipo di telemetria vengono visualizzate insieme nel pannello delle metriche, con le metriche inviate usando `TrackMetric()`. In Dati di analisi, invece, le misure personalizzate sono ancora associate al tipo di telemetria su cui sono state rilevate e le metriche vengono visualizzate all'interno del proprio flusso `metrics`.


## [Summarize](app-insights-analytics-reference.md#summarize-operator): aggregare gruppi di righe

`Summarize` applica una *funzione di aggregazione* specificata a gruppi di righe.

Ad esempio, il tempo che l'app Web impiega per rispondere a una richiesta viene indicato nel campo `duration`. Verrà visualizzato il tempo medio di risposta a tutte le richieste:

![](./media/app-insights-analytics-tour/410.png)

È anche possibile separare il risultato in richieste con nomi diversi:


![](./media/app-insights-analytics-tour/420.png)

`Summarize` raccoglie i punti dati del flusso in gruppi che la clausola `by` valuta in ugual misura. Ogni valore nell'espressione `by` (ogni nome di operazione nell'esempio precedente) restituisce una riga nella tabella dei risultati.

È anche possibile raggruppare i risultati per ora del giorno:

![](./media/app-insights-analytics-tour/430.png)

Si noti che si sta usando la funzione `bin` (detta anche `floor`). Se si usasse solo `by timestamp`, ogni riga di input finirebbe in un piccolo gruppo distinto. Per i valori scalari continui, ad esempio ore o numeri, è necessario suddividere l'intervallo continuo in un numero gestibile di valori discreti. Il modo più semplice per eseguire questa operazione è tramite `bin`, che è in realtà la nota funzione `floor` per l'arrotondamento per difetto.

È possibile usare la stessa tecnica per ridurre gli intervalli di stringhe:


![](./media/app-insights-analytics-tour/440.png)

Si noti che è possibile usare `name=` per impostare il nome di una colonna di risultati, nelle espressioni di aggregazione o nella clausola by.

## Conteggio dei dati campionati

`sum(itemCount)` è l'aggregazione consigliata per contare gli eventi. In molti casi, itemCount==1, quindi la funzione somma semplicemente il numero di righe nel gruppo. Ma quando nell'operazione è previsto un [campionamento](app-insights-sampling.md), solo una frazione degli eventi originali verrà conservata come punto dati in Application Insights, in modo che siano presenti eventi `itemCount` per ogni punto dati visualizzato. Il riepilogo di itemCount offre quindi una stima valida del numero di eventi originale.


![](./media/app-insights-analytics-tour/510.png)

È disponibile anche un'aggregazione `count()`, oltre a un'operazione di conteggio, per i casi in cui si vuole effettivamente contare il numero di righe in un gruppo.


Esiste un intervallo di [funzioni di aggregazione](app-insights-analytics-reference.md#aggregations).


## Disegnare i risultati



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


## [Where](app-insights-analytics-reference.md#where-operator): filtro in base a una condizione

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
 * `=~`, `!=`: stringa con distinzione maiuscole/minuscole uguale a e non uguale a. Sono disponibili numerosi altri operatori di confronto delle stringhe.

Informazioni dettagliate sulle [espressioni scalari](app-insights-analytics-reference.md#scalars).

### Filtro degli eventi

Individuare le richieste non riuscite:

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` è di tipo stringa, quindi per un confronto numerico è necessario [eseguire il cast](app-insights-analytics-reference.md#casts).

Riepilogare le diverse risposte:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## Grafici di tempo

Visualizzare il numero di eventi presenti in ogni giorno:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Selezionare l'opzione di visualizzazione grafico:

![timechart](./media/app-insights-analytics-tour/080.png)

L'asse x per i grafici a linee deve essere di tipo DateTime.

## Serie multiple 

Usare più valori in una clausola `summarize by` per creare una riga distinta per ogni combinazione di valori:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

Per visualizzare più righe in un grafico, fare clic su **Split by** e selezionare una colonna.

![](./media/app-insights-analytics-tour/100.png)



## Ciclo della media giornaliera

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


## Confrontare più serie giornaliere

Come varia l'uso nelle ore del giorno nei diversi stati?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Suddividere il grafico per stato:

![Suddividere per client\_StateOrProvince](./media/app-insights-analytics-tour/130.png)


## Tracciare una distribuzione

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



## [Percentili](app-insights-analytics-reference.md#percentiles)

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

Per ottenere una suddivisione separata per ogni paese, è sufficiente visualizzare la colonna client\_CountryOrRegion separatamente attraverso entrambi gli operatori summarize:

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


## [Join](app-insights-analytics-reference.md#join)

È possibile accedere a più tabelle, incluse le richieste e le eccezioni.

Per trovare le eccezioni correlate a una richiesta che ha restituito una risposta di errore, è possibile creare un join delle tabelle in `session_Id`:

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


È buona norma usare `project` per selezionare solo le colonne necessarie prima di eseguire il join. Nelle stesse clausole, si rinomina la colonna timestamp.



## [Let](app-insights-analytics-reference.md#let-clause): assegnare un risultato a una variabile

Usare [let](./app-insights-analytics-syntax.md#let-statements) per separare le parti dell'espressione precedente. I risultati rimangono invariati:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Suggerimento: nel client Analisi non inserire righe vuote tra le parti di questo oggetto. Verificare che vengano eseguiti tutti gli elementi.


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0518_2016-->