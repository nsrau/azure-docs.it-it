---
title: Modelli di query comuni in Analisi di flusso di Azure
description: Questo articolo descrive diversi modelli di query e progettazioni comuni utili nei processi di Analisi di flusso di Azure.This article describes several common query patterns and designs that are useful in Azure Stream Analytics jobs.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982322"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Modelli di query comuni in Analisi di flusso di Azure

Le query in Analisi di flusso di Azure sono espresse in un linguaggio di query di tipo SQL. Questi costrutti di linguaggio sono documentati nella guida [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference). 

La progettazione della query può esprimere una semplice logica pass-through per spostare i dati degli eventi da un flusso di input in un archivio dati di output oppure può eseguire la ricerca di criteri avanzati e analisi temporali per calcolare le aggregazioni in varie finestre temporali come nella guida Creare una [soluzione IoT tramite Analisi di flusso.](stream-analytics-build-an-iot-solution-using-stream-analytics.md) È possibile unire i dati da più input per combinare gli eventi di streaming ed è possibile eseguire ricerche su dati di riferimento statici per arricchire i valori degli eventi. È inoltre possibile scrivere dati in più output.

In questo articolo vengono descritte le soluzioni a diversi modelli di query comuni basati su scenari reali.

## <a name="supported-data-formats"></a>Formati di dati supportati

Analisi di flusso di Azure supporta l'elaborazione di eventi nei formati di dati CSV, JSON e Avro.

Entrambi i formati JSON e Avro possono contenere tipi complessi come le matrici o gli oggetti nidificati (record). Per altre informazioni sull'uso di questi tipi di dati complessi, vedere l'articolo [Analisi dei dati JSON e AVRO.](stream-analytics-parsing-json.md)

## <a name="simple-pass-through-query"></a>Semplice query pass-through

Una semplice query pass-through può essere usata per copiare i dati del flusso di input nell'output. Ad esempio, se un flusso di dati contenente informazioni sui veicoli in tempo reale deve essere salvato in un database SQL per l'analisi delle lettere, una semplice query pass-through eseguirà il processo.

**Ingresso**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Uscita**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Interrogazione**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

Una query **SELECT** : proietta tutti i campi di un evento in ingresso e li invia all'output. Allo stesso modo, **SELECT** può essere utilizzato anche per proiettare solo i campi obbligatori dall'input. In questo esempio, se vehicle *Make* e *Time* sono gli unici campi obbligatori da salvare, tali campi possono essere specificati nell'istruzione **SELECT.**

**Ingresso**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Uscita**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:04.0000000Z |

**Interrogazione**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Aggregazione dei dati nel tempo

Per calcolare le informazioni in un intervallo di tempo, i dati possono essere aggregati insieme. In questo esempio, un conteggio viene calcolato negli ultimi 10 minuti di tempo per ogni marca di auto specifica.

**Ingresso**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Uscita**:

| Casa automobilistica | Conteggio |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Interrogazione**:

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

Questa aggregazione raggruppa le auto per *Make* e le conta ogni 10 secondi. L'uscita ha la *marca* e *il conteggio* delle auto che hanno attraversato il pedaggio.

TumblingWindow è una funzione di windowing utilizzata per raggruppare gli eventi. Un'aggregazione può essere applicata su tutti gli eventi raggruppati. Per ulteriori informazioni, vedere [Funzioni di windowing](stream-analytics-window-functions.md).

Per ulteriori informazioni sull'aggregazione, fare riferimento alle funzioni di [aggregazione.](/stream-analytics-query/aggregate-functions-azure-stream-analytics)

## <a name="data-conversion"></a>Conversione di dati

È possibile eseguire il cast dei dati in tempo reale utilizzando il metodo **CAST.** Ad esempio, il peso dell'auto può essere convertito dal tipo **nvarchar(max)** al tipo **bigint** ed essere utilizzato in un calcolo numerico.

**Ingresso**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Uscita**:

| Casa automobilistica | Peso |
| --- | --- |
| Make1 |3000 |

**Interrogazione**:

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

