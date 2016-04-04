<properties 
	pageTitle="Operatori e query di Analytics in Application Insights" 
	description="Informazioni di riferimento per gli operatori usati per creare query in Analytics, lo strumento di ricerca avanzato per Application Insights." 
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
	ms.date="03/21/2016" 
	ms.author="awills"/>



# Query in Analytics


[Analytics](app-analytics.md) è lo strumento di ricerca avanzato incluso in [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Analytics.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

Una query sulla telemetria è costituita da un riferimento a un flusso di origine, seguito da una pipeline di filtri. Ad esempio:


```AIQL
requests
| where client_City == "London" and timestamp > ago(3d)
| count
```
    
Ogni filtro preceduto dal carattere di barra verticale `|` è un'istanza di un *operatore*, con alcuni parametri. L'input per l'operatore è la tabella risultante dalla pipeline precedente. Nella maggior parte dei casi, i parametri sono [espressioni scalari](app-analytics-scalars.md) sulle colonne dell'input. In alcuni casi, i parametri sono i nomi delle colonne di input e, in altri casi, il parametro è una seconda tabella. Il risultato di una query è sempre una tabella, anche se include solo una colonna e una riga.

Una query può essere preceduta da una o più [clausole let](#let-clause), che definiscono scalari, tabelle o funzioni che possono essere usate nella query.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` viene usato negli esempi di query seguenti per denotare la tabella di origine o la pipeline precedente.

## Operatore count

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



## Operatore extend

     T | extend duration = stopTime - startTime

Aggiunge una o più colonne calcolate a una tabella.


**Sintassi**

    T | extend ColumnName = Expression [, ...]

**Argomenti**

* *T:* tabella di input.
* *ColumnName:* nome di una colonna da aggiungere. 
* *Expression:* calcolo eseguito sulle colonne esistenti.

**Restituisce**

Una copia della tabella di input, con le colonne aggiuntive specificate.

**Suggerimenti**

* Usare invece [`project`](#project-operator), se si vuole anche rimuovere o rinominare alcune colonne.
* Non usare `extend` semplicemente per ottenere un nome più breve da usare in un'espressione lunga. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    Le colonne native della tabella sono state indicizzate. Il nuovo nome definisce una colonna aggiuntiva non indicizzata, quindi è probabile che la query venga eseguita più lentamente.

**Esempio**

```AIQL
traces
| extend
    Age = now() - timestamp
```


## Operatore join

    Table1 | join (Table2) on CommonColumn

Unisce le righe di due tabelle associando i valori della colonna specificata.


**Sintassi**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argomenti**

* *Table1*: "lato sinistro" del join.
* *Table2*: "lato destro" del join. Può trattarsi di un'espressione di query annidata che restituisce una tabella.
* *CommonColumn*: colonna con lo stesso nome nelle due tabelle.
* *Kind*: specifica come le righe delle due tabelle debbano essere associate.

**Restituisce**

Una tabella con:

* Una colonna per ogni colonna in ognuna delle due tabelle, incluse le chiavi corrispondenti. Le colonne del lato destro verranno automaticamente rinominate in caso di conflitti tra i nomi.
* Una riga per ogni corrispondenza tra le tabelle di input. Una corrispondenza è una riga selezionata da una tabella che ha lo stesso valore per tutti i campi `on` di una riga nell'altra tabella. 

* `Kind` non specificato.

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

[Informazioni sui tipi di join](app-analytics-samples.md#join-flavors).

## Clausola let

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

Una clausola let associa un nome a un risultato tabulare, a un valore scalare o a una funzione. La clausola è un prefisso di una query e l'ambito del binding è tale query. Let non consente di assegnare un nome agli elementi usati più avanti nella sessione.

**Sintassi**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](app-analytics-scalars.md#dynamic-type)
* *plain\_query:* una query non preceduta da una clausola let.

**esempi**




    let rows(n:long) = range steps from 1 to n step 1;
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

## Operatore limit

     T | limit 5

Restituisce un numero massimo specificato di righe dalla tabella di input. Non c'è garanzia su quali record verranno restituiti. Per ottenere record specifici, usare [`top`](#top-operator).

**Alias** `take`

**Sintassi**

    T | limit NumberOfRows


**Suggerimenti**

`Take` è un modo semplice ed efficiente per visualizzare un esempio dei risultati quando si lavora in modo interattivo. Tenere presente che non è garantito che vengano generate righe particolari o in un ordine particolare.

Esiste un limite implicito per il numero di righe restituite al client, anche se non si usa `take`. Per aumentare questo limite, usare l'opzione di richiesta client `notruncation`.



## Operatore mvexpand

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

* *ColumnName:* nel risultato, le matrici nella colonna denominata vengono espanse su più righe. 
* *ArrayExpression:* espressione che supporta una matrice. Se viene usato questo formato, viene aggiunta una nuova colonna e quella esistente viene mantenuta.
* *Name:* nome della nuova colonna.
* *Typename:* esegue il cast dell'espressione espansa in un tipo particolare.
* *RowLimit:* numero massimo di righe generate da ogni riga originale. Il valore predefinito è 128.

**Restituisce**

Più righe per ogni valore in una matrice nella colonna denominata o nell'espressione di matrice.

La colonna espansa ha sempre il tipo dinamico. Usare un cast, ad esempio `todatetime()` o `toint()`, per calcolare o aggregare i valori.

Sono supportate due modalità di espansione dei contenitori delle proprietà:

* `bagexpansion=bag`: i contenitori delle proprietà vengono espansi in contenitori delle proprietà a voce singola. Questa è l'espansione predefinita.
* `bagexpansion=array`: i contenitori delle proprietà vengono espansi in strutture di matrici a due elementi `[`*key*`,`*value*`]`, che consentono l'accesso uniforme alle chiavi e ai valori (oltre, ad esempio, all'esecuzione di un'aggregazione di conteggi di valori univoci sui nomi delle proprietà). 

**esempi**


    exceptions | take 1 
    | mvexpand details[0]

Divide il record di un'eccezione in righe per ogni elemento nel campo dei dettagli.

Vedere [Grafico del conteggio delle attività in esecuzione nel tempo](app-analytics-samples.md#concurrent-activities).


## Operatore parse

    T | parse "I am 63 next birthday" with "I am" Year:int "next birthday"

    T | parse kind=regex "My 62nd birthday" 
        with "My" Year:regex("[0..9]+") regex("..") "birthday"

Estrae i valori da una stringa. È possibile usare la corrispondenza con espressioni semplici o regolari.

Gli elementi nella clausola `with` vengono confrontati a loro volta con la stringa di origine. Ogni elemento usa un blocco del testo di origine. Se è una stringa di testo normale, il cursore corrispondente si sposta alla stessa distanza della corrispondenza. Se è una colonna con un nome di tipo, il cursore si sposta fino alla distanza sufficiente per analizzare il tipo specificato. Le corrispondenze di stringa si spostano fino a trovare una corrispondenza con l'elemento successivo. Se è un'espressione regex, viene associata l'espressione regolare (e la colonna risultante ha sempre il tipo stringa).

**Sintassi**

    T | parse StringExpression with [SimpleMatch | Column:Type] ...

    T | parse kind=regex StringExpression 
        with [SimpleMatch | Column : regex("Regex")] ...

**Argomenti**

* *T:* tabella di input.
* *kind:* simple o regex. Il valore predefinito è simple.
* *StringExpression:* espressione che restituisce una stringa o può essere convertita in una stringa.
* *SimpleMatch:* stringa che corrisponde alla parte successiva del testo.
* *Column:* specifica la nuova colonna a cui assegnare una corrispondenza.
* *Type:* specifica come analizzare la parte successiva della stringa di origine.
* *Regex:* espressione regolare per la corrispondenza con la parte successiva della stringa. 

**Restituisce**

La tabella di input, estesa in base all'elenco di colonne.


**esempi**

L'operatore `parse` consente di applicare facilmente un operatore `extend` a una tabella usando più applicazioni `extract` nella stessa espressione `string`. Ciò è molto utile quando la tabella include una colonna `string` contenente diversi valori che si vuole dividere in singole colonne, ad esempio una colonna generata da un'istruzione di traccia ("`printf`"/"`Console.WriteLine`") dello sviluppatore.

Nell'esempio seguente si supponga che la colonna `EventNarrative` della tabella `StormEvents` contenga stringhe nel formato `{0} at {1} crested at {2} feet around {3} on {4} {5}`. L'operazione seguente estenderà la tabella con due colonne: `SwathSize` e `FellLocation`.


|EventNarrative|
|---|
|The Green River at Brownsville crested at 18.8 feet around 0930EST on December 12. Flood stage at Brownsville is 18 feet. Minor flooding occurs at this level. The river overflows lock walls and some of the lower banks, along with some agricultural bottom land.|
|The Rolling Fork River at Boston crested at 39.3 feet around 1700EST on December 12. Flood stage at Boston is 35 feet. Minor flooding occurs at this level, with some agricultural bottom land covered.|
|The Green River at Woodbury crested at 36.7 feet around 0600EST on December 16. Flood stage at Woodbury is 33 feet. Minor flooding occurs at this level, with some lowlands around the town of Woodbury covered with water.|
|The Ohio River at Tell City crested at 39.0 feet around 7 AM EST on December 18. Flood stage at Tell City is 38 feet. At this level, the river begins to overflow its banks above the gage. Indiana Highway 66 floods between Rome and Derby.|

```AIQL

StormEvents 
|  parse EventNarrative 
   with RiverName:string 
        "at" 
        Location:string 
        "crested at" 
        Height:double  
        "feet around" 
        Time:string 
        "on" 
        Month:string 
        " " 
        Day:long 
        "." 
        notImportant:string
| project RiverName , Location , Height , Time , Month , Day

```

|RiverName|Percorso|Altezza|Time|Mese|Giorno|
|---|---|---|---|---|---|
|The Green River | Woodbury |36\.7| 0600EST | December|16|
|The Rolling Fork River | Boston |39\.3| 1700EST | December|12|
|The Green River | Brownsville |18\.8| 0930EST | December|12|
|The Ohio River | Tell City |39| 7 AM EST | December|18|

È anche possibile trovare la corrispondenza usando espressioni regolari. Si ottiene lo stesso risultato, ma tutte le colonne dei risultati hanno il tipo stringa:

```AIQL

StormEvents
| parse kind=regex EventNarrative 
  with RiverName:regex("(\\s?[a-zA-Z]+\\s?)+") 
  "at" Location:regex(".*") 
  "crested at " Height:regex("\\d+\\.\\d+") 
  " feet around" Time:regex(".*") 
  "on " Month:regex("(December|November|October)") 
   " " Day:regex("\\d+") 
   "." notImportant:regex(".*")
| project RiverName , Location , Height , Time , Month , Day
```


## Operatore project

    T | project cost=price*quantity, price

Selezionare le colonne da includere, rinominare o rimuovere e inserire le nuove colonne calcolate. L'ordine delle colonne nel risultato viene specificato dall'ordine degli argomenti. Solo le colonne specificate negli argomenti vengono incluse nel risultato, mentre le altre nell'input vengono rimosse. Vedere anche `extend`.


**Sintassi**

    T | project ColumnName [= Expression] [, ...]

**Argomenti**

* *T:* tabella di input.
* *ColumnName:* nome di una colonna da visualizzare nell'output. Se *Expression* non è presente, deve essere visualizzata un colonna con quel nome nell'input. 
* *Expression:* espressione scalare facoltativa che fa riferimento alle colonne di input. 

    È consentito restituire una nuova colonna calcolata con lo stesso nome di una colonna esistente nell'input.

**Restituisce**

Una tabella che ha le colonne denominate come argomenti e lo stesso numero di righe della tabella di input.

**Esempio**

L'esempio seguente illustra diverse tipologie di manipolazioni che possono essere eseguite usando l'operatore `project`. La tabella di input `T` include tre colonne di tipo `int`: `A`, `B` e `C`.

```AIQL
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```


[Altri esempi](app-analytics-samples.md#activities).


## Operatore range

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
* *Stop:* il valore più alto che viene generato nell'output (o un limite per il valore più alto, se *step* supera questo valore).
* *Step:* la differenza tra due valori consecutivi. 

Gli argomenti devono essere valori numerici, date o intervalli di tempo. Non possono fare riferimento alle colonne di nessuna tabella. Per calcolare l'intervallo basato su una tabella di input, usare la [*funzione* range](app-analytics-scalars.md#range), ad esempio con l'[operatore mvexpand](#mvexpand-operator).

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

Illustra come l'operatore `range` possa essere usato per creare una piccola tabella delle dimensioni ad hoc che viene quindi usata per introdurre gli zeri dove i dati di origine sono senza valori.

## Operatore reduce

    exceptions | reduce by outerMessage

Cerca di raggruppare record simili. Per ogni gruppo, l'operatore restituisce l'oggetto `Pattern` che descrive meglio il gruppo e l'oggetto `Count` dei record del gruppo.


![](./media/app-analytics-queries/reduce.png)

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


## Direttiva render

    T | render [ table | timechart  | barchart | piechart ]

Indica al livello di presentazione la modalità di visualizzazione della tabella. Deve essere l'ultimo elemento della pipe. Si tratta di una valida alternativa all'uso dei controlli su schermo che consente di salvare una query con un metodo di presentazione specifico.


## Operatore sort 

    T | sort by country asc, price desc

Ordina le righe della tabella input in base a una o più colonne.

**Alias** `order`

**Sintassi**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argomenti**

* *T:* input della tabella da ordinare.
* *Column:* colonna di *T* in base alla quale eseguire l'ordinamento. Il tipo dei valori deve essere numerico, di data, intervallo di tempo o stringa.
* `asc` Ordina in senso crescente, dal basso verso l'alto. Il valore predefinito è `desc`, ovvero decrescente dall'alto verso il basso.

**Esempio**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Tutte le righe della tabella Traces con uno specifico `ActivityId`, ordinate in base al timestamp.

## Operatore summarize

Produce una tabella che aggrega il contenuto della tabella di input.
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Tabella che indica il numero, la durata media della richiesta e il set di città in ogni paese. Nell'output è presente una riga per ogni singolo paese. Le colonne di output indicano il conteggio, la durata media, le città e il paese. Tutte le altre colonne di input vengono ignorate.


    T | summarize count() by price_range=bin(price, 10.0)

Tabella che indica quanti elementi presentano prezzi in ogni intervallo [0,10.0], [10.0,20.0] e così via. Questo esempio ha una colonna per il conteggio e una per l'intervallo di prezzi. Tutte le altre colonne di input vengono ignorate.

[Altri esempi](app-analytics-aggregations.md).



**Sintassi**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argomenti**

* *Column:* nome facoltativo per una colonna di risultati. Il valore predefinito è un nome derivato dall'espressione.
* *Aggregation:* chiamata a una [funzione di aggregazione](app-analytics-aggregations.md), ad esempio `count()` o `avg()` con nomi di colonna come argomenti. Vedere l'[elenco delle funzioni di aggregazione](app-analytics-aggregations.md).
* *GroupExpression:* espressione sulle colonne che fornisce un set di valori distinti. Si tratta in genere di un nome di colonna che fornisce già un set di valori limitato oppure di `bin()` con una colonna numerica o di data e ora come argomento. 

Se si specifica un'espressione numerica o di data e ora senza usare `bin()`, Analytics applica automaticamente l'espressione con un intervallo di `1h` per i valori di data/ora o `1.0` per i numeri.

Se non si specifica *GroupExpression*, l'intera tabella viene riepilogata in un'unica riga di output.



**Restituisce**

Le righe di input vengono disposte in gruppi con gli stessi valori delle espressioni `by`. Vengono quindi calcolate per ogni gruppo le funzioni di aggregazione specificate, generando una riga per ogni gruppo. Il risultato contiene le colonne `by`, oltre ad almeno una colonna per ogni aggregazione calcolata. Alcune funzioni di aggregazione restituiscono più colonne.

Il risultato contiene una riga per ogni singola combinazione di valori `by`. Per riepilogare gli intervalli di valori numerici, usare `bin()` per ridurre gli intervalli a valori discreti.

**Nota**

Anche se è possibile specificare espressioni arbitrarie per le espressioni di aggregazione e raggruppamento, è preferibile usare nomi di colonna semplici o applicare `bin()` a una colonna numerica.



## Operatore take

Alias di [limit](#limit-operator)


## Operatore top

    T | top 5 by Name desc

Restituisce i primi *N* record ordinati in base alle colonne specificate.


**Sintassi**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**Argomenti**

* *NumberOfRows:* numero di righe di *T* da restituire.
* *Sort\_expression:* espressione in base alla quale ordinare le righe. In genere è semplicemente un nome di colonna. È possibile specificare più di un elemento sort\_expression.
* È possibile che venga visualizzato `asc` o `desc` (valore predefinito) per controllare se la selezione nell'intervallo è effettivamente in ordine crescente o decrescente.


**Suggerimenti**

`top 5 by name` è all'apparenza equivalente a `sort by name | take 5`, ma viene eseguito più rapidamente e restituisce sempre risultati ordinati, mentre `take` non offre questo tipo di garanzia.


## Operatore union

     Table1 | union Table2, Table3

Considera due o più tabelle e restituisce le righe di tutte.

**Sintassi**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argomenti**

* *Table1*, *Table2*...
 *  Il nome di una tabella, ad esempio `events`.
 *  Un'espressione di query, ad esempio `(events | where id==42)`
 *  Un set di tabelle specificato con un carattere jolly. Ad esempio, `E*` creerà l'unione di tutte le tabelle del database i cui nomi iniziano con `E`.
* `kind`: 
 * `inner`: il risultato include il subset di colonne comuni a tutte le tabelle di input.
 * `outer`: il risultato include tutte le colonne presenti in tutti gli input. Le celle non definite da una riga di input vengono impostate su `null`.
* `withsource=`*ColumnName:* se specificato, l'output includerà una colonna denominata *ColumnName* il cui valore indica quale tabella di origine ha contribuito a ogni riga.

**Restituisce**

Una tabella con un numero di righe corrispondente a quello delle righe di tutte le tabelle di input.

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
Numero di utenti distinti che hanno generato un evento `exceptions` o un evento `traces` nel corso del giorno passato. Nel risultato la colonna "SourceTable" indicherà "Query" o "Command".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Questa versione più efficiente genera lo stesso risultato. Filtra ogni tabella prima di creare l'unione.

## Operatore where

     T | where fruit=="apple"

Filtra una tabella per trovare il subset di righe che soddisfano un predicato.

**Alias** `filter`

**Sintassi**

    T | where Predicate

**Argomenti**

* *T*: input tabulare i cui record devono essere filtrati.
* *Predicate:* [espressione](app-analytics-scalars.md#boolean) `boolean` sulle colonne di *T*. Viene valutato per ogni riga in *T*.

**Restituisce**

Righe in *T* per cui *Predicate* è `true`.

**Suggerimenti**

Per ottenere prestazioni ottimali:

* **Usare confronti semplici** tra i nomi di colonna e le costanti. Con "costante" si intende costante nella tabella, quindi `now()` e `ago()` sono validi e sono dunque valori scalari assegnati con un'istruzione [`let`](app-analytics-syntax.md#let-statements).

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





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->