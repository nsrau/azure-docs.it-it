---
title: Esempi di query per modelli di uso comune in Analisi di flusso | Documentazione Microsoft
description: Modelli di query comuni di Analisi di flusso di Azure
keywords: esempi di query
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jenniehubbard
editor: cgronlun
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/08/2017
ms.author: jenniehubbard
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: bdaaf8fe6d18ba9dcb533864e691f11272dc9f9f
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Esempi di query per modelli di uso comune di Analisi di flusso
## <a name="introduction"></a>Introduzione
Le query in Analisi di flusso di Azure sono espresse in un linguaggio di query di tipo SQL. Queste query sono documentate nella guida [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx). Questo articolo illustra le soluzioni per diversi modelli di query comuni basati su scenari reali. È un lavoro in corso che continua a essere periodicamente aggiornato con nuovi modelli.

## <a name="query-example-convert-data-types"></a>Esempio di query: convertire tipi di dati
**Descrizione**: definire i tipi di proprietà nel flusso di input.
Il peso dell'auto, ad esempio, viene immesso nel flusso di input come stringa e deve essere convertito in **INT** per eseguire l'operazione **SUM** dei valori.

**Input**:

| Casa automobilistica | Tempo | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Output**:

| Casa automobilistica | Peso |
| --- | --- |
| Honda |3000 |