Utilizzare **un'istruzione CAST** per specificare il tipo di dati. Vedere l'elenco dei tipi di dati supportati nei [tipi di dati (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Per ulteriori informazioni sulle funzioni di [conversione dei dati](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="string-matching-with-like-and-not-like"></a>Corrispondenza delle stringhe con LIKE e NOT LIKE

**LIKE** e **NOT LIKE** possono essere utilizzati per verificare se un campo corrisponde a un determinato modello. Ad esempio, è possibile creare un filtro per restituire solo le targhe che iniziano con la lettera 'A' e terminano con il numero 9.

**Ingresso**:

| Casa automobilistica | License_plate | Tempo |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Uscita**:

| Casa automobilistica | License_plate | Tempo |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Interrogazione**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Utilizzare l'istruzione **LIKE** per controllare il valore del campo **License_plate.** Dovrebbe iniziare con la lettera 'A', quindi avere qualsiasi stringa di zero o più caratteri, che termina con il numero 9.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>specificare la logica per i diversi casi/valori (istruzioni CASE)

**Le** istruzioni CASE possono fornire calcoli diversi per campi diversi, in base a criteri specifici. Ad esempio, assegnare la corsia 'A' alle auto di *Make1* e "B" a qualsiasi altra marca.

**Ingresso**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Uscita**:

| Casa automobilistica |Dispatch_to_lane | Tempo |
| --- | --- | --- |
| Make1 |"A" |2015-01-01T00:00:01.0000000Z |
| Make2 |"B" |2015-01-01T00:00:02.0000000Z |

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

L'espressione **CASE** confronta un'espressione con un set di espressioni semplici per determinarne il risultato. In questo esempio, i veicoli di *Make1* vengono spediti alla corsia 'A' mentre ai veicoli di qualsiasi altra marca verrà assegnata la corsia 'B'.

Per ulteriori informazioni, fare riferimento a [espressione case](/stream-analytics-query/case-azure-stream-analytics).

## <a name="send-data-to-multiple-outputs"></a>inviare dati a più output

È possibile utilizzare più istruzioni **SELECT** per restituire dati in sink di output diversi. Ad esempio, **un'istruzione SELECT** può generare un avviso basato su soglia mentre un'altra può generare eventi nell'archiviazione BLOB.

**Ingresso**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Output ArchiveOutput**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**AlertOutput in uscita**:

| Casa automobilistica | Tempo | Conteggio |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000Z |3 |

**Interrogazione**:

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

La clausola **INTO** indica a Stream Analytics in quale output scrivere i dati. La prima **istruzione SELECT** definisce una query pass-through che riceve i dati dall'input e li invia all'output denominato **ArchiveOutput**. La seconda query esegue alcune semplici operazioni di aggregazione e filtro prima di inviare i risultati a un output del sistema di avvisi downstream denominato **AlertOutput**.

Si noti che la clausola **WITH** può essere utilizzata per definire più blocchi di sottoquery. Questa opzione ha il vantaggio di aprire un numero inferiore di lettori all'origine di input.

**Interrogazione**:

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

Per ulteriori informazioni, fare riferimento alla [clausola **WITH** ](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>contare valori univoci

**COUNT** e **DISTINCT** possono essere utilizzati per contare il numero di valori di campo univoci visualizzati nel flusso all'interno di un intervallo di tempo. È possibile creare una query per calcolare il numero di *marche* uniche di auto passate attraverso il casello in una finestra di 2 secondi.

**Ingresso**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Output:**

| Count_make | Tempo |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Query:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINCT Make)** restituisce il conteggio dei valori distinti nella colonna **Make** all'interno di un intervallo di tempo.
Per ulteriori informazioni, vedere [ **Funzione** di aggregazione COUNT](/stream-analytics-query/count-azure-stream-analytics).

## <a name="calculation-over-past-events"></a>Calcolo su eventi passati

La funzione **LAG** può essere utilizzata per esaminare gli eventi passati all'interno di un intervallo di tempo e confrontarli con l'evento corrente. Ad esempio, la marca auto corrente può essere emessa se è diversa dall'ultima auto che ha attraversato il pedaggio.

**Ingresso**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Uscita**:

| Casa automobilistica | Tempo |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Interrogazione**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Utilizzare **IL GAL** per visualizzare nel flusso di input un evento, recuperando il valore *Make* e confrontandolo con il valore *Make* dell'evento corrente e producendo l'evento.

Per ulteriori informazioni, fare riferimento a [**GAL**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Recuperare il primo evento in una finestraRetrieve the first event in a window

**IsFirst** può essere utilizzato per recuperare il primo evento in un intervallo di tempo. Ad esempio, l'output delle prime informazioni sull'auto a intervalli di 10 minuti.

**Ingresso**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Make3 |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Make1 |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Make2 |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Make2 |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |Marca4 |27-07-2015T00:13:45.0000000Z |

**Uscita**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |

**Interrogazione**:

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

**IsFirst** può anche partizionare i dati e calcolare il primo evento per ogni auto specifica *Make* found ad ogni intervallo di 10 minuti.

**Uscita**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Make3 |27-07-2015T00:02:17.0000000Z |
| YHN 6970 |Make2 |27-07-2015T00:06:00.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |Marca4 |27-07-2015T00:13:45.0000000Z |

**Interrogazione**:

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

Per ulteriori informazioni, fare riferimento a [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Restituire l'ultimo evento in una finestra

Poiché gli eventi vengono utilizzati dal sistema in tempo reale, non esiste alcuna funzione in grado di determinare se un evento sarà l'ultimo ad arrivare per tale intervallo di tempo. A tale scopo, il flusso di input deve essere unito a un altro in cui il tempo di un evento è il tempo massimo per tutti gli eventi in tale finestra.

**Ingresso**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Make3 |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Make1 |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Make2 |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Make2 |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |Marca4 |27-07-2015T00:13:45.0000000Z |

**Uscita**:

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| VFE 1616 |Make2 |27-07-2015T00:09:31.0000000Z |
| MDR 6128 |Marca4 |27-07-2015T00:13:45.0000000Z |

**Interrogazione**:

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

Il primo passaggio della query trova il timestamp massimo nelle finestre di 10 minuti, ovvero il timestamp dell'ultimo evento per tale finestra. Il secondo passaggio unisce i risultati della prima query con il flusso originale per trovare l'evento che corrisponde agli ultimi timestamp in ogni finestra. 

**DATEDIFF** è una funzione specifica della data che confronta e restituisce la differenza di orario tra due campi DateTime, per ulteriori informazioni, fare riferimento a [funzioni di data](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Per ulteriori informazioni sull'unione di flussi, fare riferimento a [**JOIN**](/stream-analytics-query/join-azure-stream-analytics).


## <a name="correlate-events-in-a-stream"></a>Correlare gli eventi in un flussoCorrelate events in a stream

La correlazione degli eventi nello stesso flusso può essere eseguita esaminando gli eventi passati utilizzando la funzione **LAG.** Ad esempio, un output può essere generato ogni volta che due auto consecutive dello stesso *Make* passano attraverso il pedaggio per gli ultimi 90 secondi.

**Ingresso**:

| Casa automobilistica | License_plate | Tempo |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Uscita**:

| Casa automobilistica | Tempo | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Interrogazione**:

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

La funzione **GAL** può esaminare nel flusso di input un evento e recuperare il valore *Make,* confrontandolo con il valore *Make* dell'evento corrente.  Una volta soddisfatta la condizione, i dati dell'evento precedente possono essere proiettati utilizzando **il GAL** nell'istruzione **SELECT.**

Per ulteriori informazioni, fare riferimento a [GAL](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>rilevare la durata tra gli eventi

La durata di un evento può essere calcolata esaminando l'ultimo evento Start dopo la ricezione di un evento End. Questa query può essere utile per determinare il tempo che un utente trascorre in una pagina o una funzionalità.

**Ingresso**:  

| Utente | Funzionalità | Event | Tempo |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Inizia |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Fine |2015-01-01T00:00:08.0000000Z |

**Uscita**:  

| Utente | Funzionalità | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Interrogazione**:

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

La funzione **LAST** può essere utilizzata per recuperare l'ultimo evento all'interno di una condizione specifica. In questo esempio, la condizione è un evento di tipo Start, partizionando la ricerca per utente e funzionalità **PARTITION BY.** In questo modo, ogni utente e funzionalità viene trattato in modo indipendente durante la ricerca dell'evento Start. **LIMIT DURATION** limita la ricerca indietro nel tempo a 1 ora tra gli eventi End e Start.

## <a name="detect-the-duration-of-a-condition"></a>rilevare la durata di una condizione

Per le condizioni che si estendono su più eventi, la funzione **GAL** può essere utilizzata per identificare la durata di tale condizione. Ad esempio, si supponga che un bug abbia generato un peso errato per tutte le automobili (oltre 20.000 libbre) e che debba essere calcolata la durata di tale bug.

**Ingresso**:

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

**Uscita**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Interrogazione**:

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
La prima istruzione **SELECT** correla la misurazione del peso corrente con la misurazione precedente, proiettandola insieme alla misurazione corrente. La seconda **istruzione SELECT** risale all'ultimo evento in cui il *previous_weight* è inferiore a 20000, dove il peso corrente è inferiore a 20000 e il *previous_weight* dell'evento corrente è maggiore di 20000.

Il End_fault è l'evento non errore corrente in cui l'evento precedente era difettoso e la Start_fault è l'ultimo evento non difettoso prima di tale evento.

## <a name="periodically-output-values"></a>Valori di uscita periodica

In caso di eventi irregolari o mancanti, è possibile generare un output a intervalli regolari da un input di dati più sparse. Generare, ad esempio, un evento ogni 5 secondi che segnali il punto di dati più recente individuato.

**Ingresso**:

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

**Interrogazione**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

questa query genera eventi ogni cinque secondi e restituisce l'ultimo evento ricevuto in precedenza. La durata di **HOPPINGWINDOW** determina la distanza dall'aspetto della query per trovare l'evento più recente.

Per ulteriori informazioni, fare riferimento a [Hopping window](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="process-events-with-independent-time-substreams"></a>Eventi di processo con tempo indipendente (substream)

gli eventi possono arrivare in ritardo o non in ordine a causa di sfasamenti di orario tra producer di eventi, sfasamenti di orario tra partizioni o latenza di rete.
Ad esempio, l'orologio del dispositivo per *TollID* 2 è cinque secondi dietro *TollID* 1 e l'orologio del dispositivo per *TollID* 3 è dieci secondi dietro *TollID* 1. Un calcolo può avvenire in modo indipendente per ogni pedaggio, considerando solo i propri dati di clock come timestamp.

**Ingresso**:

| Targa | Casa automobilistica | Tempo | ID casello |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 |27-07-2015T00:00:05.0000000Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Marca4 |2015-07-27T00:00:07.0000000Z | 3 |

**Uscita**:

| ID casello | Conteggio |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Interrogazione**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

La clausola **TIMESTAMP OVER BY** esamina ogni sequenza temporale del dispositivo in modo indipendente utilizzando i sottoflussi secondari. L'evento di output per ogni *TollID* viene generato mentre vengono calcolati, vale a dire che gli eventi sono in ordine rispetto a ogni *TollID* anziché essere riordinati come se tutti i dispositivi fossero sullo stesso orologio.

Per ulteriori informazioni, fare riferimento a [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="remove-duplicate-events-in-a-window"></a>rimuovere gli eventi duplicati in una finestra

quando si esegue un'operazione come il calcolo delle medie sugli eventi di un determinato intervallo di tempo, gli eventi duplicati devono essere filtrati. Nell'esempio seguente, il secondo evento è un duplicato del primo.

**Ingresso**:  

| deviceId | Tempo | Attributo | valore |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatura |100 |

**Uscita**:  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Interrogazione**:

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

**COUNT(DISTINCT Time)** restituisce il numero di valori distinct della colonna Tempo all'interno di una finestra temporale. L'output del primo passaggio può quindi essere usato per calcolare la media per dispositivo, scartando i duplicati.

Per ulteriori informazioni, fare riferimento a [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="session-windows"></a>Finestre di sessione

Una finestra di sessione è una finestra che continua ad espandersi man mano che si verificano eventi e si chiude per il calcolo se non viene ricevuto alcun evento dopo un determinato periodo di tempo o se la finestra raggiunge la durata massima.
Questa finestra è particolarmente utile quando si calcolano i dati di interazione dell'utente. Una finestra inizia quando un utente inizia a interagire con il sistema e si chiude quando non vengono rispettati altri eventi, ovvero l'utente ha smesso di interagire.
Ad esempio, un utente interagisce con una pagina Web in cui viene registrato il numero di clic, una finestra di sessione può essere utilizzata per scoprire per quanto tempo l'utente ha interagito con il sito.

**Ingresso**:

| User_id | Tempo | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000 | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000 | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000 | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000 | "www.example.com/e.html" |

**Uscita**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 | 2017-01-26T00:01:10.0000000 | 70 |
| 1 | 2017-01-26T00:00:55.0000000 | 2017-01-26T00:01:15.0000000 | 20 |

**Interrogazione**:

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

**L'istruzione SELECT** proietta i dati rilevanti per l'interazione dell'utente, insieme alla durata dell'interazione. Raggruppamento dei dati in base all'utente e un **SessionWindow** che si chiude se non viene eseguita alcuna interazione entro 1 minuto, con una dimensione massima della finestra di 60 minuti.

Per ulteriori informazioni su **SessionWindow**, fare riferimento a [Finestra di sessione](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Estendibilità del linguaggio con funzione definita dall'utente in JavaScript e C #

Il linguaggio di query di Analisi flusso di Azure può essere esteso con funzioni personalizzate scritte in linguaggio JavaScript o In linguaggio C. Le funzioni definite dall'utente (UDF, User Defined Functions) sono calcoli personalizzati/complessi che non possono essere espressi facilmente utilizzando il linguaggio **SQL.** Queste funzioni definite dall'utente possono essere definite una sola volta e utilizzate più volte all'interno di una query. Ad esempio, una funzione definita dall'utente può essere utilizzata per convertire un valore *esadecimale nvarchar(max)* in un valore *bigint.*

**Ingresso**:

| Device_id | Valore esadecimale |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Uscita**:

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

La funzione definita dall'utente calcolerà il valore *bigint* da HexValue in ogni evento utilizzato.

Per ulteriori informazioni, fare riferimento a [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) e [C .](/azure/stream-analytics/stream-analytics-edge-csharp-udf)

## <a name="advanced-pattern-matching-with-match_recognize"></a>Criteri di ricerca avanzati con MATCH_RECOGNIZE

**MATCH_RECOGNIZE** è un meccanismo di criteri di ricerca avanzato che può essere utilizzato per associare una sequenza di eventi a un modello di espressione regolare ben definito.
Ad esempio, un atM viene monitorato in tempo reale per gli errori, durante il funzionamento del bancomat se sono presenti due messaggi di avviso consecutivi che l'amministratore deve ricevere una notifica.

**Ingresso**:

| ATM_id | Operation_id | Return_Code | Tempo |
| --- | --- | --- | --- |
| 1 | "Inserire il pin" | "Esito positivo" | 2017-01-26T00:10:00.0000000 |
| 2 | "Apertura Money Slot" | "Esito positivo" | 2017-01-26T00:10:07.0000000 |
| 2 | "Chiusura money Slot" | "Esito positivo" | 2017-01-26T00:10:11.0000000 |
| 1 | "Immissione della quantità di prelievo" | "Esito positivo" | 2017-01-26T00:10:08.0000000 |
| 1 | "Apertura Money Slot" | "Avviso" | 2017-01-26T00:10:14.0000000 |
| 1 | "Stampa saldo bancario" | "Avviso" | 2017-01-26T00:10:19.0000000 |

**Uscita**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Apertura Money Slot" | 2017-01-26T00:10:14.0000000 |

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

Questa query corrisponde ad almeno due eventi di errore consecutivi e genera un allarme quando vengono soddisfatte le condizioni.
**PATTERN** definisce l'espressione regolare da utilizzare nella corrispondenza, in questo caso, un numero qualsiasi di operazioni riuscite seguite da almeno due errori consecutivi.
Operazione riuscita e Operazione non riuscita vengono definite utilizzando Return_Code valore e, una volta soddisfatta la condizione, **i MEASURES** vengono proiettati con *ATM_id*, la prima operazione di avviso e la prima volta.

Per ulteriori informazioni, fare riferimento a [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geofencing e query geospaziali
Azure Stream Analytics offre funzioni geospaziali integrate che possono essere usate per implementare scenari come la gestione della flotta, la condivisione delle corse, le auto connesse e il monitoraggio delle risorse.
I dati geospaziali possono essere ingeriti nei formati GeoJSON o WKT come parte del flusso di eventi o dei dati di riferimento.
Ad esempio, una società specializzata nella produzione di macchine per la stampa di passaporti, affitta relitto le proprie macchine a governi e consolati. La posizione di tali macchine è fortemente controllata per evitare la sostituzione errata e l'eventuale utilizzo per la contraffazione dei passaporti. Ogni computer è dotato di un tracker GPS, che le informazioni vengono inoltrate a un processo di Analisi di flusso di Azure.Each machine is fitted with a GPS tracker, that information is relayed back to an Azure Stream Analytics job.
La manifattura desidera tenere traccia della posizione di tali macchine ed essere avvisata se una di esse lascia un'area autorizzata, in questo modo può disabilitare da remoto, allertare le autorità e recuperare l'apparecchiatura.

**Ingresso**:

| Equipment_id | Equipment_current_location | Tempo |
| --- | --- | --- |
| 1 | "PUNTO(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000 |
| 1 | "PUNTO(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000 |
| 1 | "PUNTO(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000 |
| 1 | "PUNTO(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 |

**Input dati di riferimento**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.640983866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794)) |

**Uscita**:

| Equipment_id | Equipment_alert_location | Tempo |
| --- | --- | --- |
| 1 | "PUNTO(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 |

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

La query consente al produttore di monitorare automaticamente la posizione delle macchine, ricevendo avvisi quando una macchina lascia il geofence consentito. La funzione geospaziale integrata consente agli utenti di utilizzare i dati GPS all'interno della query senza librerie di terze parti.

Per altre informazioni, vedere l'articolo Scenari di [geofencing e aggregazione geospaziale con Analisi di flusso di Azure.For](geospatial-scenarios.md) more information, refer to the Geofencing and geospatial aggregation scenarios with Azure Stream Analytics article.

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, prova il forum di Analisi di Flusso di [Azure.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di AzureGet started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Guida di riferimento al linguaggio di query di Analisi di flusso di AzureAzure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
