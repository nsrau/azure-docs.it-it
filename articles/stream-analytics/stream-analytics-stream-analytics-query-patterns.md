---
title: Modelli di query comuni in Analisi di flusso di Azure
description: Questo articolo descrive alcuni modelli e diverse progettazioni comuni di query che possono rivelarsi utili nei processi di Analisi di flusso di Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: ffcf81ee8637c2ce01b3a7822d179609bd9dbfaa
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794533"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Esempi di query per modelli di uso comune di Analisi di flusso

## <a name="introduction"></a>Introduzione
Le query in Analisi di flusso di Azure sono espresse in un linguaggio di query di tipo SQL. Questi costrutti di linguaggio sono documentati nella guida [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

La progettazione delle query può definire una logica pass-through semplice per spostare i dati degli eventi da un flusso di input in un altro archivio dati di output. In alternativa, è possibile eseguire criteri di ricerca e analisi temporali avanzate per calcolare le aggregazioni attraverso diverse finestre temporali, come nel TollApp di esempio. È possibile aggiungere dati da più input per combinare flussi di eventi ed eseguire ricerche sui dati di riferimento statici per arricchire i valori degli eventi. È anche possibile scrivere dati per più output.

Questo articolo illustra le soluzioni per diversi modelli di query comuni basati su scenari reali. È un lavoro in corso che continua a essere periodicamente aggiornato con nuovi modelli.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Usare tipi di dati complessi in JSON e AVRO 
Analisi di flusso di Azure supporta l'elaborazione di eventi nei formati di dati CSV, JSON e Avro.
Entrambi i formati JSON e Avro possono contenere tipi complessi come le matrici o gli oggetti nidificati (record). Per usare questi tipi di dati complessi, vedere l'articolo [Analisi di dati JSON e AVRO](stream-analytics-parsing-json.md).


## <a name="query-example-convert-data-types"></a>Esempio di query: Convertire tipi di dati
**Descrizione**: definire i tipi di proprietà nel flusso di input.
Il peso dell'auto, ad esempio, viene immesso nel flusso di input come stringa e deve essere convertito in **INT** per eseguire l'operazione **SUM** dei valori.

**Input**:

| Assicurarsi | Tempo | Peso |
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

**Spiegazione**: usare un'istruzione **CAST** nel campo **Peso** per specificarne il tipo di dati. Visualizzare l'elenco dei tipi di dati supportati in [Tipi di dati (Analisi di flusso di Azure)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Esempio di query: usare Like/Not like per la corrispondenza dei modelli
**Descrizione**: verificare che un valore del campo dell'evento corrisponda a un determinato modello.
Verificare, ad esempio, che il risultato restituisca le targhe che iniziano per A e terminano con 9.

**Input**:

| Assicurarsi | Targa | Tempo |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Output**:

| Assicurarsi | Targa | Tempo |
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

**Spiegazione**: usare l'istruzione **LIKE** per verificare che il valore del campo **LicensePlate**. inizi con la lettera A, contenga una stringa di zeri o altri caratteri e termini con 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Esempio di query: specificare la logica per i diversi casi/valori (istruzioni CASE)
**Descrizione**: fornire un calcolo diverso per un campo in base un determinato criterio.
Fornire ad esempio una stringa descrittiva relativa al numero di automobili della stessa casa automobilistica che sono passate, con un caso speciale impostato su 1.

**Input**:

| Assicurarsi | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output**:

| Auto passate | Tempo |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyota |2015-01-01T00:00:10.0000000Z |

**Soluzione**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Spiegazione**: l'espressione **CASE** confronta un'espressione con un set di espressioni semplici per determinare il risultato. In questo esempio, le marche di veicolo con un conteggio pari a 1 hanno restituito una stringa descrittiva diversa da quella delle marche di veicolo con un numero diverso da 1. 

## <a name="query-example-send-data-to-multiple-outputs"></a>Esempio di query: inviare dati a più output
**Descrizione**: inviare dati a più destinazioni di output da un singolo processo.
Analizzare, ad esempio, i dati per un avviso basato su soglie e archiviare tutti gli eventi nell'archiviazione BLOB.

**Input**:

| Assicurarsi | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Assicurarsi | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Assicurarsi | Tempo | Conteggio |
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
        System.TimeStamp AS Time,
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

**Spiegazione**: la clausola **INTO** indica all'Analisi di flusso in quali output scrivere i dati ottenuti con questa istruzione.
La prima è una query pass-through dei dati ricevuti per un output denominato **ArchiveOutput**.
La seconda query effettua una semplice aggregazione, filtra e invia i risultati a un sistema di avviso downstream.

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
**Descrizione**: contare il numero di valori di campo univoci presenti nel flusso in un intervallo di tempo.
Ad esempio, quante automobili appartenenti alla stessa casa automobilistica sono passate da un casello autostradale in una finestra di due secondi?

**Input**:

| Assicurarsi | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output:**

| CountMake | Tempo |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Soluzione:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Spiegazione:**
**COUNT(DISTINCT Make)** restituisce il numero di valori distinct della colonna **Casa automobilistica** all'interno di una finestra temporale.

## <a name="query-example-determine-if-a-value-has-changed"></a>Esempio di query: determinare la potenziale variazione di un valore
**Descrizione**: esaminare un valore precedente per determinare se è diverso rispetto al valore corrente.
L'auto precedente passata dal casello autostradale, ad esempio, è della stessa casa automobilistica dell'auto corrente?

**Input**:

| Assicurarsi | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Output**:

| Assicurarsi | Tempo |
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

| Targa | Assicurarsi | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Ford |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Honda |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Toyota |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Toyota |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Output**:

| Targa | Assicurarsi | Tempo |
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

Si ridefinirà ora il problema per trovare la prima auto di una particolare casa automobilistica a intervalli di 10 minuti.

| Targa | Assicurarsi | Tempo |
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

| Targa | Assicurarsi | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Ford |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Honda |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Toyota |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Toyota |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Output**:

| Targa | Assicurarsi | Tempo |
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

| Assicurarsi | Targa | Tempo |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Output**:

| Assicurarsi | Tempo | Targa auto corrente | Targa prima auto | Tempo prima auto |
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

| Utente | Funzionalità | Event | Tempo |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Inizia |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Output**:  

| Utente | Funzionalità | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Soluzione**:

```SQL
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**Spiegazione**: usare la funzione **LAST** per recuperare l'ultimo valore di **TIME** se il tipo di evento corrisponde a **Start**. La funzione **LAST** usa **PARTITION BY [user]** per indicare che il risultato viene calcolato per utente univoco. La query dispone di una soglia massima di 1 ora per la differenza di tempo tra gli eventi **Start** e **Stop**, ma è configurabile in base alle esigenze: **(LIMIT DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Esempio di query: rilevare la durata di una condizione
**Descrizione**: individuare la durata di una condizione.
Ad esempio, si supponga che un bug abbia generato un peso errato per tutte le automobili (oltre 20.000 libbre) e che debba essere calcolata la durata di tale bug.

**Input**:

| Assicurarsi | Tempo | Peso |
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
**Descrizione**: per il flusso di eventi con i valori mancanti, generare un flusso di eventi con intervalli regolari.
Generare, ad esempio, un evento ogni 5 secondi che segnali il punto di dati più recente individuato.

**Input**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Output (prime 10 righe)**:

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
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Spiegazione**: questa query genera eventi ogni cinque secondi e restituisce l'ultimo evento ricevuto in precedenza. La durata della [finestra di salto](https://msdn.microsoft.com/library/dn835041.aspx "Finestra di salto - Analisi di flusso di Azure") determina fino a quando risale la query per cercare l'evento più recente. In questo esempio, 300 secondi.


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Esempio di query: correlare due tipi di evento all'interno dello stesso flusso
**Descrizione**: a volte è necessario generare gli avvisi in base a più tipi di evento che si sono verificati in un determinato intervallo di tempo.
Nello scenario IoT per forni domestici, ad esempio, deve essere generato un avviso quando la temperatura della ventola è inferiore a 40 e la potenza massima negli ultimi tre minuti è minore di 10.

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
        System.TimeStamp AS windowTime,
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

**Spiegazione**: la prima query `max_power_during_last_3_mins` usa la [finestra scorrevole](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) per trovare il valore massimo del sensore di alimentazione per ogni dispositivo, durante gli ultimi 3 minuti. La seconda query viene unita alla prima query per trovare il valore di potenza nella finestra più recente rilevante per l'evento corrente. A condizione che le condizioni siano soddisfatte, viene quindi generato un avviso per il dispositivo.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Esempio di query: elaborare eventi indipendenti dallo sfasamento di orario dei dispositivi (substream)
**Descrizione**: gli eventi possono arrivare in ritardo o non in ordine a causa di sfasamenti di orario tra producer di eventi, sfasamenti di orario tra partizioni o latenza di rete. Nell'esempio seguente il clock di dispositivo per TollID 2 è dieci secondi indietro rispetto a TollID 1 e il clock di dispositivo per TollID 3 è cinque secondi indietro rispetto a TollID 1. 


**Input**:
| Targa | Assicurarsi | Tempo | ID casello |
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

**Spiegazione**: la clausola [TIMESTAMP BY OVER](https://msdn.microsoft.com/azure/stream-analytics/reference/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) esamina la sequenza temporale di ogni dispositivo separatamente tramite substream. Gli eventi di output per ogni TollID vengono generati man mano che vengono elaborati, vale a dire che gli eventi sono in ordine rispetto a ogni TollID anziché venire riordinati come se tutti i dispositivi fossero nello stesso clock.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Esempio di query: rimuovere gli eventi duplicati in una finestra
**Descrizione**: quando si esegue un'operazione come il calcolo delle medie sugli eventi di un determinato intervallo di tempo, gli eventi duplicati devono essere filtrati.

**Input**:  

| deviceId | Tempo | Attributo | Valore |
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
        SYSTEM.TIMESTAMP
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Spiegazione**: [COUNT(DISTINCT Time)](https://docs.microsoft.com/en-us/stream-analytics-query/count-azure-stream-analytics) restituisce il numero di valori distinct della colonna Tempo all'interno di una finestra temporale. È quindi possibile usare l'output di questo passaggio per calcolare la media di ogni dispositivo eliminando i duplicati.

## <a name="get-help"></a>Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

