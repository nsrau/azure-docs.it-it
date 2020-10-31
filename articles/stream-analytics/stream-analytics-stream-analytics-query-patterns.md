---
title: Modelli di query comuni in Analisi di flusso di Azure
description: Questo articolo descrive alcuni modelli e diverse progettazioni comuni di query che possono rivelarsi utili nei processi di Analisi di flusso di Azure.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: f0c5363cfec42ba78ee6c41a1970211518b74a71
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127536"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Modelli di query comuni in Analisi di flusso di Azure

Le query in Analisi di flusso di Azure sono espresse in un linguaggio di query di tipo SQL. Questi costrutti di linguaggio sono documentati nella guida [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference). 

La progettazione delle query può esprimere una semplice logica di pass-through per spostare i dati dell'evento da un flusso di input a un archivio dati di output oppure può eseguire criteri di ricerca avanzati e analisi temporali per calcolare le aggregazioni in varie finestre temporali, come nella guida [Creare una soluzione IoT usando Analisi di flusso di Azure](stream-analytics-build-an-iot-solution-using-stream-analytics.md). È possibile aggiungere dati da più input per combinare flussi di eventi ed eseguire ricerche sui dati di riferimento statici per arricchire i valori degli eventi. È anche possibile scrivere dati per più output.

Questo articolo illustra le soluzioni per diversi modelli di query comuni basati su scenari reali.

## <a name="supported-data-formats"></a>Formati di dati supportati

Analisi di flusso di Azure supporta l'elaborazione di eventi nei formati di dati CSV, JSON e Avro.

Entrambi i formati JSON e Avro possono contenere tipi complessi come le matrici o gli oggetti nidificati (record). Per altre informazioni sull'uso di questi tipi di dati complessi, vedere l'articolo [Analisi di dati JSON e AVRO](stream-analytics-parsing-json.md).

## <a name="send-data-to-multiple-outputs"></a>inviare dati a più output

È possibile usare più istruzioni **SELECT** per restituire dati a sink di output diversi. Ad esempio, un'istruzione **SELECT** può restituire un avviso basato su soglia mentre un'altra può restituire gli eventi nell'archivio BLOB.

**Input** :

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Output ArchiveOutput** :

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Output AlertOutput** :

| Casa automobilistica | Tempo | Conteggio |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000Z |3 |

**Query** :

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

la clausola **INTO** indica all'Analisi di flusso di Azure in quali output scrivere i dati. La prima istruzione **SELECT** definisce una query pass-through che riceve i dati dall'input e li invia all'output denominato **ArchiveOutput** . La seconda query effettua una semplice aggregazione, filtra e invia i risultati a un output di sistema di avviso downstream denominato **AlertOutput** .

Si noti che è possibile usare la clausola **WITH** per definire più blocchi di sottoquery. Questa opzione offre il vantaggio di aprire un numero inferiore di lettori nell'origine di input.

**Query** :

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

Per altre informazioni, vedere la clausola [**WITH**](/stream-analytics-query/with-azure-stream-analytics).

## <a name="simple-pass-through-query"></a>Query pass-through semplice

Una semplice query pass-through può essere usata per copiare i dati del flusso di input nell'output. Ad esempio, se un flusso di dati contenente informazioni sui veicoli in tempo reale deve essere salvato in un database SQL per l'analisi delle lettere, una semplice query pass-through sarà sufficiente.

**Input** :

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Output** :

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Query** :

```SQL
SELECT
    *
INTO Output
FROM Input
```

Una query **SELECT** * proietta tutti i campi di un evento in ingresso e li invia all'output. Allo stesso modo, è possibile usare **SELECT** anche per proiettare solo i campi obbligatori dall'input. In questo esempio, se i campi *Make* e *Time* del veicolo sono gli unici campi obbligatori da salvare ed è possibile specificarli nell'istruzione **SELECT** .

**Input** :

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Output** :

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make1 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:04.0000000Z |

**Query** :

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>Stringa corrispondente a LIKE e NOT LIKE

È possibile usare **LIKE** e **NOT LIKE** per verificare se un campo corrisponde a un determinato criterio. Ad esempio, è possibile creare un filtro per restituire solo le targhe che iniziano con la lettera "A" e terminano con il numero 9.

**Input** :

| Casa automobilistica | License_plate | Tempo |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Output** :

