---
title: Modelli di query comuni in Analisi di flusso di Azure
description: Questo articolo descrive alcuni modelli e diverse progettazioni comuni di query che possono rivelarsi utili nei processi di Analisi di flusso di Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 88df7ae0d4e6054d82302ad5f0adabcf656cb0f5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620810"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Esempi di query per modelli di uso comune di Analisi di flusso

Le query in Analisi di flusso di Azure sono espresse in un linguaggio di query di tipo SQL. Questi costrutti di linguaggio sono documentati nella guida [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference). 

La progettazione delle query può esprimere la logica di pass-through semplice per spostare i dati dell'evento da un flusso di input in un archivio dati di output o l'attività possa eseguire analisi dei modelli avanzati corrispondente e temporali per calcolare le aggregazioni su diversi intervalli di tempo come nel [compilare IoT soluzione tramite Stream Analitica](stream-analytics-build-an-iot-solution-using-stream-analytics.md) Guida. È possibile unire dati da più input per combinare gli eventi di streaming ed è possibile eseguire ricerche di dati di riferimento statici per arricchire i valori di evento. È anche possibile scrivere i dati per più output.

Questo articolo illustra le soluzioni per diversi modelli di query comuni basati su scenari reali.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Usare tipi di dati complessi in JSON e AVRO

Analisi di flusso di Azure supporta l'elaborazione di eventi nei formati di dati CSV, JSON e Avro.

Entrambi i formati JSON e Avro possono contenere tipi complessi come le matrici o gli oggetti nidificati (record). Per altre informazioni sull'uso di questi tipi di dati complessi, vedere la [i dati di analisi JSON e AVRO](stream-analytics-parsing-json.md) articolo.

## <a name="query-example-convert-data-types"></a>Esempio di query: Convertire tipi di dati

**Descrizione**: definire i tipi di proprietà nel flusso di input. Ad esempio, il peso dell'auto è immesso nel flusso di input come stringhe e deve essere convertito **INT** eseguire **somma**.

**Input**:

| Assicurarsi | Time | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Output**:

| Assicurarsi | Peso |
| --- | --- |
| Honda |3000 |

**Soluzione**:

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

