<properties 
	pageTitle="Istruzioni di Application Analytics" 
	description="Query, espressioni e istruzioni ";let"; in Application Analytics, lo strumento di ricerca avanzato per Application Insights." 
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
	ms.date="03/01/2016" 
	ms.author="awills"/>


 
# Istruzioni di Application Analytics

[Application Analytics](app-analytics.md) è un motore di ricerca avanzato per i dati di telemetria di [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Application Analytics (CSL).


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Modello di dati

In CSL:

* Un *database* contiene nessuna o più *tabelle* denominate.
* Una *tabella* contiene:
 * Una o più *colonne* denominate. Ogni colonna ha un *tipo*.
 * Una o più *righe*.
* Ogni riga include una o più *celle*, una per ogni colonna della tabella.
* Una cella può contenere un valore del tipo di colonna, o `null`.


## Nomi delle entità

Ogni colonna, tabella o database ha un nome univoco all'interno del contenitore.

Fare riferimento a un'entità tramite il relativo nome (se il contesto è chiaro) o tramite il nome completo del relativo contenitore. Ad esempio, è possibile fare riferimento a `MyColumn` anche come `MyTable.MyColumn` o `MyDatabase.MyTable.MyColumn`.

I nomi possono contenere fino a 1024 caratteri. Fanno distinzione tra maiuscole e minuscole e possono contenere lettere, cifre e caratteri di sottolineatura (`_`).

Inoltre, i nomi potrebbero essere racchiusi tra virgolette in modo da contenere altri caratteri. Ad esempio:

|||
|---|---|
|`['path\\file\n\'x\'']` | Usare `` per i caratteri di escape|
|`["d-e.=/f#\n"]` | |
|`[@'path\file']`| Nessun carattere di escape - `` è un valore letterale|
|`[@"\now & then"]` | |
|`[where]` | Parola chiave della lingua usata come nome|

I nomi possono anche essere qualificati con il nome del database (vedere [Query tra database](#cross-database-queries))

```
database("MyDb").Table
```


## Istruzioni

Esistono quattro tipi di istruzioni in CSL:

### Query di dati
  
Richieste di sola lettura sui dati archiviati in Application Analytics. Ad esempio:

* `event` : restituisce tutti i record nella tabella denominata "event".
* `event | count` : restituisce un numero di record nella tabella "event".

    
## Istruzioni "let"

#### Panoramica
È possibile includere un prefisso all'istruzione della query sui dati mediante una o più istruzioni "let". Questo consente di associare un nome (un nome entità valido) a un'espressione. I nomi definiti da un'istruzione "let" possono quindi essere usati una o più volte nelle istruzioni della query sui dati seguenti.

Un'istruzione "let" può associare un nome alle espressioni del tipo seguente:
1. Valori scalari
2. Dati tabulari 

Durante l'associazione a dati tabulari, è possibile alzare di livello l'istruzione "let" a "view" - che consentirà all'istruzione di partecipare a operazioni "union *".

#### Sintassi

`let <name> = <expression>`

<expression> può essere una dichiarazione lambda con nessuno, uno o più argomenti:

`() {...}`

`(<arg0>:<type0>, ...) {...}`

Esempi: l'esempio seguente associa il nome 'x' al valore letterale scalare '1':

```
let x=1;
range y from x to x step x
```

L'esempio seguente associa un'ora di log al nome Window, quindi esegue un self-join in Window:


```
let Window=Logs | where Timestamp > ago(1h);
Window | where  ... | join (Window | where …) on ActivityId| ...
```

Gli esempi seguenti illustrano l'uso di un'istruzione "let" con un'espressione lambda:


```
let Test = () { range x from 1 to 10 step 1 };
Test | count

let step=1;
let Test = (start:long, end:long) { range x from start to end step 1 };
Test(1, 10) | count
```

Uso della parola chiave 'view' per alzare di livello le istruzioni "let" a View in modo da partecipare alle operazioni "union *".


```
let Test1 = view () { range x from start to end step 1 };
let Test2 = view () { range x from start to end step 1 };
union * | count
// Result: 20
```


## Istruzione "restrict"

#### Panoramica
È possibile limitare l'accesso alle query nell'ambito dell'elenco delle istruzioni tramite l'istruzione successiva:


```
restrict access to (<entity1, entity2, ...>);
```
 
Questa istruzione consente l'accesso unicamente a quelle entità (istruzioni "let" o entità Tabular) consentite dall'istruzione restrict.
 
Esempi:

```
// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```


## Composizione della query

Una *query* segue il modello:

- *source* | *filter* | *filter* ...

Ad esempio:


```
Logs | where Timestamp > ago(1d) | count
```

* *Source* è il nome di una tabella di database o di una tabella di risultati definita in precedenza.
* Ogni elemento *filter* richiama un operatore query come ad esempio `where` o `count`, con i parametri appropriati. I parametri possono essere *espressioni scalari*, *query di dati* annidati o nomi di colonna.

Ad esempio:


```
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
``` 

## Istruzioni "let"

Un'istruzione "let" può essere utilizzata per definire le funzioni - espressioni denominate che accettano nessuno o più argomenti e restituiscono valori. Altre istruzioni sono poi in grado di "richiamare" quelle funzioni.

### Valori denominati

Definizione dei nomi per rappresentare i valori scalari:


```
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

Definizione dei nomi per rappresentare i risultati della query:


```
let Cities = Events | summarize dcount(city) by country;
Cities | take 10
```

Questo è particolarmente utile per un self-join:


```
let Recent = Events | where timestamp > ago(7d);
Recent | where name contains "session_started" 
| project start = timestamp, session_id
| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
    on session_id
| extend duration = stop - start 
```

## Funzioni denominate

Definizione di funzioni che restituiscono risultati scalari:


```
let square = (n:long){n*n};
// yield 9 rows
Events | take square(3)    
```

Definizione di funzioni che restituiscono risultati di query:


```
let TopEvents= (n:long, when:datetime){Events 
                | where timestamp > when | take n};
TopEvents(5, ago(7d)) 
```

I parametri per le funzioni denominate devono essere valori scalari.





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->