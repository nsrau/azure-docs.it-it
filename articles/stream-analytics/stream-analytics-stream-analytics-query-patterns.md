---
title: Modelli di query comuni in Analisi di flusso di Azure
description: Questo articolo descrive alcuni modelli e progettazioni di query comuni che risultano utili nei processi di analisi di flusso di Azure.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75982322"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Modelli di query comuni in Analisi di flusso di Azure

Le query in Analisi di flusso di Azure sono espresse in un linguaggio di query di tipo SQL. Questi costrutti di linguaggio sono documentati nella guida [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference). 

La progettazione della query può esprimere una semplice logica pass-through per spostare i dati degli eventi da un flusso di input in un archivio dati di output oppure può eseguire un'analisi temporale e una corrispondenza dei modelli avanzati per calcolare le aggregazioni in diverse finestre temporali come nella [soluzione di compilazione di una soluzione Internet tramite analisi di flusso](stream-analytics-build-an-iot-solution-using-stream-analytics.md) . È possibile unire i dati di più input per combinare gli eventi di flusso ed è possibile eseguire ricerche su dati di riferimento statici per arricchire i valori dell'evento. È anche possibile scrivere dati in più output.

Questo articolo illustra le soluzioni per diversi modelli di query comuni basati su scenari reali.

## <a name="supported-data-formats"></a>Formati di dati supportati

Analisi di flusso di Azure supporta l'elaborazione di eventi nei formati di dati CSV, JSON e Avro.

Entrambi i formati JSON e Avro possono contenere tipi complessi come le matrici o gli oggetti nidificati (record). Per altre informazioni sull'uso di questi tipi di dati complessi, vedere l'articolo [analisi dei dati JSON e avro](stream-analytics-parsing-json.md) .

## <a name="simple-pass-through-query"></a>Semplice query pass-through

Una semplice query pass-through può essere usata per copiare i dati del flusso di input nell'output. Ad esempio, se un flusso di dati contenente informazioni sui veicoli in tempo reale deve essere salvato in un database SQL per l'analisi delle lettere, una semplice query pass-through eseguirà il processo.

**Input**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Output**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Query**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

Una query **Select** * proietta tutti i campi di un evento in ingresso e li invia all'output. Allo stesso modo, **Select** può essere usato anche per proiettare solo i campi obbligatori dall'input. In questo esempio, se il veicolo *make* e *Time* sono gli unici campi obbligatori da salvare, è possibile specificare tali campi nell'istruzione **Select** .

**Input**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Output**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:04.0000000Z |

**Query**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Aggregazione dei dati nel tempo

Per calcolare le informazioni in un intervallo di tempo, i dati possono essere aggregati insieme. In questo esempio un conteggio viene calcolato negli ultimi 10 minuti di tempo per ogni marca di automobile specifica.

**Input**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Output**:

| Casa automobilistica | Conteggio |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Query**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Questa aggregazione raggruppa le automobili *creando e* conteggia ogni 10 secondi. L'output ha la *marca* e il *conteggio* delle automobili che hanno raggiunto il pedaggio.

TumblingWindow è una funzione windowing utilizzata per raggruppare gli eventi. Un'aggregazione può essere applicata a tutti gli eventi raggruppati. Per altre informazioni, vedere [funzioni windowing](stream-analytics-window-functions.md).

Per ulteriori informazioni sull'aggregazione, vedere [funzioni di aggregazione](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="data-conversion"></a>Conversione di dati

È possibile eseguire il cast dei dati in tempo reale usando il metodo **cast** . Ad esempio, il peso dell'auto può essere convertito dal tipo **nvarchar (max)** al tipo **bigint** e utilizzato per un calcolo numerico.

**Input**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Output**:

| Casa automobilistica | Peso |
| --- | --- |
| Make1 |3000 |

**Query**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Utilizzare un'istruzione **cast** per specificare il tipo di dati. Vedere l'elenco dei tipi di dati supportati per i [tipi di dati (analisi di flusso di Azure)](/stream-analytics-query/data-types-azure-stream-analytics).

