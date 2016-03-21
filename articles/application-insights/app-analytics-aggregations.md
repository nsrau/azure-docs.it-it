<properties 
	pageTitle="Istruzione summarize e funzioni di aggregazione in Application Insights Analytics" 
	description="Riferimento per le funzioni di aggregazione e l'istruzione summarize in Application Insights Analytics, lo strumento di ricerca avanzato per Application Insights." 
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
	ms.date="03/06/2016" 
	ms.author="awills"/>


# Aggregazione in Application Insights Analytics

[Application Insights Analytics](app-analytics.md) è un motore di ricerca avanzato per i dati di telemetria di [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Application Insights Analytics (AIQL).

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]



## Operatore `summarize`

Produce una tabella che aggrega il contenuto della tabella di input.

    purchases
    | summarize avg(Price) 
      by Fruit, Supplier

![](./media/app-analytics-aggregations/01.png)

* I record di input vengono raccolti in gruppi, in modo che ogni gruppo includa una combinazione specifica di valori nei campi `by`.
* Le espressioni di aggregazione vengono valutate nei membri di ogni gruppo.
* È presente una riga di output per ogni singola combinazione di valori delle espressioni 'by'. 
* È presente una colonna di output per ogni espressione di aggregazione e ogni espressione 'by'. Tutte le altre colonne di input vengono eliminate.

### Sintassi

    T | summarize
         [  [Column =] Aggregation [`,` ...]]
         [ by
            [Column =] GroupExpression [`,` ...]]

**Argomenti**

* *Colonna:* nome facoltativo per una colonna di risultati. Il valore predefinito è un nome derivato dall'espressione.
* *Aggregazione:* chiamata a una funzione di aggregazione, ad esempio `count()` o `avg()` con nomi di colonna come argomenti. Vedere l'elenco delle funzioni di aggregazione riportato di seguito.
* *Espressione di gruppo:* espressione sulle colonne che fornisce un set di valori distinct. Si tratta in genere di un nome di colonna che fornisce già un set di valori limitato oppure di `bin()` con una colonna numerica o di data e ora come argomento. 

Se si specifica un'espressione numerica o di data e ora senza usare `bin()`, AI Analytics applica automaticamente l'espressione con un intervallo di `1h` per gli orari o `1.0` per i numeri.

Se non si specifica un'*espressione di gruppo*, l'intera tabella viene riepilogata in un'unica riga di output.

Nella clausola `by` è necessario usare un tipo semplice anziché un tipo dinamico. Ad esempio, il cast `tostring` specificato di seguito è fondamentale:

    exceptions
	| summarize count()
      by tostring(customDimensions.ClientRequestId)

### Riepilogare per colonne con valori discreti

Eseguire una query per visualizzare i tempi medi di risposta alle diverse richieste HTTP, separando tutte le richieste con codici di risposta diversi:

    requests 
    | summarize count(), avg(duration) 
      by operation_Name, resultCode

![risultato](./media/app-analytics-aggregations/03.png)


* Le espressioni di aggregazione, ad esempio count e avg, dovranno essere costituite dalle funzioni di aggregazione descritte in questo articolo. Gli argomenti possono essere rappresentati da qualsiasi funzione scalare.
* Le espressioni di raggruppamento (che seguono 'by') possono essere costituite da qualsiasi espressione scalare, ma sono più efficienti se sono costituite da semplici nomi di campo.

### Riepilogare per le colonne numeriche

Se si desidera raggruppare in base a un valore scalare continuo, ad esempio un numero o un'ora, è necessario usare la funzione `bin` (nota anche come `floor`) per suddividere l'intervallo continuo in contenitori.

    requests
    | summarize count() 
      by duration_range=bin(duration, 1)

![risultato](./media/app-analytics-aggregations/04.png)

 
## Suggerimenti

* Usare `where` per rimuovere tutte le righe che non si desidera includere prima di `summarize`.
 * Rimuovere i valori null. Un solo valore null in un gruppo renderà null il risultato dell'aggregazione. 

```

        requests 
        | where isnotnull(duration) 
        | summarize count(), avg(duration)
          by operation_Name
```

* Sebbene sia possibile specificare espressioni arbitrarie per le espressioni di aggregazione e raggruppamento, è preferibile usare nomi di campo semplici o applicare `bin()` a un campo numerico.





## esempi

Individuare il timestamp minimo e massimo di tutti i record della tabella Attività. Poiché non è presente alcuna clausola group-by, l'output include una sola riga:

```

requests | summarize Min = min(timestamp), Max = max(timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`



#### Durata della sessione

Le sessioni di un registro eventi includono più eventi. Individuare l'inizio e la fine di ogni sessione ricercando il primo e l'ultimo evento di ogni sessione:

```

    requests 
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) by session_Id 
    | extend duration = bin(stop - start, 1s)
```

L'operazione `extend` aggiunge la colonna della durata e usa `bin` (nota anche come `floor`) per arrotondarla al secondo più vicino.

![](./media/app-analytics-aggregations/minmax.png)

#### Esempio: durata media

Di seguito vengono determinate le durate medie delle sessioni client in diverse città:

```

    requests
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) 
      by session_Id, client_City 
    | extend duration = stop - start
    | summarize duration_by_city=bin(avg(duration),1s) by client_City
    | top 50 by duration_by_city
```

È stata aggiunta la colonna `client_City` alla clausola `by` in modo che passi la prima operazione di riepilogo. Poiché si suppone che tutti gli eventi della stessa sessione client si verifichino nella stessa città, non verrà aggiunto alcun output al numero di output del riepilogo.


![](./media/app-analytics-aggregations/durationcity.png)


#### Esempio: 

Individuare l'ora del giorno di maggior attività della città di ogni client. Per ora di "maggior attività" si intende l'ora del giorno in cui viene iniziato il maggior numero di sessioni in una giornata standard.

```
requests  
| summarize start=min(timestamp) by session_Id, city=client_City 
| extend timeofday=start % 1d 
| summarize popularity=dcount(session_Id) by bin(timeofday, 1h), city 
| summarize argmax(popularity, *) by city  
| sort by max_pop_tod asc
```

## FUNZIONI DI AGGREGAZIONE

## qualsiasi 

    any(Expression)

Seleziona in modo casuale una riga del gruppo e restituisce il valore dell'espressione specificata.

Questa funzione è utile quando una colonna include un numero elevato di valori simili, ad esempio una colonna "testo errore", e si desidera eseguire un solo campionamento della colonna per valore univoco della chiave di gruppo composta.

**Esempio**

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

## argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Trova una riga del gruppo che riduce al minimo o al massimo *ExprToMaximize* e restituisce il valore di *ExprToReturn* (o `*` per restituire l'intera riga).

**Suggerimento**: le colonne passate vengono rinominate automaticamente. Per assicurarsi di usare i nomi corretti, esaminare i risultati usando `take 5` prima di inviare i risultati in un altro operatore.

**esempi**

Per ogni nome di richiesta, indicare quando si è verificata la richiesta più lunga:

    requests | summarize argmax(duration, timestamp) by name

Mostrare tutti i dettagli della richiesta più lunga, non solo il timestamp:

    requests | summarize argmax(duration, *) by name


Trovare il valore più basso di ogni metrica, con timestamp e altri dati:

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-analytics-aggregations/argmin.png)
 


## avg

    avg(Expression)

Calcola la media dii *Expression* all'interno del gruppo.

## buildschema

    buildschema(DynamicExpression)

Restituisce lo schema minimo che ammette tutti i valori di *DynamicExpression*.

Il tipo di colonna dei parametri deve essere `dynamic` - un contenitore di matrici o proprietà.

**Esempio**

    exceptions | summarize buildschema(details)

