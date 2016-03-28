<properties 
	pageTitle="Introduzione ad Application Insights Analytics" 
	description="Brevi esempi di tutte le principali query in Application Insights Analytics, lo strumento di ricerca avanzato per Application Insights." 
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
	ms.date="03/07/2016" 
	ms.author="awills"/>


 
# Introduzione ad Application Insights Analytics


Application Insights Analytics è un motore di ricerca di diagnostica avanzato per i dati di telemetria di [Application Insights](app-insights-overview.md).


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]
 
Di seguito sono descritte alcune query di base utili per iniziare.

## Connettersi ai dati di Application Insights

Aprire Analytics dal [pannello di panoramica](app-insights-dashboards.md) dell'applicazione in Application Insights:

![In portal.azure.com, aprire la risorsa di Application Insights e scegliere Analytics.](./media/app-analytics/001.png)

## [Conteggio](app-analytics-aggregations.md#count) delle righe

Le metriche, ad esempio i contatori delle prestazioni, vengono archiviate in una tabella denominata metriche. Ogni riga è un punto dati di telemetria ricevuto da Application Insights SDK in un'applicazione. Per scoprire le dimensioni della tabella, si invia una pipe del contenuto in un operatore che semplicemente conteggia le righe:

```AIQL
	
    requests | count
```

> [AZURE.NOTE] Inserire il cursore in un punto nell'istruzione prima fare clic su Vai. È possibile suddividere un'istruzione su più righe, ma non inserire righe vuote in un'unica istruzione. Per mantenere più query nella finestra, separarle con righe vuote.

Il risultato è il seguente:


![](./media/app-analytics-tour/010.png)

	
[`Count`](app-analytics-aggregations.md#count) è uno dei molti [operatori di query](app-analytics-queries.md) che è possibile disporre in una pipe quando si filtrano, si modificano e si uniscono i dati di più fasi.
	
## [Take](app-analytics-aggregations.md#take): mostrare n righe


Osserviamo alcuni dati. Che cosa c'è in 5 righe di esempio?

```AIQL

	requests | take 5
```

Il risultato è il seguente:

![results](./media/app-analytics-tour/020.png)

Scegliere le colonne e modificarne le posizioni:

![Fare clic sulla selezione di colonna in alto a destra dei risultati](./media/app-analytics-tour/030.png)

Espandere un elemento per visualizzare i dettagli:
 
![Scegliere Tabella e utsare Configura colonne](./media/app-analytics-tour/040.png)


## [Top](app-analytics-aggregations.md#top) e [sort](app-analytics-aggregations.md#sort)

`take` è utile per ottenere un rapido esempio di un risultato, ma mostra le righe della tabella senza un ordine particolare. Per ottenere una visualizzazione ordinata, usare `top` (per un esempio) o `sort` (per l'intera tabella).

Mostrare le prime n righe, ordinate in base a una colonna specifica:

```AIQL

	requests | top 10 by timestamp desc 
```

* *Sintassi:* la maggior parte degli operatori dispongono di parametri parola chiave, ad esempio `by`.
* `desc` = ordine decrescente, `asc` = ordine crescente.

![](./media/app-analytics-tour/260.png)

`top...` è più efficiente di `sort ... | take...`. Si sarebbe potuto scrivere:

```AIQL

	requests | sort by timestamp desc | take 10
```

Il risultato sarebbe stato lo stesso, ma l'esecuzione sarebbe risultata più lenta. Sarebbe stato anche possibile scrivere `order`, che è un alias di `sort`.

Le intestazioni di colonna nella visualizzazione tabella possono essere usate anche per ordinare i risultati sullo schermo. Naturalmente, se è stato usato `take` o `top` per recuperare solo parte di una tabella, verranno ordinati soltanto i record recuperati.


## [Project](app-analytics-aggregations.md#project): selezionare, rinominare e calcolare le colonne

Usare [`project`](app-analytics-aggregations.md#project) per selezionare solo le colonne desiderate:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-analytics-tour/240.png)


È inoltre possibile rinominare le colonne e definire nuove colonne:

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![risultato](./media/app-analytics-tour/270.png)

Nell'espressione scalare:

* `%` è il consueto operatore modulo. 
* `1d` (la cifra uno seguita da 'd') è il valore letterale di un intervallo di tempo che indica un giorno. Altri valori letterali di intervallo di tempo sono: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) arrotonda un valore per difetto al multiplo più vicino del valore di base specificato. `floor(aTime, 1s)` arrotonderà un'ora per difetto al secondo più vicino.

Le [espressioni](app-analytics-scalars.md) possono includere tutti gli operatori consueti (`+`, `-`, ...) ed è disponibile una gamma di funzioni utili.

## [Extend](app-analytics-aggregations.md#extend): calcolare le colonne

Se si desidera aggiungere colonne a quelli esistenti, usare [`extend`](app-analytics-aggregations.md#extend):

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

[`extend`](app-analytics-aggregations.md#extend) è meno dettagliato di [`project`](app-analytics-aggregations.md#project) se si desidera mantenere tutte le colonne esistenti.

## [Summarize](app-analytics-aggregations.md#summarize): aggregare gruppi di righe

Esaminando un esempio di tabella è possibile vedere i campi in cui sono riportati i diversi dati di telemetria. Ad esempio, `exception | take 20` mostra immediatamente che i messaggi di eccezione vengono riportati in un campo denominato `outerExceptionType`.

Anziché esaminare nel dettaglio le singole istanze, si richiede il numero di eccezioni riportate, di ogni tipo:

```AIQL

	exceptions 
    | summarize count() by outerExceptionType
```

![](./media/app-analytics-tour/210.png)

`Summarize` raggruppa le righe che contengono gli stessi valori nei campi denominati nella clausola `by` producendo una singola riga di risultati per ogni gruppo. Di conseguenza, in questo caso è presente una riga per ogni tipo di eccezione. La funzione di aggregazione `count()` conta le righe in ogni gruppo, inserendo una colonna nel risultato.


È disponibile una gamma di [funzioni di aggregazione](app-analytics-aggregations.md) . Molte di esse possono essere usate in un unico operatore summarize per ottenere più colonne calcolate.

È possibile, ad esempio, visualizzare un elenco delle richieste HTTP per le quali si verificano queste eccezioni. Esaminando nuovamente un esempio della tabella di eccezioni, si noterà che i percorsi delle richieste HTTP sono riportati in una colonna denominata `operation_Name`.

```AIQL

    exceptions 
    | summarize count(), makeset(operation_Name)
      by outerExceptionType	      
```

![](./media/app-analytics-tour/220.png)

La funzione di aggregazione `makeset()` crea un set di tutti i valori specificati in ogni gruppo. Come accade nell'esempio, esiste una sola operazione che dà origine a ogni eccezione.


Il risultato di summarize include:

* ogni colonna denominata in `by`;
* una colonna per ogni espressione di aggregazione;
* una riga per ogni combinazione di valori `by`.


## Riepilogare per valori scalari


È possibile usare valori scalari (numerici, di ora o intervallo) nella clausola by. I numeri tuttavia si presentano in genere in un intervallo continuo. Per raggruppare i punti dati, è necessario assegnarli utenti a contenitori di valori discreti. La funzione `bin` è utile a tale scopo:

```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

![](./media/app-analytics-tour/225.png)

`bin` riduce tutti i timestamp a intervalli di 1 giorno. È un alias di `floor`, una funzione comune alla maggior parte dei linguaggi. Ogni valore viene ridotto al multiplo più vicino del modulo specificato, in modo che `summarize` possa assegnare le righe a gruppi di dimensione ragionevole. Diversamente, si avrebbe una riga di risultati per ogni frazione di secondo senza alcun riepilogo dei dati.

È possibile ottenere un risultato migliore della visualizzazione tabella riportata sopra. Si osservino i risultati nella visualizzazione grafico con l'opzione delle barre verticali:

![Fare clic su Grafico, quindi scegliere Grafico a barre verticali e assegnare gli assi x e y](./media/app-analytics-tour/230.png)

Si noti che anche se i risultati non sono stati ordinati in base all'ora (come è possibile osservare nella visualizzazione tabella), la visualizzazione grafico mostra sempre i dati data/ora nell'ordine corretto.


## [Where](app-analytics-aggregations.md#where): filtrare una condizione

Se Application Insights è stato configurato per il monitoraggio di entrambi i lati [client](app-insights-javascript.md) e server dell'applicazione, alcuni dati di telemetria del database provengono dai browser.

Si osservino solo le eccezioni riportate dai browser:

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-analytics-tour/250.png)

L'operatore `where` accetta un'espressione booleana. Tenere presente i punti chiave seguenti:

 * `and`, `or`: operatori booleani
 * `==`, `<>` : uguale a e non uguale a
 * `=~`, `!=` : stringa con distinzione maiuscole/minuscole uguale a e non uguale a. Sono disponibili numerosi altri operatori di confronto delle stringhe.

Leggere tutte le informazioni sulle [espressioni scalari](app-analytics-scalars.md).

### Filtro degli eventi

Individuare le richieste non riuscite:

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` è di tipo stringa, pertanto è necessario [eseguire il cast](app-analytics-scalars.md#casts) per un confronto numerico.

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

![timechart](./media/app-analytics-tour/080.png)

L'asse x per i grafici a linee deve essere di tipo DateTime.

## Serie multiple 

Usare più valori in una clausola `summarize by` per creare una riga separata per ogni combinazione di valori:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-analytics-tour/090.png)

Per visualizzare più righe in un grafico, fare clic su **Split by** e selezionare una colonna.

![](./media/app-analytics-tour/100.png)



## Ciclo della media giornaliera

Come varia l'uso nella giornata media?

Conteggiare le richieste per un modulo di un giorno, suddivise in ore:

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Grafico a linee delle ore di un giorno medio](./media/app-analytics-tour/120.png)

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

![Suddividere per client\_StateOrProvince](./media/app-analytics-tour/130.png)


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


![](./media/app-analytics-tour/290.png)



## [Percentili](app-analytics-aggregations.md#percentiles)

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

![risultato](./media/app-analytics-tour/180.png)

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

![](./media/app-analytics-tour/190.png)


## [Join](app-analytics-aggregations.md#join)

È possibile accedere a più tabelle, incluse le richieste e le eccezioni.

Per individuare le eccezioni correlate a una richiesta che ha restituito una risposta di errore, è possibile eseguire il join delle tabelle in `session_Id`:

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


È buona norma usare `project` per selezionare solo le colonne necessarie prima di eseguire il join. Nelle stesse clausole, si rinomina la colonna timestamp.



## [Let](app-analytics-aggregations.md#let): assegnare un risultato a una variabile

Usare [let](./app-analytics-syntax.md#let-statements) per separare le parti dell'espressione precedente. I risultati rimangono invariati:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Suggerimento: nel client AI Analytics non inserire righe vuote tra le parti di questo oggetto. Verificare che vengano eseguiti tutti gli elementi.


[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0316_2016-->