**Spiegazione**: usare un'istruzione **CAST** nel campo **Peso** per specificarne il tipo di dati. Visualizzare l'elenco dei tipi di dati supportati in [Tipi di dati (Analisi di flusso di Azure)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Esempio di query: Usare LIKE/NOT LIKE e la corrispondenza dei modelli

**Descrizione**: verificare che un valore del campo dell'evento corrisponda a un determinato modello.
Verificare, ad esempio, che il risultato restituisca le targhe che iniziano per A e terminano con 9.

**Input**:

| Assicurarsi | Targa | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Output**:

| Assicurarsi | Targa | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Soluzione**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Spiegazione**: usare l'istruzione **LIKE** per verificare che il valore del campo **LicensePlate**. Deve iniziare con la lettera A, quindi contenga una stringa di zero o più caratteri e quindi terminare con il numero di 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Esempio di query: specificare la logica per i diversi casi/valori (istruzioni CASE)

**Descrizione**: fornire un calcolo diverso per un campo in base un determinato criterio. Fornire ad esempio una stringa descrittiva relativa al numero di automobili della stessa casa automobilistica che sono passate, con un caso speciale impostato su 1.

**Input**:

| Assicurarsi | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output**:

| Auto passate | Tempo |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyota |2015-01-01T00:00:10.0000000Z |

**Soluzione**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Spiegazione**: l'espressione **CASE** confronta un'espressione con un set di espressioni semplici per determinare il risultato. In questo esempio, le marche di veicolo con un conteggio pari a 1 hanno restituito una stringa descrittiva diversa da quella delle marche di veicolo con un numero diverso da 1.

## <a name="query-example-send-data-to-multiple-outputs"></a>Esempio di query: inviare dati a più output

**Descrizione**: inviare dati a più destinazioni di output da un singolo processo. Analizzare, ad esempio, i dati per un avviso basato su soglie e archiviare tutti gli eventi nell'archiviazione BLOB.

**Input**:

| Assicurarsi | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Assicurarsi | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Assicurarsi | Time | Conteggio |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Soluzione**:

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

**Spiegazione**: la clausola **INTO** indica all'Analisi di flusso in quali output scrivere i dati ottenuti con questa istruzione. La prima query è un pass-through dei dati ricevuti da un output denominato **ArchiveOutput**. La seconda query effettua una semplice aggregazione, Filtra e lo invia i risultati a un sistema di avviso downstream **AlertOutput**.

È anche possibile riusare i risultati delle espressioni di tabella comune (CTE), ovvero le istruzioni **WITH**, in più istruzioni di output. Questa opzione offre il vantaggio aggiuntivo di aprire un numero inferiore di lettori nell'origine di input.

Ad esempio: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>Esempio di query: contare valori univoci

**Descrizione**: contare il numero di valori di campo univoci presenti nel flusso in un intervallo di tempo. Ad esempio, quante automobili appartenenti alla stessa casa automobilistica sono passate da un casello autostradale in una finestra di due secondi?

**Input**:

| Assicurarsi | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output:**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Soluzione:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Spiegazione:** 
**COUNT(DISTINCT Make)** restituisce il numero di valori distinct della colonna **Casa automobilistica** all'interno di una finestra temporale.

## <a name="query-example-determine-if-a-value-has-changed"></a>Esempio di query: determinare la potenziale variazione di un valore

**Descrizione**: esaminare un valore precedente per determinare se è diverso rispetto al valore corrente. L'auto precedente passata dal casello autostradale, ad esempio, è della stessa casa automobilistica dell'auto corrente?

**Input**:

| Assicurarsi | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Output**:

| Assicurarsi | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Soluzione**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**Spiegazione**: usare **LAG** per esaminare il flusso di input di un evento precedente e ottenere il valore **Casa automobilistica**. Confrontarlo quindi con il valore **Casa automobilistica** dell'evento corrente per restituire l'evento di variazione.

## <a name="query-example-find-the-first-event-in-a-window"></a>Esempio di query: trovare il primo evento in una finestra

**Descrizione**: trovare la prima auto in ogni intervallo di 10 minuti.

**Input**:

| Targa | Assicurarsi | Time |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Ford |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Honda |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Toyota |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Toyota |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Output**:

| Targa | Assicurarsi | Time |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |

**Soluzione**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

A questo punto è possibile modificare il problema e trovare la prima Auto di una particolare marca in ogni intervallo di 10 minuti.

| Targa | Assicurarsi | Time |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Ford |27-07-2015T00:02:17.0000000Z |
| YHN 6970 |Toyota |27-07-2015T00:06:00.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Soluzione**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>Esempio di query: trovare l'ultimo evento in una finestra

**Descrizione**: trovare l'ultima auto in ogni intervallo di 10 minuti.

**Input**:

| Targa | Assicurarsi | Time |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Ford |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Honda |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Toyota |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Toyota |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Output**:

| Targa | Assicurarsi | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |27-07-2015T00:09:31.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Soluzione**:

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
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**Spiegazione**: la query si articola in due passaggi. Il primo rileva il timestamp più recente in finestre di 10 minuti, il secondo unisce i risultati della prima query con il flusso originale per trovare gli eventi corrispondenti ai timestamp più recenti in ogni finestra. 

## <a name="query-example-detect-the-absence-of-events"></a>Esempio di query: rilevare l'assenza di eventi

**Descrizione**: verificare che un flusso non abbia un valore corrispondente a determinati criteri.
Ad esempio, 2 automobili consecutive della stessa casa automobilistica hanno attraversato il casello negli ultimi 90 secondi?

**Input**:

| Assicurarsi | Targa | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Output**:

| Assicurarsi | Time | Targa auto corrente | Targa prima auto | Tempo prima auto |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Soluzione**:

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Spiegazione**: usare **LAG** per esaminare il flusso di input di un evento precedente e ottenere il valore **Casa automobilistica**. Confrontarlo quindi con il valore **Casa automobilistica** dell'evento corrente e, se corrispondono, restituire l'evento. È possibile anche usare **LAG** per ottenere i dati relativi all'auto precedente.

## <a name="query-example-detect-the-duration-between-events"></a>Esempio di query: rilevare la durata tra gli eventi

**Descrizione**: trovare la durata di un determinato evento. Dato un clickstream Web, determinare ad esempio i tempi di una funzionalità.

**Input**:  

| Utente | Funzionalità | event | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Fine |2015-01-01T00:00:08.0000000Z |

**Output**:  

| Utente | Funzionalità | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Soluzione**:

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

**Spiegazione**: usare la funzione **LAST** per recuperare l'ultimo valore di **TIME** se il tipo di evento corrisponde a **Start**. La funzione **LAST** usa **PARTITION BY [user]** per indicare che il risultato viene calcolato per utente univoco. La query dispone di una soglia massima di 1 ora per la differenza di tempo tra gli eventi **Start** e **Stop**, ma è configurabile in base alle esigenze: **(LIMIT DURATION(hour, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Esempio di query: rilevare la durata di una condizione
**Descrizione**: individuare la durata di una condizione.
Ad esempio, si supponga che un bug abbia generato un peso errato per tutte le automobili (oltre 20.000 libbre) e che debba essere calcolata la durata di tale bug.

**Input**:

| Assicurarsi | Time | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Output**:

| Inizio errore | Fine errore |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Soluzione**:

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**Spiegazione**: usare **LAG** per visualizzare il flusso di input per 24 ore e cercare le istanze in cui **StartFault** e **StopFault** vengono intervallati in base al peso (< 20000).

## <a name="query-example-fill-missing-values"></a>Esempio di query: immettere i valori mancanti

**Descrizione**: per il flusso di eventi con i valori mancanti, generare un flusso di eventi con intervalli regolari. Generare, ad esempio, un evento ogni 5 secondi che segnali il punto di dati più recente individuato.

**Input**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Output (prime 10 righe)** :

| windowend | lastevent.t | lastevent.value |
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

**Soluzione**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Spiegazione**: questa query genera eventi ogni cinque secondi e restituisce l'ultimo evento ricevuto in precedenza. Il [finestra di salto](/stream-analytics-query/hopping-window-azure-stream-analytics) durata determina fino a quando risale la query per trovare l'evento più recente (300 secondi in questo esempio).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Esempio di query: correlare due tipi di evento all'interno dello stesso flusso

**Descrizione**: a volte è necessario generare gli avvisi in base a più tipi di evento che si sono verificati in un determinato intervallo di tempo. Nello scenario IoT per forni domestici, ad esempio, deve essere generato un avviso quando la temperatura della ventola è inferiore a 40 e la potenza massima negli ultimi tre minuti è minore di 10.

**Input**:

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**Output**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Short circuit heating elements" (Corto circuito elementi riscaldanti) |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Short circuit heating elements" (Corto circuito elementi riscaldanti) |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Short circuit heating elements" (Corto circuito elementi riscaldanti) |15 |

**Soluzione**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**Spiegazione**: la prima query `max_power_during_last_3_mins` usa la [finestra scorrevole](/stream-analytics-query/sliding-window-azure-stream-analytics) per trovare il valore massimo del sensore di alimentazione per ogni dispositivo, durante gli ultimi 3 minuti. La seconda query viene unita alla prima query per trovare il valore di potenza nella finestra più recente rilevante per l'evento corrente. A condizione che le condizioni siano soddisfatte, viene quindi generato un avviso per il dispositivo.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Esempio di query: elaborare eventi indipendenti dallo sfasamento di orario dei dispositivi (substream)

**Descrizione**: gli eventi possono arrivare in ritardo o non in ordine a causa di sfasamenti di orario tra producer di eventi, sfasamenti di orario tra partizioni o latenza di rete. Nell'esempio seguente, l'orologio di dispositivo per 2 TollID è cinque secondi dietro TollID 1 e il clock di dispositivo per 3 TollID è dieci secondi dietro TollID 1. 

**Input**:

| Targa | Assicurarsi | Time | ID casello |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |27-07-2015T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**Output**:

| ID casello | Conteggio |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Soluzione**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Spiegazione**: la clausola [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) esamina la sequenza temporale di ogni dispositivo separatamente tramite substream. Gli eventi di output per ogni TollID vengono generati man mano che vengono elaborati, vale a dire che gli eventi sono in ordine rispetto a ogni TollID anziché venire riordinati come se tutti i dispositivi fossero nello stesso clock.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Esempio di query: rimuovere gli eventi duplicati in una finestra

**Descrizione**: quando si esegue un'operazione come il calcolo delle medie sugli eventi di un determinato intervallo di tempo, gli eventi duplicati devono essere filtrati. Nell'esempio seguente, il secondo evento è un duplicato del primo.

**Input**:  

| deviceId | Time | Attributo | Valore |
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

**Soluzione**:

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

**Spiegazione**: [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics) restituisce il numero di valori distinct della colonna Tempo all'interno di una finestra temporale. È quindi possibile usare l'output di questo passaggio per calcolare la media di ogni dispositivo eliminando i duplicati.

## <a name="geofencing-and-geospatial-queries"></a>Query geospaziali e Geofencing
Azure Stream Analitica fornisce le funzioni geospaziali predefinite che possono essere utilizzate per implementare scenari come la gestione della flotta, recarsi condivisione, automobili connesse e verifica degli asset. I dati geospaziali possono essere inseriti in formato GeoJSON o WKT come parte del flusso di eventi o dati di riferimento. Per altre informazioni, vedere la [scenari di aggregazione al Geofencing e geospaziale con Azure Stream Analitica](geospatial-scenarios.md) articolo.

## <a name="language-extensibility-through-javascript-and-c"></a>Estendibilità di linguaggio tramite JavaScript eC#
Azure Stream Ananlytics query langugae può essere esteso con le funzioni personalizzate scritte in JavaScript o C# linguaggi. Per altre informazioni vedere gli articoli gli elementi seguenti:
* [Funzioni definite dall'utente di Azure Stream Analitica JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Aggregazioni definite dall'utente di Azure Stream Analitica JavaScript](stream-analytics-javascript-user-defined-aggregates.md)
* [Lo sviluppo di .NET Standard funzioni definite dall'utente per i processi Edge di Analitica Stream di Azure](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>Ottenere aiuto

Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