**Soluzione**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Spiegazione**: usare un'istruzione **CAST** nel campo **Peso** per specificarne il tipo di dati. Visualizzare l'elenco dei tipi di dati supportati in [Tipi di dati (Analisi di flusso di Azure)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Esempio di query: Usare Like/Not like per la corrispondenza dei modelli
**Descrizione**: verificare che un valore del campo dell'evento corrisponda a un determinato modello.
Verificare, ad esempio, che il risultato restituisca le targhe che iniziano per A e terminano con 9.

**Input**:

| Casa automobilistica | Targa | Tempo |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Output**:

| Casa automobilistica | Targa | Tempo |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Soluzione**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Spiegazione**: usare l'istruzione **LIKE** per verificare che il valore del campo **LicensePlate** inizi con la lettera A, contenga una stringa di zeri o altri caratteri e termini con 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Esempio di query: Specificare la logica per i diversi casi/valori (istruzioni CASE)
**Descrizione**: fornire un calcolo diverso per un campo in base un determinato criterio.
Fornire ad esempio una stringa descrittiva relativa al numero di automobili della stessa casa automobilistica che sono passate, con un caso speciale impostato su 1.

**Input**:

| Casa automobilistica | Tempo |
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

**Spiegazione**: la clausola **CASE** consente di fornire un calcolo diverso in base ad alcuni criteri (in questo esempio, il numero di automobili nella finestra di aggregazione).

## <a name="query-example-send-data-to-multiple-outputs"></a>Esempio di query: Invio di dati a più output
**Descrizione**: inviare dati a più destinazioni di output da un singolo processo.
Analizzare, ad esempio, i dati per un avviso basato su soglie e archiviare tutti gli eventi nell'archiviazione BLOB.

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Casa automobilistica | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Casa automobilistica | Tempo | Conteggio |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Soluzione**:

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

**Spiegazione**: la clausola **INTO** indica all'Analisi di flusso in quali output scrivere i dati ottenuti con questa istruzione.
La prima è una query pass-through dei dati ricevuti per un output denominato **ArchiveOutput**.
La seconda query effettua una semplice aggregazione, filtra e invia i risultati a un sistema di avviso downstream.

È anche possibile riusare i risultati delle espressioni di tabella comune (CTE), ovvero le istruzioni **WITH**, in più istruzioni di output. Questa opzione offre il vantaggio aggiuntivo di aprire un numero inferiore di lettori nell'origine di input.
Ad esempio: 

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

## <a name="query-example-count-unique-values"></a>Esempio di query: contare valori univoci
**Descrizione**: contare il numero di valori di campo univoci presenti nel flusso in un intervallo di tempo.
Ad esempio, quante automobili appartenenti alla stessa casa automobilistica sono passate da un casello autostradale in una finestra di due secondi?

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output:**

| Conteggio | Tempo |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Soluzione:**

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**Spiegazione:**
**COUNT(DISTINCT Make)** restituisce il numero di valori distinct della colonna **Casa automobilistica** all'interno di una finestra temporale.

## <a name="query-example-determine-if-a-value-has-changed"></a>Esempio di query: Determinare la potenziale variazione di un valore
**Descrizione**: esaminare un valore precedente per determinare se è diverso rispetto al valore corrente.
L'auto precedente passata dal casello autostradale, ad esempio, è della stessa casa automobilistica dell'auto corrente?

**Input**:

| Casa automobilistica | Tempo |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Output**:

| Casa automobilistica | Tempo |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Soluzione**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Spiegazione**: usare **LAG** per esaminare il flusso di input di un evento precedente e ottenere il valore **Casa automobilistica**. Confrontarlo quindi con il valore **Casa automobilistica** dell'evento corrente per restituire l'evento di variazione.

## <a name="query-example-find-the-first-event-in-a-window"></a>Esempio di query: trovare il primo evento in una finestra
**Descrizione**: trovare la prima auto in ogni intervallo di 10 minuti.

**Input**:

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Ford |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Honda |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Toyota |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Toyota |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Output**:

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |

**Soluzione**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Si ridefinirà ora il problema per trovare la prima auto di una particolare casa automobilistica a intervalli di 10 minuti.

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Ford |27-07-2015T00:02:17.0000000Z |
| YHN 6970 |Toyota |27-07-2015T00:06:00.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Soluzione**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>Esempio di query: trovare l'ultimo evento in una finestra
**Descrizione**: trovare l'ultima auto in ogni intervallo di 10 minuti.

**Input**:

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| DXE 5291 |Honda |27-07-2015T00:00:05.0000000Z |
| YZK 5704 |Ford |27-07-2015T00:02:17.0000000Z |
| RMV 8282 |Honda |27-07-2015T00:05:01.0000000Z |
| YHN 6970 |Toyota |27-07-2015T00:06:00.0000000Z |
| VFE 1616 |Toyota |27-07-2015T00:09:31.0000000Z |
| QYF 9358 |Honda |27-07-2015T00:12:02.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Output**:

| Targa | Casa automobilistica | Tempo |
| --- | --- | --- |
| VFE 1616 |Toyota |27-07-2015T00:09:31.0000000Z |
| MDR 6128 |BMW |27-07-2015T00:13:45.0000000Z |

**Soluzione**:

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

**Spiegazione**: la query si articola in due passaggi. Il primo rileva il timestamp più recente in finestre di 10 minuti, il secondo unisce i risultati della prima query con il flusso originale per trovare gli eventi corrispondenti ai timestamp più recenti in ogni finestra. 

## <a name="query-example-detect-the-absence-of-events"></a>Esempio di query: Rilevare l'assenza di eventi
**Descrizione**: verificare che un flusso non abbia un valore corrispondente a determinati criteri.
Ad esempio, 2 automobili consecutive della stessa casa automobilistica hanno attraversato il casello negli ultimi 90 secondi?

**Input**:

| Casa automobilistica | Targa | Tempo |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Output**:

| Casa automobilistica | Tempo | Targa auto corrente | Targa prima auto | Tempo prima auto |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Soluzione**:

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

**Spiegazione**: usare **LAG** per esaminare il flusso di input di un evento precedente e ottenere il valore **Casa automobilistica**. Confrontarlo quindi con il valore **Casa automobilistica** dell'evento corrente e, se corrispondono, restituire l'evento. È possibile anche usare **LAG** per ottenere i dati relativi all'auto precedente.

## <a name="query-example-detect-the-duration-between-events"></a>Esempio di query: rilevare la durata tra gli eventi
**Descrizione**: trovare la durata di un determinato evento. Dato un clickstream Web, determinare ad esempio i tempi di una funzionalità.

**Input**:  

| Utente | Funzionalità | Evento | Tempo |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Inizia |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Output**:  

| Utente | Funzionalità | Durata |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Soluzione**:

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Spiegazione**: usare la funzione **LAST** per recuperare l'ultimo valore di **TIME** se il tipo di evento corrisponde a **Start**. La funzione **LAST** usa **PARTITION BY [user]** per indicare che il risultato viene calcolato per utente univoco. La query dispone di una soglia massima di 1 ora per la differenza di tempo tra gli eventi **Start** e **Stop**, ma è configurabile in base alle esigenze: **(LIMIT DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Esempio di query: rilevare la durata di una condizione
**Descrizione**: individuare la durata di una condizione.
Si supponga, ad esempio, che un bug abbia generato un peso errato per tutte le automobili (oltre 20.000 libbre, pari a 9 tonnellate) e che si voglia calcolare la durata del bug.

**Input**:

| Casa automobilistica | Tempo | Peso |
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

````
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
````

**Spiegazione**: usare **LAG** per visualizzare il flusso di input per 24 ore e cercare le istanze in cui **StartFault** e **StopFault** vengono intervallati in base al peso (< 20.000).

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

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Spiegazione**: questa query genera eventi ogni cinque secondi e restituisce l'ultimo evento ricevuto in precedenza. La durata della [finestra di salto](https://msdn.microsoft.com/library/dn835041.aspx "Finestra di salto - Analisi di flusso di Azure") determina fino a quando risale la query per cercare l'evento più recente. In questo esempio, 300 secondi.

## <a name="get-help"></a>Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


