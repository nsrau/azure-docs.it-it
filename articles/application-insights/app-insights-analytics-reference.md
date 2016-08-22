<properties 
	pageTitle="Informazioni di riferimento sull'analisi in Application Insights | Microsoft Azure" 
	description="Informazioni di riferimento sulle istruzioni nello strumento di ricerca avanzato per l'analisi incluso in Application Insights. " 
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
	ms.date="08/09/2016" 
	ms.author="awills"/>  

# Informazioni di riferimento sull'analisi

L'[analisi](app-insights-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analisi.

> [AZURE.NOTE] [Test drive Analytics on our simulated data](https://analytics.applicationinsights.io/demo) se l'app non invia ancora file ad Application Insights.

## Indice


**Let e set** [let](#let-clause) | [set](#set-clause)


**Query e operatori** [count](#count-operator) | [evaluate](#evaluate-operator) | [extend](#extend-operator) | [join](#join-operator) | [limit](#limit-operator) | [mvexpand](#mvexpand-operator) | [parse](#parse-operator) | [project](#project-operator) | [project-away](#project-away-operator) | [range](#range-operator) | [reduce](#reduce-operator) | [render directive](#render-directive) | [restrict clause](#restrict-clause) | [sort](#sort-operator) | [summarize](#summarize-operator) | [take](#take-operator) | [top](#top-operator) | [top-nested](#top-nested-operator) | [union](#union-operator) | [where](#where-operator)

**Aggregazioni** [any](#any) | [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [count](#count) | [countif](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [percentile](#percentile) | [percentiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [sum](#sum) | [variance](#variance)

**Scalari** [Valori letterali booleani](#boolean-literals) | [Operatori booleani](#boolean-operators) | [Cast](#casts) | [Confronti scalari](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Numeri** [Operatori aritmetici](#arithmetic-operators) | [Valori letterali numerici](#numeric-literals) | [abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [log](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Data e ora** [Espressioni di data e ora](#date-and-time-expressions) | [Valori letterali di data e ora](#date-and-time-literals) | [ago](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Stringa** [GUID](#guids) | [Valori letterali stringa offuscati](#obfuscated-string-literals) | [Valori letterali stringa](#string-literals) | [Confronto tra stringhe](#string-comparisons) | [countof](#countof) | [extract](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Array, oggetti e valori dinamici** [Valori letterali array e oggetti](#array-and-object-literals) | [Funzioni oggetti dinamici](#dynamic-object-functions) | [Oggetti dinamici in clausole let](#dynamic-objects-in-let-clauses) | [Espressioni percorsi JSON](#json-path-expressions) | [Nomi](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## Let e set

### Clausola let

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

    let us_date = (t:datetime) { strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

Una clausola let associa un [nome](#names) a un risultato tabulare, a un valore scalare o a una funzione. La clausola è un prefisso di una query e l'ambito del binding è tale query. Let non consente di assegnare un nome agli elementi usati più avanti nella sessione.

**Sintassi**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](#dynamic-type)
* *plain\_query:* una query non preceduta da una clausola let.

**esempi**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...


Self-join:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
    | project start = timestamp, session_id
    | join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
    | extend duration = stop - start 

### Clausola set

La clausola set imposta un'opzione per la durata della query. Le opzioni della query controllano come una query viene eseguita e restituisce i risultati. Possono essere flag booleani (disattivate per impostazione predefinita) o avere valori interi. Una query può contenere zero, una o più istruzioni set. Le istruzioni set influiscono solo sulle istruzioni di espressione tabulare che le seguono nell'ordine del programma.

    set OptionName [= OptionValue] ; query


|Nome | Implicazione se impostata su true
|---|---
|querytrace| Aumenta il livello di analisi del debug generate da una query. 
|noexecute| Disabilita l'esecuzione effettiva della query. Viene eseguita solo la fase di pianificazione della query. 
|perftrace| Abilita la traccia delle prestazioni. 
|notruncation| Disabilita il troncamento del set di risultati. 
|truncationmaxsize| Limita le dimensioni dei dati dei risultati della query (in byte). 
|truncationmaxrecords| Limita il numero di record dei risultati della query. 
|nostreaming |Disabilita il flusso del set di risultati. 

**Esempio**

```

    set querytrace;
    requests | take 100
```

## Query e operatori

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

### Operatore count

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

### Operatore evaluate

`evaluate` è un meccanismo di estensione che consente di aggiungere algoritmi specializzati alla fine delle query.

`evaluate` deve essere l'ultimo operatore nella pipeline della query, ad eccezione di un possibile `render`. Non deve comparire nel corpo funzione.

[evaluate autocluster](#evaluate-autocluster) | [evaluate basket](#evaluate-basket) | [evaluate diffpatterns](#evaluate-diffpatterns) | [evaluate extractcolumns](#evaluate-extractcolumns)

#### evaluate autocluster

     T | evaluate autocluster()

AutoCluster trova modelli comuni di attributi discreti (dimensioni) nei dati e ridurrà i risultati della query originale (indipendentemente dal fatto che siano 100 o 100.000 righe) a un numero minore di modelli. AutoCluster è stato sviluppato per semplificare l'analisi degli errori, ad esempio eccezioni e arresti anomali, ma può potenzialmente funzionare su qualsiasi set di dati filtrato.

**Sintassi**

    T | evaluate autocluster( arguments )

**Restituisce**

AutoCluster restituisce un set di modelli solitamente di piccole dimensioni, che acquisisce parti dei dati con valori comuni condivisi tra più attributi discreti. Ogni modello è rappresentato da una riga nei risultati.

Le prime due colonne indicano il numero e la percentuale di righe della query originale che vengono acquisite dal modello. Le colonne rimanenti provengono dalla query originale e il rispettivo valore è un valore specifico della colonna oppure '*', ovvero valori variabili.

Si noti che i modelli non sono non contigui: è possibile che si sovrappongano e in genere non comprendono tutte le righe originali. È possibile che alcune righe non rientrino in alcun modello.

**Suggerimenti**

* Usare `where` e `project` nella pipeline di input per ridurre i dati ai soli dati rilevanti.
* Quando si trova una riga interessante, è possibile che si voglia visualizzarne i dettagli, aggiungendo i rispettivi valori specifici al filtro `where`.

**Argomenti (tutti facoltativi)**

* `output=all | values | minimal`

    Formato dei risultati. Le colonne Count e Percent vengono sempre visualizzate nei risultati.

 * `all`: vengono restituite tutte le colonne dell'input.
 * `values`: consente di filtrare solo le colonne con '*' nei risultati.
 * `minimal`: consente di filtrare anche le colonne identiche per tutte le righe della query originale.


* `min_percent=`*double* (valore predefinito: 1)

    Percentuale minima di copertura delle righe generate.

    Esempio: `T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=` *int* (valore predefinito: 25)

    Il numero di valori di inizializzazione determina il numero di punti di ricerca locale iniziali dell'algoritmo. In alcuni casi, in base alla struttura dei dati, l'aumento del numero di valori di inizializzazione consente di aumentare il numero o la qualità dei risultati tramite uno spazio di ricerca maggiore ma con una query più lenta. L'argomento num\_seeds presenta risultati in diminuzione in entrambe le direzioni, quindi ridurlo a un valore inferiore a 5 consentirà di ottenere miglioramenti trascurabili delle prestazioni e aumentarlo a un valore superiore a 50 genererà raramente modelli aggiuntivi.

    Esempio: `T | evaluate autocluster("num_seeds=50")`


* `size_weight=` *0<double<1*+ (valore predefinito: 0,5)

    Consente di controllare parzialmente l'equilibrio tra generico (copertura elevata) e informativo (molti valori condivisi). L'aumento di size\_weight riduce in genere il numero di modelli e ogni modello tende a coprire una percentuale più ampia. La riduzione di size\_weight produce in genere modelli più specifici con più valori condivisi e una percentuale di copertura minore. La formula predefinita è una media geometrica ponderata tra il punteggio generico normalizzato e il punteggio informativo con size\_weight e 1-size\_weight per i pesi.

    Esempio: `T | evaluate autocluster("size_weight=0.8")`


* `weight_column=` *column\_name*

    Prende in considerazione ogni riga nell'input in base al peso specificato. Per impostazione predefinita, il peso di ogni riga è '1'. Una colonna di ponderazione viene in genere usata per tenere in considerazione il campionamento o il bucket/aggregazione dei dati già incorporati in ogni riga.

    Esempio: `T | evaluate autocluster("weight_column=sample_Count")`



#### evaluate basket

     T | evaluate basket()

Basket consente di trovare tutti i modelli frequenti di attributi discreti (dimensioni) nei dati e restituirà tutti i modelli frequenti che hanno superato la soglia di frequenza nella query originale. Basket assicura l'individuazione di tutti i modelli frequenti nei dati ma non assicura un runtime polinomiale. Il runtime della query è lineare nel numero di righe ma in alcuni casi può essere esponenziale nel numero di colonne (dimensioni). Basket è basato sull'algoritmo Apriori sviluppato originariamente per il data mining di basket analysis.

**Restituisce**

Tutti i modelli disponibili in più di una frazione specificata degli eventi (valore predefinito 0,05).

**Argomenti (tutti facoltativi)**


* `threshold=` *0,015<double<1* (valore predefinito: 0,05)

    Imposta il rapporto minimo di righe da considerare frequenti. I modelli con rapporto inferiore non verranno restituiti.

    Esempio: `T | evaluate basket("threshold=0.02")`


* `weight_column=` *column\_name*

    Prende in considerazione ogni riga nell'input in base al peso specificato. Per impostazione predefinita, il peso di ogni riga è '1'. Una colonna di ponderazione viene in genere usata per tenere in considerazione il campionamento o il bucket/aggregazione dei dati già incorporati in ogni riga.

    Esempio: T | evaluate basket("weight\_column=sample\_Count")


* `max_dims=` *1<int* (valore predefinito: 5)

    Imposta il numero massimo di dimensioni non correlate per basket, limitato per impostazione predefinita per diminuire il runtime di query.


* `output=minimize` | `all`

    Formato dei risultati. Le colonne Count e Percent vengono sempre visualizzate nei risultati.

 * `minimize`: consente di filtrare solo le colonne con '*' nei risultati.
 * `all`: vengono restituite tutte le colonne dell'input.




#### evaluate diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns confronta due set di dati della stessa struttura e trova modelli di attributi discreti (dimensioni) che caratterizzano le differenze tra i due set di dati. Diffpatterns è stato sviluppato per semplificare l'analisi degli errori, ad esempio confrontando gli errori con i non errori in un determinato intervallo di tempo, ma può potenzialmente trovare differenze tra qualsiasi set di dati della stessa struttura.

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

    `requests | extend fault = toint(resultCode) >= 500` <br/> `| evaluate diffpatterns("split=fault")`

* `target=` *string*

    Indica all'algoritmo di cercare solo i modelli con percentuale più elevata nel set di dati di destinazione. La destinazione deve essere uno dei due valori della colonna suddivisa.

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=` *0,015<double<1* (valore predefinito: 0,05)

    Imposta la differenza minima tra modelli (rapporto) tra i due set.

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    Formato dei risultati. Le colonne Count e Percent vengono sempre visualizzate nei risultati.

 * `minimize`: consente di filtrare solo le colonne con '*' nei risultati.
 * `all`: vengono restituite tutte le colonne dell'input.

* `weight_column=` *column\_name*

    Prende in considerazione ogni riga nell'input in base al peso specificato. Per impostazione predefinita ogni riga ha un peso pari a '1'. Una colonna di ponderazione viene in genere usata per tenere in considerazione il campionamento o il bucket/aggregazione dei dati già incorporati in ogni riga.

    `requests | evaluate autocluster("weight_column=itemCount")`






#### evaluate extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns viene usato per arricchire una tabella con più colonne semplici estratte dinamicamente da colonne (semi) strutturate in base al rispettivo tipo. Supporta attualmente solo le colonne JSON, ovvero serializzazioni dinamiche e di stringhe di JSON.


* `max_columns=` *int* (valore predefinito: 10)

    Il numero di nuove colonne aggiunte è dinamico e può essere molto elevato, perché si tratta in effetti del numero di chiavi distinte in tutti i record JSON, quindi deve essere limitato. Le nuove colonne vengono disposte in ordine decrescente in base alla rispettiva frequenza ed è possibile aggiungere fino a max\_columns alla tabella.

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*double* (valore predefinito: 10,0)

    Un altro modo per limitare le nuove colonne consiste nell'ignorare le colonne con frequenza inferiore a min\_percent.

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=` *bool* (valore predefinito: true)

    Se true, il nome della colonna complessa verrà aggiunto come prefisso ai nomi delle colonne estratte.


* `prefix_delimiter=` *string* (valore predefinito: "\_")

    Se add\_prefix=true, questo parametro definisce il delimitatore che verrà usato per concatenare i nomi delle nuove colonne.

    `T | evaluate extractcolumns("json_column_name=json",` <br/> `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=` *bool* (valore predefinito: false)

    Se true, le colonne originali (JSON) verranno mantenute nella tabella di output.


* `output=query | table`

    Formato dei risultati.

 * `table`: l'output corrisponde alla stessa tabella ricevuta, meno le colonne di input specificate, più le nuove colonne estratte dalle colonne di input.
 * `query`: l'output è una stringa che rappresenta la query da creare per ottenere i risultati sotto forma di tabella.




### Operatore extend

     T | extend duration = stopTime - startTime

Aggiunge una o più colonne calcolate a una tabella.


**Sintassi**

    T | extend ColumnName = Expression [, ...]

**Argomenti**

* *T:* tabella di input.
* *ColumnName:* nome delle colonne da aggiungere. I [nomi](#names) fanno distinzione tra maiuscole e minuscole e possono contenere caratteri alfabetici, numerici o "\_". Usare `['...']` o `["..."]` per racchiudere tra virgolette parole chiave o nomi con altri caratteri.
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


### Operatore join

    Table1 | join (Table2) on CommonColumn

Unisce le righe di due tabelle associando i valori della colonna specificata.


**Sintassi**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argomenti**

* *Table1*: "lato sinistro" del join.
* *Table2*: "lato destro" del join. Può trattarsi di un'espressione di query annidata che restituisce una tabella.
* *CommonColumn*: colonna con lo stesso nome nelle due tabelle.
* *Kind*: specifica come associare le righe delle due tabelle.

**Restituisce**

Una tabella con:

* Una colonna per ogni colonna in ognuna delle due tabelle, incluse le chiavi corrispondenti. Le colonne del lato destro verranno automaticamente rinominate in caso di conflitti tra i nomi.
* Una riga per ogni corrispondenza tra le tabelle di input. Una corrispondenza è una riga selezionata da una tabella che ha lo stesso valore per tutti i campi `on` di una riga nell'altra tabella.

* `Kind` non specificato

    Solo una riga del lato sinistro viene associata a ogni valore della chiave `on`. L'output contiene una riga per ogni corrispondenza di questa riga con le righe a destra.

* `Kind=inner`
 
     Nell'output è presente una riga per ogni combinazione di righe corrispondenti a sinistra e a destra.

* `kind=leftouter` (oppure `kind=rightouter` o `kind=fullouter`)

     Oltre alle corrispondenza interne, è presente una riga per ogni riga a sinistra (e/o a destra), anche se non ha corrispondenze. In tal caso, le celle di output senza corrispondenza contengono valori Null.

* `kind=leftanti`

     Restituisce tutti i record del lato sinistro che non hanno corrispondenze a destra. La tabella risultante contiene solo le colonne del lato sinistro.
 
Se sono presenti più righe con gli stessi valori per tali campi, si otterranno righe per tutte le combinazioni.

**Suggerimenti**

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
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### Operatore limit

     T | limit 5

Restituisce un numero massimo specificato di righe dalla tabella di input. Non c'è garanzia su quali record verranno restituiti. Per ottenere record specifici, usare [`top`](#top-operator).

**Alias** `take`

**Sintassi**

    T | limit NumberOfRows


**Suggerimenti**

`Take` è un modo semplice ed efficiente per visualizzare un esempio dei risultati quando si lavora in modo interattivo. Tenere presente che non è garantito che vengano generate righe particolari o in un ordine particolare.

Esiste un limite implicito per il numero di righe restituite al client, anche se non si usa `take`. Per aumentare questo limite, usare l'opzione di richiesta client `notruncation`.



### Operatore mvexpand

    T | mvexpand listColumn 

Espande un elenco da una cella dinamicamente tipizzata (JSON) in modo che ogni voce abbia una riga separata. Tutte le altre celle in una riga espansa vengono duplicate.

Vedere anche [`summarize makelist`](#summarize-operator) che esegue la funzione opposta.

**Esempio**

Si supponga che la tabella di input sia:

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|[0,1,"k","v"]|

    mvexpand D

Il risultato è:

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|0|
|2|"world"|1|
|2|"world"|"k"|
|2|"world"|"v"|


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



### Operatore parse

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

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
* "*" ignora il numero minimo di caratteri per passare alla corrispondenza seguente. È possibile usare "*" all'inizio e alla fine dello schema o dopo un tipo diverso da string oppure tra corrispondenze di stringa.

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

x | counter | present | Year
---|---|---|---
1 | 2 | socks | 63

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


x | present | Year
---|---|---
1 | socks | 63


*Regex:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\)"
| project-away x, s
```

resource | slice | lock | release | previous
---|---|---|---|---
Utilità di pianificazione | 16 | 02/17/2016 08:41:00 | 02/17/2016 08:41 | 2016-02-17T08:40:00Z

### Operatore project

    T | project cost=price*quantity, price

Selezionare le colonne da includere, rinominare o rimuovere e inserire le nuove colonne calcolate. L'ordine delle colonne nel risultato viene specificato dall'ordine degli argomenti. Solo le colonne specificate negli argomenti vengono incluse nel risultato, mentre le altre nell'input vengono rimosse. Vedere anche `extend`.


**Sintassi**

    T | project ColumnName [= Expression] [, ...]

**Argomenti**

* *T:* tabella di input.
* *ColumnName:* nome di una colonna da visualizzare nell'output. Se *Expression* non è presente, deve essere visualizzata una colonna con quel nome nell'input. I [nomi](#names) fanno distinzione tra maiuscole e minuscole e possono contenere caratteri alfabetici, numerici o "\_". Usare `['...']` o `["..."]` per racchiudere tra virgolette parole chiave o nomi con altri caratteri.
* *Expression:* espressione scalare facoltativa che fa riferimento alle colonne di input.

    È consentito restituire una nuova colonna calcolata con lo stesso nome di una colonna esistente nell'input.

**Restituisce**

Una tabella che ha le colonne denominate come argomenti e lo stesso numero di righe della tabella di input.

**Esempio**

L'esempio seguente illustra diverse tipologie di manipolazioni che possono essere eseguite usando l'operatore `project`. La tabella di input `T` include tre colonne di tipo `int`: `A`, `B` e `C`.

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### Operatore project-away

    T | project-away column1, column2, ...

Escludere le colonne specificate. Il risultato contiene tutte le colonne di input ad eccezione di quelle denominate.

### Operatore range

    range LastWeek from ago(7d) to now() step 1d

Genera una tabella a colonna singola di valori. Si noti che non deve necessariamente avere un input da pipeline.

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



**Sintassi**

    range ColumnName from Start to Stop step Step

**Argomenti**

* *ColumnName:* nome della colonna singola nella tabella di output.
* *Start:* il valore più basso nell'output.
* *Stop:* il valore più alto che viene generato nell'output o un limite per il valore più alto, se *step* supera questo valore.
* *Step:* la differenza tra due valori consecutivi.

Gli argomenti devono essere valori numerici, date o intervalli di tempo. Non possono fare riferimento alle colonne di nessuna tabella. Per calcolare l'intervallo basato su una tabella di input, usare la [*funzione* range](#range), ad esempio con l'[operatore mvexpand](#mvexpand-operator).

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

### Operatore reduce

    exceptions | reduce by outerMessage

Cerca di raggruppare record simili. Per ogni gruppo l'operatore restituisce l'oggetto `Pattern` che descrive meglio il gruppo e l'oggetto `Count` dei record del gruppo.


![](./media/app-insights-analytics-reference/reduce.png)

**Sintassi**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argomenti**

* *ColumnName:* la colonna da esaminare. Deve essere di tipo stringa.
* *Threshold:* un valore compreso nell'intervallo {0..1}. Il valore predefinito è 0.001. Per gli input di grandi dimensioni, la soglia deve essere bassa.

**Restituisce**

Due colonne, `Pattern` e `Count`. In molti casi, Pattern sarà un valore completo della colonna. In alcuni casi, può identificare termini comuni e sostituire le parti variabili con "*".

Ad esempio, il risultato di `reduce by city` può includere:

|Modello | Numero |
|---|---|
| San * | 5182 |
| Saint * | 2846 |
| Moscow | 3726 |
| * -on- * | 2730 |
| Parigi | 27163 |


### Direttiva render

    T | render [ table | timechart  | barchart | piechart ]

Indica al livello di presentazione la modalità di visualizzazione della tabella. Deve essere l'ultimo elemento della pipe. Si tratta di una valida alternativa all'uso dei controlli su schermo che consente di salvare una query con un metodo di presentazione specifico.

### Clausola restrict 

Specifica il set di nomi di tabelle disponibili per gli operatori che seguono. Ad esempio:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### Operatore sort 

    T | sort by country asc, price desc

Ordina le righe della tabella input in base a una o più colonne.

**Alias** `order`

**Sintassi**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

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

### Operatore summarize

Produce una tabella che aggrega il contenuto della tabella di input.
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Tabella che indica il numero, la durata media della richiesta e il set di città in ogni paese. Nell'output è presente una riga per ogni singolo paese. Le colonne di output indicano il conteggio, la durata media, le città e il paese. Tutte le altre colonne di input vengono ignorate.


    T | summarize count() by price_range=bin(price, 10.0)

Tabella che indica quanti elementi presentano prezzi in ogni intervallo [0,10.0], [10.0,20.0] e così via. Questo esempio ha una colonna per il conteggio e una per l'intervallo di prezzi. Tutte le altre colonne di input vengono ignorate.


**Sintassi**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argomenti**

* *Column:* nome facoltativo per una colonna di risultati. Il valore predefinito è un nome derivato dall'espressione. I [nomi](#names) fanno distinzione tra maiuscole e minuscole e possono contenere caratteri alfabetici, numerici o "\_". Usare `['...']` o `["..."]` per racchiudere tra virgolette parole chiave o nomi con altri caratteri.
* *Aggregation:* chiamata a una funzione di aggregazione, ad esempio `count()` o `avg()`, con nomi di colonna come argomenti. Vedere [aggregazioni](#aggregations).
* *GroupExpression:* espressione sulle colonne che fornisce un set di valori distinti. Si tratta in genere di un nome di colonna che fornisce già un set di valori limitato oppure di `bin()` con una colonna numerica o di data e ora come argomento.

Se si specifica un'espressione numerica o di data e ora senza usare `bin()`, l'analisi applica automaticamente l'espressione con un intervallo di `1h` per i valori di data/ora o `1.0` per i numeri.

Se non si specifica *GroupExpression*, l'intera tabella viene riepilogata in un'unica riga di output.



**Restituisce**

Le righe di input vengono disposte in gruppi con gli stessi valori delle espressioni `by`. Vengono quindi calcolate per ogni gruppo le funzioni di aggregazione specificate, generando una riga per ogni gruppo. Il risultato contiene le colonne `by`, oltre ad almeno una colonna per ogni aggregazione calcolata. Alcune funzioni di aggregazione restituiscono più colonne.

Il risultato contiene una riga per ogni singola combinazione di valori `by`. Per riepilogare gli intervalli di valori numerici, usare `bin()` per ridurre gli intervalli a valori discreti.

**Nota**

Anche se è possibile specificare espressioni arbitrarie per le espressioni di aggregazione e raggruppamento, è preferibile usare nomi di colonna semplici o applicare `bin()` a una colonna numerica.



### Operatore take

Alias di [limit](#limit-operator)


### Operatore top

    T | top 5 by Name desc nulls first

Restituisce i primi *N* record ordinati in base alle colonne specificate.


**Sintassi**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**Argomenti**

* *NumberOfRows:* numero di righe di *T* da restituire.
* *Sort\_expression:* espressione in base alla quale ordinare le righe. In genere è semplicemente un nome di colonna. È possibile specificare più di un elemento sort\_expression.
* `asc` o `desc` (valore predefinito) può essere visualizzato per controllare se la selezione nell'intervallo è effettivamente in ordine crescente o decrescente.
* `nulls first` o i controlli `nulls last` in cui sono presenti valori null. `First` è il valore predefinito per `asc`, `last` è il valore predefinito per `desc`.


**Suggerimenti**

`top 5 by name` apparentemente equivale a `sort by name | take 5`, ma viene eseguito più rapidamente e restituisce sempre risultati ordinati, mentre `take` non offre questo tipo di garanzia.

### Operatore top-nested

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
* COLUMN: - colonna di raggruppamento per l'aggregazione.
* AGGREGATION: [funzione di aggregazione](#aggregations) da applicare a ogni gruppo di righe. I risultati delle aggregazioni determineranno i primi gruppi da visualizzare.


### Operatore union

     Table1 | union Table2, Table3

Considera due o più tabelle e restituisce le righe di tutte.

**Sintassi**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argomenti**

* *Table1*, *Table2*...
 *  Il nome di una tabella, ad esempio `requests`, o una tabella definita in una [clausola let](#let-clause).
 *  Un'espressione di query, ad esempio `(requests | where success=="True")`
 *  Un set di tabelle specificato con un carattere jolly. Ad esempio, `e*` forma l'unione di tutte le tabelle definite nelle clausole let precedenti il cui nome inizia per "e", insieme alla tabella "exceptions".
* `kind`:
 * `inner`: il risultato include il subset di colonne comuni a tutte le tabelle di input.
 * `outer`: il risultato include tutte le colonne presenti in tutti gli input. Le celle non definite da una riga di input vengono impostate su `null`.
* `withsource=`*ColumnName:* se specificato, l'output includerà una colonna denominata *ColumnName* il cui valore indica quale tabella di origine ha contribuito per ogni riga.

**Restituisce**

Una tabella con un numero di righe pari a quelle presenti in tutte le tabelle di input e un numero di colonne pari al numero di nomi di colonna univoci negli input.

**Esempio**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
Unione di tutte le tabelle i cui nomi iniziano con "tt".


**Esempio**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
Numero di utenti distinti che hanno generato un evento `exceptions` o un evento `traces` nel giorno precedente. Nel risultato la colonna "SourceTable" indicherà "Query" o "Command".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Questa versione più efficiente genera lo stesso risultato. Filtra ogni tabella prima di creare l'unione.

### Operatore where

     T | where fruit=="apple"

Filtra una tabella per trovare il subset di righe che soddisfano un predicato.

**Alias** `filter`

**Sintassi**

    T | where Predicate

**Argomenti**

* *T*: input tabulare i cui record devono essere filtrati.
* *Predicate:* [espressione](#boolean) `boolean` sulle colonne di *T*. Viene valutato per ogni riga in *T*.

**Restituisce**

Righe in *T* per cui *Predicate* è `true`.

**Suggerimenti**

Per ottenere prestazioni ottimali:

* **Usare confronti semplici** tra i nomi di colonna e le costanti. Con "costante" si intende costante nella tabella, quindi `now()` e `ago()` sono validi e sono dunque valori scalari assegnati con una [clausola `let`](#let-clause).

    Ad esempio, preferire `where Timestamp >= ago(1d)` a `where floor(Timestamp, 1d) == ago(1d)`.

* **Prima i termini più semplici**: se sono presenti più clausole unite con `and`, inserire prima le clausole che coinvolgono una sola colonna. Quindi `Timestamp > ago(1d) and OpId == EventId` è meglio del contrario.


**Esempio**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Record risalenti a non più di 1 ora fa e provenienti dall'elemento Source denominato "Kuskus" e con due colonne dello stesso valore.

Si noti che il confronto tra due colonne viene inserito per ultimo, perché non riesce a utilizzare l'indice e forza un'analisi.



## Aggregazioni

Le aggregazioni sono funzioni usate per combinare i valori in gruppi creati nell'[operazione di riepilogo](#summarize-operator). In questa query, ad esempio, dcount() è una funzione di aggregazione:

    requests | summarize dcount(name) by success

### qualsiasi 

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

<a name="argmin"></a> <a name="argmax"></a>
### argmin, argmax

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
 


### avg

    avg(Expression)

Calcola la media di *Expression* all'interno del gruppo.

### buildschema

    buildschema(DynamicExpression)

Restituisce lo schema minimo che ammette tutti i valori di *DynamicExpression*.

Il tipo di colonna dei parametri deve essere `dynamic`: un contenitore di matrici o proprietà.

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

##### Modello di schema

La sintassi dello schema restituito è:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
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


### count

    count([ Predicate ])

Restituisce un conteggio delle righe per il quale *Predicate* restituisce `true`. Se *Predicate* non viene specificato, restituisce il numero totale di record del gruppo.

**Suggerimento per le prestazioni**: usare `summarize count(filter)` anziché `where filter | summarize count()`

> [AZURE.NOTE] Evitare di usare count() per trovare i numeri di richieste, eccezioni o altri eventi che si sono verificati. Quando [sampling](app-insights-sampling.md) è attivo, il numero di punti dati conservato in Application Insights sarà minore del numero di eventi effettivi. Usare invece `summarize sum(itemCount)...`. La proprietà itemCount indica il numero di eventi originali rappresentati da ogni punto dati mantenuto.

### countif

    countif(Predicate)

Restituisce un conteggio delle righe per il quale *Predicate* restituisce `true`.

**Suggerimento per le prestazioni**: usare `summarize countif(filter)` anziché `where filter | summarize count()`

> [AZURE.NOTE] Evitare di usare countif() per trovare i numeri di richieste, eccezioni o altri eventi che si sono verificati. Quando [sampling](app-insights-sampling.md) è attivo, il numero di punti dati sarà minore del numero di eventi effettivi. Usare invece `summarize sum(itemCount)...`. La proprietà itemCount indica il numero di eventi originali rappresentati da ogni punto dati mantenuto.

### dcount

    dcount( Expression [ ,  Accuracy ])

Restituisce una stima del numero di valori distinct di *Expr* nel gruppo. Per visualizzare un elenco dei valori distinct, usare [`makeset`](#makeset).

*Accuracy*, se specificato, controlla il rapporto tra velocità e precisione.

 * `0` = il calcolo meno preciso e più veloce.
 * `1` = il valore predefinito che bilancia precisione e tempi di calcolo. Errore dello 0,8 % circa.
 * `2` = il calcolo più preciso e più lento. Errore dello 0,4% circa.

**Esempio**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Restituisce una stima del numero di valori distinct di *Expr* nel gruppo per cui *Predicate* è true. Per visualizzare un elenco dei valori distinct, usare [`makeset`](#makeset).

*Accuracy*, se specificato, controlla il rapporto tra velocità e precisione.

 * `0` = il calcolo meno preciso e più veloce.
 * `1` = il valore predefinito che bilancia precisione e tempi di calcolo. Errore dello 0,8 % circa.
 * `2` = il calcolo più preciso e più lento. Errore dello 0,4% circa.

**Esempio**

    pageViews 
    | summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### makelist

    makelist(Expr [ ,  MaxListSize ] )

Restituisce una matrice `dynamic` (JSON) di tutti i valori di *Expr* nel gruppo.

* *MaxListSize* è un limite di tipo integer facoltativo per il numero massimo di elementi restituiti. Il valore predefinito è *128*.

### makeset

    makeset(Expression [ , MaxSetSize ] )

Restituisce una matrice `dynamic` (JSON) del set di valori distinct che *Expr* inserisce nel gruppo. Suggerimento: per conteggiare solo i valori distinct, usare [`dcount`](#dcount).
  
*  *MaxSetSize* è un limite di tipo integer facoltativo per il numero massimo di elementi restituiti. Il valore predefinito è *128*.

**Esempio**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Vedere anche l'operatore [`mvexpand`](#mvexpand-operator) per la funzione opposta.


### max, min

    max(Expr)

Calcola il valore massimo di *Expr*.
    
    min(Expr)

Calcola il valore minimo di *Expr*.

**Suggerimento**: viene restituito solo il valore minimo o massimo, ad esempio il prezzo più alto o più basso. Per inserire invece altre colonne nella riga, ad esempio il nome del fornitore con il prezzo più basso, usare [argmin o argmax](#argmin-argmax).


<a name="percentile"></a> <a name="percentiles"></a> <a name="percentilew"></a> <a name="percentilesw"></a>
### percentile, percentiles, percentilew, percentilesw

    percentile(Expression, Percentile)

Restituisce una stima per *Expression* del percentile specificato nel gruppo. L'accuratezza dipende dalla densità della popolazione nell'area del percentile.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Simile a `percentile()`, ma calcola un numero di valori di percentile, operazione più rapida rispetto al calcolo separato di ogni percentile.

    percentilew(Expression, WeightExpression, Percentile)

Percentile ponderato. Da usare per i dati preaggregati. `WeightExpression` è un numero intero che indica il numero di righe originali rappresentate da ogni riga aggregata.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Ad esempio `percentilew()`, ma vengono calcolati numerosi valori di percentile.

**esempi**


Valore di `duration`, maggiore del 95% del set di esempio e minore del 5% del set di esempio, calcolato per ogni nome richiesta:

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

#### Percentili ponderati

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

`opCount` | `latency`| significato
---|---|---
8 | 10 | = 8 operazioni nel bin 10 ms
6 | 20 | = 6 operazioni nel bin 20 ms
3 | 30 | = 3 operazioni nel bin 30 ms
1 | 40 | = 1 operazione nel bin 40 ms

Per ottenere un quadro preciso della distribuzione originale delle latenze di evento, si usa `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

I risultati sono gli stessi dell'uso di `percentiles` normali nel set di misure originale.

> [AZURE.NOTE] I percentili ponderati non sono applicabili ai [dati campionati](app-insights-sampling.md), in cui ogni riga campionata rappresenta un campione casuale di righe originali, invece che un bin. Le funzioni del percentile normale sono appropriate per i dati campionati.

#### Errore di stima nei percentili

L'aggregazione dei percentili fornisce un valore approssimativo usando [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf).

Tenere presente le seguenti considerazioni importanti:

* I limiti nell'errore di stima variano a seconda del valore del percentile richiesto. L'accuratezza migliore è alle estremità della scala [0-100]. I percentili 0 e 100 sono i valori minimo e massimo esatti della distribuzione. L'accuratezza diminuisce gradualmente verso il centro della scala. L'accuratezza minore è in corrispondenza del punto mediano ed è limitata all'1 %.
* I limiti di errore sono visibili nella classifica, non nel valore. Si supponga che percentile(X, 50) abbia restituito il valore Xm. La stima garantisce che almeno il 49 % e al massimo il 51 % dei valori di X sono minori di Xm. Non esiste alcun limite teorico nella differenza tra Xm e il valore mediano effettivo di X.

### stdev

     stdev(Expr)

Restituisce la deviazione standard di *Expr* per il gruppo.

### variance

    variance(Expr)

Restituisce la varianza di *Expr* per il gruppo.

### sum

    sum(Expr)

Restituisce la somma di *Expr* per il gruppo.


## Scalari

[cast](#casts) | [confronti](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

I tipi supportati sono:

| Tipo | Nome/i aggiuntivo/i | Tipo .NET equivalente |
| --------- | -------------------- | -------------------- |
| `bool` | `boolean` | `System.Boolean` |
| `datetime`| `date` | `System.DateTime` |
| `dynamic` | | `System.Object` |
| `guid` | `uuid`, `uniqueid` | `System.Guid` |
| `int` | | `System.Int32` |
| `long` | | `System.Int64` |
| `double` | `real` | `System.Double` |
| `string` | | `System.String` |
| `timespan`| `time` | `System.TimeSpan` |

### Cast

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

### Confronti scalari

||
---|---
`<` |Minore
`<=`|Minore o uguale a
`>` |Maggiore
`>=`|Maggiore o uguale a
`<>`|Non uguale a
`!=`|Non uguale a 
`in`| L'operando di destra è una matrice (dinamica) e l'operando di sinistra è uguale a uno dei relativi elementi.
`!in`| L'operando di destra è una matrice (dinamica) e l'operando di sinistra non è uguale a uno dei relativi elementi.




### gettype

**Restituisce**

Una stringa che rappresenta il tipo di archiviazione sottostante del relativo singolo argomento. Si tratta di una funzione particolarmente utile quando sono presenti valori di tipo `dynamic`: in questo caso `gettype()` indicherà la modalità di codifica di un valore.

**esempi**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### hash

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
### iff

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

<a name="isnull"/></a> <a name="isnotnull"/></a> <a name="notnull"/></a>
### isnull, isnotnull, notnull

    isnull(parsejson("")) == true

Accetta un solo argomento e indica se è null.

**Sintassi**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Restituisce**

True o false a seconda se il valore è null o not null.


|x|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false

**Esempio**

    T | where isnotnull(PossiblyNull) | count

Si noti che esistono altri modi per ottenere questo risultato:

    T | summarize count(PossiblyNull)

### toscalar

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




### Valori letterali booleani

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### Operatori booleani

	and 
    or 

    

## Numeri

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### Valori letterali numerici

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### Operatori aritmetici

|| |
|---|-------------|
| + | Aggiungi |
| - | Sottrai |
| * | Moltiplica |
| / | Dividi |
| % | Modulo |
||
|`<` |Minore
|`<=`|Minore o uguale a
|`>` |Maggiore
|`>=`|Maggiore o uguale a
|`<>`|Non uguale a
|`!=`|Non uguale a


### abs

**Sintassi**

	abs(x)

**Argomenti**

* x: numero intero, reale o intervallo di tempo

**Restituisce**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### bin, floor

Arrotonda per difetto i valori fino a un numero intero multiplo della dimensione del contenitore specificata. Usato spesso nella query [`summarize by`](#summarize-operator). Se si dispone di un set di valori sparsi, i valori verranno raggruppati in un set più piccolo di valori specifici.

Alias `floor`.

**Sintassi**

     bin(value, roundTo)
     floor(value, roundTo)

**Argomenti**

* *value:* numero, data o intervallo di tempo.
* *roundTo:* dimensioni bin. Numero, data o intervallo di tempo per cui viene diviso *value*.

**Restituisce**

Il multiplo più vicino di *roundTo* inferiore a *value*.
 
    (toint((value/roundTo)-0.5)) * roundTo

**esempi**

Espressione | Risultato
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`| `datetime(1953-04-15)`


L'espressione seguente calcola un istogramma di durate, con dimensione del bucket pari a 1 secondo:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### exp

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v



### floor

Alias di [`bin()`](#bin).


### log

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v` deve essere un numero reale > 0. In caso contrario, viene restituito null.

### rand

Generatore di numeri casuali.

* `rand()`: numero reale compreso tra 0,0 e 1,0
* `rand(n)`: numero intero compreso tra 0 e n-1




### sqrt

Funzione della radice quadrata.

**Sintassi**

    sqrt(x)

**Argomenti**

* *x:* numero reale >= 0.

**Restituisce**

* Un numero positivo per cui `sqrt(x) * sqrt(x) == x`
* `null` se l'argomento è negativo o non può essere convertito in un valore `real`.




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## Data e ora


[ago](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### Valori letterali di data e ora

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Le ore sono sempre in formato UTC. L'omissione della data indica un'ora di oggi.
`now()`|Ora corrente.
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**timespan**|
`2d`|2 giorni
`1.5h`|1,5 ore 
`30m`|30 minuti
`10s`|10 secondi
`0.1s`|0,1 secondo
`100ms`| 100 millisecondi
`10microsecond`|
`1tick`|100 ns
`time("15 seconds")`|
`time("2")`| 2 giorni
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### Espressioni di data e ora

Espressione |Risultato
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Minore
`<=`|Minore o uguale a
`>` |Maggiore
`>=`|Maggiore o uguale a
`<>`|Non uguale a
`!=`|Non uguale a 




### ago

Sottrae l'intervallo di tempo specificato dall'ora UTC corrente. Analogamente a `now()`, questa funzione può essere usata più volte in un'istruzione e l'ora UTC a cui fa riferimento sarà la stessa per tutte le istanze.

**Sintassi**

    ago(a_timespan)

**Argomenti**

* *a\_timespan*: intervallo da sottrarre dall'ora UTC corrente (`now()`).

**Restituisce**

    now() - a_timespan

**Esempio**

Tutte le righe con un timestamp nell'ultima ora:

```AIQL

    T | where timestamp > ago(1h)
```

### datepart

    datepart("Day", datetime(2015-12-14)) == 14

Estrae una parte specificata di una data come numero intero.

**Sintassi**

    datepart(part, datetime)

**Argomenti**

* `part:String`: {"Year", "Month", "Day", "Hour", "Minute", "Second", "Millisecond", "Microsecond", "Nanosecond"}
* `datetime`

**Restituisce**

Valore Long che rappresenta la parte specificata.


### dayofmonth

    dayofmonth(datetime("2016-05-15")) == 15 

Il numero ordinale del giorno del mese.

**Sintassi**

    dayofmonth(a_date)

**Argomenti**

* `a_date`: valore `datetime`.


### dayofweek

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

### dayofyear

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

Il numero ordinale del giorno nell'anno.

**Sintassi**

    dayofyear(a_date)

**Argomenti**

* `a_date`: valore `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### getmonth

Visualizzare il numero del mese (1-12) di un valore datetime.

**Esempio**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

Visualizzare l'anno di un valore datetime.

**Esempio**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

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
### startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### todatetime

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


### totimespan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### weekofyear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

Il risultato è un numero intero che rappresenta il numero della settimana in base allo standard ISO 8601. Il primo giorno della settimana è domenica e la prima settimana dell'anno è la settimana che contiene il primo giovedì dell'anno. Gli ultimi giorni di un anno possono quindi contenere alcuni giorni della settimana 1 dell'anno successivo oppure i primi giorni possono contenere parte della settimana 52 o 53 dell'anno precedente.


## String

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### Valori letterali di stringa

Le regole sono le stesse di JavaScript.

Le stringhe possono essere racchiuse tra virgolette singole o doppie.

La barra rovesciata (``) viene usata per i caratteri di escape, ad esempio `\t` (tabulazione), `\n` (nuova riga) e le istanze del carattere delle virgolette.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### Valori letterali di stringhe offuscate

I valori letterali di stringhe offuscate sono stringhe che Analytics nasconde durante l'output della stringa (ad esempio, durante la traccia). Il processo di offuscamento sostituisce tutti i caratteri offuscati con il carattere `*`.

Per creare un valore letterale di stringa offuscata, anteporre `h` o "H". Ad esempio:

```
h'hello'
h@'world' 
h"hello"
```

### Confronti di stringhe

Operatore|Descrizione|Distinzione maiuscole/minuscole|Esempio true
---|---|---|---
`==`|Uguale a |Sì| `"aBc" == "aBc"`
`<>` `!=`|Non uguale a|Sì| `"abc" <> "ABC"`
`=~`|Uguale a |No| `"abc" =~ "ABC"`
`!~`|Non uguale a |No| `"aBc" !~ "xyz"`
`has`|RHS (Right-Hand-Side) è un termine intero in LHS (Left-Hand-Side)|No| `"North America" has "america"`
`!has`|RHS non è un termine completo in LHS|No|`"North America" !has "amer"` 
`hasprefix`  |RHS è un prefisso di un termine in LHS|No|`"North America" hasprefix "ame"`
`!hasprefix`  |RHS non è un prefisso di un termine in LHS|No|`"North America" !hasprefix "mer"`
`hassuffix`|RHS è un suffisso di un termine in LHS|No|`"North America" hassuffix "rth"`  
`!hassuffix`  |RHS non è un suffisso di un termine in LHS|No|`"North America" !hassuffix "mer"`  
`contains` | RHS si verifica come sottostringa di LHS|No| `"FabriKam" contains "BRik"`  
`!contains`  | RHS non si verifica in LHS|No| `"Fabrikam" !contains "xyz"`
`containscs`   | RHS si verifica come sottostringa di LHS|Sì| `"FabriKam" contains "Kam"`  
`!containscs`  | RHS non si verifica in LHS|Sì| `"Fabrikam" !contains "Kam"`  
`startswith`|RHS è una sottostringa iniziale di LHS|No|`"Fabrikam" startswith "fab"`  
`!startswith`  |RHS non è una sottostringa iniziale di LHS|No|`"Fabrikam" !startswith "abr"`
`endswith`  |RHS è una sottostringa terminale di LHS|No|`"Fabrikam" endswith "kam"`  
`!endswith`|RHS non è una sottostringa terminale di LHS|No|`"Fabrikam" !endswith "ka"`  
`matches regex`  |LHS contiene una corrispondenza per RHS|Sì| `"Fabrikam" matches regex "b.*k"`  
`in`  |Uguale a uno degli elementi|Sì|`"abc" in ("123", "345", "abc")`  
`!in`  |Non uguale a uno degli elementi|Sì|`"bc" !in ("123", "345", "abc")`  

Usare `has` o `in` se si sta testando la presenza di un termine lessicale intero, ovvero un simbolo o una parola alfanumerica delimitata da caratteri non alfanumerici o da inizio o fine del campo. `has` viene eseguito più rapidamente di `contains`, `startswith` o `endswith`. La prima delle query seguenti viene eseguita più rapidamente:

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Conta le occorrenze di una sottostringa in una stringa. Le corrispondenze di stringhe di testo normale possono sovrapporsi. Le corrispondenze regex non si sovrappongono.

**Sintassi**

    countof(text, search [, kind])

**Argomenti**

* *text:* stringa.
* *search:* stringa di testo normale o espressione regolare da ricercare in *text*.
* *kind:* `"normal"|"regex"` Valore predefinito: `normal`.

**Restituisce**

Il numero di volte in cui la stringa di ricerca può essere trovata nel contenitore. Le corrispondenze di stringhe di testo normale possono sovrapporsi. Le corrispondenze regex non si sovrappongono.

**esempi**

|||
|---|---
|`countof("aaa", "a")`  | 3 
|`countof("aaaa", "aa")`  | 3 (non 2!)
|`countof("ababa", "ab", "normal")`  | 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`  | 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Recupera una corrispondenza di un'[espressione regolare](#regular-expressions) da una stringa di testo. Facoltativamente, converte la sottostringa estratta nel tipo indicato.

**Sintassi**

    extract(regex, captureGroup, text [, typeLiteral])

**Argomenti**

* *regex:* [espressione regolare](#regular-expressions).
* *captureGroup:* costante `int` positiva che indica il gruppo Capture da estrarre. 0 indica la corrispondenza completa, 1 per il valore corrispondente alle prime '(' parentesi')' nell'espressione regolare, 2 o successivi per le parentesi successive.
* *text:* valore `string` da cercare.
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

<a name="notempty"></a> <a name="isnotempty"></a> <a name="isempty"></a>
### isempty, isnotempty, notempty

    isempty("") == true

True se l'argomento è una stringa vuota o è null. Vedere anche [isnull](#isnull).


**Sintassi**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Restituisce**

Indica se l'argomento è una stringa vuota o isnull.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false


**Esempio**


    T | where isempty(fieldName) | count




### replace

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

| x | str | valore sostituito|
|---|---|---|
| 1 | Number is 1.000000 | Number was: 1.000000|
| 2 | Number is 2.000000 | Number was: 2.000000|
| 3 | Number is 3.000000 | Number was: 3.000000|
| 4 | Number is 4.000000 | Number was: 4.000000|
| 5 | Number is 5.000000 | Number was: 5.000000|
 



### split

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




### strcat

    strcat("hello", " ", "world")

Concatena da 1 a 16 argomenti, che devono essere stringhe.

### strlen

    strlen("hello") == 5

Lunghezza di una stringa.

### substring

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

### tolower

    tolower("HELLO") == "hello"

Converte una stringa in lettere minuscole.

### toupper

    toupper("hello") == "HELLO"

Converte una stringa in lettere maiuscole.



### GUID

    guid(00000000-1111-2222-3333-055567f333de)


## Matrici, oggetti e dynamic

[valori letterali](#dynamic-literals) | [cast](#casting-dynamic-objects) | [operatori](#operators) | [clausole let](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Di seguito il risultato di una query su un'eccezione di Application Insights. Il valore in `details` è una matrice.

![](./media/app-insights-analytics-reference/310.png)  

**Indicizzazione:** indicizzare matrici e oggetti come in JavaScript.

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Usare però `arraylength` e altre funzioni di analisi. Non usare ".length".

**Cast:** in alcuni casi è necessario eseguire il cast di un elemento estratto da un oggetto perché il tipo potrebbe variare. Ad esempio, `summarize...to` richiede un tipo specifico:

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



### Valori letterali di matrice e oggetto

Per creare un valore letterale dinamico, usare `parsejson` (alias `todynamic`) con un argomento di stringa JSON:

* `parsejson('[43, 21, 65]')`: una matrice di numeri
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`  
* `parsejson('21')`: un singolo valore di tipo dinamico che contiene un numero
* `parsejson('"21"')`: un singolo valore di tipo dinamico che contiene una stringa

Si noti che, a differenza di JavaScript, JSON impone l'uso delle virgolette doppie (`"`) per racchiudere le stringhe. Di conseguenza, è in genere più semplice fare riferimento ai valori letterali di stringa con codifica JSON usando virgolette singole (`'`).

Questo esempio crea un valore dinamico e quindi usa i campi:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### Funzioni di oggetti dinamici

|||
|---|---|
| *valore* `in` *matrice*| True se è presente un elemento di *matrice* che è == *valore*<br/>`where City in ('London', 'Paris', 'Rome')`
| *valore* `!in` *matrice*| True se non è presente un elemento di *matrice* che è == *valore*
|[`arraylength(`array`)`](#arraylength)| Null se non è una matrice
|[`extractjson(`path,object`)`](#extractjson)|Usa path per navigare nell'oggetto.
|[`parsejson(`source`)`](#parsejson)| Converte una stringa JSON in un oggetto dinamico.
|[`range(`from,to,step`)`](#range)| Una matrice di valori
|[`mvexpand` listColumn](#mvexpand-operator) | Replica una riga per ogni valore in un elenco in una cella specificata.
|[`summarize buildschema(`column`)`](#buildschema) |Deduce lo schema del tipo dal contenuto della colonna
|[`summarize makelist(`column`)` ](#makelist)| Rende flat gruppi di righe e inserisce i valori della colonna in una matrice.
|[`summarize makeset(`column`)`](#makeset) | Rende flat gruppi di righe e inserisce i valori della colonna in una matrice, senza duplicazione.

### Oggetti dinamici nelle clausole let


Poiché le [clausole let](#let-clause) archiviano i valori dinamici come stringhe, le due clausole seguenti sono equivalenti e richiedono entrambe `parsejson` (o `todynamic`) prima di essere usate:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

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



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Recuperare un elemento specificato da un testo JSON usando un'espressione di percorso. Facoltativamente, convertire la stringa estratta in un tipo specifico.


**Sintassi**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**Restituisce**

Questa funzione esegue una query JsonPath in dataSource che contiene una stringa JSON valida e, facoltativamente, la conversione di tale valore in un tipo diverso, a seconda del terzo argomento.



**Esempio**

La notazione [parentesi quadre] e la notazione punto sono equivalenti:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Suggerimenti per incrementare le prestazioni**

* Applicare le clausole where prima di usare `extractjson()`
* È consigliabile usare una corrispondenza di espressione regolare con [extract](#extract). L'esecuzione risulta molto più rapida ed è efficace se JSON è prodotto in base a un modello.
* Usare `parsejson()` se è necessario estrarre più di un valore da JSON.
* Considerare la possibilità di analizzare JSON al momento dell'inserimento dichiarando il tipo della colonna come dinamico.

### Espressioni di percorso JSON

|||
|---|---|
|`$`  |Oggetto radice|
|`@`  |Oggetto corrente|
|`[0]`  |Indice inferiore di matrice|
|`.` oppure `[0]` | Figlio|

*(Attualmente non vengono implementati caratteri jolly, ricorsione, unione o sezioni).*




### parsejson

Interpreta `string` come un [valore JSON](http://json.org/) e restituisce il valore come `dynamic`. È preferibile rispetto a `extractjson()` quando è necessario estrarre più di un elemento di un oggetto composto JSON.

**Sintassi**

    parsejson(json)

**Argomenti**

* *json:* un documento JSON.

**Restituisce**

Un oggetto di tipo `dynamic` specificato da *json*.

**Esempio**

Nell'esempio seguente, quando `context_custom_metrics` è un valore `string` simile al seguente:

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

Il frammento seguente recupera il valore dello slot `duration` nell'oggetto e da tale valore recupera due slot, `duration.value` e `duration.min` (rispettivamente `118.0` e `110.0`).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

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

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Converte una stringa in un valore dinamico.

### treepath

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

### Nomi

I nomi possono contenere fino a 1024 caratteri. Fanno distinzione tra maiuscole e minuscole e possono contenere lettere, cifre e caratteri di sottolineatura (`_`).

Racchiudere tra virgolette un nome con [' ... '] o [" ... "] per includere altri caratteri o usare una parola chiave come nome. Ad esempio:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|['path\\file\\n'x''] | Usare \\ per i caratteri di escape|
|["d-e.=/f#\\n"] | |
|[@'path\\file'] | Nessun carattere di escape, \\ è un valore letterale|
|[@"\\now & then"] | |
|[where] | Parola chiave della lingua usata come nome|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0810_2016-->