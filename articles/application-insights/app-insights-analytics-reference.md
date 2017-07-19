---
title: Informazioni di riferimento su Analisi in Azure Application Insights | Microsoft Docs
description: 'Informazioni di riferimento sulle istruzioni nello strumento di ricerca avanzato per l''analisi incluso in Application Insights. '
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: eea324de-d5e5-4064-9933-beb3a97b350b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: dd3478966e4e5ccc9f108940401c7ee9454087dd
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="reference-for-analytics"></a>Informazioni di riferimento sull'analisi
L'[analisi](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analytics.

Fonti di informazioni aggiuntive:

* Diversi materiali di riferimento sono disponibili in Analytics non appena si digita il testo. Quando si inizia a digitare una query, vengono suggeriti i completamenti possibili.
* [La pagina dell'esercitazione](app-insights-analytics-tour.md) offre un'introduzione dettagliata alle funzionalità del linguaggio.
* Il [foglio informativo sugli utenti SQL](https://aka.ms/sql-analytics) traduce i linguaggi più comuni.
* [Eseguire la versione di test di Analisi sui dati simulati](https://analytics.applicationinsights.io/demo) se l'app non invia i dati ad Application Insights.
 

## <a name="index"></a>Indice
**Let** [let](#let-clause) | [materialize](#materialize) 

**Query e operatori** [as](#as-operator) | [autocluster](#evaluate-autocluster_v2) | [basket](#evaluate-basketv2) | [count](#count-operator) | [datatable](#datatable-operator) | [diffpatterns](#evaluate-diffpatterns_v2) | [distinct](#distinct-operator) | [evaluate](#evaluate-operator) | [extend](#extend-operator) | [extractcolumns](#evaluate-extractcolumns) | [find](#find-operator) | [getschema](#getschema-operator) | [join](#join-operator) | [limit](#limit-operator) | [make-series](#make-series-operator) | [mvexpand](#mvexpand-operator) | [parse](#parse-operator) | [project](#project-operator) | [project-away](#project-away-operator) | [range](#range-operator) | [reduce](#reduce-operator) | [render directive](#render-directive) | [restrict clause](#restrict-clause) | [sample](#sample-operator) | [sample-distinct](#sample-distinct-operator) | [sort](#sort-operator) | [summarize](#summarize-operator) | [table](#table-operator) | [take](#take-operator) | [top](#top-operator) | [top-nested](#top-nested-operator) | [union](#union-operator) | [where](#where-operator) 

**Aggregazioni** [any](#any) | [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [count](#count) | [countif](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [percentile](#percentile) | [percentiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [sum](#sum) | [variance](#variance)

**Scalari** [Valori letterali booleani](#boolean-literals) | [Operatori booleani](#boolean-operators) | [Cast](#casts) | [Confronti scalari](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Numeri** [Operatori aritmetici](#arithmetic-operators) | [Valori letterali numerici](#numeric-literals) | [abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) | [log](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Serie numeriche** 
[series_fir](#seriesfir) | [series\_fit\_line](#seriesfitline) | [series\_fit\_2lines](#seriesfit2lines) | [series_iir](#seriesiir) |[series_outliers](#seriesoutliers)| [series_periods](#seriesperiods) | [series_stats](#seriesstats) | 

**Data e ora** [Espressioni di data e ora](#date-and-time-expressions) | [Valori letterali di data e ora](#date-and-time-literals) | [ago](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Stringa** [GUID](#guids) | [Valori letterali stringa offuscati](#obfuscated-string-literals) | [Valori letterali stringa](#string-literals) | [Confronto tra stringhe](#string-comparisons) | [countof](#countof) | [extract](#extract) | [in, !in](#in) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Array, oggetti e valori dinamici** [Valori letterali di matrice](#array-and-object-literals) | [Funzioni di oggetti dinamici](#dynamic-object-functions) | [Oggetti dinamici nelle clausole let](#dynamic-objects-in-let-clauses) | [Espressioni di percorso JSON](#json-path-expressions) | [Nomi](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [in, !in](#in) | [parsejson](#parsejson) | [range](#range) | [todynamic](#todynamic) | [treepath](#treepath)

## <a name="let"></a>Let
### <a name="let-clause"></a>Clausola let
**let tabulare: denominazione di una tabella**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**let scalare: denominazione di un valore**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**let lambda: denominazione di una funzione**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ", 
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests 
    | summarize count() by bin(timestamp, 1h) 
    | project count_, pacificTime=us_date(timestamp-8h)

Una clausola let associa un [nome](#names) a un risultato tabulare, a un valore scalare o a una funzione. La clausola è un prefisso di una query e l'ambito del binding è tale query. Let non consente di assegnare un nome agli elementi usati più avanti nella sessione.

**Sintassi**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](#dynamic-type)
* *plain_query:* una query non preceduta da una clausola let.

**esempi**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...

Convertire un risultato di tabella in un valore scalare e usarlo in una query:

```
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

### <a name="materialize"></a>materialize

Usare materialize() per migliorare le prestazioni in cui il risultato di una clausola let viene usato più di una volta a valle. Materialize() valuta e memorizza nella cache il risultato di una clausola let tabulare al momento dell'esecuzione della query, verificando che la query non venga eseguita più volte.

**Sintassi**

    materialize(expression)

**Argomenti**

* `expresion`: espressione tabulare da valutare e memorizzare nella cache durante l'esecuzione della query.

**Suggerimenti**

* Usare Materialize quando si dispone di un join/union in cui i relativi operandi hanno sottoquery reciproche che possono essere eseguite una volta; vedere gli esempi seguenti.
* È utile anche negli scenari quando occorre eseguire join/union di fork leg.
* È possibile usare Materialize solo nelle istruzioni let, assegnando un nome al risultato memorizzato nella cache.
* Materialize prevede un limite di 5 GB per la dimensione della cache. Questo limite vale per ogni nodo del cluster ed è reciproco per tutte le query.

**Esempio: self-join**


```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let materializedScope = pageViews
| summarize by name, Day = startofday(timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project name, Day1 = Day
| join kind = inner
(
    cachedResult
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

La versione non memorizzata nella cache usa il risultato `scope` due volte:

```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let scope = pageViews
| summarize by name, Day = startofday(timestamp);
scope      // First use of this table.
| project name, Day1 = Day
| join kind = inner
(
    scope  // Second use can cause evaluation twice.
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

## <a name="queries-and-operators"></a>Query e operatori
Una query sulla telemetria è costituita da un riferimento a un flusso di origine, seguito da una pipeline di filtri. Ad esempio:

```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```

Ogni filtro preceduto dal carattere di barra verticale `|` è un'istanza di un *operatore*, con alcuni parametri. L'input per l'operatore è la tabella risultante dalla pipeline precedente. Nella maggior parte dei casi i parametri sono [espressioni scalari](#scalars) sulle colonne dell'input. In alcuni casi, i parametri sono i nomi delle colonne di input e, in altri casi, il parametro è una seconda tabella. Il risultato di una query è sempre una tabella, anche se include solo una colonna e una riga.

Le query possono contenere singole interruzioni di riga, ma terminano con una riga vuota. Possono includere commenti tra `//` e la fine della riga.

Una query può essere preceduta da una o più [clausole let](#let-clause), che definiscono valori scalari, tabelle o funzioni che è possibile usare nella query.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` viene usato negli esempi di query seguenti per indicare la tabella di origine o la pipeline precedente.
> 
> 

### <a name="as-operator"></a>operatore as

Associa temporaneamente un nome all'espressione tabulare di input.

**Sintassi**

    T | as name

**Argomenti**

* *name:* un nome temporaneo per la tabella

**Note**

* Usare [let](#let-clause) anziché *as* se si vuole usare il nome in una sottoespressione successiva.
* Usare *as* per specificare il nome della tabella così come appare nel risultato di un'operazione [union](#union-operator), [find](#find-operator), o [search](#search-operator).

**Esempio**

```AIQL
range x from 1 to 10 step 1 | as T1
| union withsource=TableName (requests | take 10 | as T2)
```

### <a name="count-operator"></a>Operatore count
L'operatore `count` restituisce il numero di record (righe) nel set di record di input.

**Sintassi**

    T | count

**Argomenti**

* *T*: dati tabulari i cui record devono essere contati.

**Restituisce**

Questa funzione restituisce una tabella con un solo record e una sola colonna di tipo `long`. Il valore dell'unica cella è il numero di record in *T*. 

**Esempio**

```AIQL
requests | count
```

### <a name="datatable-operator"></a>Operatore datatable

Specificare una tabella inline. Lo schema e i valori sono definiti nella query stessa.

Si noti che questo operatore non ha una pipeline di input.

**Sintassi**

    datatable ( ColumnName1 : ColumnType1 , ...) [ScalarValue1, ...]

* *ColumnName* Il nome per una colonna.
* *ColumnType* Un [tipo di dati](#scalars). 
* *ScalarValue* Un valore del tipo appropriato. Il numero di valori deve essere un multiplo del numero di colonne. 

**Restituisce**

Una tabella contenente i valori specificati.

**Esempio**

```AIQL
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```

### <a name="distinct-operator"></a>Operatore distinct

Restituisce una tabella contenente il set di righe che presentano combinazioni di valori distinte. Facoltativamente esegue una proiezione su un sottoinsieme di colonne prima dell'operazione.

**Sintassi**

    T | distinct *              // All columns
    T | distinct Column1, ...   // Columns to project

**Esempio**

```AIQL
datatable (Supplier: string, Fruit: string, Price:int) 
["Contoso", "Grapes", 22,
"Fabrikam", "Apples", 14,
"Contoso", "Apples", 15,
"Fabrikam", "Grapes", 22]
| distinct Fruit, Price 
```


|Frutta|Prezzo|
|---|---|
|Uva|22|
|Mele|14|
|Mele|15|


### <a name="evaluate-operator"></a>Operatore evaluate
`evaluate` è un meccanismo di estensione che consente di aggiungere algoritmi specializzati alla fine delle query.

`evaluate` deve essere l'ultimo operatore nella pipeline della query, ad eccezione di un possibile `render`. Non deve comparire nel corpo funzione.

[evaluate autocluster](#evaluate-autocluster_v2) | [evaluate basket](#evaluate-basketv2) | [evaluate diffpatterns](#evaluate-diffpatterns_v2) | [evaluate extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster-deprecated"></a>evaluate autocluster (deprecato)
     T | evaluate autocluster()

Autocluster è un modo rapido per individuare i raggruppamenti naturali in un set di dati. Da una considerevole quantità di dati richiesta, ad esempio, è possibile identificare rapidamente l'80% degli errori 404 come richieste per un particolare URL, eseguite da un client di una determinata città.

AutoCluster trova modelli comuni di attributi discreti (dimensioni) nei dati e ridurrà i risultati della query originale (indipendentemente dal fatto che siano 100 o 100.000 righe) a un numero minore di modelli. AutoCluster è stato sviluppato per semplificare l'analisi degli errori, ad esempio eccezioni e arresti anomali, ma può potenzialmente funzionare su qualsiasi set di dati filtrato. 

**Questa versione di `autocluster` è deprecata. Usare [autocluster_v2](#evaluate-autocluster_v2).**

**Sintassi**

    T | evaluate autocluster( arguments )

**Restituisce**

AutoCluster restituisce un set di modelli solitamente di piccole dimensioni, che acquisisce parti dei dati con valori comuni condivisi tra più attributi discreti. Ogni modello è rappresentato da una riga nei risultati. 

Le prime due colonne indicano il numero e la percentuale di righe della query originale che vengono acquisite dal modello. Le colonne rimanenti provengono dalla query originale e il rispettivo valore è un valore specifico della colonna oppure '*', ovvero valori variabili. 

Si noti che i modelli non sono non contigui: è possibile che si sovrappongano e in genere non comprendono tutte le righe originali. È possibile che alcune righe non rientrino in alcun modello.

**Suggerimenti**

* Usare `where` e `project` nella pipeline di input per ridurre i dati ai soli dati rilevanti.
* Quando si trova una riga interessante, è possibile che si voglia visualizzarne i dettagli, aggiungendo i rispettivi valori specifici al filtro `where` .

**Argomenti (tutti facoltativi)**

* `output=all | values | minimal` 
  
    Formato dei risultati. Le colonne Count e Percent vengono sempre visualizzate nei risultati. 
  
  * `all` : vengono restituite tutte le colonne dell'input.
  * `values` : consente di filtrare solo le colonne con '*' nei risultati.
  * `minimal` : consente di filtrare anche le colonne identiche per tutte le righe della query originale. 
* `min_percent=`*double* (valore predefinito: 1)
  
    Percentuale minima di copertura delle righe generate.
  
    Esempio: `T | evaluate autocluster("min_percent=5.5")`
* `num_seeds=` *int* (valore predefinito: 25) 
  
    Il numero di valori di inizializzazione determina il numero di punti di ricerca locale iniziali dell'algoritmo. In alcuni casi, in base alla struttura dei dati, l'aumento del numero di valori di inizializzazione consente di aumentare il numero o la qualità dei risultati tramite uno spazio di ricerca maggiore ma con una query più lenta. L'argomento num_seeds presenta risultati in diminuzione in entrambe le direzioni, quindi ridurlo a un valore inferiore a 5 consentirà di ottenere miglioramenti trascurabili delle prestazioni e aumentarlo a un valore superiore a 50 genererà raramente modelli aggiuntivi.
  
    Esempio: `T | evaluate autocluster("num_seeds=50")`
* `size_weight=` *0<double<1*+ (valore predefinito: 0,5)
  
    Consente di controllare parzialmente l'equilibrio tra generico (copertura elevata) e informativo (molti valori condivisi). L'aumento di size_weight riduce in genere il numero di modelli e ogni modello tende a coprire una percentuale più ampia. La riduzione di size_weight produce in genere modelli più specifici con più valori condivisi e una percentuale di copertura minore. La formula predefinita è una media geometrica ponderata tra il punteggio generico normalizzato e il punteggio informativo con size_weight e 1-size_weight per i pesi. 
  
    Esempio: `T | evaluate autocluster("size_weight=0.8")`
* `weight_column=` *column_name*
  
    Prende in considerazione ogni riga nell'input in base al peso specificato. Per impostazione predefinita, il peso di ogni riga è '1'. Una colonna di ponderazione viene in genere usata per tenere in considerazione il campionamento o il bucket/aggregazione dei dati già incorporati in ogni riga.
  
    Esempio: `T | evaluate autocluster("weight_column=sample_Count")` 

<a name="evaluate-autocluster_v2"></a>

#### <a name="evaluate-autoclusterv2"></a>evaluate autocluster_v2

    T | evaluate autocluster_v2()

AutoCluster trova modelli comuni di attributi discreti (dimensioni) nei dati e ridurrà i risultati della query originale (indipendentemente dal fatto che siano 100 o 100.000 righe) a un numero minore di modelli. AutoCluster è stato sviluppato per semplificare l'analisi degli errori, ad esempio eccezioni e arresti anomali, ma può potenzialmente funzionare su qualsiasi set di dati filtrato. L'algoritmo AutoCluster è stato sviluppato dal team di ricerca Developer Analytics (KustoML@microsoft.com).

Questo plug-in sostituisce la sintassi del plug-in autocluster deprecato.     

**Sintassi**
`T | evaluate autocluster_v2( arguments )`

**Restituisce** AutoCluster restituisce un set, solitamente di piccole dimensioni, di modelli che acquisiscono parti dei dati con valori comuni condivisi tra più attributi discreti. Ogni modello è rappresentato da una riga nei risultati. La prima colonna è l'ID segmento. Le due colonne successive indicano il numero e la percentuale di righe esterne alla query originale acquisite dal modello. Le colonne rimanenti provengono dalla query originale e il rispettivo valore è un valore specifico della colonna o un carattere jolly (per impostazione predefinita null), ovvero valori variabili. Si noti che i modelli non sono distinti: è possibile che si sovrappongano e in genere non comprendono tutte le righe originali. È possibile che alcune righe non rientrino in alcun modello.

**Suggerimenti** Usare `where` e `project` nella pipe di input per limitare i dati a quelli di interesse.
Quando si trova una riga interessante, è possibile che si voglia visualizzarne i dettagli, aggiungendo i rispettivi valori specifici al filtro `where` .

**Gli argomenti (tutti facoltativi)** `T | restituiscono autocluster_V2([*SizeWight*,*WeightColumn*,*NumSeeds*,*CustomWildcard*,...])

Tutti gli argomenti sono facoltativi, ma devono essere ordinati come riportato sopra. Per indicare che è necessario usare il valore predefinito, usare il carattere tilde della stringa "~" (vedere gli esempi di seguito).

**Argomenti disponibili**

- SizeWeight - 0<*double* <1 [default 0,5] Offre un certo controllo sull'equilibrio tra generico (copertura elevata) e informativo (molti valori condivisi). L'aumento del valore comporta in genere la riduzione del numero di modelli e ogni motivo tende a coprire una percentuale maggiore. La riduzione del valore produce in genere modelli più specifici con un maggior numero di valori condivisi e una percentuale di copertura inferiore. La formula predefinita è una media geometrica ponderata tra il punteggio generico normalizzato e il punteggio informativo con *SizeWeight* e *1-SizeWeight* come i pesi. 

**Esempio**
`T | evaluate autocluster_v2(0.8)`

- WeightColumn - *column_name*

Prende in considerazione ogni riga nell'input in base al peso specificato. Per impostazione predefinita ogni riga ha un peso pari a '1'. L'argomento deve essere un nome di una colonna numerica (ad esempio int, long, real). Una colonna di ponderazione viene in genere usata per tenere in considerazione il campionamento o il bucket/aggregazione dei dati già incorporati in ogni riga.

**Esempio**
`T | evaluate autocluster_v2('~', sample_Count)`

`- NumSeeds - *int* [default 25]

Il numero di valori di inizializzazione determina il numero di punti di ricerca locale iniziali dell'algoritmo. In alcuni casi, in base alla struttura dei dati, l'aumento del numero di valori di inizializzazione consente di aumentare il numero o la qualità dei risultati tramite uno spazio di ricerca maggiore ma con una query più lenta. Il valore presenta risultati in diminuzione in entrambe le direzioni, quindi ridurlo a un valore inferiore a 5 consentirà di ottenere miglioramenti trascurabili delle prestazioni, mentre aumentarlo a un valore superiore a 50 genererà raramente modelli aggiuntivi.

**Esempio**
`T | evaluate autocluster_v2('~','~',15)`

- CustomWildcard - *any_value_per_type*

Imposta il valore del carattere jolly per un tipo specifico nella tabella dei risultati, che indica che il criterio corrente non prevede una restrizione su questa colonna. Il valore predefinito è null, mentre per le stringhe il valore predefinito è una stringa vuota. Se il valore predefinito è un valore usabile nei dati, deve essere usato un valore diverso per il carattere jolly (ad esempio *).

**Esempio**

`T | evaluate autocluster_v2('~','~','~',int (-1), double(-1), long(0), datetime(1900-1-1))`

**Esempio**
```
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster_v2(0.6)
```
**Risultati**
|SegmentId|Numero|Percentuale|Stato|EventType|Danni|
----------|-----|-------|-----|---------|------|
0|2278|38.7||Grandine|NO
1|512|8.7||Vento di tempesta|SÌ
2|898|15.3|TEXAS|||

**Esempio con caratteri jolly personalizzati**
```
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster_v2(0.2, '~', '~', '*')
```
**Risultati**
|SegmentId|Numero|Percentuale|Stato|EventType|Danni|
----------|-----|-------|-----|---------|------|
0|2278|38.7|\*|Grandine|NO
1|512|8.7|\*|Vento di tempesta|SÌ
2|898|15.3|TEXAS|\*|\*|

**Informazioni aggiuntive**

-  AutoCluster in gran parte è basato sull'algoritmo Seed-Expand dal seguente documento: [Algoritmi per data mining di telemetria usante gli attributi discreti](http://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1), collegamento full-text: [pdf](https://kusto.azurewebsites.net/docs/queryLanguage/images/queries/ICPRAM17telemetry.pdf). 


#### <a name="evaluate-basket-deprecated"></a>evaluate basket (deprecato)

     T | evaluate basket()

**Questa versione di `basket` è deprecata. Usare [basket_v2](#evaluate-basketv2).**

**Restituisce**

Tutti i modelli disponibili in più di una frazione specificata degli eventi (valore predefinito 0,05).

**Argomenti (tutti facoltativi)**

* `threshold=` *0,015<double<1* (valore predefinito: 0,05) 
  
    Imposta il rapporto minimo di righe da considerare frequenti. I modelli con rapporto inferiore non verranno restituiti.
  
    Esempio: `T | evaluate basket("threshold=0.02")`
* `weight_column=` *column_name*
  
    Prende in considerazione ogni riga nell'input in base al peso specificato. Per impostazione predefinita, il peso di ogni riga è '1'. Una colonna di ponderazione viene in genere usata per tenere in considerazione il campionamento o il bucket/aggregazione dei dati già incorporati in ogni riga.
  
    Esempio: T | evaluate basket("weight_column=sample_Count")
* `max_dims=` *1<int* (valore predefinito: 5)
  
    Imposta il numero massimo di dimensioni non correlate per basket, limitato per impostazione predefinita per diminuire il runtime di query.
* `output=minimize` | `all` 
  
    Formato dei risultati. Le colonne Count e Percent vengono sempre visualizzate nei risultati.
  
  * `minimize` : consente di filtrare solo le colonne con '*' nei risultati.
  * `all` : vengono restituite tutte le colonne dell'input.

<a name="evaluate-basket"></a>
#### <a name="evaluate-basketv2"></a>evaluate basket_v2

     T | evaluate basket_v2()

Basket consente di trovare tutti i modelli frequenti di attributi discreti (dimensioni) nei dati e restituirà tutti i modelli frequenti che hanno superato la soglia di frequenza nella query originale. Basket assicura l'individuazione di tutti i modelli frequenti nei dati ma non assicura un runtime polinomiale. Il runtime della query è lineare nel numero di righe ma in alcuni casi può essere esponenziale nel numero di colonne (dimensioni). Basket è basato sull'algoritmo Apriori sviluppato originariamente per il data mining di basket analysis. 

Sostituisce la sintassi deprecata `evaluate basket`.

**Restituisce**

Basket restituisce tutti i modelli frequenti superiori alla soglia (predefinito: 0,05) delle righe. Ogni modello è rappresentato da una riga nei risultati.

La prima colonna è l'ID segmento. Le due colonne successive indicano il numero e la percentuale di righe esterne alla query originale acquisite dal modello. Le colonne rimanenti provengono dalla query originale e il rispettivo valore è un valore specifico della colonna o un carattere jolly (per impostazione predefinita null), ovvero valori variabili.

**Argomenti (tutti facoltativi)**

Esempio: `T | evaluate basket_v2([Threshold, WeightColumn, MaxDimensions, CustomWildcard, CustomWildcard, ...])`

Tutti gli argomenti sono facoltativi, ma devono rispettare l'ordine seguente. Per indicare che deve essere usato un valore predefinito, usare il carattere tilde "~" (vedere esempi riportati di seguito).

* Soglia: 0,015 < *double* < 1 (valore predefinito: 0,05) 
  
    Imposta il rapporto minimo di righe da considerare frequenti. I modelli con rapporto inferiore non verranno restituiti.
  
    Esempio: `T | evaluate basket_v2(0.02)`
* colonna di ponderazione *-column_name*
  
    Prende in considerazione ogni riga nell'input in base al peso specificato. Per impostazione predefinita ogni riga ha un peso pari a '1'. L'argomento deve essere un nome di una colonna numerica (ad esempio int, long, real). Una colonna di ponderazione viene in genere usata per tenere in considerazione il campionamento o il bucket/aggregazione dei dati già incorporati in ogni riga.
  
    Esempio: `T | evaluate basket_v2('~', sample_Count)`
* Dimensioni massime: 1 < *int* (valore predefinito: 5)
  
    Imposta il numero massimo di dimensioni non correlate per basket, limitato per impostazione predefinita per diminuire il runtime di query.

    Esempio: `T | evaluate basket_v2('~', '~', 3)`
* Tipi di carattere jolly personalizzato: *qualsiasi valore per tipo*
  
    Imposta il valore del carattere jolly per un tipo specifico nella tabella dei risultati, che indica che il criterio corrente non prevede una restrizione su questa colonna. Il valore predefinito è null, mentre per le stringhe il valore predefinito è una stringa vuota. Se il valore predefinito è un valore usabile nei dati, deve essere usato un valore diverso per il carattere jolly (ad esempio *).

    Esempio: `T | evaluate basket_v2('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**esempi**

``` 
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket_v2(0.2)
```
Risultati

|SegmentId|Numero|Percentuale|Stato|EventType|Danni|DamageCrops
----------|-----|-------|-----|---------|------|-----------
0|4574|77.7|||NO|0
1|2278|38.7||Grandine|NO|0
2|5675|96.4||||0
3|2371|40.3||Grandine||0
4|1279|21.7||Vento di tempesta||0
5|2468|41.9||Grandine|||
6|1310|22.3|||SÌ||
7|1291|21.9||Vento di tempesta||

Esempio con caratteri jolly personalizzati
```
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket_v2(0.2, '~', '~', '*', int(-1))
```
Risultati

|SegmentId|Numero|Percentuale|Stato|EventType|Danni|DamageCrops
----------|-----|-------|-----|---------|------|-----------
0|4574|77.7|\*|\*|NO|0
1|2278|38.7|\*|Grandine|NO|0
2|5675|96.4|\*|\*|\*|0
3|2371|40.3|\*|Grandine|\*|0
4|1279|21.7|\*|Vento di tempesta|\*|0
5|2468|41.9|\*|Grandine|\*|-1|
6|1310|22.3|\*|\*|SÌ|-1|
7|1291|21.9|\*|Vento di tempesta|\*|-1|

#### <a name="evaluate-diffpatterns-deprecated"></a>evaluate diffpatterns (deprecato)
**Questa versione del plug-in diffpatterns è deprecata. Usare la sintassi del nuovo plug-in [diffpatterns](#evaluate-diffpatterns_v2).**
richieste | evaluate diffpatterns("split=success")

Diffpatterns identifica le differenze tra due set di dati della stessa struttura, ad esempio il log delle richieste al momento di un evento imprevisto e i log delle richieste normali. Diffpatterns è stato sviluppato per semplificare l'analisi degli errori, ad esempio confrontando gli errori con i non errori in un determinato intervallo di tempo, ma può potenzialmente trovare differenze tra qualsiasi set di dati della stessa struttura. 

**Sintassi**

`T | evaluate diffpatterns("split=` *BinaryColumn* `" [, arguments] )`

**Restituisce**

Diffpatterns restituisce un set di modelli, in genere di piccole dimensioni, che acquisiscono diverse parti dei dati nei due set, ovvero un modello che acquisisce una percentuale elevata delle righe nel primo set di dati e una percentuale ridotta delle righe nel secondo set di dati. Ogni modello è rappresentato da una riga nei risultati.

Le prime quattro colonne indicano il numero e la percentuale di righe della query originale che vengono acquisite dal modello in ogni set. La quinta colonna indica la differenza tra i due set, in punti percentuale assoluti. Le colonne rimanenti provengono dalla query originale e il rispettivo valore è un valore specifico della colonna oppure *, ovvero valori variabili. 

Si noti che i modelli non sono distinti: è possibile che si sovrappongano e in genere non comprendono tutte le righe originali. È possibile che alcune righe non rientrino in alcun modello.

**Suggerimenti**

* Usare where e project nella pipeline di input per ridurre i dati ai soli dati rilevanti.
* Quando si trova una riga interessante, è possibile che si voglia visualizzarne i dettagli, aggiungendo i rispettivi valori specifici al filtro where.

**Argomenti**

* `split=` *nome colonna* (obbligatorio)
  
    La colonna deve avere esattamente due valori. Se necessario, creare questa colonna:
  
    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`
* `target=` *string*
  
    Indica all'algoritmo di cercare solo i modelli con percentuale più elevata nel set di dati di destinazione. La destinazione deve essere uno dei due valori della colonna suddivisa.
  
    `requests | evaluate diffpatterns("split=success", "target=false")`
* `threshold=` *0,015<double<1* (valore predefinito: 0,05) 
  
    Imposta la differenza minima tra modelli (rapporto) tra i due set.
  
    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`
* `output=minimize | all`
  
    Formato dei risultati. Le colonne Count e Percent vengono sempre visualizzate nei risultati. 
  
  * `minimize` : consente di filtrare solo le colonne con '*' nei risultati.
  * `all` : vengono restituite tutte le colonne dell'input.
* `weight_column=` *column_name*
  
    Prende in considerazione ogni riga nell'input in base al peso specificato. Per impostazione predefinita ogni riga ha un peso pari a '1'. Una colonna di ponderazione viene in genere usata per tenere in considerazione il campionamento o il bucket/aggregazione dei dati già incorporati in ogni riga.
  
    `requests | evaluate autocluster("weight_column=itemCount")`

<a name="evaluate-diffpatterns_v2"></a>
#### <a name="evaluate-diffpatternsv2"></a>evaluate diffpatterns_v2
'T | evaluate diffpatterns_v2(splitColumn)`

Diffpatterns confronta due set di dati della stessa struttura e trova modelli di attributi discreti (dimensioni) che caratterizzano le differenze tra i due set di dati. Diffpatterns è stato sviluppato per semplificare l'analisi degli errori, ad esempio confrontando gli errori con i non errori in un determinato intervallo di tempo, ma può potenzialmente trovare differenze tra qualsiasi set di dati della stessa struttura. L'algoritmo Diffpatterns è stato sviluppato dal team di ricerca Developer Analytics (KustoML@microsoft.com).

Questo plug-in sostituisce la sintassi del plug-in diffpatterns deprecato.

**Sintassi**

`T | evaluate diffpatterns_v2(SplitColumn, SplitValueA, SplitValueB [, arguments] )`

**Restituisce**

Diffpatterns restituisce un set di modelli, in genere di piccole dimensioni, che acquisiscono diverse parti dei dati nei due set, ovvero un modello che acquisisce una percentuale elevata delle righe nel primo set di dati e una percentuale ridotta delle righe nel secondo set di dati. Ogni modello è rappresentato da una riga nei risultati.
La prima colonna è l'ID segmento. Le quattro colonne seguenti indicano il numero e la percentuale di righe della query originale acquisite dal modello in ogni set. La sesta colonna indica la differenza tra i due set, in punti percentuale assoluti. Le colonne rimanenti provengono dalla query originale.
Per ogni modello, le colonne non impostate nel modello, ovvero senza restrizione su un valore specifico, conterranno un carattere jolly null per impostazione predefinita (vedere la sezione Argomenti di seguito per indicazioni su come modificare manualmente i caratteri jolly).
Si noti che i modelli non sono distinti: è possibile che si sovrappongano e in genere non comprendono tutte le righe originali. È possibile che alcune righe non rientrino in alcun modello.

**Suggerimenti**

Usare where e project nella pipeline di input per ridurre i dati ai soli dati rilevanti.
Quando si trova una riga interessante, è possibile che si voglia visualizzarne i dettagli, aggiungendo i rispettivi valori specifici al filtro `where` .

**Argomenti obbligatori**

`T | evaluate diffpatterns_v2(SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...])` 

- SplitColumn - *column_name* 

Indica all'algoritmo come dividere la query in set di dati. In base ai valori specificati per gli argomenti SplitValueA e SplitValueB (vedere di seguito), l'algoritmo suddivide la query in due set di dati, "A" e "B", e analizza le differenze tra di essi. Di conseguenza, la colonna di divisione deve avere almeno due valori distinti.

- SplitValueA - *string*

Rappresentazione in forma di stringa di uno dei valori in SplitColumn che è stato specificato. Tutte le righe per le quali questo valore è presente in SplitColumn sono considerate il set di dati "A".

- SplitValueB - *string*

Rappresentazione in forma di stringa di uno dei valori in SplitColumn che è stato specificato. Tutte le righe per le quali questo valore è presente in SplitColumn sono considerate il set di dati "B".

**Esempio**

```
T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure")
```
**Argomenti facoltativi**

Tutti gli argomenti sono facoltativi, ma devono essere ordinati come riportato di seguito. Per indicare che è necessario usare il valore predefinito, usare il carattere tilde della stringa "~" (vedere gli esempi di seguito).

- WeightColumn - *column_name*

Prende in considerazione ogni riga nell'input in base al peso specificato. Per impostazione predefinita ogni riga ha un peso pari a '1'. L'argomento deve essere un nome di una colonna numerica (ad esempio int, long, real). Una colonna di ponderazione viene in genere usata per tenere in considerazione il campionamento o il bucket/aggregazione dei dati già incorporati in ogni riga.

**Esempio**
```
T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure", sample_Count)
```
- Soglia: 0,015 < double < 1 [predefinito: 0,05]

Imposta la differenza minima tra modelli (rapporto) tra i due set.

**Esempio**
```
T | extend splitColumn = iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure", "~", 0.04)
```
- MaxDimensions - 0 < int [predefinito: unlimited]

Imposta il numero massimo di dimensioni non correlate per il modello di risultato, specificando un limite si riduce il runtime della query.

**Esempio**
```
T | extend splitColumn = iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure", "~", "~", 3)
```
- CustomWildcard - *any_value_per_type*

Imposta il valore del carattere jolly per un tipo specifico nella tabella dei risultati, che indica che il criterio corrente non prevede una restrizione su questa colonna. Il valore predefinito è null, mentre per le stringhe il valore predefinito è una stringa vuota. Se il valore predefinito è un valore usabile nei dati, deve essere usato un valore diverso per il carattere jolly (ad esempio *). Ecco un esempio.

**Esempio**
```
T | extend splitColumn = iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns_v2(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))
```

**Esempio**
```
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns_v2(Damage, "0", "1" )
```
**Risultati**

|SegmentId|CountA|CountB|PercentA|PercentB|DiffAB|Stato|EventType|Sorgente|DamageCrops
----------|------|------|--------|--------|------|-----|---------|------|-----------
0|2278|93|49.8|7.1|42.7||Grandine||0
1|779|512|17.03|39.08|22.05||Vento di tempesta|||
2|1098|118|24.01|9.01|15|||Trained Spotter|0|
3|136|158|2.97|12.06|9.09|||Giornale||
4|359|214|7.85|16.34|8.49||Piena improvvisa|||
5|50|122|1.09|9.31|8.22|IOWA||||
6|655|279|14.32|21.3|6.98|||Applicazione della legge||
7|150|117|3.28|8.93|5.65||Alluvione|||
8|362|176|7.91|13.44|5.52|||Responsabile della gestione emergenze||

#### <a name="evaluate-extractcolumns"></a>evaluate extractcolumns
     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns viene usato per arricchire una tabella con più colonne semplici estratte dinamicamente da colonne (semi) strutturate in base al rispettivo tipo. Supporta attualmente solo le colonne JSON, ovvero serializzazioni dinamiche e di stringhe di JSON.

* `max_columns=` *int* (valore predefinito: 10) 
  
    Il numero di nuove colonne aggiunte è dinamico e può essere molto elevato, perché si tratta in effetti del numero di chiavi distinte in tutti i record JSON, quindi deve essere limitato. Le nuove colonne vengono disposte in ordine decrescente in base alla rispettiva frequenza ed è possibile aggiungere fino a max_columns alla tabella.
  
    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`
* `min_percent=` *double* (valore predefinito: 10,0) 
  
    Un altro modo per limitare le nuove colonne consiste nell'ignorare le colonne con frequenza inferiore a min_percent.
  
    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`
* `add_prefix=` *bool* (valore predefinito: true) 
  
    Se true, il nome della colonna complessa verrà aggiunto come prefisso ai nomi delle colonne estratte.
* `prefix_delimiter=` *string* (valore predefinito: "_") 
  
    Se add_prefix=true, questo parametro definisce il delimitatore che verrà usato per concatenare i nomi delle nuove colonne.
  
    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`
* `keep_original=` *bool* (valore predefinito: false) 
  
    Se true, le colonne originali (JSON) verranno mantenute nella tabella di output.
* `output=query | table` 
  
    Formato dei risultati. 
  
  * `table` : l'output corrisponde alla stessa tabella ricevuta, meno le colonne di input specificate, più le nuove colonne estratte dalle colonne di input.
  * `query` : l'output è una stringa che rappresenta la query da creare per ottenere i risultati sotto forma di tabella. 

### <a name="extend-operator"></a>Operatore extend
     T | extend duration = stopTime - startTime

Aggiunge una o più colonne calcolate a una tabella. 

**Sintassi**

    T | extend ColumnName = Expression [, ...]

**Argomenti**

* *T:* tabella di input.
* *ColumnName:* nome delle colonne da aggiungere. I [nomi](#names) fanno distinzione tra maiuscole e minuscole e possono contenere caratteri alfabetici, numerici o "_". Usare `['...']` o `["..."]` per racchiudere tra virgolette parole chiave o nomi con altri caratteri.
* *Expression:* calcolo eseguito sulle colonne esistenti.

**Restituisce**

Una copia della tabella di input, con le colonne aggiuntive specificate.

**Suggerimenti**

* Usare invece [`project`](#project-operator) se si vuole anche eliminare o rinominare alcune colonne.
* Non usare `extend` per ottenere semplicemente un nome più breve da usare in un'espressione lunga. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 
  
    Le colonne native della tabella sono state indicizzate. Il nuovo nome definisce una colonna aggiuntiva non indicizzata, quindi è probabile che la query venga eseguita più lentamente.

**Esempio**

```AIQL
traces
| extend
    Age = now() - timestamp
```

### <a name="find-operator"></a>Operatore find

    find in (Table1, Table2, Table3) where id=="a string"
    find in (Table1, Table2, Table3) where id=="a string" project column1, column2
    find in (Table1, Table2, Table3) where * has "a string"
    find in (Table1, Table2, Table3) where appName in ("string 1", "string 2", "string 3")

Trova le righe corrispondenti a un predicato in un set di tabelle.

**Sintassi**

    find in (Table1, ...) 
    where Predicate 
    [project Column1, ...]

**Argomenti**

* *Table1* Nome di tabella o query. Può essere una tabella definita da let, ma non una funzione. Un nome di tabella offre prestazioni migliori di una query.
* *Predicate* Espressione booleana valutata per ogni riga nelle tabelle specificate.
 * È possibile usare "*" invece di un nome di colonna nei confronti tra stringhe
* *Column1* L'opzione `project` consente di specificare le colonne che devono essere sempre visualizzate nell'output. 

**Risultato**

Per impostazione predefinita, la tabella di output contiene:

* `source_`: indicatore della tabella di origine per ogni riga. Usare [as](#as-operator) alla fine di ogni espressione di tabella, se si vuole specificare il nome visualizzato in questa colonna.
* Colonne menzionate in modo esplicito nel predicato.
* Colonne non vuote comuni a tutte le tabelle di input.
* `pack_`: contenitore delle proprietà che include i dati delle altre colonne.

Si noti che questo formato può cambiare con le modifiche apportate ai dati di input o al predicato. Per specificare un set fisso di colonne, usare `project`.

**Esempio**

Ottenere tutte le richieste e le eccezioni, escluse quelle dei test di disponibilità e dei riavvii:

```AIQL

    find in (requests, exceptions) where isempty(operation_SyntheticSource)
```

Trovare tutte le richieste e le eccezioni dal Regno Unito, escluse quelle dei test di disponibilità e dei riavvii:

```AIQL

    let requk = requests
    | where client_CountryOrRegion == "United Kingdom";
    let exuk = exceptions
    | where client_CountryOrRegion == "United Kingdom";
    find in (requk, exuk) where isempty(operation_SyntheticSource)
```

Trovare i dati di telemetria più recenti in cui qualsiasi campo contiene il termine "test":

```AIQL

    find in (traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions) 
    where * has 'test' 
    | top 100 by timestamp desc
```

**Suggerimenti per incrementare le prestazioni**

* Aggiungere termini con scadenza al predicato `where`.
* Usare clausole `let` invece di scrivere query inline.

### <a name="getschema-operator"></a>operatore getschema

   T | getschema
   
Restituisce una tabella che mostra i nomi e i tipi di colonne della tabella di input.

```AIQL
requests
| project appId, appName, customDimensions, duration, iKey, itemCount, success, timestamp 
| getschema 
```

![Risultati di getschema](./media/app-insights-analytics-reference/getschema.png)

### <a name="join-operator"></a>Operatore join
    Table1 | join (Table2) on CommonColumn

Unisce le righe di due tabelle associando i valori della colonna specificata.

**Sintassi**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argomenti**

* *Table1* : "lato sinistro" del join.
* *Table2* : "lato destro" del join. Può trattarsi di un'espressione di query annidata che restituisce una tabella.
* *CommonColumn* : colonna con lo stesso nome nelle due tabelle.
* *Kind* : specifica come associare le righe delle due tabelle.

**Restituisce**

Una tabella con:

* Una colonna per ogni colonna in ognuna delle due tabelle, incluse le chiavi corrispondenti. Le colonne del lato destro verranno automaticamente rinominate in caso di conflitti tra i nomi.
* Una riga per ogni corrispondenza tra le tabelle di input. Una corrispondenza è una riga selezionata da una tabella che ha lo stesso valore per tutti i campi `on` di una riga nell'altra tabella. 
* `Kind` non specificato o `= innerunique`
  
    Solo una riga del lato sinistro viene associata a ogni valore della chiave `on` . L'output contiene una riga per ogni corrispondenza di questa riga con le righe a destra.
* `kind=inner`
  
     Nell'output è presente una riga per ogni combinazione di righe corrispondenti a sinistra e a destra.
* `kind=leftouter` (oppure `kind=rightouter` o `kind=fullouter`)
  
     Oltre alle corrispondenza interne, è presente una riga per ogni riga a sinistra (e/o a destra), anche se non ha corrispondenze. In tal caso, le celle di output senza corrispondenza contengono valori Null.
* `kind=leftanti`
  
     Restituisce tutti i record del lato sinistro che non hanno corrispondenze a destra. La tabella risultante contiene solo le colonne del lato sinistro. 
* `kind=leftsemi` (o `leftantisemi`)

    Restituisce una riga della tabella a sinistra se è presente (oppure no) una corrispondenza nella tabella a destra. Il risultato non include i dati di destra.


**Suggerimenti**

È previsto un limite di 64 MB per la tabella dei risultati.

Per prestazioni ottimali:

* Usare `where` e `project` per ridurre i numeri di righe e colonne nelle tabelle di input, prima di `join`. 
* Se una tabella è sempre più piccola dell'altra, usarla come lato sinistro (pipe inviata) del join.
* Le colonne per la corrispondenza del join devono avere lo stesso nome. Usare l'operatore project se necessario per rinominare una colonna in una delle tabelle.



**Esempio**

Ottenere le attività estese da un log in cui alcune voci contrassegnano l'inizio e la fine di un'attività. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration=StopTime-StartTime

```


### <a name="limit-operator"></a>Operatore limit
     T | limit 5

Restituisce un numero massimo specificato di righe dalla tabella di input. Non c'è garanzia su quali record verranno restituiti. Per ottenere record specifici, usare [`top`](#top-operator).

**Alias** `take`

**Sintassi**

    T | limit NumberOfRows


**Suggerimenti**

`Take` è un modo semplice ed efficiente per visualizzare un esempio dei risultati quando si lavora in modo interattivo. Tenere presente che non è garantito che vengano generate righe particolari o in un ordine particolare.

Esiste un limite implicito per il numero di righe restituite al client, anche se non si usa `take`. Per aumentare questo limite, usare l'opzione di richiesta client `notruncation` .

### <a name="make-series-operator"></a>operatore make-series

Esegue un'aggregazione. A differenza di [summarize](#summarize-operator), è disponibile una riga di output per ogni gruppo. Nelle colonne dei risultati i valori in ogni gruppo vengono compressi in matrici. 

**Sintassi**

    T | 
    make-series [Column =] Aggregation default = DefaultValue [, ...] 
    on AxisColumn in range(start, stop, step) 
    by [Column =] GroupExpression [, ...]


**Argomenti**

* *Column:* nome facoltativo per una colonna di risultati. Il valore predefinito è un nome derivato dall'espressione.
* *DefaultValue:* se non esiste un riga con valori specifici in AxisColumn e GroupExpression, nei risultati verrà assegnato l'elemento corrispondente della matrice con un DefaultValue. 
* *Aggregation:* un'espressione numerica che usa una [funzione di aggregazione](#aggregations). 
* *AxisColumn:* una colonna in base a cui viene ordinata la serie. Può essere considerata come una sequenza temporale, ma vengono accettati tutti i tipi numerici.
*start, stop, step:* definisce l'elenco dei valori di AxisColumn per ogni riga. Tutte le altre colonne di aggregazione dei risultati hanno una matrice della stessa lunghezza. 
* *GroupExpression:* espressione sulle colonne che fornisce un set di valori distinti. È disponibile una riga nell'output per ogni valore di GroupExpression. In genere è un nome di colonna che fornisce già un set limitato di valori. 

**Suggerimento**

Le matrici di risultati vengono visualizzate in un grafico di analisi nello stesso modo dell'operazione summarize corrispondente.

**Esempio**

```AIQL
requests
| make-series sum(itemCount) default=0, avg(duration) default=0
  on timestamp in range (ago(7d), now(), 1d)
  by client_City
```

![Risultati di make-series](./media/app-insights-analytics-reference/make-series.png)

### <a name="mvexpand-operator"></a>Operatore mvexpand
    T | mvexpand listColumn 

Espande un elenco da una cella dinamicamente tipizzata (JSON) in modo che ogni voce abbia una riga separata. Tutte le altre celle in una riga espansa vengono duplicate. 

Vedere anche [`summarize makelist`](#summarize-operator) che esegue la funzione opposta.

**Esempio**

Si supponga che la tabella di input sia:

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |[0,1,"k","v"] |

    mvexpand D

Il risultato è:

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |0 |
| 2 |"world" |1 |
| 2 |"world" |"k" |
| 2 |"world" |"v" |

**Sintassi**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Argomenti**

* *ColumnName:* nel risultato le matrici nella colonna denominata vengono espanse su più righe. 
* *ArrayExpression:* espressione che supporta una matrice. Se viene usato questo formato, viene aggiunta una nuova colonna e quella esistente viene mantenuta.
* *Name:* nome della nuova colonna.
* *Typename:* esegue il cast dell'espressione espansa in un tipo particolare
* *RowLimit:* numero massimo di righe generate da ogni riga originale. Il valore predefinito è 128.

**Restituisce**

Più righe per ogni valore in una matrice nella colonna denominata o nell'espressione di matrice.

La colonna espansa ha sempre il tipo dinamico. Usare un cast come `todatetime()` o `toint()` per calcolare o aggregare i valori.

Sono supportate due modalità di espansione dei contenitori delle proprietà:

* `bagexpansion=bag`: i contenitori delle proprietà vengono espansi in contenitori delle proprietà a voce singola. Questa è l'espansione predefinita.
* `bagexpansion=array`: i contenitori delle proprietà vengono espansi in strutture di matrici a due elementi `[`*key*`,`*value*`]`, che consentono l'accesso uniforme alle chiavi e ai valori oltre ad eseguire, ad esempio, un'aggregazione di conteggi di valori univoci sui nomi delle proprietà.

**esempi**

    exceptions | take 1
    | mvexpand details[0]

Divide il record di un'eccezione in righe per ogni elemento nel campo dei dettagli.

### <a name="parse-operator"></a>Operatore parse
    T | parse "I got 2 socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *

    T |  parse kind=regex "I got socks for my 63rd birthday"
    with "(I|She) got " present " for .*?" year:long *

Estrae i valori da una stringa. È possibile usare la corrispondenza con espressioni semplici o regolari.

**Sintassi**

    T | parse [kind=regex|relaxed] SourceText
        with [Match | Column [: Type [*]] ]  ...

**Argomenti**

* `T`: tabella di input.
* `kind`: 
  * `simple` (valore predefinito): le stringhe `Match` sono normali stringhe.
  * `relaxed`: se il testo non viene analizzato come tipo di una colonna, la colonna viene impostata su null e l'analisi continua 
  * `regex`: le stringhe `Match` sono espressioni regolari.
* `Text`: colonna o altra espressione che restituisce una stringa o può essere convertita in una stringa.
* *Match:* trova la corrispondenza nella parte successiva della stringa e la rimuove.
* *Column:* assegna la parte successiva della stringa a questa colonna. Se non esiste, la colonna viene creata.
* *Type:* analizza la parte successiva della stringa come tipo specificato, ad esempio int, date, double. 

**Restituisce**

La tabella di input, estesa in base all'elenco di colonne.

Gli elementi nella clausola `with` vengono confrontati a loro volta con il testo di origine. Ogni elemento usa un blocco del testo di origine: 

* Una stringa letterale o un'espressione regolare sposta il cursore corrispondente in base alla lunghezza della corrispondenza.
* In un'analisi regex un'espressione regolare può usare l'operatore di riduzione "?" per passare il prima possibile alla corrispondenza seguente.
* Un nome di colonna con un tipo analizza il testo come tipo specificato. A meno che kind=relaxed, un'analisi non riuscita invalida la corrispondenza con l'intero schema.
* Un nome di colonna senza un tipo o con il tipo "string", copia il numero minimo di caratteri per passare alla corrispondenza seguente.
* "*" ignora il numero minimo di caratteri per passare alla corrispondenza seguente. È possibile usare "*" all'inizio e alla fine dello schema, dopo un tipo diverso da string oppure tra corrispondenze di stringa.

Tutti gli elementi in uno schema di analisi devono corrispondere correttamente. In caso contrario, non verrà generato alcun risultato. L'eccezione a questa regola è che, quando kind=relaxed, se l'analisi di una variabile tipizzata non riesce, il resto dell'analisi continua.

**esempi**

*Simple:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

| x | counter | present | Year |
| --- | --- | --- | --- |
| 1 |2 |socks |63 |

*Relaxed:*

Quando l'input contiene una corrispondenza corretta per ogni colonna tipizzata, un'analisi ridotta genera gli stessi risultati di un'analisi semplice. Se però una delle colonne tipizzate non viene analizzata correttamente, un'analisi ridotta continua a elaborare il resto dello schema, mentre un'analisi semplice si arresta e non riesce a generare risultati.

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


| x | present | Year |
| --- | --- | --- |
| 1 |socks |63 |

*Regex:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 07:31, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 06:20:00 ) }" 
// Parse it:
| parse kind=regex s 
  with ".*?=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       @".*\)" *
| project-away x, s
```

| resource | slice | lock | release | previous |
| --- | --- | --- | --- | --- |
| Utilità di pianificazione |16 |02/17/2016 07:31:00 |02/17/2016 08:41 |2016-02-17T06:20:00Z |

### <a name="project-operator"></a>Operatore project
    T | project cost=price*quantity, price

Selezionare le colonne da includere, rinominare o rimuovere e inserire le nuove colonne calcolate. L'ordine delle colonne nel risultato viene specificato dall'ordine degli argomenti. Solo le colonne specificate negli argomenti vengono incluse nel risultato, mentre le altre nell'input vengono rimosse.  Vedere anche `extend`.

**Sintassi**

    T | project ColumnName [= Expression] [, ...]

**Argomenti**

* *T:* tabella di input.
* *ColumnName:* nome di una colonna da visualizzare nell'output. Se *Expression*non è presente, deve essere visualizzata una colonna con quel nome nell'input. I [nomi](#names) fanno distinzione tra maiuscole e minuscole e possono contenere caratteri alfabetici, numerici o "_". Usare `['...']` o `["..."]` per racchiudere tra virgolette parole chiave o nomi con altri caratteri.
* *Expression:* espressione scalare facoltativa che fa riferimento alle colonne di input. 
  
    È consentito restituire una nuova colonna calcolata con lo stesso nome di una colonna esistente nell'input.

**Restituisce**

Una tabella che ha le colonne denominate come argomenti e lo stesso numero di righe della tabella di input.

**Esempio**

L'esempio seguente illustra diverse tipologie di manipolazioni che possono essere eseguite usando l'operatore `project` . La tabella di input `T` include tre colonne di tipo `int`: `A`, `B` e `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>Operatore project-away
    T | project-away column1, column2, ...

Escludere le colonne specificate. Il risultato contiene tutte le colonne di input ad eccezione di quelle denominate.

### <a name="range-operator"></a>Operatore range
    range LastWeek from ago(7d) to now() step 1d

Genera una tabella a colonna singola di valori. Si noti che non deve necessariamente avere un input da pipeline. 

| LastWeek |
| --- |
| 2015-12-05 09:10:04.627 |
| 2015-12-06 09:10:04.627 |
| ... |
| 2015-12-12 09:10:04.627 |

**Sintassi**

    range ColumnName from Start to Stop step Step

**Argomenti**

* *ColumnName:* nome della colonna singola nella tabella di output.
* *Start:* il valore più basso nell'output.
* *Stop:* il valore più alto che viene generato nell'output o un limite per il valore più alto, se *step* supera questo valore.
* *Step:* la differenza tra due valori consecutivi. 

Gli argomenti devono essere valori numerici, date o intervalli di tempo. Non possono fare riferimento alle colonne di nessuna tabella. Per calcolare l'intervallo basato su una tabella di input, usare la [funzione *range*](#range), ad esempio con l'[operatore mvexpand](#mvexpand-operator). 

**Restituisce**

Una tabella con una sola colonna denominata *ColumnName*, i cui valori sono *Start*, *Start* + *Step* e così via fino a includere *Stop*.

**Esempio**  

```AIQL
range Steps from 1 to 8 step 3
```

Una tabella con una sola colonna denominata `Steps` il cui tipo è `long` e i cui valori sono `1`, `4` e `7`.

**Esempio**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Una tabella relativa alla mezzanotte degli ultimi sette giorni. La funzione bin (floor) riduce ogni ora all'inizio del giorno.

**Esempio**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Illustra come l'operatore `range` possa essere usato per creare una piccola tabella di dimensioni ad hoc che viene quindi usata per introdurre zeri dove i dati di origine sono senza valori.

### <a name="reduce-operator"></a>Operatore reduce
    exceptions | reduce by outerMessage

Cerca di raggruppare record simili. Per ogni gruppo l'operatore restituisce l'oggetto `Pattern` che descrive meglio il gruppo e l'oggetto `Count` dei record del gruppo.

![](./media/app-insights-analytics-reference/reduce.png)

**Sintassi**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argomenti**

* *ColumnName:* la colonna da esaminare. Deve essere di tipo stringa.
* <seg>
  *Threshold:* un valore compreso nell'intervallo {0..1}.</seg> Il valore predefinito è 0.001. Per gli input di grandi dimensioni, la soglia deve essere bassa. 

**Restituisce**

Due colonne, `Pattern` e `Count`. In molti casi, Pattern sarà un valore completo della colonna. In alcuni casi, può identificare termini comuni e sostituire le parti variabili con "*".

Ad esempio, il risultato di `reduce by city` può includere: 

| Modello | Numero |
| --- | --- |
| San * |5182 |
| Saint * |2846 |
| Moscow |3726 |
| \* -on- \* |2730 |
| Parigi |27163 |

### <a name="render-directive"></a>Direttiva render
    T | render [ table | timechart  | barchart | piechart | areachart | scatterchart ] 
        [kind= default|stacked|stacked100|unstacked]

Indica al livello di presentazione la modalità di visualizzazione della tabella. Deve essere l'ultimo elemento della pipe. Si tratta di una valida alternativa all'uso dei controlli su schermo che consente di salvare una query con un metodo di presentazione specifico.

Per alcuni tipi di grafico, `kind` rende disponibili altre opzioni. Un grafico a barre `stacked` segmenta ad esempio ogni barra in base a una dimensione scelta, mostrando il contributo al totale dei diversi valori della dimensione. In un grafico `stacked100` ogni barra ha un'altezza equivalente, pari a 100%, in modo che sia possibile confrontare i relativi contributi.


### <a name="restrict-clause"></a>Clausola restrict
Specifica il set di nomi di tabelle disponibili per gli operatori che seguono. Ad esempio:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sample-operator"></a>Operatore sample

Restituisce righe casuali distribuite uniformemente dalla tabella di input.


**Sintassi**

    T | sample NumerOfRows

* *NumberOfRows* Il numero di righe da restituire nel campione.

**Suggerimento**

Usare `Take` quando non è necessario un campione distribuito in modo uniforme.


### <a name="sample-distinct-operator"></a>Operatore sample-distinct

Restituisce una singola colonna che contiene fino al numero specificato di valori distinti della colonna richiesta. Non restituisce attualmente un campione equamente distribuito.

**Sintassi**

    T | sample-distinct NumberOfValues of ColumnName

* *NumberOfValues* La lunghezza della tabella desiderata.
* *ColumnName* La colonna desiderata.

**Suggerimenti**

Può essere utile campionare una popolazione inserendo sample-distinct in un'istruzione let e filtrando in un secondo momento tramite l'operatore in (vedere l'esempio).
 
Se si vuole estrarre i primi valori anziché solo un campione, è possibile usare l'operatore top-hitters.

Se si vuole campionare le righe di dati (anziché i valori di una specifica colonna), vedere l'[operatore sample](#sample-operator).

**Esempio**

Campionare una popolazione ed eseguire ulteriori calcoli sapendo che il riepilogo non supererà i limiti della query.

```AIQL
let sampleops = toscalar(requests | sample-distinct 10 of OperationName);
requests | where OperationName in (sampleops) | summarize total=count() by OperationName
```
### <a name="search-operator"></a>operatore search

Ricercare le stringhe in più tabelle e colonne.

**Sintassi**

    search [kind=case_sensitive] [in (TableName, ...)] SearchToken

    T | search [kind=case_sensitive] SearchToken

    search [kind=case_sensitive] [in (TableName, ...)] SearchPredicate

    T | search [kind=case_sensitive] SearchPredicate

Trova le occorrenze della stringa di token specificata in qualsiasi colonna di qualsiasi tabella.
 
* *TableName* nome di una tabella definita a livello globale (richieste, eccezioni,...) o da un [clausola let](#let-clause). È possibile usare i caratteri jolly come r*.
* *SearchToken:* una stringa token, che deve corrispondere a una parola intera. È possibile usare caratteri jolly finali. "Amster*" corrisponde ad "Amsterdam", ma non ad "Amster".
* *SearchPredicate:* un'espressione booleana sulle colonne nelle tabelle. È possibile usare "*" come carattere jolly nei nomi di colonna.

**esempi**

```AIQL
search "Amster*"  //All columns, all tables

search name has "home"  // one column

search * has "home"     // all columns

search in (requests, exceptions) "Amster*"  // two tables

requests | search "Amster*"

requests | search name has "home"

```




### <a name="sort-operator"></a>Operatore sort
    T | sort by country asc, price desc

Ordina le righe della tabella input in base a una o più colonne.

**Alias** `order`

**Sintassi**

    T  | sort by Column [ asc | desc ] [ , ... ]

**Argomenti**

* *T:* input della tabella da ordinare.
* *Column:* colonna di *T* in base alla quale eseguire l'ordinamento. Il tipo dei valori deve essere numerico, di data, intervallo di tempo o stringa.
* `asc` ordina in senso crescente, dal basso verso l'alto. Il valore predefinito è `desc`, ovvero decrescente dall'alto verso il basso.

**Esempio**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Tutte le righe della tabella Traces con uno specifico `ActivityId`, ordinate in base al timestamp.

### <a name="summarize-operator"></a>Operatore summarize
Produce una tabella che aggrega il contenuto della tabella di input.

    requests
    | summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Tabella che indica il numero, la durata media della richiesta e il set di città in ogni paese. Nell'output è presente una riga per ogni singolo paese. Le colonne di output indicano il conteggio, la durata media, le città e il paese. Tutte le altre colonne di input vengono ignorate.

    T | summarize count() by price_range=bin(price, 10.0)

Tabella che indica quanti elementi presentano prezzi in ogni intervallo [0,10.0], [10.0,20.0] e così via. Questo esempio ha una colonna per il conteggio e una per l'intervallo di prezzi. Tutte le altre colonne di input vengono ignorate.

**Sintassi**

    T | summarize
         [  [ Column = ] Aggregation [ , ... ] ]
         [ by
            [ Column = ] GroupExpression [ , ... ] ]

**Argomenti**

* *Column:* nome facoltativo per una colonna di risultati. Il valore predefinito è un nome derivato dall'espressione. I [nomi](#names) fanno distinzione tra maiuscole e minuscole e possono contenere caratteri alfabetici, numerici o "_". Usare `['...']` o `["..."]` per racchiudere tra virgolette parole chiave o nomi con altri caratteri.
* *Aggregation:* chiamata a una funzione di aggregazione, ad esempio `count()` o `avg()`, con nomi di colonna come argomenti. Vedere [aggregazioni](#aggregations).
* *GroupExpression:* espressione sulle colonne che fornisce un set di valori distinti. Si tratta in genere di un nome di colonna che fornisce già un set di valori limitato oppure di `bin()` con una colonna numerica o di data e ora come argomento. 

Se si specifica un'espressione numerica o di data e ora senza usare `bin()`, l'analisi applica automaticamente l'espressione con un intervallo di `1h` per i valori di data/ora o `1.0` per i numeri.

Se non si specifica *GroupExpression* , l'intera tabella viene riepilogata in un'unica riga di output.

**Restituisce**

Le righe di input vengono disposte in gruppi con gli stessi valori delle espressioni `by` . Vengono quindi calcolate per ogni gruppo le funzioni di aggregazione specificate, generando una riga per ogni gruppo. Il risultato contiene le colonne `by` , oltre ad almeno una colonna per ogni aggregazione calcolata. Alcune funzioni di aggregazione restituiscono più colonne.

Il risultato contiene una riga per ogni singola combinazione di valori `by` . Per riepilogare gli intervalli di valori numerici, usare `bin()` per ridurre gli intervalli a valori discreti.

> [!NOTE]
> Anche se è possibile specificare espressioni arbitrarie per le espressioni di aggregazione e raggruppamento, è preferibile usare nomi di colonna semplici o applicare `bin()` a una colonna numerica.

### <a name="table-operator"></a>operatore table

    table('pageViews')

La tabella denominata nella stringa di argomento.

**Sintassi**

    table(tableName)

**Argomenti**

* *tableName:* una stringa. Il nome di una tabella che può essere statico o il risultato di una clausola let.

**esempi**

    table('requests');


    let size = (tableName: string) {
        table(tableName) | summarize sum(itemCount)
    };
    size('pageViews');



### <a name="take-operator"></a>Operatore take
Alias di [limit](#limit-operator)

### <a name="top-operator"></a>Operatore top
    T | top 5 by Name desc nulls first

Restituisce i primi *N* record ordinati in base alle colonne specificate.

**Sintassi**

    T | top NumberOfRows by Sort_expression [ asc | desc ] [nulls first|nulls last] [, ... ]

**Argomenti**

* *NumberOfRows:* numero di righe *T* da restituire.
* *Sort_expression:* espressione in base alla quale ordinare le righe. In genere è semplicemente un nome di colonna. È possibile specificare più di un elemento sort_expression.
* `asc` o `desc` (valore predefinito) può essere visualizzato per controllare se la selezione nell'intervallo è effettivamente in ordine crescente o decrescente.
* Controlli `nulls first` o `nulls last` in cui sono presenti valori null. `First` è il valore predefinito per `asc`, `last` è il valore predefinito per `desc`.

**Suggerimenti**

`top 5 by name` apparentemente equivale a `sort by name | take 5`, ma viene eseguito più rapidamente e restituisce sempre risultati ordinati, mentre `take` non offre questo tipo di garanzia.

### <a name="top-nested-operator"></a>Operatore top-nested
    requests
    | top-nested 5 of name by count()
    , top-nested 3 of performanceBucket by count()
    , top-nested 3 of client_CountryOrRegion by count()
    | render barchart

Restituisce i risultati in una gerarchia in cui ogni livello è un drill-down del livello precedente. È utile per rispondere a domande come: "Quali sono le prime 5 richieste e, per ognuna di esse, quali sono i 3 bucket di prestazione principali e, per ognuno di essi, quali sono i primi 3 paesi da cui provengono le richieste?"

**Sintassi**

   T | top-nested N of COLUMN by AGGREGATION [, ...]

**Argomenti**

* N:int: numero di righe da restituire o passare al livello successivo. In una query con tre livelli, dove N è 5, 3 e 3, il numero totale di righe sarà 45.
* COLUMN:  - colonna di raggruppamento per l'aggregazione. 
* AGGREGATION: [funzione di aggregazione](#aggregations) da applicare a ogni gruppo di righe. I risultati delle aggregazioni determineranno i primi gruppi da visualizzare.

### <a name="union-operator"></a>Operatore union
     Table1 | union Table2, Table3

Considera due o più tabelle e restituisce le righe di tutte. 

**Sintassi**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argomenti**

* *Table1*, *Table2* ...
  * Il nome di una tabella, ad esempio `requests`, o una tabella definita in una [clausola let](#let-clause).
  * Un'espressione di query, ad esempio `(requests | where success=="True")`
  * Un set di tabelle specificato con un carattere jolly. Ad esempio, `e*` forma l'unione di tutte le tabelle definite nelle clausole let precedenti il cui nome inizia per "e", insieme alla tabella "exceptions".
* `kind`: 
  * `inner` : il risultato include il subset di colonne comuni a tutte le tabelle di input.
  * `outer` : il risultato include tutte le colonne presenti in tutti gli input. Le celle non definite da una riga di input vengono impostate su `null`.
* `withsource=`*ColumnName:* se specificato, l'output includerà una colonna denominata *ColumnName* il cui valore indica quale tabella di origine ha contribuito per ogni riga. Usare [as](#as-operator) alla fine di ogni espressione di tabella, se si vuole specificare il nome visualizzato in questa colonna.

**Restituisce**

Una tabella con un numero di righe pari a quelle presenti in tutte le tabelle di input e un numero di colonne pari al numero di nomi di colonna univoci negli input.

Non è garantito l'ordinamento delle righe.


**Esempio**

Numero di utenti distinti che hanno generato un evento `exceptions` o un evento `traces` nelle 12 ore precedenti. Nel risultato la colonna "SourceTable" indicherà "exceptions" o "traces":

```AIQL
    
    union withsource=SourceTable kind=outer exceptions, traces
    | where timestamp > ago(12h)
    | summarize dcount(user_Id) by SourceTable
```

Questa versione più efficiente genera lo stesso risultato. Filtra ogni tabella prima di creare l'unione:

```AIQL
    exceptions
    | where timestamp > ago(24h) | as exceptions
    | union withsource=SourceTable kind=outer (requests | where timestamp > ago(12h) | as traces)
    | summarize dcount(user_Id) by SourceTable 
```

Usare [as](#as-operator) per specificare il nome che verrà visualizzato nella colonna di origine.

#### <a name="forcing-an-order-of-results"></a>Forzare l'ordine dei risultati

L'unione non garantisce un ordinamento specifico delle righe di risultati.
Per ottenere lo stesso ordine ogni volta che si esegue la query, aggiungere una colonna di tag a ogni tabella di input:

    let r1 = (traces | count | extend tag = 'r1');
    let r2 = (requests | count| extend tag = 'r2');
    let r3 = (pageViews | count | extend tag = 'r3');
    r1 | union r2,r3 | sort by tag

#### <a name="see-also"></a>Vedere anche

Considerare l'[operatore join](#join-operator) come alternativa.

### <a name="where-operator"></a>Operatore where
     requests | where resultCode=="200"

Filtra una tabella per trovare il subset di righe che soddisfano un predicato.

**Alias** `filter`

**Sintassi**

    T | where Predicate
    T | where * has Term

**Argomenti**

* *T* : input tabulare i cui record devono essere filtrati.
* *Predicate:* `boolean` [espressione](#boolean) sulle colonne di *T*. Viene valutato per ogni riga in *T*.
* *Term*: stringa che deve corrispondere a una parola intera in una colonna.

**Restituisce**

Righe in *T* per cui *Predicate* è `true`.

**Suggerimenti**

Per ottenere prestazioni ottimali:

* **Usare confronti semplici** tra i nomi di colonna e le costanti. Con "costante" si intende costante nella tabella, quindi `now()` e `ago()` sono validi e sono dunque valori scalari assegnati con una [clausola `let`](#let-clause).
  
    Ad esempio, preferire `where Timestamp >= ago(1d)` a `where floor(Timestamp, 1d) == ago(1d)`.
* **Prima i termini più semplici**: se sono presenti più clausole unite con `and`, inserire prima le clausole che coinvolgono una sola colonna. Quindi `Timestamp > ago(1d) and OpId == EventId` è meglio del contrario.

**Esempio**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Record risalenti a non più di 1 ora fa e provenienti dall'elemento Source denominato "Kuskus" e con due colonne dello stesso valore. 

Si noti che il confronto tra due colonne viene inserito per ultimo, perché non riesce a utilizzare l'indice e forza un'analisi.



## <a name="aggregations"></a>Aggregazioni
Le aggregazioni sono funzioni usate per combinare i valori in gruppi creati nell' [operazione di riepilogo](#summarize-operator). In questa query, ad esempio, dcount() è una funzione di aggregazione:

    requests | summarize dcount(name) by success

### <a name="any"></a>qualsiasi
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

<a name="argmin"></a>
<a name="argmax"></a>

### <a name="argmin-argmax"></a>argmin, argmax
    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Trova una riga del gruppo che riduce al minimo o aumenta al massimo *ExprToMaximize* e restituisce il valore di *ExprToReturn* (o `*` per restituire l'intera riga).

**Suggerimento**: le colonne pass-through vengono rinominate automaticamente. Per verificare di usare i nomi corretti, esaminare i risultati usando `take 5` prima di inviare pipe dei risultati in un altro operatore.

**esempi**

Per ogni nome di richiesta, indicare quando si è verificata la richiesta più lunga:

    requests | summarize argmax(duration, timestamp) by name

Mostrare tutti i dettagli della richiesta più lunga, non solo il timestamp:

    requests | summarize argmax(duration, *) by name


Trovare il valore più basso di ogni metrica, con timestamp e altri dati:

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)

### <a name="avg"></a>avg
    avg(Expression)

Calcola la media di *Expression* all'interno del gruppo.

### <a name="buildschema"></a>buildschema
    buildschema(DynamicExpression)

Restituisce lo schema minimo che ammette tutti i valori di *DynamicExpression*. 

Il tipo di colonna dei parametri deve essere `dynamic` : un contenitore di matrici o proprietà. 

**Esempio**

    exceptions | summarize buildschema(details)

Risultato:

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer": 
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

|  |
| --- |
| `{"x":1, "y":3.5}` |
| `{"x":"somevalue", "z":[1, 2, 3]}` |
| `{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}` |

Lo schema risultante sarà:

    {
      "x":["int", "string"],
      "y":["double", {"w": "string"}],
      "z":{"indexer": ["int", "string"]},
      "t":{"indexer": "string"}
    }

Lo schema indica che:

* L'oggetto radice è un contenitore con quattro proprietà denominate x, y, z e t.
* La proprietà denominata "x" può essere di tipo "int" o "string".
* La proprietà denominata "y" può essere di tipo "double" o un altro contenitore con una proprietà denominata "w" di tipo "string".
* La parola chiave ``indexer`` indica che "z" e "t" sono matrici.
* Ogni elemento della matrice "z" è di tipo int o string.
* "t" è una matrice di stringhe.
* Ogni proprietà è facoltativa in modo implicito e qualsiasi matrice può essere vuota.

##### <a name="schema-model"></a>Modello di schema
La sintassi dello schema restituito è:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"indexer"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Equivalgono a un subset di annotazioni di tipo TypeScript, codificato come valore dinamico. In Typescript, lo schema di esempio sarebbe:

    var someobject:
    {
      x?: (number | string),
      y?: (number | { w?: string}),
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string }
    }


### <a name="count"></a>count
    count([ Predicate ])

Restituisce un conteggio delle righe per il quale *Predicate* restituisce `true`. Se *Predicate* non viene specificato, restituisce il numero totale di record del gruppo. 

**Suggerimento per le prestazioni**: usare `summarize count(filter)` invece di `where filter | summarize count()`

> [!NOTE]
> Evitare di usare count() per trovare i numeri di richieste, eccezioni o altri eventi che si sono verificati. Quando [sampling](app-insights-sampling.md) è attivo, il numero di punti dati conservato in Application Insights sarà minore del numero di eventi effettivi. Usare invece `summarize sum(itemCount)...`. La proprietà itemCount indica il numero di eventi originali rappresentati da ogni punto dati mantenuto.
> 
> 

### <a name="countif"></a>countif
    countif(Predicate)

Restituisce un conteggio delle righe per il quale *Predicate* restituisce `true`.

**Suggerimento per le prestazioni**: usare `summarize countif(filter)` invece di `where filter | summarize count()`

> [!NOTE]
> Evitare di usare countif() per trovare i numeri di richieste, eccezioni o altri eventi che si sono verificati. Quando [sampling](app-insights-sampling.md) è attivo, il numero di punti dati sarà minore del numero di eventi effettivi. Usare invece `summarize sum(itemCount)...`. La proprietà itemCount indica il numero di eventi originali rappresentati da ogni punto dati mantenuto.
> 
> 

### <a name="dcount"></a>dcount
    dcount( Expression [ ,  Accuracy ])

Restituisce una stima del numero di valori distinct di *Expr* nel gruppo. Per visualizzare un elenco dei valori distinct, usare [`makeset`](#makeset).

*Accuracy*, se specificato, controlla il rapporto tra velocità e precisione.

* `0` = il calcolo meno accurato e più veloce.
* `1` il valore predefinito che bilancia accuratezza e tempi di calcolo; errore dello 0,8 % circa.
* `2` = il calcolo più accurato e più lento; errore dello 0,4 % circa.

**Esempio**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)

### <a name="dcountif"></a>dcountif
    dcountif( Expression, Predicate [ ,  Accuracy ])

Restituisce una stima del numero di valori distinct di *Expr* nel gruppo per cui *Predicate* è true. Per visualizzare un elenco dei valori distinct, usare [`makeset`](#makeset).

*Accuracy*, se specificato, controlla il rapporto tra velocità e precisione.

* `0` = il calcolo meno accurato e più veloce.
* `1` il valore predefinito che bilancia accuratezza e tempi di calcolo; errore dello 0,8 % circa.
* `2` = il calcolo più accurato e più lento; errore dello 0,4 % circa.

**Esempio**

    pageViews 
    | summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist
    makelist(Expr [ ,  MaxListSize ] )

Restituisce una matrice `dynamic` (JSON) di tutti i valori di *Expr* nel gruppo. 

* *MaxListSize* è un limite di tipo integer facoltativo per il numero massimo di elementi restituiti. Il valore predefinito è *128*.

### <a name="makeset"></a>makeset
    makeset(Expression [ , MaxSetSize ] )

Restituisce una matrice `dynamic` (JSON) del set di valori distinct che *Expr* inserisce nel gruppo. Suggerimento: per conteggiare solo i valori distinct, usare [`dcount`](#dcount).

* *MaxSetSize* è un limite di tipo integer facoltativo per il numero massimo di elementi restituiti. Il valore predefinito è *128*.

**Esempio**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Vedere anche l'operatore [`mvexpand`](#mvexpand-operator) per la funzione opposta.

### <a name="max-min"></a>max, min
    max(Expr)

Calcola il valore massimo di *Expr*.

    min(Expr)

Calcola il valore minimo di *Expr*.

**Suggerimento**: viene restituito solo il valore minimo o massimo, ad esempio il prezzo più alto o più basso. Per inserire invece altre colonne nella riga, ad esempio il nome del fornitore con il prezzo più basso, usare [argmin o argmax](#argmin-argmax).

<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>

### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentile, percentiles, percentilew, percentilesw
    percentile(Expression, Percentile)

Restituisce una stima per *Expression* del percentile specificato nel gruppo. L'accuratezza dipende dalla densità della popolazione nell'area del percentile.

    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Simile a `percentile()`, ma calcola un numero di valori di percentile, operazione più rapida rispetto al calcolo separato di ogni percentile.

    percentilew(Expression, WeightExpression, Percentile)

Percentile ponderato. Da usare per i dati preaggregati.  `WeightExpression` è un numero intero che indica il numero di righe originali rappresentate da ogni riga aggregata.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Ad esempio `percentilew()`, ma vengono calcolati numerosi valori di percentile.

**esempi**

Valore di `duration` , maggiore del 95% del set di esempio e minore del 5% del set di esempio, calcolato per ogni nome richiesta:

    request 
    | summarize percentile(duration, 95)
      by name

Per il calcolo dell'intera tabella, omettere "by...".

Calcolare contemporaneamente più percentili per nomi richiesta diversi:

    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

I risultati mostrano che per la richiesta /Events/Index, il 5 % delle richieste riceve una risposta in meno di 2,44,s, la metà di esse in 3,52 s, mentre il 5 % risulta più lento di 6,85 s.

Calcolare più statistiche:

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Percentili ponderati
Utilizzare le funzioni di percentile ponderato in quei casi in cui i dati sono stati preaggregati. 

Ad esempio, se l'applicazione esegue molte migliaia di operazioni al secondo e si desidera conoscerne la latenza. La soluzione più semplice sarebbe generare una richiesta di Application Insights o un evento personalizzato per ogni operazione. Verrebbe a crearsi molto traffico, anche se verrebbe applicato il campionamento adattivo per ridurlo. Una soluzione ancora migliore consisterebbe invece nello scrivere codice nell'app per aggregare i dati prima di inviarli ad Application Insights. Verrà inviato il riepilogo aggregato a intervalli regolari, riducendo la frequenza di dati anche a pochi punti al minuto.

Il codice registra il flusso delle misurazioni di latenza in millisecondi, Ad esempio:

     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Conta le misurazioni nei seguenti bin: `{ 10, 20, 30, 40, 50, 100 }`

Periodicamente, esegue una serie di chiamate TrackEvent, una per ogni bucket, con misure personalizzate in ogni chiamata: 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

In Analytics, si noterà un gruppo di eventi simile al seguente:

| `opCount` | `latency` | significato |
| --- | --- | --- |
| 8 |10 |= 8 operazioni nel bin 10 ms |
| 6 |20 |= 6 operazioni nel bin 20 ms |
| 3 |30 |= 3 operazioni nel bin 30 ms |
| 1 |40 |= 1 operazione nel bin 40 ms |

Per ottenere un quadro preciso della distribuzione originale delle latenze di evento, si usa `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

I risultati sono gli stessi dell'uso di `percentiles` normali nel set di misure originale.

> [!NOTE]
> I percentili ponderati non sono applicabili ai [dati campionati](app-insights-sampling.md), in cui ogni riga campionata rappresenta un campione casuale di righe originali, invece che un bin. Le funzioni del percentile normale sono appropriate per i dati campionati.
> 
> 

#### <a name="estimation-error-in-percentiles"></a>Errore di stima nei percentili
L'aggregazione dei percentili fornisce un valore approssimativo usando [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Tenere presente le seguenti considerazioni importanti: 

* I limiti nell'errore di stima variano a seconda del valore del percentile richiesto. L'accuratezza migliore è alle estremità della scala [0-100]. I percentili 0 e 100 sono i valori minimo e massimo esatti della distribuzione. L'accuratezza diminuisce gradualmente verso il centro della scala. L'accuratezza minore è in corrispondenza del punto mediano ed è limitata all'1 %. 
* I limiti di errore sono visibili nella classifica, non nel valore. Si supponga che percentile(X, 50) abbia restituito il valore Xm. La stima garantisce che almeno il 49 % e al massimo il 51 % dei valori di X sono minori di Xm. Non esiste alcun limite teorico nella differenza tra Xm e il valore mediano effettivo di X.

### <a name="stdev"></a>stdev
     stdev(Expr)

Restituisce la deviazione standard di *Expr* per il gruppo.

### <a name="variance"></a>variance
    variance(Expr)

Restituisce la varianza di *Expr* per il gruppo.

### <a name="sum"></a>sum
    sum(Expr)

Restituisce la somma di *Expr* per il gruppo.                      

## <a name="scalars"></a>Scalari
[Cast](#casts) | [Confronti](#scalar-comparisons)
<br/>
[gettype](#gettype) | [hash](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

I tipi supportati sono:

| Tipo | Nome/i aggiuntivo/i | Tipo .NET equivalente |
| --- | --- | --- |
| `bool` |`boolean` |`System.Boolean` |
| `datetime` |`date` |`System.DateTime` |
| `dynamic` | |`System.Object` |
| `guid` |`uuid`, `uniqueid` |`System.Guid` |
| `int` | |`System.Int32` |
| `long` | |`System.Int64` |
| `double` |`real` |`System.Double` |
| `string` | |`System.String` |
| `timespan` |`time` |`System.TimeSpan` |

### <a name="casts"></a>Cast
È possibile eseguire il cast da un tipo a un altro. In generale, se la conversione è possibile, avrà esito positivo:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Controllare se una stringa può essere convertita in un tipo specifico:

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)







### <a name="scalar-comparisons"></a>Confronti scalari
|  |  |
| --- | --- |
| `<` |Minore |
| `<=` |Minore o uguale a |
| `>` |Maggiore |
| `>=` |Maggiore o uguale a |
| `<>` |Non uguale a |
| `!=` |Non uguale a |
| `in` |L'operando di destra è una matrice (dinamica) e l'operando di sinistra è uguale a uno dei relativi elementi. |
| `!in` |L'operando di destra è una matrice (dinamica) e l'operando di sinistra non è uguale a uno dei relativi elementi. |

### <a name="gettype"></a>gettype
**Restituisce**

Una stringa che rappresenta il tipo di archiviazione sottostante del relativo singolo argomento. Si tratta di una funzione particolarmente utile quando sono presenti valori di tipo `dynamic`: in questo caso `gettype()` indicherà la modalità di codifica di un valore.

**esempi**

|  |  |
| --- | --- |
| `gettype("a")` |`"string" ` |
| `gettype(111)` |`"long" ` |
| `gettype(1==1)` |`"int8"` |
| `gettype(now())` |`"datetime" ` |
| `gettype(1s)` |`"timespan" ` |
| `gettype(parsejson('1'))` |`"int" ` |
| `gettype(parsejson(' "abc" '))` |`"string" ` |
| `gettype(parsejson(' {"abc":1} '))` |`"dictionary"` |
| `gettype(parsejson(' [1, 2, 3] '))` |`"array"` |
| `gettype(123.45)` |`"real" ` |
| `gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` |
| `gettype(parsejson(''))` |`"null"` |
| `gettype(1.2)==real` |`true` |

### <a name="hash"></a>hash
**Sintassi**

    hash(source [, mod])

**Argomenti**

* *source*: scalare di origine su cui viene calcolato l'hash.
* *mod*: valore modulo da applicare al risultato hash.

**Restituisce**

Il valore xxhash (long)value dello scalare specificato, modulo del valore mod specificato (se specificato).

**esempi**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>iff
La funzione `iff()` calcola il primo argomento (predicato) e restituisce il valore del secondo o del terzo argomento a seconda che sia stato specificato il predicato `true` o `false`. Il secondo e terzo argomento devono essere dello stesso tipo.

**Sintassi**

    iff(predicate, ifTrue, ifFalse)


**Argomenti**

* *predicate:* espressione che restituisce un valore `boolean`.
* *ifTrue:* espressione calcolata il cui valore viene restituito dalla funzione se *predicate* restituisce `true`.
* *ifFalse:* espressione calcolata il cui valore viene restituito dalla funzione se *predicate* restituisce `false`.

**Restituisce**

Questa funzione restituisce il valore di *ifTrue* se *predicate* restituisce `true`. In caso contrario, restituisce il valore di *ifFalse*.

**Esempio**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>

### <a name="isnull-isnotnull-notnull"></a>isnull, isnotnull, notnull
    isnull(parsejson("")) == true

Accetta un solo argomento e indica se è null.

**Sintassi**

    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Restituisce**

True o false a seconda se il valore è null o not null.

| x | isnull(x) |
| --- | --- |
| "" |false |
| "x" |false |
| parsejson("") |true |
| parsejson("[]") |false |
| parsejson("{}") |false |

**Esempio**

    T | where isnotnull(PossiblyNull) | count

Si noti che esistono altri modi per ottenere questo risultato:

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar
Valuta una query o un'espressione e restituisce il risultato come valore singolo. Questa funzione è utile per i calcoli eseguiti a fasi, ad esempio, per calcolare un conteggio totale degli eventi e usarlo come linea di base.

**Sintassi**

    toscalar(query)
    toscalar(scalar)

**Restituisce**

L'argomento valutato. Se l'argomento è una tabella, restituisce la prima colonna della prima riga. È consigliabile predisporre l'argomento in modo che abbia una sola colonna e una sola riga.

**Esempio**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
    | extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Valori letterali booleani
    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Operatori booleani
    and 
    or 

### <a name="convert-to-boolean"></a>Conversione in valore booleano

Se si ha una stringa `aStringBoolean` che contiene un valore "true" o "false", è possibile convertirla in un valore booleano come indicato di seguito:

    booleanResult = aStringBoolean =~ "true"



## <a name="numbers"></a>Numeri
[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Valori letterali numerici
|  |  |
| --- | --- |
| `42` |`long` |
| `42.0` |`real` |

### <a name="arithmetic-operators"></a>Operatori aritmetici
|  |  |
| --- | --- |
| + |Add |
| - |Sottrazione |
| * |Moltiplicazione |
| / |Divisione |
| % |Modulo |
| `<` |Minore |
| `<=` |Minore o uguale a |
| `>` |Maggiore |
| `>=` |Maggiore o uguale a |
| `<>` |Non uguale a |
| `!=` |Non uguale a |

### <a name="abs"></a>abs
**Sintassi**

    abs(x)

**Argomenti**

* x: numero intero, reale o intervallo di tempo

**Restituisce**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>

### <a name="bin-floor"></a>bin, floor
Arrotonda per difetto i valori fino a un numero intero multiplo della dimensione del contenitore specificata. Usato spesso nella query [`summarize by`](#summarize-operator) . Se si dispone di un set di valori sparsi, i valori verranno raggruppati in un set più piccolo di valori specifici.

Alias `floor`.

**Sintassi**

     bin(value, roundTo)
     floor(value, roundTo)

**Argomenti**

* *value:* numero, data o intervallo di tempo. 
* *roundTo:* dimensioni bin. Numero, data o intervallo di tempo per cui viene diviso *value*. 

**Restituisce**

Il multiplo più vicino di *roundTo* inferiore a *value*.  

    (toint(value/roundTo)) * roundTo

**esempi**

| Expression | Risultato |
| --- | --- |
| `bin(4.5, 1)` |`4.0` |
| `bin(time(16d), 7d)` |`14d` |
| `bin(datetime(1953-04-15 22:25:07), 1d)` |`datetime(1953-04-15)` |

L'espressione seguente calcola un istogramma di durate, con dimensione del bucket pari a 1 secondo:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>exp
    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>floor
Alias di [`bin()`](#bin).

### <a name="gamma"></a>gamma
[Funzione gamma](https://en.wikipedia.org/wiki/Gamma_function)

**Sintassi**

    gamma(x)

**Argomenti**

* *x:* numero reale

Per i numeri interi positivi, `gamma(x) == (x-1)!` Ad esempio, `gamma(5) == 4 * 3 * 2 * 1`.

Vedere anche [loggamma](#loggamma).

### <a name="log"></a>log
    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v` deve essere un numero reale > 0. In caso contrario, viene restituito null.

### <a name="loggamma"></a>loggamma
Logaritmo naturale del valore assoluto della [funzione gamma](#gamma).

**Sintassi**

    loggamma(x)

**Argomenti**

* *x:* numero reale

### <a name="rand"></a>rand
Generatore di numeri casuali.

* `rand()` : numero reale compreso tra 0,0 e 1,0
* `rand(n)` : numero intero compreso tra 0 e n-1

### <a name="sqrt"></a>sqrt
Funzione della radice quadrata.  

**Sintassi**

    sqrt(x)

**Argomenti**

* *x:* numero reale >= 0.

**Restituisce**

* Un numero positivo per cui `sqrt(x) * sqrt(x) == x`
* `null` se l'argomento è negativo o non può essere convertito in un valore `real`. 

### <a name="toint"></a>toint
    toint(100)        // cast from long
    toint(20.7) == 20 // nearest int below double
    toint(20.4) == 20 // nearest int below double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble
    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic


### <a name="tolong"></a>tolong
    tolong(20.7) == 20 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic

## <a name="numeric-series"></a>Serie numeriche

[series_fir](#seriesfir) | [series\_fit\_line](#seriesfitline) | [series\_fit\_2lines](#seriesfit2lines) | [series_iir](#seriesiir) |  [series_periods](#seriesperiods) | [series_stats](#seriesstats)  

### <a name="seriesfir"></a>series_fir
La funzione series_fir() applica un filtro [Finite Impulse Response](https://wikipedia.org/wiki/Finite_impulse_response) a una serie (che è rappresentata da una colonna dinamica contenente una matrice numerica).

Specificando i coefficienti di filtro, può essere usata per il calcolo della media mobile, per l'arrotondamento, il rilevamento delle modifiche e molti altri casi d'uso.

La funzione accetta come input la colonna che contiene la matrice dinamica e una matrice dinamica statica dei coefficienti del filtro e applica il filtro alla colonna. Restituisce una nuova colonna di matrice dinamica, che contiene l'output filtrato. 

**Sintassi**

`series_fir(x, filter [, normalize[, center]])`

**Argomenti**

* *x:* cella della matrice dinamica costituita da una matrice di valori numerici, in genere l'output risultante degli operatori [make-series](#make-series-operator) o [makelist](#makelist-operator).
* *filter:* valore booleano facoltativo che indica se i coefficienti di filtro devono essere normalizzati (vale a dire divisi per la somma). Per impostazione predefinita, il valore di normalize è true. Se filter contiene valori negativi, la normalizzazione automatica non può essere eseguita e normalize deve essere impostato espressamente su false.
* *normalize:* valore booleano facoltativo che indica se il filtro deve essere normalizzato. Per impostazione predefinita il valore di normalize è true. Se filter contiene valori negativi, normalize deve essere specificato come false. 
* *center:* valore booleano facoltativo che indica se il filtro viene applicato in modo simmetrico in un intervallo di tempo prima e dopo il punto corrente o in un intervallo di tempo che va all'indietro dal punto corrente. Per impostazione predefinita, il valore di center è false e questo soddisfa lo scenario del flusso di dati, in cui è possibile applicare solo il filtro nel punto corrente e in quelli precedenti; tuttavia, per un'elaborazione ad hoc è possibile impostarlo su true, che mantiene la sincronizzazione con la serie temporale (vedere esempi riportati di seguito). Tecnicamente, questo parametro controlla il ritardo di gruppo del filtro.

**esempi**

Il calcolo di una media mobile di 5 punti può essere eseguito impostando filter=[1,1,1,1,1] e normalize=true (impostazione predefinita). Da notare l'effetto di center=false (impostazione predefinita) rispetto a true:

```AIQL
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=makelist(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

Questa query restituisce:

* *5h_MovingAvg:* filtro media mobile di 5 punti. Il picco viene smussato e il massimo viene spostato di (5-1)/2 = 2h.
* *5h_MovingAvg_centered:* come il precedente ma impostando center=true e questo fa sì che il massimo rimanga nella sua posizione originale.

![Risultati della query](./media/app-insights-analytics-reference/series-fir-1.png) (per visualizzare più righe, deselezionare l'opzione "split" nei comandi del grafico.)

Il calcolo della differenza tra un punto e il precedente può essere eseguito impostando filter=[1,-1]:

```AIQL
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=makelist(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```


![Risultati query](./media/app-insights-analytics-reference/series-fir-2.png)


### <a name="seriesfitline"></a>series\_fit\_line
La funzione series_fit_line() accetta una colonna contenente una matrice dinamica numerica come input ed esegue una regressione lineare per individuare la linea che meglio si adatta. Usare questa funzione nelle matrici di serie temporali, dato che si adatta all'output dell'operatore make-series. In questo modo viene generata una colonna dinamica contenente i campi seguenti:

* *slope:* pendenza della retta che meglio approssima la funzione (cioè il coefficiente `a` di `y=ax+b`).
* *interception:* intercetta della retta che meglio approssima la funzione (cioè il coefficiente `b` di `y=ax+b`).
* *rsquare:* r-square è una misura standard del livello di fit. È un numero compreso nell'intervallo [0-1], dove 1 è il miglior fit possibile e 0 indica che i dati sono del tutto non ordinati e non si adattano a nessuna linea.
* *variance:* varianza dei dati di input.
* *rvariance:* varianza residua che indica la varianza tra i valori dei dati di input e quelli approssimati.
* *line_fit:* matrice numerica che contiene una serie di valori della linea che si adatta meglio. La lunghezza della serie è uguale alla lunghezza della matrice di input. Viene usata principalmente per la creazione di grafici.

Il modo più semplice per usare questa funzione è applicarla ai risultati dell'operatore make-series.

**Sintassi**
    
    series_fit_line(x)

**Argomenti**

* `x:` Matrice dinamica di valori numerici. Da notare che la funzione prevede che tutte le righe abbiano un numero uguale di elementi della matrice. In caso contrario verranno restituiti risultati vuoti. 

**esempi**

```AIQL
range x from 1 to 1 step 1
| project id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (s,i,rs,v,rv,LineFit)=series_fit_line(y)
| render timechart 
```

![Risultati query](./media/app-insights-analytics-reference/series-fit-line.png)

|Slope|Interception|RSquare|Variance|RVariance|LineFit|
|---|---|---|---|---|---|
|0,982|2,730|98,628|1,686|-1,666| 1,064, 3,7945, 6,526, 9,256, 11,987, 14,718, 17,449, 20,180, 22,910, 25,641, 28,371, |

### <a name="seriesfit2lines"></a>serie\_fit\_2lines

La funzione series_fit_2lines() applica una regressione lineare a due segmenti di una serie (temporale) per identificare e quantificare la variazione di tendenza in una serie. La funzione esegue un'iterazione sugli indici della serie e a ogni iterazione suddivide la serie in 2 parti, adatta una linea separata (usando series_fit_line()) a ogni parte e calcola il valore r-square totale. La suddivisione migliore è quella che rende massimo il valore di r-square; la funzione restituisce i relativi parametri:

* *rsquare:* r-square è una misura standard del livello di fit. È un numero compreso nell'intervallo [0-1], dove 1 è il miglior fit possibile e 0 indica che i dati sono del tutto non ordinati e non si adattano a nessuna linea
* *split_idx:* indice del punto di divisione in 2 segmenti (con base zero)
* *variance:* varianza dei dati di input
* *rvariance:* varianza residua che indica la varianza tra i valori dei dati di input e quelli approssimati (dai due segmenti di linea).
* *line_fit:* matrice numerica che contiene una serie di valori della linea che si adatta meglio. La lunghezza della serie è uguale alla lunghezza della matrice di input. Viene usata principalmente per la creazione di grafici.
* *right_rsquare:* r-square della linea dal lato destro della suddivisione, vedere series_fit_line()
* *right_slope:* pendenza della retta che meglio approssima la funzione a destra (cioè il coefficiente a di y=ax+b)
* *right_interception:* intercetta della retta che meglio approssima la funzione a destra (cioè il coefficiente b di y=ax+b)
* *right_variance:* varianza dei dati di input sul lato destro della suddivisione
* *right_rvariance:* varianza residua dei dati di input sul lato destro della suddivisione
* *left_rsquare:* r-square della linea sul lato sinistro della suddivisione, vedere series_fit_line()
* *left_slope:* pendenza della retta che meglio approssima la funzione a sinistra (cioè il coefficiente a di y=ax+b)
* *left_interception:* intercetta della retta che meglio approssima la funzione a sinistra (cioè il coefficiente b di y=ax+b)
* *left_variance:* varianza dei dati di input sul lato sinistro della suddivisione
* *left_rvariance:* varianza residua dei dati di input sul lato sinistro della suddivisione

Si noti che questa funzione restituisce più colonne, pertanto non può essere usata come argomento per un'altra funzione.

**Sintassi**

    project series_fit_2lines(x)

Restituisce tutte le colonne citate in precedenza con i nomi seguenti: `series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx and etc.`

    project (rs, si, v)=series_fit_2lines(x)

Restituisce le colonne seguenti: `rs (r-square), si (split index), v (variance)` e il resto sarà simile a: `series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit` e così via.

    extend (rs, si, v)=series_fit_2lines(x)

Restituisce solo: rs (r-square), si (split index) e v (varianza).

**Argomenti**

* *x:* matrice dinamica di valori numerici. 

Il modo più semplice per usare questa funzione è applicarla ai risultati dell'operatore make-series.

**esempi**

```AIQL
range x from 1 to 1 step 1
| project id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```


![Risultati query](./media/app-insights-analytics-reference/series-fit-2lines.png)

### <a name="seriesiir"></a>series_iir

La funzione series_iir() applica un filtro Infinite Impulse Response a una serie (che è rappresentata da una colonna dinamica contenente una matrice numerica).

Specificando i coefficienti di filtro, può essere usata ad esempio per calcolare la somma cumulativa della serie o per applicare operazioni di arrotondamento ma anche diversi filtri passa-alto, passa-banda e passa-basso.

La funzione accetta come input la colonna che contiene la matrice dinamica e due matrici dinamiche statiche dei coefficienti a e b del filtro e applica il filtro alla colonna. Restituisce una nuova colonna di matrice dinamica, che contiene l'output filtrato. 

**Sintassi**

    series_iir(x, b , a)

**Argomenti**


* *x:* cella di matrice dinamica costituita da valori numerici, in genere l'output risultante di operatori make-series o makelist.
* *b:* espressione costante che contiene i coefficienti del numeratore del filtro (archiviati come matrice dinamica di valori numerici).
* *a:* espressione costante, come b, che contiene i coefficienti del denominatore del filtro.

    Il primo elemento di a (vale a dire a[0]) non deve essere uguale a zero (per evitare una divisione per 0; vedere la formula riportata di seguito).

**Altre informazioni sulla formula ricorsiva del filtro**

Data una matrice di input X e le matrici dei coefficienti a, b di lunghezza `n_a` e `n_b` rispettivamente, la funzione di trasferimento del filtro, che genera la matrice di output Y, è definita da (vedere anche su Wikipedia): 

    Y[i] = 1/a[0] * ( b[0]*X[i] + b[1]*X[i-1] + … 
                 + b[n_b-1]*X[i-n_b-1] — a[1]*Y[i-1] – a[2]*Y[i-2] – …
                 – a[n_a-1]*Y[i-n_a-1] )


**esempi**

Il calcolo della somma cumulativa può essere eseguito dal filtro iir con coefficienti a=[1,-1] e b=[1]: 

```AIQL
let x = range(1.0, 10, 1);
range t from 1 to 1 step 1
| project x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mvexpand x, y
```

|x|y|
|---|---|
|1.0|1.0|
|2.0|3.0|
|3.0|6.0|
|4.0|10,0|
### <a name="seriesoutliers"></a>series_outliers 

La funzione series_outliers() accetta una colonna contenente una matrice dinamica come input e genera una matrice numerica dinamica della stessa lunghezza come input. Ogni valore della matrice indica un punteggio che indica una possibile anomalia usando il test di Tukey. Un valore maggiore di 1,5 o minore di -1,5 indica un aumento o una diminuzione anomala rispettivamente nello stesso elemento di input.  

**Sintassi**  

```
series_outliers(x,kind,ignore_val,min_percentile,max_percentile)  
```
**Argomenti** 
* *x:* cella di matrice dinamica costituita da una matrice di valori numerici. Si presuppone che i valori siano equidistanti, in caso contrario si potrebbero ottenere risultati imprevisti.  
* *tipo:* algoritmo di rilevamento outlier. Supporta attualmente "tukey" e "ctukey". Il valore predefinito è "ctukey".  
* *ignore_val:* valore numerico che indica valori mancanti nella serie, il valore predefinito è double(null).
* *min_percentile:* per il calcolo dell'intervallo interquantile normale, il valore predefinito è 10 (solo ctukey).
* *max_percentile:* per il calcolo dell'intervallo interquantile normale, il valore predefinito è 90 (solo ctukey).

Nella tabella seguente vengono descritte le differenze tra "tukey" e "ctukey":

|Algoritmo|Intervallo quantile predefinito|Supporta l'intervallo quantile personalizzato|
|---------|----------------------|------------------------------|
|"tukey"|25% / 75%|No|
|"ctukey"|10% / 90%|Sì|

**Nota importante** Il modo più pratico di usare questa funzione consiste nell'applicarla ai risultati dell'operatore `make-series`.

**esempi** 

Per l'input seguente   
```
[30,28,5,27,31,38,29,80,25,37,30]
``` 
series_outliers() restituisce  
[0.0,0.0,-3.206896551724138,-0.1724137931034483,0.0,2.6666666666666667,0.0,16.666666666666669,-0.4482758620689655,2.3333333333333337,0.0]

ovvero il 5 è un'anomalia in calo e 80 è un'anomalia in aumento rispetto al resto della serie. 

### <a name="seriesperiods"></a>series_periods

La funzione series_periods() identifica i periodi più significativi presenti in una serie temporale.

Ad esempio, molto spesso una metrica che misura il traffico di un'applicazione è caratterizzata da due cicli significativi, uno con frequenza settimanale e uno con frequenza giornaliera. A fronte di questa serie temporale, series_periods() dovrà rilevare questi 2 periodi dominanti.

La funzione accetta come input una colonna contenente una matrice dinamica della serie temporale (in genere l'output risultante dell'operatore make-series), due numeri reali che definiscono la dimensione minima e massima del periodo (ovvero il numero di contenitori: ad esempio, per un contenitore 1h la dimensione di un periodo giornaliero sarebbe 24) da ricercare e un numero lungo che definisce il numero totale di periodi per la funzione da ricercare. L'output è una matrice dinamica contenente la dimensione dei periodi individuati, ordinati in base alla significatività dei periodi nei dati.

**Sintassi**

    series_periods(x, min_period, max_period, num_periods)`

**Argomenti**

* *x:* cella di matrice dinamica costituita da valori numerici, in genere l'output risultante di operatori make-series o makelist.
* *min_period:* numero reale che specifica il periodo minimo per la ricerca.
* *max_period:* numero reale che specifica il periodo massimo per la ricerca.
* *num_periods:* numero lungo che specifica il massimo numero di periodi necessari. Questa sarà la lunghezza della matrice dinamica di output.

**Note importanti**

* L'algoritmo alla base di series\_periods() richiede almeno 4 punti in un periodo per il rilevamento. Pertanto, il valore minimo per min_period è 4. Se min_period è impostato su un valore inferiore, la funzione lo sostituirà con 4.
* Il valore massimo per max_period è la metà della lunghezza della serie di input. Se max_period è impostato su un valore più elevato, la funzione verrà ritagliata su tale valore.
* Come indicato in precedenza, i periodi risultanti sono espressi in unità di creazione di contenitori, ad esempio se la serie originale ha un periodo giornaliero ed è stata aggregata per contenitori orari, un periodo giornaliero nell'output varrà 24; se i dati vengono aggregati per minuto, l'output sarà pari a 60*24=1440.
* È necessario impostare min\_period leggermente al di sotto e max\_period leggermente al di sopra dei periodi che si prevede di individuare nella serie temporale. Ad esempio, se si dispone di un segnale aggregato su base oraria e si ricercano periodi sia giornalieri che settimanali (che varrebbero 24 e 168 rispettivamente) è possibile impostare min\_period=0,824 e *max\_period=1,2*168.
* La lunghezza della matrice dinamica di output è num\_of\_periods; se la funzione ha individuato un numero di periodi significativi minore di num\_of\_periods, le rimanenti voci della matrice verranno impostate su 0.
* L'input della serie temporale deve essere regolare, vale a dire aggregato in contenitori costanti (e questo è sempre il caso se è stato creato usando make-series). In caso contrario, l'output è privo di significato.

**Esempio**

Ad esempio, la query seguente contiene una snapshot di un mese di traffico di un'applicazione, aggregato due volte al giorno (cioè ogni 12 ore).

```AIQL
range x from 1 to 1 step 1
| project y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, arraylength(y), 1), y  
| render linechart
```

![Risultati query](./media/app-insights-analytics-reference/series-periods1.png)

L'esecuzione di series_periods() in questa serie dà come risultato nel periodo settimanale (lungo 14 punti):

```AIQL
range x from 1 to 1 step 1
| project y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, arraylength(y), 1), y  
| project periods = series_periods(y, 4.0, 50.0, 2)
```

|periodi|
|---|
|[14,0, 0,0]|

### <a name="seriesstats"></a>series_stats

La funzione series_stats() accetta come input una colonna contenente una matrice dinamica numerica e calcola le colonne seguenti:

* *min:* valore minimo nella matrice di input
* *min_idx:* valore minimo nella matrice di input
* *max:* valore massimo nella matrice di input
* *max_idx:* valore massimo nella matrice di input
* *average:* valore medio della matrice di input
* *variance:* varianza del campione della matrice di input
* *StDev:* deviazione standard del campione della matrice di input

Si noti che questa funzione restituisce più colonne, pertanto non può essere usata come argomento per un'altra funzione.

**Sintassi**

    project series_stats(x)

Restituisce le colonne precedentemente citate con i nomi seguenti: serie\_stats\_x\_min, serie\_stats\_x\_min\_idx e così via.

    project (m, mi)=series_stats(x)

Restituisce le colonne seguenti: `m (min), mi (min_idx)` e il resto sarà simile a `series_stats_x_max, series_stats_x_line_max_idx` e così via.

    extend (m, mi)=series_stats(x)

Restituisce solo: m (min) e mi (min_idx).

**Argomenti**

* *x:* cella di matrice dinamica costituita da una matrice di valori numerici. 

**esempi**

Per l'input seguente:

` [1,6,11,16,21,26,31,36,41,46,51,56,61,66,71,76,81,86,91,96]`

series_stats() restituisce:

|Min|min\_idx|max|max\_idx|average|variance|stdev|
|---|---|---|---|---|---|---|
|1.0|1|96,0|19|48,5|29,58039891549808|875,0|


## <a name="date-and-time"></a>Data e ora
[ago](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

Un valore timespan rappresenta un intervallo di tempo, ad esempio 3 ore o 1 anno.

Un valore datetime rappresenta una data e ora di calendario/orologio in formato UTC.

Non esiste un tipo "date" separato. Per rimuovere l'ora da un valore datetime, usare un'espressione, ad esempio `bin(timestamp, 1d)`.

### <a name="date-and-time-literals"></a>Valori letterali di data e ora
|  |  |
| --- | --- |
| **datetime** | |
| `datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")` |Le ore sono sempre in formato UTC. L'omissione della data indica un'ora di oggi. |
| `now()` |Ora corrente. |
| `now(`-*timespan*`)` |`now()-`*timespan* |
| `ago(`*timespan*`)` |`now()-`*timespan* |
| **timespan** | |
| `2d` |2 giorni |
| `1.5h` |1,5 ore |
| `30m` |30 minuti |
| `10s` |10 secondi |
| `0.1s` |0,1 secondo |
| `100ms` |100 millisecondi |
| `10microsecond` | |
| `1tick` |100 ns |
| `time("15 seconds")` | |
| `time("2")` |2 giorni |
| `time("0.12:34:56.7")` |`0d+12h+34m+56.7s` |

### <a name="date-and-time-expressions"></a>Espressioni di data e ora
| Expression | Risultato |Effetto|
| --- | --- |---|
| `datetime("2015-01-02") - datetime("2015-01-01")` |`1d` | Differenza di tempo|
| `datetime("2015-01-01") + 1d` |`datetime("2015-01-02")` | Aggiunta di giorni |
| `datetime("2015-01-01") - 1d` |`datetime("2014-12-31")` | Sottrazione di giorni|
| `2h * 24` |`2d` |Moltiplicazione di un valore timespan|
| `2d` / `2h` |`24` |Divisione di un valore timespan|
| `datetime("2015-04-15T22:33") % 1d` |`timespan("22:33")` |Ora di un valore datetime|
| `bin(datetime("2015-04-15T22:33"), 1d)` |`datetime("2015-04-15T00:00")` |Data di un valore datetime|
|  | ||
| `<` ||Minore |
| `<=` ||Minore o uguale |
| `>` ||Maggiore |
| `>=` ||Maggiore o uguale |
| `<>` ||Diverso |
| `!=` ||Diverso |

### <a name="ago"></a>ago
Sottrae l'intervallo di tempo specificato dall'ora UTC corrente. Analogamente a `now()`, questa funzione può essere usata più volte in un'istruzione e l'ora UTC a cui fa riferimento sarà la stessa per tutte le istanze.

**Sintassi**

    ago(a_timespan)

**Argomenti**

* *a_timespan*: intervallo da sottrarre dall'ora UTC corrente (`now()`).

**Restituisce**

    now() - a_timespan

**Esempio**

Tutte le righe con un timestamp nell'ultima ora:

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>datepart
    datepart("Day", datetime(2015-12-14)) == 14

Estrae una parte specificata di una data come numero intero.

**Sintassi**

    datepart(part, datetime)

**Argomenti**

* `part:String` : {"Year", "Month", "Day", "Hour", "Minute", "Second", "Millisecond", "Microsecond", "Nanosecond"}
* `datetime`

**Restituisce**

Valore Long che rappresenta la parte specificata.

### <a name="dayofmonth"></a>dayofmonth
    dayofmonth(datetime("2016-05-15")) == 15 

Il numero ordinale del giorno del mese.

**Sintassi**

    dayofmonth(a_date)

**Argomenti**

* `a_date`: valore `datetime`.

### <a name="dayofweek"></a>dayofweek
    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Numero intero di giorni a partire dalla domenica precedente, come `timespan`.

**Sintassi**

    dayofweek(a_date)

**Argomenti**

* `a_date`: valore `datetime`.

**Restituisce**

`timespan` a partire dalla mezzanotte all'inizio della domenica precedente, arrotondato per difetto a un numero intero di giorni.

**esempi**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>dayofyear
    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

Il numero ordinale del giorno nell'anno.

**Sintassi**

    dayofyear(a_date)

**Argomenti**

* `a_date`: valore `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>

### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear
    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getmonth
Visualizzare il numero del mese (1-12) di un valore datetime.

**Esempio**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getyear
Visualizzare l'anno di un valore datetime.

**Esempio**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>now
    now()
    now(-2d)

Ora UTC corrente, con offset facoltativo di un intervallo di tempo specificato. Questa funzione può essere usata più volte in un'istruzione e l'ora a cui fa riferimento sarà la stessa per tutte le istanze.

**Sintassi**

    now([offset])

**Argomenti**

* *offset:* `timespan`, aggiunto all'ora UTC corrente. Valore predefinito: 0.

**Restituisce**

L'ora UTC corrente come `datetime`.

    now() + offset

**Esempio**

Determina l'intervallo a partire dall'evento identificato da predicate:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>

### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear
    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>todatetime
Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Verificare se una stringa è una data valida:

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>totimespan
Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>weekofyear
    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

Il risultato è un numero intero che rappresenta il numero della settimana in base allo standard ISO 8601. Il primo giorno della settimana è domenica e la prima settimana dell'anno è la settimana che contiene il primo giovedì dell'anno. Gli ultimi giorni di un anno possono quindi contenere alcuni giorni della settimana 1 dell'anno successivo oppure i primi giorni possono contenere parte della settimana 52 o 53 dell'anno precedente.

## <a name="string"></a>String
[countof](#countof) | [extract](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)

### <a name="string-literals"></a>Valori letterali di stringa
Le regole sono le stesse di JavaScript.

Le stringhe possono essere racchiuse tra virgolette singole o doppie. 

La barra rovesciata (`\`) viene usata per i caratteri di escape, ad esempio `\t` (tabulazione), `\n` (nuova riga) e le istanze del carattere delle virgolette.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Valori letterali di stringhe offuscate
I valori letterali di stringhe offuscate sono stringhe che Analytics nasconde durante l'output della stringa (ad esempio, durante la traccia). Il processo di offuscamento sostituisce tutti i caratteri offuscati con il carattere`*`.

Per creare un valore letterale di stringa offuscata, anteporre `h` o "H". Ad esempio:

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Confronti di stringhe
| operatore | Descrizione | Distinzione maiuscole/minuscole | Esempio true |
| --- | --- | --- | --- |
| `==` |Uguale a |Sì |`"aBc" == "aBc"` |
| `<>` `!=` |Non uguale a |Sì |`"abc" <> "ABC"` |
| `=~` |Uguale a |No |`"abc" =~ "ABC"` <br/>`boolAsString =~ "true"` |
| `!~` |Non uguale a |No |`"aBc" !~ "xyz"` |
| `has` |RHS (Right-Hand-Side) è un termine intero in LHS (Left-Hand-Side) |No |`"North America" has "america"` |
| `!has` |RHS non è un termine completo in LHS |No |`"North America" !has "amer"` |
| `hasprefix` |RHS è un prefisso di un termine in LHS |No |`"North America" hasprefix "ame"` |
| `!hasprefix` |RHS non è un prefisso di un termine in LHS |No |`"North America" !hasprefix "mer"` |
| `hassuffix` |RHS è un suffisso di un termine in LHS |No |`"North America" hassuffix "rth"` |
| `!hassuffix` |RHS non è un suffisso di un termine in LHS |No |`"North America" !hassuffix "mer"` |
| `contains` |RHS si verifica come sottostringa di LHS |No |`"FabriKam" contains "BRik"` |
| `!contains` |RHS non si verifica in LHS |No |`"Fabrikam" !contains "xyz"` |
| `containscs` |RHS si verifica come sottostringa di LHS |Sì |`"FabriKam" contains "Kam"` |
| `!containscs` |RHS non si verifica in LHS |Sì |`"Fabrikam" !contains "Kam"` |
| `startswith` |RHS è una sottostringa iniziale di LHS |No |`"Fabrikam" startswith "fab"` |
| `!startswith` |RHS non è una sottostringa iniziale di LHS |No |`"Fabrikam" !startswith "abr"` |
| `endswith` |RHS è una sottostringa terminale di LHS |No |`"Fabrikam" endswith "kam"` |
| `!endswith` |RHS non è una sottostringa terminale di LHS |No |`"Fabrikam" !endswith "ka"` |
| `matches regex` |LHS contiene una corrispondenza per RHS |Sì |`"Fabrikam" matches regex "b.*k"` |
| [`in`](#in) |Uguale a uno degli elementi |Sì |`"abc" in ("123", "345", "abc")` |
| [`!in`](#in) |Non uguale a uno degli elementi |Sì |`"bc" !in ("123", "345", "abc")` |

Usare `has` o `in` se si sta testando la presenza di un termine lessicale intero, ovvero un simbolo o una parola alfanumerica delimitata da caratteri non alfanumerici o da inizio o fine del campo. `has` è più rapido di `contains`, `startswith` o `endswith`. La prima delle query seguenti viene eseguita più rapidamente:

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof
    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Conta le occorrenze di una sottostringa in una stringa. Le corrispondenze di stringhe di testo normale possono sovrapporsi. Le corrispondenze regex non si sovrappongono.

**Sintassi**

    countof(text, search [, kind])

**Argomenti**

* *text:* stringa.
* *search:* stringa di testo normale o espressione regolare da ricercare in *text*.
* *kind:* `"normal"|"regex"` Valore predefinito `normal`. 

**Restituisce**

Il numero di volte in cui la stringa di ricerca può essere trovata nel contenitore. Le corrispondenze di stringhe di testo normale possono sovrapporsi. Le corrispondenze regex non si sovrappongono.

**esempi**

|  |  |
| --- | --- |
| `countof("aaa", "a")` |3 |
| `countof("aaaa", "aa")` |3 (non 2!) |
| `countof("ababa", "ab", "normal")` |2 |
| `countof("ababa", "aba")` |2 |
| `countof("ababa", "aba", "regex")` |1 |
| `countof("abcabc", "a.c", "regex")` |2 |

### <a name="extract"></a>extract
    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Recupera una corrispondenza di un' [espressione regolare](#regular-expressions) da una stringa di testo. Facoltativamente, converte la sottostringa estratta nel tipo indicato.

**Sintassi**

    extract(regex, captureGroup, text [, typeLiteral])

**Argomenti**

* *regex:*[espressione regolare](#regular-expressions).
* *captureGroup:* costante `int` positiva che indica il gruppo Capture da estrarre. 0 indica la corrispondenza completa, 1 per il valore corrispondente alle prime '(' parentesi')' nell'espressione regolare, 2 o successivi per le parentesi successive.
* *text:*  `string` da cercare.
* *typeLiteral:* valore letterale di tipo facoltativo, ad esempio `typeof(long)`. Se specificato, la sottostringa estratta viene convertita nel tipo. 

**Restituisce**

Se *regex* trova una corrispondenza in *text*: sottostringa corrispondente nel gruppo Capture indicato *captureGroup*, facoltativamente convertita in *typeLiteral*.

Se non esiste alcuna corrispondenza o la conversione del tipo non riesce: `null`. 

**esempi**

Nella stringa di esempio `Trace` viene cercata una definizione per `Duration`. La corrispondenza viene convertita in `real` e quindi moltiplicata per una costante di tempo (`1s`) in modo che `Duration` sia di tipo `timespan`. In questo esempio è uguale a 123,45 secondi:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Questo esempio è equivalente a `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>



### <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty
    isempty("") == true

True se l'argomento è una stringa vuota o è null.
Vedere anche [isnull](#isnull).

**Sintassi**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Restituisce**

Indica se l'argomento è una stringa vuota o isnull.

| x | isempty(x) |
| --- | --- |
| "" |true |
| "x" |false |
| parsejson("") |true |
| parsejson("[]") |false |
| parsejson("{}") |false |

**Esempio**

    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl
Suddivide un URL nelle parti che lo compongono.

**Sintassi**

    parseurl(urlstring)

**Argomenti**

* *urlstring:* un URL.

**Restituisce**

Un oggetto che contiene le parti come stringhe.

**Esempio**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>replace
Sostituire tutte le corrispondenze di regex con un'altra stringa.

**Sintassi**

    replace(regex, rewrite, text)

**Argomenti**

* *regex:* [espressione regolare](https://github.com/google/re2/wiki/Syntax) per cercare *text*. Può contenere gruppi di acquisizione tra '('parentesi')'. 
* *rewrite:* regex di sostituzione per ogni corrispondenza creata da *matchingRegex*. Usare `\0` per fare riferimento all'intera corrispondenza, `\1` per il primo gruppo Capture, `\2` e così via per i gruppi Capture successivi.
* *text:* stringa.

**Restituisce**

*text* dopo la sostituzione di tutte le corrispondenze di *regex* con valutazioni di *rewrite*. Le corrispondenze non si sovrappongano.

**Esempio**

L'istruzione seguente:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Ha i seguenti risultati:

| x | str | valore sostituito |
| --- | --- | --- |
| 1 |Number is 1.000000 |Number was: 1.000000 |
| 2 |Number is 2.000000 |Number was: 2.000000 |
| 3 |Number is 3.000000 |Number was: 3.000000 |
| 4 |Number is 4.000000 |Number was: 4.000000 |
| 5 |Number is 5.000000 |Number was: 5.000000 |

### <a name="split"></a>split
    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Suddivide una stringa specificata in base a un delimitatore specificato e restituisce una matrice di stringhe con le sottostringhe contenute. Facoltativamente, è possibile restituire una sottostringa specifica, se esistente.

**Sintassi**

    split(source, delimiter [, requestedIndex])

**Argomenti**

* *source*: stringa di origine che viene suddivisa in base al delimitatore specificato.
* *delimiter*: delimitatore usato per suddividere la stringa di origine.
* *requestedIndex*: indice in base zero facoltativo `int`. Se specificato, la matrice di stringhe restituita conterrà la sottostringa richiesta, se esistente. 

**Restituisce**

Una matrice di stringhe che contiene le sottostringhe della stringa di origine specificata delimitate dal delimitatore specificato.

**esempi**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat
    strcat("hello", " ", "world")

Concatena da 1 a 16 argomenti, che devono essere stringhe.

### <a name="strlen"></a>strlen
    strlen("hello") == 5

Lunghezza di una stringa.

### <a name="substring"></a>substring
    substring("abcdefg", 1, 2) == "bc"

Estrarre una sottostringa da una stringa di origine specificata a partire da un indice specificato. Facoltativamente, è possibile specificare la lunghezza della sottostringa richiesta.

**Sintassi**

    substring(source, startingIndex [, length])

**Argomenti**

* *source:* stringa di origine da cui viene ricavata la sottostringa.
* *startingIndex:* posizione del carattere iniziale in base zero della sottostringa richiesta.
* *length:* parametro facoltativo che può essere usato per specificare il numero di caratteri richiesto nella sottostringa. 

**Restituisce**

Una sottostringa ricavata dalla stringa specificata. La sottostringa inizia dalla posizione del carattere startingIndex (su base zero) e continua fino alla fine della stringa o dei caratteri di lunghezza, se specificati.

**esempi**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>tolower
    tolower("HELLO") == "hello"

Converte una stringa in lettere minuscole.

### <a name="toupper"></a>toupper
    toupper("hello") == "HELLO"

Converte una stringa in lettere maiuscole.

### <a name="guids"></a>GUID
    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Array, oggetti e valori dinamici
[valori letterali](#dynamic-literals) | [cast](#casting-dynamic-objects) | [operatori](#operators) | [clausole let](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)

Di seguito il risultato di una query su un'eccezione di Application Insights. Il valore in `details` è una matrice.

![](./media/app-insights-analytics-reference/310.png)

**Indicizzazione:** indicizzare matrici e oggetti come in JavaScript:

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Ma usare `arraylength` e altre funzioni Analytics (non usare ".length")

**Cast** In alcuni casi è necessario eseguire il cast di un elemento estratto da un oggetto poiché il tipo può variare. Ad esempio, `summarize...to` richiede un tipo specifico:

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions
    | summarize count()
      by tostring(details[0].parsedStack[0].assembly)

**Valori letterali:** per creare una matrice esplicita o un oggetto contenitore delle proprietà, scrivere l'elemento come stringa JSON ed eseguire il cast.

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** per suddividere le proprietà di un oggetto in righe separate, usare mvexpand.

    exceptions | take 1
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)

**treepath:** per trovare tutti i percorsi in un oggetto complesso:

    exceptions | take 1 | project timestamp, details
    | extend path = treepath(details)
    | mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema:** per trovare lo schema minimo che ammette tutti i valori dell'espressione nella tabella:

    exceptions | summarize buildschema(details)

Risultato:

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer":
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



### <a name="array-and-object-literals"></a>Valori letterali di matrice e oggetto
Per creare un valore letterale dinamico, usare `parsejson` (alias `todynamic`) con un argomento di stringa JSON:

* `parsejson('[43, 21, 65]')` : una matrice di numeri
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`
* `parsejson('21')` : un singolo valore di tipo dinamico che contiene un numero
* `parsejson('"21"')` : un singolo valore di tipo dinamico che contiene una stringa

> [!NOTE]
> Le doppie virgolette (`"`) devono essere usate per delimitare le etichette e i valori stringa in JSON. Di conseguenza, è in genere più semplice fare riferimento ai valori letterali di stringa con codifica JSON usando virgolette singole (`'`).
> 

Questo esempio crea un valore dinamico e quindi usa i campi:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Funzioni di oggetti dinamici
|  |  |
| --- | --- |
| [*valore* `in` *matrice*](#in) |*matrice* contiene *valore* |
| [*valore* `!in` *matrice*](#in) |*matrice* non contiene *valore* |
| [`arraylength(`matrice`)`](#arraylength) |Null se non è una matrice |
| [`extractjson(`path,object`)`](#extractjson) |Usa path per navigare nell'oggetto. |
| [`parsejson(`source`)`](#parsejson) |Converte una stringa JSON in un oggetto dinamico. |
| [`range(`from,to,step`)`](#range) |Una matrice di valori |
| [`mvexpand` listColumn](#mvexpand-operator) |Replica una riga per ogni valore in un elenco in una cella specificata. |
| [`summarize buildschema(`column`)`](#buildschema) |Deduce lo schema del tipo dal contenuto della colonna |
| [`summarize makelist(`column`)` ](#makelist) |Rende flat gruppi di righe e inserisce i valori della colonna in una matrice. |
| [`summarize makeset(`column`)`](#makeset) |Rende flat gruppi di righe e inserisce i valori della colonna in una matrice, senza duplicazione. |

### <a name="dynamic-objects-in-let-clauses"></a>Oggetti dinamici nelle clausole let
Poiché le [clausole let](#let-clause) archiviano i valori dinamici come stringhe, le due clausole seguenti sono equivalenti e richiedono entrambe `parsejson` (o `todynamic`) prima di essere usate:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a


### <a name="in"></a>iniziare
    value in (listExpression)
    value !in (listExpression)

Determina se è presente (non) un elemento nell'elenco uguale al valore. Distinzione maiuscole/minuscole nel caso in cui il valore è una stringa.

**Argomenti**

* `value`: espressione scalare.
* `listExpression`...: Un elenco di espressioni scalari o un'espressione che restituisce un elenco. 

Una matrice nidificata viene appiattita in un unico elenco, ad esempio `where x in (dynamic([1,[2,3]]))` diventa `where x in (1,2,3)`.  

**esempi**

```AIQL
    requests | where client_City in ("London", "Paris", "Rome")
```

```AIQL
let cities = dynamic(['Dublin','Redmond','Amsterdam']);
requests | where client_City in (cities) 
|  summarize count() by client_City
```

Elenco calcolato:

```AIQL
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

Uso di una chiamata di funzione come espressione dell'elenco:

```AIQL
let topCities =  (n:int) {toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City)) };
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```
 

### <a name="arraylength"></a>arraylength
Numero di elementi in una matrice dinamica.

**Sintassi**

    arraylength(array)

**Argomenti**

* *array:* un valore `dynamic`.

**Restituisce**

Il numero di elementi in *array* oppure `null` se *array* non è una matrice.

**esempi**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson
    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Recuperare un elemento specificato da un testo JSON usando un'espressione di percorso. Facoltativamente, convertire la stringa estratta in un tipo specifico.

**Sintassi**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Restituisce**

Questa funzione esegue una query JsonPath in dataSource che contiene una stringa JSON valida e, facoltativamente, la conversione di tale valore in un tipo diverso, a seconda del terzo argomento.

**Esempio**

La notazione [parentesi quadre] e la notazione punto sono equivalenti:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Suggerimenti per incrementare le prestazioni**

* Applicare le clausole where prima di usare `extractjson()`
* È consigliabile usare una corrispondenza di espressione regolare con [extract](#extract) . L'esecuzione risulta molto più rapida ed è efficace se JSON è prodotto in base a un modello.
* Usare `parsejson()` se è necessario estrarre più di un valore da JSON.
* Considerare la possibilità di analizzare JSON al momento dell'inserimento dichiarando il tipo della colonna come dinamico.

### <a name="json-path-expressions"></a>Espressioni di percorso JSON
|  |  |
| --- | --- |
| `$` |Oggetto radice |
| `@` |Oggetto corrente |
| `[0]` |Indice inferiore di matrice |
| `.` oppure `[0]` |Figlio |

*(Attualmente non vengono implementati caratteri jolly, ricorsione, unione o sezioni).*

### <a name="parsejson"></a>parsejson
Interpreta `string` come un [valore JSON](http://json.org/) e restituisce il valore come `dynamic`. È preferibile rispetto a `extractjson()` quando è necessario estrarre più di un elemento di un oggetto composto JSON.

**Sintassi**

    parsejson(json)

**Argomenti**

* *json:* un documento JSON.

**Restituisce**

Un oggetto di tipo `dynamic` specificato da *json*.

**Esempio**

Nell'esempio seguente `customDimensions.person` è un elemento `string` analogo al seguente: 

```
"\"addresses\":[{\"postcode\":\"C789\",\"street\":\"high st\",\"town\":\"Cardigan\"},{\"postcode\":\"J456\",\"street\":\"low st\",\"town\":\"Jumper\"}],\"name\":\"Ada\""
```

Il frammento seguente recupera il valore dello slot `duration` nell'oggetto e da tale valore recupera due slot, `duration.value` e `duration.min` (rispettivamente `118.0` e `110.0`).

```AIQL
customEvents
| where name == "newMember"
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

> [!NOTE]
> Le virgolette doppie devono essere usate per delimitare le etichette e i valori stringa in JSON. 
>


### <a name="range"></a>range
La funzione `range()`, da non confondere con l'operatore `range`, genera una matrice dinamica contenente una serie di valori equidistanti.

**Sintassi**

    range(start, stop, step)

**Argomenti**

* *start:* valore del primo elemento nella matrice risultante. 
* *stop:* valore dell'ultimo elemento nella matrice risultante o valore minimo maggiore rispetto all'ultimo elemento nella matrice risultante e all'interno di un numero intero multiplo di *step* da *start*.
* *step:* differenza tra due elementi consecutivi della matrice.

**esempi**

L'esempio seguente restituisce `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

L'esempio seguente restituisce una matrice che contiene tutti i giorni dell'anno 2015:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic
    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Converte una stringa in un valore dinamico.

### <a name="treepath"></a>treepath
    treepath(dynamic_object)

Enumera tutte le espressioni di percorso che identificano gli oggetti foglia in un oggetto dinamico. 

**Restituisce**

Una matrice di espressioni di percorso.

**esempi**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Si noti che "[0]" indica la presenza di una matrice, ma non specifica l'indice usato da un percorso specifico.

### <a name="zip"></a>zip
    zip(list1, list2, ...)

Combina un set di elenchi in un elenco di tuple.

* `list1...`: elenco di valori

**esempi**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]


    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>nomi
I nomi possono contenere fino a 1024 caratteri. Fanno distinzione tra maiuscole e minuscole e possono contenere lettere, cifre e caratteri di sottolineatura (`_`). 

Racchiudere tra virgolette un nome con [' ... '] o [" ... "] per includere altri caratteri o usare una parola chiave come nome. Ad esempio:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|  |  |
| --- | --- |
| ['path\\file\n\'x\''] |Usare \ per i caratteri di escape |
| ["d-e.=/f#\n"] | |
| [@'path\file'] |Nessun carattere di escape, \ è un valore letterale |
| [@"\now & then\"] | |
| [where] |Parola chiave della lingua usata come nome |

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