Risultato:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Si noti che `indexer` indica il punto in cui è necessario usare un indice numerico. Per questo schema, alcuni percorsi validi potrebbero essere i seguenti (presupponendo che questi indici di esempio siano inclusi nell'intervallo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Esempio**

Si supponga che la colonna di input abbia tre valori dinamici:

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


Lo schema risultante sarà:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Lo schema indica che:

* L'oggetto radice è un contenitore con quattro proprietà denominate x, y, z e t.
* La proprietà denominata "x" può essere di tipo "int" o "string".
* La proprietà denominata "y" può essere di tipo "double" o un altro contenitore con una proprietà denominata "w" di tipo "string".
* La parola chiave ``indexer`` indica che "z" e "t" sono matrici.
* Ogni elemento della matrice "z" è di tipo int o string.
* "t" è una matrice di stringhe.
* Ogni proprietà è facoltativa in modo implicito e qualsiasi matrice può essere vuota.

#### Modello di schema

La sintassi dello schema restituito è:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
	Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Equivalgono a un subset di annotazioni di tipo TypeScript, codificato come valore dinamico AIQL. In Typescript, lo schema di esempio sarebbe:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


## count

    count([ Predicate ])

Restituisce un conteggio delle righe per il quale *Predicate* restituisce `true`. Se *Predicate* non viene specificato, restituisce il numero totale di record del gruppo.

**Suggerimento per le prestazioni**: usare `summarize count(filter)` anziché `where filter | summarize count()`
   

## dcount

    dcount( Expression [ ,  Accuracy ])

Restituisce una stima del numero di valori distinct di *Expr* nel gruppo. Per visualizzare un elenco dei valori distinct, usare [`makeset`](#makeset).

*Accuracy*, se specificato, controlla il rapporto tra velocità e accuratezza.

 * `0` = il calcolo meno accurato e più veloce.
 * `1` il valore predefinito che bilancia accuratezza e tempi di calcolo; errore dello 0,8 % circa.
 * `2` = il calcolo più accurato e più lento; errore dello 0,4 % circa.

**Esempio**

    pageViews 
    | summarize countries=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/dcount.png)

## makelist

    makelist(Expr [ ,  MaxListSize ] )

Restituisce una matrice `dynamic` (JSON) di tutti i valori di *Expr* nel gruppo.

* *MaxListSize* è un limite di tipo integer facoltativo per il numero massimo di elementi restituiti (il valore predefinito è *128*).

## makeset

    makeset(Expression [ , MaxSetSize ] )

Restituisce una matrice `dynamic` (JSON) del set di valori distinct che *Expr* inserisce nel gruppo. Suggerimento: per conteggiare solo i valori distinct, usare [`dcount`](#dcount).
  
*  *MaxSetSize* è un limite di tipo integer facoltativo per il numero massimo di elementi restituiti (il valore predefinito è *128*).

**Esempio**

    pageViews 
    | summarize countries=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/makeset.png)

Vedere anche l'[operatore `mvexpand`](app-analytics-queries.md#mvexpand-operator) per la funzione opposta.


## max, min

    max(Expr)

Calcola il valore massimo di *Expr*.
    
    min(Expr)

Calcola il valore minimo di *Expr*.

**Suggerimento**: viene restituito solo il valore minimo o massimo, ad esempio il prezzo maggiore o minore. Ma se si desidera inserire altre colonne nella riga, ad esempio il nome del fornitore con il prezzo più basso, usare [argmin o argmax](#argmin-argmax).

## percentile, percentiles

    percentile(Expression, Percentile)

Restituisce una stima per *Expression* del percentile specificato nel gruppo. L'accuratezza dipende dalla densità della popolazione nell'area del percentile.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )

Simile a `percentile()` ma calcola un numero di valori di percentile (operazione più rapida rispetto al calcolo separato di ogni percentile).

**esempi**


Valore di `duration` maggiore del 95 % del set di esempio e minore del 5 % del set di esempio, calcolato per ogni nome richiesta:

    request 
    | summarize percentile(duration, 95)
      by name

Per il calcolo dell'intera tabella, omettere "by...".

Calcolare contemporaneamente più percentili per nomi richiesta diversi:

    
    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-analytics-aggregations/percentiles.png)

I risultati mostrano che per la richiesta /Events/Index, il 5 % delle richieste riceve una risposta in meno di 2,44,s, la metà di esse in 3,52 s, mentre il 5 % risulta più lento di 6,85 s.


Calcolare più statistiche:

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### Errore di stima nei percentili

L'aggregazione dei percentili fornisce un valore approssimativo mediante [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf).

Tenere presente le seguenti considerazioni importanti:

* I limiti nell'errore di stima variano a seconda del valore del percentile richiesto. L'accuratezza migliore è alle estremità della scala [0-100]. I percentili 0 e 100 sono i valori minimo e massimo esatti della distribuzione. L'accuratezza diminuisce gradualmente verso il centro della scala. L'accuratezza minore è in corrispondenza del punto mediano ed è limitata all'1 %. 
* I limiti di errore sono visibili nella classifica, non nel valore. Si supponga che percentile(X, 50) abbia restituito il valore Xm. La stima garantisce che almeno il 49 % e al massimo il 51 % dei valori di X sono minori di Xm. Non esiste alcun limite teorico nella differenza tra Xm e il valore mediano effettivo di X.

## stdev

     stdev(Expr)

Restituisce la deviazione standard di *Expr* del gruppo.

## variance

    variance(Expr)

Restituisce la varianza di *Expr* del gruppo.

## sum

    sum(Expr)

Restituisce la somma di *Expr* del gruppo.




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->