Per ulteriori informazioni sulle [funzioni di conversione dei dati](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="string-matching-with-like-and-not-like"></a>Stringa corrispondente a LIKE e NOT LIKE

**Like** e **not like** possono essere usati per verificare se un campo corrisponde a un determinato modello. Ad esempio, è possibile creare un filtro per restituire solo le targhe che iniziano con la lettera "A" e terminano con il numero 9.

**Input**:

| Casa automobilistica | License_plate | Tempo |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Output**:

| Casa automobilistica | License_plate | Tempo |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Query**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Usare l'istruzione **like** per verificare il valore del campo **License_plate** . Deve iniziare con la lettera "A", quindi contenere una stringa di zero o più caratteri, che termina con il numero 9.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>specificare la logica per i diversi casi/valori (istruzioni CASE)

Le istruzioni **case** possono fornire calcoli diversi per campi diversi, in base a un determinato criterio. Ad esempio, assegnare la corsia ' A ' alle automobili di *Make1* e Lane ' B ' a qualsiasi altra marca.

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Output**:

| Casa automobilistica |Dispatch_to_lane | Tempo |
| --- | --- | --- |
| Make1 |Un |2015-01-01T00:00:01.0000000Z |
| Make2 |B |2015-01-01T00:00:02.0000000Z |

**Soluzione**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

L'espressione **case** confronta un'espressione con un set di espressioni semplici per determinarne il risultato. In questo esempio, i veicoli di *Make1* vengono inviati alla corsia ' a', mentre i veicoli di qualsiasi altra marca verranno assegnati alla corsia ' B '.

Per ulteriori informazioni, fare riferimento all' [espressione case](/stream-analytics-query/case-azure-stream-analytics).

## <a name="send-data-to-multiple-outputs"></a>inviare dati a più output

È possibile utilizzare più istruzioni **Select** per restituire dati a diversi sink di output. Ad esempio, un **Select** può restituire un avviso basato su soglia mentre un altro può restituire gli eventi nell'archivio BLOB.

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**ArchiveOutput di output**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**AlertOutput di output**:

| Casa automobilistica | Tempo | Conteggio |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000Z |3 |

**Query**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

La clausola **into** indica all'analisi di flusso in quali output scrivere i dati. La prima **selezione** definisce una query pass-through che riceve i dati dall'input e li invia all'output denominato **ArchiveOutput**. La seconda query esegue un'aggregazione e un filtro semplici prima di inviare i risultati a un output del sistema di avvisi downstream denominato **AlertOutput**.

Si noti che la clausola **with** può essere usata per definire più blocchi sottoquery. Questa opzione offre il vantaggio di aprire un numero inferiore di lettori nell'origine di input.

**Query**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Per ulteriori informazioni, vedere [ **la clausola with** ](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>contare valori univoci

**Count** e **Distinct** possono essere utilizzati per conteggiare il numero di valori di campo univoci presenti nel flusso in un intervallo di tempo. È possibile creare una query per calcolare il numero di *automobili univoche* passate attraverso il casello in una finestra di 2 secondi.

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Output**

| Count_make | Tempo |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Query**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**Count (DISTINCT make)** restituisce il numero di valori distinct nella colonna **make** all'interno di un intervallo di tempo.
Per ulteriori informazioni, vedere [funzione di aggregazione **count** ](/stream-analytics-query/count-azure-stream-analytics).

## <a name="calculation-over-past-events"></a>Calcolo degli eventi passati

La funzione **lag** può essere usata per esaminare gli eventi passati in un intervallo di tempo e confrontarli con l'evento corrente. Ad esempio, l'auto corrente può essere rilasciata se è diversa dall'ultima auto che ha attraversato il pedaggio.

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Output**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Query**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Usare **lag** per esaminare il flusso di input di un evento, recuperare il valore *make* e confrontarlo con il valore *make* dell'evento corrente e restituire l'evento.

Per ulteriori informazioni, fare riferimento a [**lag**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Recuperare il primo evento in una finestra

È possibile utilizzare la funzione **First** per recuperare il primo evento in una finestra temporale. Ad esempio, l'output delle prime informazioni sulla macchina a ogni intervallo di 10 minuti.

**Input**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Make3 |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Make1 |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Make2 |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Make2 |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |Make4 |27-07-2015T00:13:45.0000000Z |

**Output**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |

**Query**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

Per **prima cosa** è anche possibile partizionare i dati e calcolare il primo evento per ogni *singola macchina a* cui è stato rilevato ogni intervallo di 10 minuti.

**Output**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Make3 |27-07-2015T00:02:17.0000000Z |
| YHN 6970 |Make2 |27-07-2015T00:06:00.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |Make4 |27-07-2015T00:13:45.0000000Z |

**Query**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Per ulteriori informazioni, vedere l'articolo relativo alla [**prima**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Restituisce l'ultimo evento in una finestra

Poiché gli eventi vengono utilizzati dal sistema in tempo reale, non esiste alcuna funzione che possa determinare se un evento sarà l'ultimo ad arrivare per quel intervallo di tempo. A tale scopo, il flusso di input deve essere unito a un altro in cui l'ora di un evento è il tempo massimo per tutti gli eventi in tale finestra.

**Input**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Make3 |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Make1 |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Make2 |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Make2 |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |Make4 |27-07-2015T00:13:45.0000000Z |

**Output**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| VFE 1616 |Make2 |27-07-2015T00:09:31.0000000Z |
| MDR 6128 |Make4 |27-07-2015T00:13:45.0000000Z |

**Query**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

Il primo passaggio della query consente di trovare il timestamp massimo nelle finestre di 10 minuti, ovvero il timestamp dell'ultimo evento per la finestra. Il secondo passaggio unisce i risultati della prima query con il flusso originale per trovare l'evento che corrisponde agli ultimi timestamp in ogni finestra. 

**DateDiff** è una funzione specifica della data che confronta e restituisce la differenza di ora tra due campi DateTime. per ulteriori informazioni, vedere [funzioni di data](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Per ulteriori informazioni sull'Unione dei flussi, vedere [**join**](/stream-analytics-query/join-azure-stream-analytics).


## <a name="correlate-events-in-a-stream"></a>Correlare gli eventi in un flusso

Per eseguire la correlazione degli eventi nello stesso flusso, è possibile esaminare gli eventi passati usando la funzione **lag** . Ad esempio, un output può essere generato ogni volta che due automobili consecutive della stessa *marca* passano attraverso il pedaggio per gli ultimi 90 secondi.

**Input**:

| Casa automobilistica | License_plate | Tempo |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Output**:

| Casa automobilistica | Tempo | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Query**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

La funzione **lag** può esaminare il flusso di input di un evento e recuperare il valore *make* , confrontando con il valore *make* dell'evento corrente.  Una volta soddisfatta la condizione, è possibile proiettare i dati dell'evento precedente utilizzando un **ritardo** nell'istruzione **SELECT** .

Per ulteriori informazioni, fare riferimento a [lag](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>rilevare la durata tra gli eventi

La durata di un evento può essere calcolata esaminando l'ultimo evento di avvio dopo la ricezione di un evento finale. Questa query può essere utile per determinare il tempo impiegato da un utente in una pagina o una funzionalità.

**Input**:  

| Utente | Funzionalità | Event | Tempo |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Inizia |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Fine |2015-01-01T00:00:08.0000000Z |

**Output**:  

| Utente | Funzionalità | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Query**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

L' **Ultima** funzione può essere utilizzata per recuperare l'ultimo evento in una condizione specifica. In questo esempio, la condizione è un evento di tipo Start, partizionamento della ricerca per **partizione per** utente e funzionalità. In questo modo, ogni utente e funzionalità viene gestito in modo indipendente durante la ricerca dell'evento di avvio. **Limite durata** limita la ricerca indietro nel tempo a 1 ora tra gli eventi di fine e inizio.

## <a name="detect-the-duration-of-a-condition"></a>rilevare la durata di una condizione

Per le condizioni che si estendono su più eventi, è possibile usare la funzione **lag** per identificare la durata di tale condizione. Ad esempio, si supponga che un bug abbia generato un peso errato per tutte le automobili (oltre 20.000 libbre) e che debba essere calcolata la durata di tale bug.

**Input**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000Z |2000 |

**Output**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Query**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
La prima istruzione **Select** mette in correlazione la misurazione del peso corrente con la misura precedente, proiettando il valore con la misura corrente. La seconda **Select** consente di tornare all'ultimo evento in cui la *previous_weight* è inferiore a 20000, dove il peso corrente è inferiore a 20000 e il *previous_weight* dell'evento corrente è maggiore di 20000.

Il End_fault è l'evento non difettoso corrente in cui l'evento precedente è difettoso e il Start_fault è l'ultimo evento non difettoso.

## <a name="periodically-output-values"></a>Valori di output periodici

In caso di eventi irregolari o mancanti, è possibile generare un output di intervallo regolare da un input di dati di tipo sparse. Generare, ad esempio, un evento ogni 5 secondi che segnali il punto di dati più recente individuato.

**Input**:

| Tempo | valore |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Output (prime 10 righe)**:

| Window_end | Last_event. Tempo | Last_event. Valore |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Query**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

questa query genera eventi ogni cinque secondi e restituisce l'ultimo evento ricevuto in precedenza. La durata **HOPPINGWINDOW** determina l'aspetto della query per trovare l'evento più recente.

Per altre informazioni, vedere [finestra di salto](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="process-events-with-independent-time-substreams"></a>Elaborare eventi con tempo indipendente (flussi substream)

gli eventi possono arrivare in ritardo o non in ordine a causa di sfasamenti di orario tra producer di eventi, sfasamenti di orario tra partizioni o latenza di rete.
Ad esempio, il clock del dispositivo per *ID casello* 2 è cinque secondi dietro *ID casello* 1 e l'orologio del dispositivo per *ID casello* 3 è dieci secondi dietro *ID casello* 1. Un calcolo può avvenire in modo indipendente per ogni casello, considerando solo i propri dati di clock come timestamp.

**Input**:

| Targa | Casa automobilistica | Tempo | ID casello |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 |27-07-2015T00:00:05.0000000Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000Z | 3 |

**Output**:

| ID casello | Conteggio |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Query**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

La clausola **timestamp over by** esamina ogni sequenza temporale del dispositivo in modo indipendente utilizzando i flussi sottoflussi. L'evento di output per ogni *ID casello* viene generato quando vengono calcolati, vale a dire che gli eventi sono ordinati rispetto a ogni *ID casello* anziché essere riordinati come se tutti i dispositivi si trovino nello stesso clock.

Per ulteriori informazioni, vedere [timestamp by over](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="remove-duplicate-events-in-a-window"></a>rimuovere gli eventi duplicati in una finestra

quando si esegue un'operazione come il calcolo delle medie sugli eventi di un determinato intervallo di tempo, gli eventi duplicati devono essere filtrati. Nell'esempio seguente, il secondo evento è un duplicato del primo.

**Input**:  

| deviceId | Tempo | Attributo | valore |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatura |100 |

**Output**:  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Query**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**COUNT(DISTINCT Time)** restituisce il numero di valori distinct della colonna Tempo all'interno di una finestra temporale. L'output del primo passaggio può quindi essere usato per calcolare la media per ogni dispositivo, ignorando i duplicati.

Per ulteriori informazioni, vedere [conteggio (tempo DISTINCT)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="session-windows"></a>Finestre di sessione

Una finestra di sessione è una finestra che continua ad espandersi quando si verificano eventi e si chiude per il calcolo se non viene ricevuto alcun evento dopo un periodo di tempo specifico o se la finestra raggiunge la durata massima.
Questa finestra è particolarmente utile quando si calcolano i dati di interazione dell'utente. Una finestra viene avviata quando un utente inizia a interagire con il sistema e si chiude quando non viene rilevato alcun evento, ovvero l'utente ha interrotto l'interazione.
Ad esempio, un utente sta interagendo con una pagina Web in cui il numero di clic viene registrato, è possibile usare una finestra di sessione per determinare il tempo di interazione dell'utente con il sito.

**Input**:

| User_id | Tempo | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000 Z | "www.example.com/e.html" |

**Output**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | 2017-01-26T00:01:10.0000000 Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000 Z | 2017-01-26T00:01:15.0000000 Z | 20 |

**Query**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

La **selezione** proietta i dati rilevanti per l'interazione dell'utente, insieme alla durata dell'interazione. Raggruppamento dei dati in base all'utente e a un **SessionWindow** che si chiude se non si verifica alcuna interazione entro 1 minuto, con una dimensione massima della finestra di 60 minuti.

Per ulteriori informazioni su **SessionWindow**, vedere la [finestra della sessione](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Estendibilità del linguaggio con la funzione definita dall'utente in JavaScript e C #

Il linguaggio di query di analisi di flusso di Azure può essere esteso con funzioni personalizzate scritte in linguaggio JavaScript o C#. Le funzioni definite dall'utente (UDF) sono calcoli personalizzati o complessi che non possono essere espressi facilmente tramite il linguaggio **SQL** . Queste funzioni definite dall'utente possono essere definite una volta e utilizzate più volte all'interno di una query. Ad esempio, è possibile usare una funzione definita dall'utente per convertire un valore *nvarchar (max)* esadecimale in un valore *bigint* .

**Input**:

| Device_id | HexValue |
| --- | --- |
| 1 | B4 |
| 2 | 11b |
| 3 | "121" |

**Output**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

La funzione definita dall'utente calcolerà il valore *bigint* da HexValue a ogni evento utilizzato.

Per ulteriori informazioni, fare riferimento a [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) e [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Criteri di ricerca avanzati con MATCH_RECOGNIZE

**MATCH_RECOGNIZE** è un meccanismo avanzato di ricerca di criteri di ricerca che può essere usato per trovare la corrispondenza di una sequenza di eventi con un modello di espressione regolare ben definito.
Ad esempio, un ATM viene monitorato in tempo reale per gli errori, durante il funzionamento dell'ATM se sono presenti due messaggi di avviso consecutivi, l'amministratore deve ricevere una notifica.

**Input**:

| ATM_id | Operation_id | Return_Code | Tempo |
| --- | --- | --- | --- |
| 1 | "Immissione del PIN" | "Esito positivo" | 2017-01-26T00:10:00.0000000 Z |
| 2 | "Apertura dello slot Money" | "Esito positivo" | 2017-01-26T00:10:07.0000000 Z |
| 2 | "Chiusura dello slot Money" | "Esito positivo" | 2017-01-26T00:10:11.0000000 Z |
| 1 | "Immissione della quantità di ritiro" | "Esito positivo" | 2017-01-26T00:10:08.0000000 Z |
| 1 | "Apertura dello slot Money" | Avviso | 2017-01-26T00:10:14.0000000 Z |
| 1 | "Stampa del saldo bancario" | Avviso | 2017-01-26T00:10:19.0000000 Z |

**Output**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Apertura dello slot Money" | 2017-01-26T00:10:14.0000000 Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Questa query corrisponde a almeno due eventi di errore consecutivi e genera un allarme quando vengono soddisfatte le condizioni.
Il **criterio** definisce l'espressione regolare da utilizzare per la corrispondenza, in questo caso un numero qualsiasi di operazioni completate, seguite da almeno due errori consecutivi.
L'esito positivo e negativo vengono definiti utilizzando Return_Code valore e una volta soddisfatta la condizione, le **misure** vengono proiettate con *ATM_id*, la prima operazione di avviso e la prima ora di avviso.

Per ulteriori informazioni, vedere [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geoschermatura e query geospaziali
Analisi di flusso di Azure offre funzioni geospaziali predefinite che possono essere usate per implementare scenari quali gestione della flotta, condivisione delle corse, automobili connesse e rilevamento di asset.
I dati geospaziali possono essere inseriti in formati GeoJSON o WKT come parte del flusso di eventi o dei dati di riferimento.
Ad esempio, una società specializzata nella produzione di computer per la stampa di passaporti, il leasing dei computer a enti pubblici e consolati. La posizione di tali computer è fortemente controllata per evitare il mancato posizionamento e il possibile utilizzo per la contraffazione dei passaporti. Ogni computer è dotato di un tracker GPS, le informazioni vengono ritrasmesse a un processo di analisi di flusso di Azure.
Il produttore desidera tenere traccia della posizione di tali computer e ricevere un avviso se uno di essi lascia un'area autorizzata, in questo modo possono disabilitare, ricevere avvisi e recuperare le attrezzature in remoto.

**Input**:

| Equipment_id | Equipment_current_location | Tempo |
| --- | --- | --- |
| 1 | "POINT (-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000 Z |
| 1 | "POINT (-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000 Z |
| 1 | "POINT (-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000 Z |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

**Input dei dati di riferimento**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON ((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Output**:

| Equipment_id | Equipment_alert_location | Tempo |
| --- | --- | --- |
| 1 | "POINT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

La query consente al produttore di monitorare automaticamente il percorso dei computer, ricevendo avvisi quando un computer lascia la rete geografica consentita. La funzione geospaziale predefinita consente agli utenti di usare i dati GPS all'interno della query senza librerie di terze parti.

Per altre informazioni, vedere l'articolo [scenari di geoschermatura e aggregazione geospaziale con analisi di flusso di Azure](geospatial-scenarios.md) .

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, provare il [Forum di analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Riferimento al linguaggio di query di analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