| Casa automobilistica | License_plate | Tempo |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Query** :

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

usare l'istruzione **LIKE** per verificare il valore del campo **License_plate** . Deve iniziare con la lettera "A", quindi contenere una stringa di zeri o altri caratteri e terminare con 9.

## <a name="calculation-over-past-events"></a>Calcolo sugli eventi passati

È possibile usare la funzione **LAG** per cercare gli eventi passati in una finestra temporale e confrontarli con l'evento corrente. Ad esempio, è possibile restituire la marca dell'automobile attuale se è diversa dall'ultima automobile che ha attraversato il casello.

**Input** :

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Output** :

| Casa automobilistica | Tempo |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000Z |

**Query** :

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Usare **LAG** per controllare il flusso di input dell'evento precedente, recuperando il valore *Make* e confrontandolo al valore *Make* dell'evento corrente, quindi restituire l'evento.

Per altre informazioni, vedere [**LAG**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Restituire l'ultimo evento in una finestra

Poiché gli eventi vengono raccolti dal sistema in tempo reale, non è presente una funzione in grado di determinare se un evento sarà l'ultimo ad arrivare in tale finestra temporale. A tale scopo, il flusso di input deve essere unito a un altro in cui l'ora di un evento è il tempo massimo per tutti gli eventi in tale finestra.

**Input** :

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Make3 |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Make1 |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Make2 |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Make2 |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |Make4 |27-07-2015T00:13:45.0000000Z |

**Output** :

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| VFE 1616 |Make2 |27-07-2015T00:09:31.0000000Z |
| MDR 6128 |Make4 |27-07-2015T00:13:45.0000000Z |

**Query** :

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

Il primo passaggio della query consente di trovare il timestamp massimo nelle finestre di 10 minuti, ovvero il timestamp dell'ultimo evento per la finestra. Il secondo unisce i risultati della prima query con il flusso originale per trovare gli eventi corrispondenti ai timestamp più recenti in ogni finestra. 

**DATEDIFF** è una funzione con data specifica che confronta e restituisce la differenza di tempo tra due campi DateTime. Per maggior informazioni, vedere le [funzioni delle date](/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Per altre informazioni sul join dei flussi, vedere [**JOIN**](/stream-analytics-query/join-azure-stream-analytics).

## <a name="data-aggregation-over-time"></a>Aggregazione dei dati nel tempo

Per calcolare le informazioni in un intervallo di tempo, è possibile aggregare insieme i dati. In questo esempio un conteggio viene calcolato negli ultimi 10 secondi di tempo per ogni marca di automobile specifica.

**Input** :

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000Z |1500 |

**Output** :

| Casa automobilistica | Conteggio |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Query** :

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

Questa aggregazione raggruppa le automobili per *Make* (Marca) e le conteggia ogni 10 secondi. L'output indica i valori *Make* e *Count* delle automobili che hanno superato il casello.

TumblingWindow è una funzione finestra usata per raggruppare gli eventi. È possibile applicare un'aggregazione a tutti gli eventi raggruppati. Per altre informazioni, vedere [Funzioni finestra](stream-analytics-window-functions.md).

Per altre informazioni sull'aggregazione, vedere [Funzioni di aggregazione](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="periodically-output-values"></a>Valori di output periodici

In caso di eventi irregolari o mancanti, è possibile generare un output di intervalli regolari da un input di dati più sparso. Generare, ad esempio, un evento ogni 5 secondi che segnali il punto di dati più recente individuato.

**Input** :

| Tempo | valore |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Output (prime 10 righe)** :

| Window_end | Last_event.Time | Last_event.Value |
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

**Query** :

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

questa query genera eventi ogni cinque secondi e restituisce l'ultimo evento ricevuto in precedenza. La durata **HOPPINGWINDOW** determina il raggio di ricerca della query nel passato per trovare l'evento più recente.

Per altre informazioni, vedere [Finestra di salto](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="correlate-events-in-a-stream"></a>Correlare gli eventi in un flusso

È possibile correlare gli eventi nello stesso flusso guardando gli eventi passati con la funzione **LAG** . Ad esempio, è possibile generare un output ogni volta che due automobili della stessa *Marca* passano una dopo l'altra dal casello negli ultimi 90 secondi.

**Input** :

| Casa automobilistica | License_plate | Tempo |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Make1 |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Output** :

| Casa automobilistica | Tempo | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Query** :

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

La funzione **LAG** può analizzare il flusso di input dell'evento precedente e recuperare il valore *Make* , confrontandolo con il valore *Make* dell'evento corrente.  Una volta raggiunta la condizione, è possibile proiettare i dati dell'evento precedente usando **LAG** nell'istruzione **SELECT** .

For more inforPer altre informazioni, vedere [LAG](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>rilevare la durata tra gli eventi

La durata di un evento può essere calcolata esaminando l'ultimo evento di avvio dopo la ricezione di un evento di fine. Questa query può essere utile per determinare il tempo impiegato da un utente in una pagina o una funzionalità.

**Input** :  

| Utente | Funzionalità | Event | Tempo |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Inizia |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Fine |2015-01-01T00:00:08.0000000Z |

**Output** :  

| Utente | Funzionalità | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Query** :

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

È possibile utilizzare la funzione **LAST** per recuperare l'ultimo evento in una condizione specifica. In questo esempio, la condizione è un evento di tipo Avvio, con partizionamento della ricerca per utente e funzionalità **PARTITION BY** . In questo modo, ogni utente e funzionalità vengono gestiti in modo indipendente durante la ricerca dell'evento di avvio. **LIMIT DURATION** limita la ricerca indietro nel tempo a un'ora tra gli eventi di fine e di inizio.

## <a name="count-unique-values"></a>contare valori univoci

È possibile usare **COUNT** e **DISTINCT** per contare il numero di valori di campo univoci presenti nel flusso in un intervallo di tempo. È possibile creare una query per calcolare quante automobili appartenenti a *marche* diverse sono passate da un casello autostradale in una finestra di due secondi.

**Input** :

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

**COUNT(DISTINCT Make)** restituisce il numero di valori distinti della colonna **Make** all'interno di una finestra temporale.
Per altre informazioni, vedere la funzione aggregata [**COUNT**](/stream-analytics-query/count-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Recuperare il primo evento in una finestra

È possibile usare **IsFirst** per recuperare il primo evento in una finestra temporale. Ad esempio, restituendo le informazioni sulla prima automobile a intervalli di 10 minuti.

**Input** :

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Make3 |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Make1 |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Make2 |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Make2 |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |Make4 |27-07-2015T00:13:45.0000000Z |

**Output** :

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |

**Query** :

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

**IsFirst** può anche eseguire la partizione dei dati e calcolare il primo evento per ogni *Marca* di automobile specifica, rilevato a intervalli di 10 minuti.

**Output** :

| License_plate | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Make1 |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Make3 |27-07-2015T00:02:17.0000000Z |
| YHN 6970 |Make2 |27-07-2015T00:06:00.0000000Z |
| QYF 9358 |Make1 |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |Make4 |27-07-2015T00:13:45.0000000Z |

**Query** :

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

Per altre informazioni, vedere [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="remove-duplicate-events-in-a-window"></a>rimuovere gli eventi duplicati in una finestra

quando si esegue un'operazione come il calcolo delle medie sugli eventi di un determinato intervallo di tempo, gli eventi duplicati devono essere filtrati. Nell'esempio seguente, il secondo evento è un duplicato del primo.

**Input** :  

| deviceId | Tempo | Attributo | Valore |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatura |100 |

**Output** :  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Query** :

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

**COUNT(DISTINCT Time)** restituisce il numero di valori distinct della colonna Tempo all'interno di una finestra temporale. È quindi possibile usare l'output del primo passaggio per calcolare la media di ogni dispositivo eliminando i duplicati.

Per altre informazioni, vedere [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>specificare la logica per i diversi casi/valori (istruzioni CASE)

Le istruzioni **CASE** possono fornire calcoli diversi per campi diversi, in base a un determinato criterio. Ad esempio, assegnare la corsia "A" alle automobili *Make1* e la corsia "B" alle altre marche.

**Input** :

| Casa automobilistica | Tempo |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |
| Make2 |2015-01-01T00:00:02.0000000Z |
| Make2 |2015-01-01T00:00:03.0000000Z |

**Output** :

| Casa automobilistica |Dispatch_to_lane | Tempo |
| --- | --- | --- |
| Make1 |"A" |2015-01-01T00:00:01.0000000Z |
| Make2 |"B" |2015-01-01T00:00:02.0000000Z |

**Soluzione** :

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

l'espressione **CASE** confronta un'espressione con un set di espressioni semplici per determinare il risultato. In questo esempio, i veicoli *Make1* vengono inviati alla corsia "A", mentre i veicoli di qualsiasi altra marca verranno assegnati alla corsia "B".

Per altre informazioni, vedere l'[Espressione dei casi](/stream-analytics-query/case-azure-stream-analytics).

## <a name="data-conversion"></a>Conversione di dati

È possibile eseguire il cast dei dati in tempo reale usando il metodo **CAST** . Il peso dell'auto può ad esempio essere convertito dal tipo **nvarchar(max)** al tipo **bigint** e usato in un calcolo numerico.

**Input** :

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000Z |"2000" |

**Output** :

| Casa automobilistica | Peso |
| --- | --- |
| Make1 |3000 |

**Query** :

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

usare un'istruzione **CAST** per specificarne il tipo di dati. Visualizzare l'elenco dei tipi di dati supportati in [Tipi di dati (Analisi di flusso di Azure)](/stream-analytics-query/data-types-azure-stream-analytics).

Per altre informazioni sulle [Funzioni di conversione dei dati](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="detect-the-duration-of-a-condition"></a>rilevare la durata di una condizione

Per le condizioni che si estendono su più eventi, è possibile usare la funzione **LAG** per identificare la durata di tale condizione. Ad esempio, si supponga che un bug abbia generato un peso errato per tutte le automobili (oltre 20.000 libbre) e che debba essere calcolata la durata di tale bug.

**Input** :

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

**Output** :

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Query** :

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
La prima istruzione **SELECT** abbina la misurazione del peso corrente con quella precedente, proiettandola insieme alla misura corrente. La seconda istruzione **SELECT** esamina l'ultimo evento in cui il valore *previous_weight* era inferiore a 20.000, dove il peso corrente era inferiore a 20.000 e il valore *previous_weight* dell'evento corrente era maggiore di 20.000.

End_fault è l'evento senza errori corrente in cui l'evento precedente aveva riscontrato errori, mentre Start_fault è l'ultimo evento senza errori prima di quello.

## <a name="process-events-with-independent-time-substreams"></a>Elaborare eventi con tempo indipendente (substream)

gli eventi possono arrivare in ritardo o non in ordine a causa di sfasamenti di orario tra producer di eventi, sfasamenti di orario tra partizioni o latenza di rete.
Ad esempio, l'orologio del dispositivo per *TollID* 2 è cinque secondi indietro rispetto a *TollID* 1, mentre quello di *TollID* 3 è 10 secondi indietro rispetto a *TollID* 1. Un calcolo può avvenire in modo indipendente per ogni casello, considerando solo i propri dati orari come timestamp.

**Input** :

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

**Output** :

| ID casello | Conteggio |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Query** :

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

la clausola **TIMESTAMP OVER BY** esamina la sequenza temporale di ogni dispositivo separatamente tramite substream. Gli eventi di output per ogni *TollID* vengono generati man mano che vengono elaborati, vale a dire che gli eventi sono in ordine rispetto a ogni *TollID* anziché venire riordinati come se tutti i dispositivi fossero nello stesso clock.

Per altre informazioni, vedere [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="session-windows"></a>Finestre delle sessioni

Una Finestra sessione è una finestra che continua a espandersi man mano che si verificano eventi e si chiude per il calcolo se non vengono ricevuti eventi dopo un periodo specifico o se raggiunge la durata massima.
Questa funzione è particolarmente utile quando si calcolano i dati di interazione degli utenti. Una finestra ha inizio quando un utente comincia a interagire con il sistema e si chiude quando non vengono più osservati eventi, ovvero quando l'utente ha cessato di interagire.
Ad esempio, un utente interagisce con una pagina Web dove viene registrato il numero di clic. È possibile usare una Finestra sessione per scoprire quanto a lungo l'utente ha interagito con il sito.

**Input** :

| User_id | Tempo | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**Output** :

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

**Query** :

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

**SELECT** proietta i dati rilevanti per l'interazione utente insieme alla durata dell'interazione. Raggruppamento dei dati per utente e una **SessionWindow** che si chiude se non si verificano interazioni entro 1 minuto, con dimensioni massime della finestra di 60 minuti.

Per altre informazioni su **SessionWindow** , vedere [Finestra di sessione](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Estendibilità del linguaggio con le funzioni definite dall'utente in JavaScript e C#

È possibile estendere il linguaggio di query di Analisi di flusso di Azure con funzioni personalizzate scritte in JavaScript o C#. Le funzioni definite dall'utente (UDF) sono calcoli personalizzate/complessi che non posso essere espresse facilmente nel linguaggio **SQL** . Queste funzioni possono essere definite una volta e usate più volte all'interno di una query. Ad esempio, è possibile usare un'UDF per convertire un valore *nvarchar(max)* esadecimale in un valore *bigint* .

**Input** :

| Device_id | HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Output** :

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

La funzione definita dall'utente calcolerà il valore *bigint* da HexValue su ogni evento utilizzato.

Per altre informazioni, vedere [JavaScript](./stream-analytics-javascript-user-defined-functions.md) e [C#](./stream-analytics-edge-csharp-udf.md).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Criteri di ricerca avanzati con MATCH_RECOGNIZE

**MATCH_RECOGNIZE** è un meccanismo avanzato di criteri di ricerca utilizzabile per associare una sequenza di eventi a un modello di espressione regolare ben definito.
Ad esempio, un ATM viene monitorato in tempo reale in caso di errori. Se durante il suo funzionamento vengono generati due messaggi di avviso consecutivi, l'amministratore deve essere avvisato.

**Input** :

| ATM_id | Operation_id | Return_Code | Tempo |
| --- | --- | --- | --- |
| 1 | "Immissione PIN" | "Esito positivo" | 2017-01-26T00:10:00.0000000Z |
| 2 | "Apertura fessura denaro" | "Esito positivo" | 2017-01-26T00:10:07.0000000Z |
| 2 | "Chiusura fessura denaro" | "Esito positivo" | 2017-01-26T00:10:11.0000000Z |
| 1 | "Immissione della quantità di ritiro" | "Esito positivo" | 2017-01-26T00:10:08.0000000Z |
| 1 | "Apertura fessura denaro" | "Avviso" | 2017-01-26T00:10:14.0000000Z |
| 1 | "Stampa del saldo bancario" | "Avviso" | 2017-01-26T00:10:19.0000000Z |

**Output** :

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Apertura fessura denaro" | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
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
**PATTERN** definisce l'espressione regolare da usare per la corrispondenza, in questo caso il numero di operazioni riuscite seguite da almeno due errori consecutivi.
Esiti positivo e negativo sono definiti dal valore Return_code e, una volta raggiunta la condizione, le **MEASURES** (Misurazioni) sono proiettate con *ATM_id* , l'operazione e l'ora del primo avviso.

Per altre informazioni, vedere [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Query geospaziali e di geofencing

Analisi di flusso di Azure offre funzioni geospaziali predefinite utilizzabili per implementare scenari quali gestione della flotta, condivisione delle corse, automobili connesse e rilevamento di asset.
I dati geospaziali possono essere inseriti in formati GeoJSON o WKT come parte del flusso di eventi o dei dati di riferimento.
Ad esempio, una società specializzata nella produzione di computer per la stampa di passaporti, fornisce in lease i propri computer a enti pubblici e consolati. La posizione di questi computer è controllata in modo rigoroso per evitare problemi di posizionamento e un possibile utilizzo per la contraffazione di passaporti. Ogni computer è dotato di un tracciatore GPS e queste informazioni vengono inviate nuovamente a un processo di Analisi di flusso di Azure.
Il produttore desidera tenere traccia della posizione di tali computer e ricevere un avviso se uno di essi lascia un'area autorizzata, in questo modo può disabilitarlo da remoto, avvisare le autorità e recuperarlo.

**Input** :

| Equipment_id | Equipment_current_location | Tempo |
| --- | --- | --- |
| 1 | "POINT(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000Z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000Z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

**Input dei dati di riferimento** :

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Output** :

| Equipment_id | Equipment_alert_location | Tempo |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

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

La query consente al produttore di monitorare automaticamente la posizione dei computer e di ricevere avvisi quando un computer lascia la rete geografica consentita. La funzione geospaziale predefinita consente agli utenti di usare i dati GPS all'interno della query senza librerie di terze parti.

Per altre informazioni, vedere l'articolo [Scenari di aggregazione geospaziale e di geofencing con Analisi di flusso di Azure](geospatial-scenarios.md).

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore supporto, provare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](/rest/api/streamanalytics/)