---
title: Esempi di query per modelli di uso comune in Analisi di flusso | Documentazione Microsoft
description: 'Modelli di query comuni di Analisi di flusso di Azure  '
keywords: esempi di query
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 976e765a906896c236497d074ca2a8c5fb1f1c53
ms.lasthandoff: 05/01/2017


---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Esempi di query per modelli di uso comune di Analisi di flusso
## <a name="introduction"></a>Introduzione
Le query in analisi di flusso di Azure vengono espresse in un linguaggio di query simile a SQL, documentato nella guida di [riferimento sul linguaggio di query con l'analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx) .  Questo articolo illustra le soluzioni per diversi modelli di query comuni basati su scenari reali.  È un lavoro in corso che continuerà a essere aggiornato con nuovi modelli su base continuativa.

## <a name="query-example-data-type-conversions"></a>Esempio di query: Conversioni di tipi di dati
**Descrizione**: definire i tipi delle proprietà nel flusso di input.
Ad esempio, il peso dell'auto è immesso nel flusso di input come stringa e deve essere convertito in INT per eseguire SUM dei vari valori.

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

**Spiegazione**: utilizzare un'istruzione CAST nel campo Peso per specificarne il tipo (vedere l'elenco dei tipi di dati supportati [qui](https://msdn.microsoft.com/library/azure/dn835065.aspx)).

## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>Esempio di query: Uso di Like/Not like per la corrispondenza dei modelli
**Descrizione**: verificare che un valore del campo dell'evento corrisponda a un determinato modello. Ad esempio, restituire le targhe che iniziano per A e terminano con 9

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

**Spiegazione**: utilizzare l'istruzione LIKE per verificare che il valore del campo LicensePlate inizi con la lettera A, contenga una stringa di zeri o altri caratteri e termini con 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Esempio di query: Specificare la logica per i diversi casi/valori (istruzioni CASE)
**Descrizione**: fornire calcoli differenti per un campo in base ad alcuni criteri.
Ad esempio, fornire una stringa descrittiva relativa al numero di automobili della stessa casa automobilistica sono passate con un caso speciale di 1.

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

**Spiegazione**: la clausola CASE consente di fornire un calcolo diverso in base ad alcuni criteri (in questo esempio, il numero di automobili nella finestra di aggregazione).

## <a name="query-example-send-data-to-multiple-outputs"></a>Esempio di query: Invio di dati a più output
**Descrizione**: inviare dati a più destinazioni di output da un singolo processo.
Ad esempio, analizzare i dati per un avviso basato su soglie e archiviare tutti gli eventi nell'archiviazione BLOB

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

**Spiegazione**: la clausola INTO indica all’analisi di flusso in quali output scrivere i dati ottenuti con questa istruzione.
La prima è una query pass-through dei dati ricevuti per un output denominato ArchiveOutput.
La seconda query effettua una semplice aggregazione, filtra e invia i risultati a un sistema di avviso downstream.
*Nota*: è inoltre possibile riutilizzare i risultati delle CTE (vale a dire le istruzioni WITH) in più istruzioni di output; questo ha l'ulteriore vantaggio di aprire ad esempio un numero inferiore di lettori nell'origine di input.
Ad esempio, 

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

## <a name="query-example-counting-unique-values"></a>Esempio di query: Conteggio di valori univoci
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


**Spiegazione:** COUNT(DISTINCT Make) restituisce il numero di valori distinct della colonna "Make" all'interno di una finestra temporale.

## <a name="query-example-determine-if-a-value-has-changed"></a>Esempio di query: Determinare la potenziale variazione di un valore
**Descrizione**: esaminare un valore precedente per determinarne la potenziale variazione rispetto al valore corrente. Ad esempio, l'auto passata in precedenza dal casello autostradale è della stessa casa automobilistica dell'auto corrente?

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

**Spiegazione**: usare LAG per esaminare il flusso di input di un evento precedente e ottenere il valore Casa automobilistica. Quindi confrontarlo con il valore Casa automobilistica dell'evento corrente per restituire l'evento di variazione.

## <a name="query-example-find-first-event-in-a-window"></a>Esempio di query: Individuazione del primo evento in una finestra
**Descrizione**: trovare la prima auto in ogni intervallo di 10 minuti?

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

Ridefinire il problema e trovare la prima auto di una particolare casa automobilistica a intervalli di 10 minuti.

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

## <a name="query-example-find-last-event-in-a-window"></a>Esempio di query: Individuazione dell'ultimo evento in una finestra
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

**Spiegazione**: nella query esistono due passaggi: il primo rileva il timestamp più recente in finestre di 10 minuti. Il secondo passaggio unisce i risultati della prima query con il flusso originale per trovare gli eventi corrispondenti ai timestamp più recenti in ogni finestra. 

## <a name="query-example-detect-the-absence-of-events"></a>Esempio di query: Rilevare l'assenza di eventi
**Descrizione**: verificare che un flusso non abbia un valore corrispondente a determinati criteri.
Ad esempio, 2 automobili consecutive della stessa casa automobilistica sono entrate in autostrada entro 90 secondi?

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

**Spiegazione**: usare LAG per esaminare il flusso di input di un evento precedente e ottenere il valore Casa automobilistica. Quindi confrontare tale valore con il valore Casa automobilistica dell'evento corrente e ottenere l'evento di eventuale corrispondenza, quindi usare LAG per ottenere dati sull'auto precedente.

## <a name="query-example-detect-duration-between-events"></a>Esempio di query: Rilevare la durata tra gli eventi
**Descrizione**: trovare la durata di un determinato evento. Ad esempio, dato un clickstream Web determinare i tempi di una funzionalità.

**Input**:  

| Utente | Funzionalità | Evento | Tempo |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Inizia |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Output**:  

| Utente | Funzionalità | Durata |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Soluzione**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Spiegazione**: usare la funzione LAST per recuperare l'ultimo valore di Time quando il tipo di evento presenta il valore 'Start'. Notare che la funzione LAST usa PARTITION BY [user] per indicare che il risultato deve essere calcolato per utente univoco.  La query dispone di una soglia massima di 1 ora per la differenza di tempo tra gli eventi 'Start' e 'Stop', ma è configurabile in base alle esigenze: LIMIT DURATION(hour, 1).

## <a name="query-example-detect-duration-of-a-condition"></a>Esempio di query: Rilevare la durata di una condizione
**Descrizione**: individuare la durata di una condizione.
Ad esempio, si supponga che un bug abbia generato un peso errato per tutte le automobili (oltre 20.000 libbre) e che si desideri calcolare la durata di tale bug.

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

**Spiegazione**: usare LAG per visualizzare il flusso di input per 24 ore e cercare le istanze in cui StartFault e StopFault vengono intervallati per peso < 20.000.

## <a name="query-example-fill-missing-values"></a>Esempio di query: immettere i valori mancanti
**Descrizione**: per il flusso di eventi con i valori mancanti, generare un flusso di eventi con intervalli regolari.
Ad esempio, generare un evento ogni 5 secondi che segnalerà il punto di dati più recente individuato.

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


**Spiegazione**: questa query genera eventi ogni cinque secondi e restituisce l'ultimo evento ricevuto prima. La durata della [finestra di salto](https://msdn.microsoft.com/library/dn835041.aspx "Hopping Window - Azure Stream Analytics") determina fino a quando risale la query per cercare l'evento più recente. In questo esempio, 300 secondi.

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


