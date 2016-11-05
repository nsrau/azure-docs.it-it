---
title: Uso delle funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake | Microsoft Docs
description: 'Informazioni su come usare le funzioni finestra di U-SQL. '
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca

---
# Uso delle funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake
Le funzioni finestra sono state introdotte nello Standard SQL ISO/ANSI nel 2003. U-SQL adotta un subset di funzioni finestra definite dallo Standard SQL ANSI.

Le funzioni finestra vengono usate per eseguire calcoli in set di righe chiamati *finestre*. Le finestre vengono definite dalla clausola OVER. Le funzioni finestra risolvono alcuni scenari chiave in modo estremamente efficiente.

Questa guida all'apprendimento usa due set di dati di esempio per illustrare in dettaglio alcuni scenari di esempio in cui è possibile applicare le funzioni finestra. Per altre informazioni, vedere [Riferimento al linguaggio U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

Le funzioni finestra sono classificate come segue:

* [Funzioni di aggregazione di report](#reporting-aggregation-functions), ad esempio SUM o AVG
* [Funzioni di rango](#ranking-functions), ad esempio DENSE\_RANK, ROW\_NUMBER, NTILE e RANK
* [Funzioni analitiche](#analytic-functions), ad esempio distribuzione cumulativa, percentili o accessi ai dati da una riga precedente nello stesso set di risultati senza usare un self-join

**Prerequisiti:**

* Eseguire le due esercitazioni seguenti:
  
  * [Introduzione all'uso di Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
  * [Introduzione all'uso di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Creare un account Analisi Data Lake come indicato in [Introduzione all'uso di Azure Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Creare un progetto U-SQL di Visual Studio, come indicato in [Introduzione all'uso di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md).

## Set di dati di esempio
Questa esercitazione usa due set di dati:

* QueryLog
  
    QueryLog rappresenta un elenco delle ricerche effettuate dalle persone nel motore di ricerca. Ogni log di query include:
  
        - Query - What the user was searching for.
        - Latency - How fast the query came back to the user in milliseconds.
        - Vertical - What kind of content the user was interested in (Web links, Images, Videos).
  
    Copiare e incollare lo script seguente nel progetto U-SQL per creare il set di righe QueryLog:
  
        @querylog = 
            SELECT * FROM ( VALUES
                ("Banana"  , 300, "Image" ),
                ("Cherry"  , 300, "Image" ),
                ("Durian"  , 500, "Image" ),
                ("Apple"   , 100, "Web"   ),
                ("Fig"     , 200, "Web"   ),
                ("Papaya"  , 200, "Web"   ),
                ("Avocado" , 300, "Web"   ),
                ("Cherry"  , 400, "Web"   ),
                ("Durian"  , 500, "Web"   ) )
            AS T(Query,Latency,Vertical);
  
    In pratica, i dati vengono per lo più archiviati in un file di dati. Si accederà a questi dati in un file con valori delimitati da tabulazioni usando il codice seguente:
  
        @querylog = 
        EXTRACT 
            Query    string, 
            Latency  int, 
            Vertical string
        FROM "/Samples/QueryLog.tsv"
        USING Extractors.Tsv();
* Employees
  
    Il set di dati Employees include i campi seguenti:
  
        - EmpID - Employee ID.
        - EmpName  Employee name.
        - DeptName - Department name. 
        - DeptID - Deparment ID.
        - Salary - Employee salary.
  
    Copiare e incollare lo script seguente nel progetto U-SQL per creare il set di righe Employees:
  
        @employees = 
            SELECT * FROM ( VALUES
                (1, "Noah",   "Engineering", 100, 10000),
                (2, "Sophia", "Engineering", 100, 20000),
                (3, "Liam",   "Engineering", 100, 30000),
                (4, "Emma",   "HR",          200, 10000),
                (5, "Jacob",  "HR",          200, 10000),
                (6, "Olivia", "HR",          200, 10000),
                (7, "Mason",  "Executive",   300, 50000),
                (8, "Ava",    "Marketing",   400, 15000),
                (9, "Ethan",  "Marketing",   400, 10000) )
            AS T(EmpID, EmpName, DeptName, DeptID, Salary);
  
    L'istruzione seguente illustra la creazione di set di righe estraendole da un file di dati.
  
        @employees = 
        EXTRACT 
            EmpID    int, 
            EmpName  string, 
            DeptName string, 
            DeptID   int, 
            Salary   int
        FROM "/Samples/Employees.tsv"
        USING Extractors.Tsv();

Quando si testano gli esempi nell'esercitazione, è necessario includere le definizioni dei set di righe. U-SQL richiede la definizione solo dei set di righe usati. Alcuni esempi richiedono un solo set di righe.

È anche necessario aggiungere l'istruzione seguente per generare il set di righe di risultati in un file di dati:

    OUTPUT @result TO "/wfresult.csv" 
        USING Outputters.Csv();

 La maggior parte degli esempi usa la variabile denominata **@result** per i risultati.

## Confrontare le funzioni finestra con il raggruppamento
Funzioni finestra e raggruppamento sono concettualmente correlati ma anche diversi. È utile comprendere questa relazione.

### Usare aggregazione e raggruppamento
La query seguente usa un'aggregazione per calcolare le retribuzioni totali per tutti i dipendenti:

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;

> [!NOTE]
> Per istruzioni relative a test e controllo dell'output, vedere [Introduzione all'uso di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
> 
> 

Il risultato è una singola riga con una singola colonna. $ 165000 è la somma del valore Salary dell'intera tabella.

| TotalSalary |
| --- |
| 165000 |

> [!NOTE]
> Se non si ha familiarità con le funzioni finestra, è utile ricordare i numeri presenti nell'output.
> 
> 

L'istruzione seguente usa la clausola GROUP BY per calcolare la retribuzione totale per ogni reparto:

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

I risultati sono:

| DeptName | SalaryByDept |
| --- | --- |
| Engineering |60000 |
| HR |30000 |
| Executive |50000 |
| Marketing |25000 |

La somma della colonna SalaryByDept è $ 165000, che corrisponde all'importo nell'ultimo script.

In entrambi i casi sono presenti meno righe di output che righe di input:

* Senza GROUP BY l'aggregazione comprime tutte le righe in una sola.
* Con GROUP BY sono presenti N righe di output, dove N è il numero di valori distinti visualizzati nei dati. In questo caso, saranno restituite 4 righe nell'output.

### Usare una funzione finestra
La clausola OVER nell'esempio seguente è vuota. Definisce la "finestra" per includere tutte le righe. SUM in questo esempio viene applicata alla clausola OVER che la precede.

È possibile leggere questa query come: "La somma della retribuzione in una finestra di tutte le righe".

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

A differenza di GROUP BY sono presenti tante righe di output quante sono quelle di input:

| EmpName | TotalAllDepts |
| --- | --- |
| Noah |165000 |
| Sophia |165000 |
| Liam |165000 |
| Emma |165000 |
| Jacob |165000 |
| Olivia |165000 |
| Mason |165000 |
| Ava |165000 |
| Ethan |165000 |

Il valore 165000 (il totale di tutte le retribuzioni) viene inserito in ogni riga di output. Il totale deriva dalla "finestra" di tutte le righe, che include quindi tutte le retribuzioni.

L'esempio successivo illustra come affinare la "finestra" per elencare tutti i dipendenti, il reparto e la retribuzione totale per il reparto. PARTITION BY viene aggiunta alla clausola OVER.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

I risultati sono:

| EmpName | DeptName | SalaryByDep |
| --- | --- | --- |
| Noah |Engineering |60000 |
| Sophia |Engineering |60000 |
| Liam |Engineering |60000 |
| Mason |Executive |50000 |
| Emma |HR |30000 |
| Jacob |HR |30000 |
| Olivia |HR |30000 |
| Ava |Marketing |25000 |
| Ethan |Marketing |25000 |

Anche in questo caso, il numero di righe di input è uguale a quello delle righe di output. Tuttavia, per ogni riga è indicata una retribuzione totale per il reparto corrispondente.

## Funzioni di aggregazione di report
Le funzioni finestra supportano anche le aggregazioni seguenti:

* COUNT
* SUM
* MIN
* MAX
* MEDIA
* STDEV
* VAR

Ecco la sintassi:

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Note:

* Per impostazione predefinita, le funzioni di aggregazione, eccetto COUNT, ignorano i valori Null.
* Quando le funzioni di aggregazione vengono specificate insieme alla clausola OVER, la clausola ORDER BY non è consentita nella clausola OVER.

### Usare SUM
L'esempio seguente aggiunge una retribuzione totale per reparto a ogni riga di input:

    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Ecco l'output:

| EmpID | EmpName | DeptName | DeptID | Salary | TotalByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Engineering |100 |10000 |60000 |
| 2 |Sophia |Engineering |100 |20000 |60000 |
| 3 |Liam |Engineering |100 |30000 |60000 |
| 7 |Mason |Executive |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |30000 |
| 5 |Jacob |HR |200 |10000 |30000 |
| 6 |Olivia |HR |200 |10000 |30000 |
| 8 |Ava |Marketing |400 |15000 |25000 |
| 9 |Ethan |Marketing |400 |10000 |25000 |

### Usare COUNT
L'esempio seguente aggiunge un campo a ogni riga per visualizzare i numero totale di dipendenti in ogni reparto.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

Ecco il risultato:

| EmpID | EmpName | DeptName | DeptID | Salary | CountByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Engineering |100 |10000 |3 |
| 2 |Sophia |Engineering |100 |20000 |3 |
| 3 |Liam |Engineering |100 |30000 |3 |
| 7 |Mason |Executive |300 |50000 |1 |
| 4 |Emma |HR |200 |10000 |3 |
| 5 |Jacob |HR |200 |10000 |3 |
| 6 |Olivia |HR |200 |10000 |3 |
| 8 |Ava |Marketing |400 |15000 |2 |
| 9 |Ethan |Marketing |400 |10000 |2 |

### Usare MIN e MAX
L'esempio seguente aggiunge un campo a ogni riga per visualizzare la retribuzione più bassa di ogni reparto.

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

I risultati sono:

| EmpID | EmpName | DeptName | DeptID | Salary | MinSalary |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Engineering |100 |10000 |10000 |
| 2 |Sophia |Engineering |100 |20000 |10000 |
| 3 |Liam |Engineering |100 |30000 |10000 |
| 7 |Mason |Executive |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |10000 |
| 5 |Jacob |HR |200 |10000 |10000 |
| 6 |Olivia |HR |200 |10000 |10000 |
| 8 |Ava |Marketing |400 |15000 |10000 |
| 9 |Ethan |Marketing |400 |10000 |10000 |

Sostituire MIN con MAX, quindi provare a eseguire la query.

## Funzioni di rango
Le funzioni di rango restituiscono un valore di rango (long) per ogni riga in ogni partizione definita dalle clausole PARTITION BY e OVER. L'ordine di rango viene controllato da ORDER BY nella clausola OVER.

Di seguito sono elencate le funzioni di rango supportate:

* RANK
* DENSE\_RANK
* NTILE
* ROW\_NUMBER

**Sintassi:**

    [ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
        OVER (
            [PARTITION BY <identifier, > …[n]]
            [ORDER BY <identifier, > …[n] [ASC|DESC]] 
    ) AS <alias>

* La clausola ORDER BY è facoltativa per le funzioni di rango. Se ORDER BY viene specificata, determina l'ordine di rango. Se ORDER BY non viene specificata, U-SQL assegna i valori in base all'ordine in cui legge i record. Nel caso in cui la clausola ORDER BY non sia specificata, viene restituito un valore non deterministico di numero di riga, rango o rango di tipo dense.
* NTILE richiede un'espressione che restituisce un intero positivo. Questo numero specifica il numero di gruppi in cui è necessario suddividere ogni partizione. Questo identificatore viene usato solo con la funzione di rango NTILE.

Per altre informazioni sulla clausola OVER, vedere [Riferimento al linguaggio U-SQL]().

Le funzioni ROW\_NUMBER, RANK e DENSE\_RANK assegnano tutte numeri alle righe in una finestra. Invece di descriverle separatamente, è più intuitivo vedere come rispondono allo stesso input.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;

Si noti che le clausole OVER sono identiche. Ecco il risultato:

| Query | Latency:int | Vertical | RowNumber | Rank | DenseRank |
| --- | --- | --- | --- | --- | --- |
| Banana |300 |Image |1 |1 |1 |
| Cherry |300 |Image |2 |1 |1 |
| Durian |500 |Image |3 |3 |2 |
| Apple |100 |Web |1 |1 |1 |
| Fig |200 |Web |2 |2 |2 |
| Papaya |200 |Web |3 |2 |2 |
| Fig |300 |Web |4 |4 |3 |
| Cherry |400 |Web |5 |5 |4 |
| Durian |500 |Web |6 |6 |5 |

### ROW\_NUMBER
All'interno di ogni finestra (Vertical, che sia Image o Web), il numero di riga viene aumentato di 1 in base all'ordine definito da Latency.

![Funzione finestra ROW\_NUMBER di U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### RANK
Diversamente da ROW\_NUMBER (), RANK() prende in considerazione il valore di Latency specificato nella clausola ORDER BY per la finestra.

RANK inizia con (1,1,3) perché i primi due valori per Latency sono uguali. Il valore successivo è quindi 3, perché il valore di Latency è passato a 500. Il punto chiave è che anche se ai valori duplicati viene attribuito lo stesso rango, il numero di RANK "passerà" al valore ROW\_NUMBER successivo. Come si può vedere, questo schema viene ripetuto con la sequenza (2,2,4) in Vertical "Web".

![Funzione finestra RANK di U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### DENSE\_RANK
DENSE\_RANK è uguale a RANK, eccetto il fatto che non "passa" al valore ROW\_NUMBER successivo, ma al numero successivo nella sequenza. Notare le sequenze (1,1,2) e (2,2,3) nell'esempio.

![Funzione finestra DENSE\_RANK di U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### Osservazioni
* Se non si specifica ORDER BY, la funzione di rango verrà applicata al set di righe senza alcun tipo di ordinamento. Si avrà quindi un comportamento non deterministico nel modo di applicare la funzione di rango.
* Non è possibile garantire che le righe restituite da una query tramite ROW\_NUMBER siano ordinate esattamente allo stesso modo in ogni esecuzione, a meno che siano rispettate le condizioni seguenti.
  
  * I valori della colonna partizionata sono univoci.
  * I valori delle colonne ORDER BY sono univoci.
  * Le combinazioni di valori della colonna partizionata e delle colonne ORDER BY sono univoche.

### NTILE
NTILE distribuisce le righe in una partizione ordinata in un numero di gruppi specificato. I gruppi sono numerati a partire da uno.

L'esempio seguente divide il set di righe in ogni partizione (Vertical) in 4 gruppi nell'ordine di latenza della query e restituisce il numero di gruppo per ogni riga.

Per Vertical, Image, sono presenti 3 righe, quindi 3 gruppi.

Per Vertical, Web, sono presenti 6 righe, le due righe aggiuntive vengono distribuite ai primi due gruppi. Per questo motivo, sono presenti 2 righe nei gruppi 1 e 2 e solo 1 riga nei gruppi 3 e 4.

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;

I risultati sono:

| Query | Latency | Vertical | Quartile |
| --- | --- | --- | --- |
| Banana |300 |Image |1 |
| Cherry |300 |Image |2 |
| Durian |500 |Image |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |1 |
| Papaya |200 |Web |2 |
| Fig |300 |Web |2 |
| Cherry |400 |Web |3 |
| Durian |500 |Web |4 |

NTILE accetta un parametro, "numgroups". Numgroups è un'espressione costante di tipo int o long positiva che specifica il numero di gruppi in cui è necessario suddividere ogni partizione.

* Se il numero di righe nella partizione è divisibile uniformemente per numgroups, le dimensioni dei gruppi saranno uguali.
* Se il numero di righe in una partizione non è divisibile per numgroups, verranno creati gruppi di due dimensioni che differiscono per un membro. I gruppi più grandi precedono i gruppi più piccoli nell'ordine specificato dalla clausola OVER.

ad esempio:

* 100 righe divise in 4 gruppi: [25, 25, 25, 25]
* 102 righe divise in 4 gruppi: [26, 26, 25, 25]

### Primi N record per partizione tramite RANK, DENSE\_RANK o ROW\_NUMBER
Molti utenti preferiscono selezionare solo le PRIME n righe per ogni gruppo. Questo non è possibile con la tradizionale clausola GROUP BY.

L'esempio seguente è riportato all'inizio della sezione Funzioni di rango. Non visualizza i primi N record per ogni partizione:

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

I risultati sono:

| Query | Latency | Vertical | Rank | DenseRank | RowNumber |
| --- | --- | --- | --- | --- | --- |
| Banana |300 |Image |1 |1 |1 |
| Cherry |300 |Image |1 |1 |2 |
| Durian |500 |Image |3 |2 |3 |
| Apple |100 |Web |1 |1 |1 |
| Fig |200 |Web |2 |2 |2 |
| Papaya |200 |Web |2 |2 |3 |
| Fig |300 |Web |4 |3 |4 |
| Cherry |400 |Web |5 |4 |5 |
| Durian |500 |Web |6 |5 |6 |

### PRIMI N con DENSE RANK
L'esempio seguente restituisce i primi 3 record di ogni gruppo senza interruzioni nella numerazione di rango sequenziale delle righe in ogni partizione finestra.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

I risultati sono:

| Query | Latency | Vertical | DenseRank |
| --- | --- | --- | --- |
| Banana |300 |Image |1 |
| Cherry |300 |Image |1 |
| Durian |500 |Image |2 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |2 |
| Fig |300 |Web |3 |

### PRIMI N con RANK
    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

I risultati sono:

| Query | Latency | Vertical | Rank |
| --- | --- | --- | --- |
| Banana |300 |Image |1 |
| Cherry |300 |Image |1 |
| Durian |500 |Image |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |2 |

### PRIMI N con ROW\_NUMBER
    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

I risultati sono:

| Query | Latency | Vertical | RowNumber |
| --- | --- | --- | --- |
| Banana |300 |Image |1 |
| Cherry |300 |Image |2 |
| Durian |500 |Image |3 |
| Apple |100 |Web |1 |
| Fig |200 |Web |2 |
| Papaya |200 |Web |3 |

### Assegnare il numero di riga univoco globale
Spesso è utile assegnare un numero univoco globale a ogni riga. Con le funzioni di rango è facile e più efficiente rispetto all'uso di un riduttore.

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## Funzioni analitiche
Le funzioni analitiche vengono usate per comprendere le distribuzioni di valori in finestre. Lo scenario più comune per l'uso di funzioni analitiche è il calcolo dei percentili.

**Funzioni finestra analitiche supportate**

* CUME\_DIST
* PERCENT\_RANK
* PERCENTILE\_CONT
* PERCENTILE\_DISC

### CUME\_DIST
CUME\_DIST calcola la posizione relativa di un valore specificato in un gruppo di valori. Calcola la percentuale di query con una latenza inferiore o uguale alla latenza di query corrente nello stesso parametro Vertical. Per una riga R, supponendo un ordinamento crescente, la funzione CUME\_DIST di R è il numero di righe con valori inferiori o uguali al valore di R, diviso per il numero di righe valutato nel set di risultati di query o partizione. CUME\_DIST restituisce numeri nell'intervallo 0 < x < = 1.

**Sintassi**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

L'esempio seguente usa la funzione CUME\_DIST per calcolare il percentile della latenza per ogni query all'interno di un parametro Vertical.

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

I risultati sono:

| Query | Latency | Vertical | CumeDist |
| --- | --- | --- | --- |
| Durian |500 |Image |1 |
| Banana |300 |Image |0,666666666666667 |
| Cherry |300 |Image |0,666666666666667 |
| Durian |500 |Web |1 |
| Cherry |400 |Web |0,833333333333333 |
| Fig |300 |Web |0,666666666666667 |
| Fig |200 |Web |0,5 |
| Papaya |200 |Web |0,5 |
| Apple |100 |Web |0,166666666666667 |

Sono presenti 6 righe nella partizione in cui la chiave di partizione è "Web" (quarta riga e successive):

* Sono presenti 6 righe con valore uguale o minore di 500, quindi CUME\_DIST è uguale a 6/6=1
* Sono presenti 5 righe con valore uguale o minore di 400, quindi CUME\_DIST è uguale a 5/6=0,83.
* Sono presenti 4 righe con valore uguale o minore di 300, quindi CUME\_DIST è uguale a 4/6=0.66
* Sono presenti 3 righe con valore uguale o minore di 200, quindi CUME\_DIST è uguale a 3/6=0,5. Sono presenti due righe con lo stesso valore di latenza.
* È presente 1 riga con valore uguale o minore di 100, quindi CUME\_DIST è uguale a 1/6=0,16.

**Note sull'utilizzo:**

* I valori equivalenti restituiscono sempre lo stesso valore di distribuzione cumulativa.
* I valori NULL vengono considerati come i valori più bassi possibile.
* È necessario specificare la clausola ORDER BY per calcolare CUME\_DIST.
* CUME\_DIST è simile alla funzione PERCENT\_RANK

Nota: la clausola ORDER BY non è consentita se l'istruzione SELECT non è seguita da OUTPUT. La clausola ORDER BY nell'istruzione OUTPUT determina quindi l'ordine di visualizzazione del set di righe risultante.

### PERCENT\_RANK
PERCENT\_RANK calcola il rango relativo di una riga in un gruppo di righe. PERCENT\_RANK si usa per valutare il rango relativo di un valore all'interno di un set di righe o una partizione. L'intervallo di valori restituiti da PERCENT\_RANK è maggiore di 0 e minore o uguale a 1. A differenza di CUME\_DIST, PERCENT\_RANK è sempre 0 per la prima riga.

**Sintassi**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Note**

* La prima riga in qualsiasi set ha un valore PERCENT\_RANK pari a 0.
* I valori NULL vengono considerati come i valori più bassi possibile.
* È necessario specificare la clausola ORDER BY per calcolare PERCENT\_RANK.
* CUME\_DIST è simile alla funzione PERCENT\_RANK

L'esempio seguente usa la funzione PERCENT\_RANK per calcolare il percentile della latenza per ogni query all'interno di un parametro Vertical.

La clausola PARTITION BY viene specificata per il partizionamento delle righe nel set di risultati in base a Vertical. La clausola ORDER BY nella clausola OVER ordina le righe in ogni partizione.

Il valore restituito dalla funzione PERCENT\_RANK rappresenta il rango della latenza delle query in un parametro Vertical come percentuale.

    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

I risultati sono:

| Query | Latency:int | Vertical | PercentRank |
| --- | --- | --- | --- |
| Banana |300 |Image |0 |
| Cherry |300 |Image |0 |
| Durian |500 |Image |1 |
| Apple |100 |Web |0 |
| Fig |200 |Web |0,2 |
| Papaya |200 |Web |0,2 |
| Fig |300 |Web |0,6 |
| Cherry |400 |Web |0,8 |
| Durian |500 |Web |1 |

### PERCENTILE\_CONT e PERCENTILE\_DISC
Queste due funzioni calcolano un percentile in base a una distribuzione continua o discreta dei valori di colonna.

**Sintassi**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric\_literal**: percentile da calcolare. Il valore deve essere compreso tra 0,0 e 1,0.

WITHIN GROUP ( ORDER BY <identificativo> [ ASC | DESC ]): specifica un elenco di valori numerici per ordinare e calcolare il percentile. È consentito un solo identificatore di colonna. L'espressione deve restituire un tipo numerico. Non sono consentiti altri tipi di dati. L'ordinamento predefinito è crescente.

OVER ([ PARTITION BY <identificativo,>…[n] ] ): divide il set di righe di input in partizioni in base alla chiave di partizione a cui si applica la funzione percentile. Per altre informazioni, vedere la sezione RANKING di questo documento. Nota: gli eventuali valori Null nel set di dati vengono ignorati.

**PERCENTILE\_CONT** calcola un percentile in base a una distribuzione continua del valore di colonna. Il risultato viene interpolato e potrebbe non essere uguale ad alcuno dei valori specifici nella colonna.

**PERCENTILE\_DISC** calcola il percentile in base a una distribuzione discreta dei valori di colonna. Il risultato è uguale a un valore specifico nella colonna. In altre parole, PERCENTILE\_DISC, a differenza di PERCENTILE\_CONT, restituisce sempre un valore (input originale) effettivo.

È possibile vedere il funzionamento di entrambe le funzioni nell'esempio seguente che prova a trovare il valore mediano (percentile=0,50) per Latency in ogni Vertical

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 

        FROM @querylog;

I risultati sono:

| Query | Latency:int | Vertical | PercentileCont50 | PercentilDisc50 |
| --- | --- | --- | --- | --- |
| Banana |300 |Image |300 |300 |
| Cherry |300 |Image |300 |300 |
| Durian |500 |Image |300 |300 |
| Apple |100 |Web |250 |200 |
| Fig |200 |Web |250 |200 |
| Papaya |200 |Web |250 |200 |
| Fig |300 |Web |250 |200 |
| Cherry |400 |Web |250 |200 |
| Durian |500 |Web |250 |200 |

Perché i valori possano essere interpolati con PERCENTILE\_CONT, il valore mediano per Web è 250 anche se nessuna query nel parametro Vertical Web ha una latenza pari a 250.

PERCENTILE\_DISC non esegue l'interpolazione dei valori, quindi il valore mediano per Web è 200, che corrisponde a un valore effettivo trovato nelle righe di input.

## Vedere anche
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introduzione a Analisi Data Lake tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Introduzione ad Azure Data Lake Analytics con Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Sviluppare script U-SQL con Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usare le esercitazioni interattive di Analisi Azure Data Lake](data-lake-analytics-use-interactive-tutorials.md)
* [Analizzare i log dei siti Web con Analisi Azure Data Lake](data-lake-analytics-analyze-weblogs.md)
* [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
* [Gestire Analisi Data Lake di Azure tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
* [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0914_2016-->