---
title: Guida all'utilizzo del rilevamento delle anomalie in Azure (anteprima)| Microsoft Docs
description: Usare l'analisi di flusso e Machine Learning per rilevare le anomalie.
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: dubansal
ms.openlocfilehash: ff8571c6447f32ef9a435f5200803e76f6013ffa
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="using-the-anomalydetection-operator"></a>Uso dell'operatore ANOMALYDETECTION

> [!IMPORTANT]
> Questa funzionalità è in anteprima. Non è consigliabile usarla nell'ambiente di produzione.

L'operatore **ANOMALYDETECTION** può essere usato per rilevare le anomalie nei flussi di eventi.
Ad esempio, una lenta diminuzione della memoria libera a lungo termine può indicare una perdita di memoria oppure il numero di richieste al servizio Web stabili all'interno di un intervallo può aumentare o diminuire drasticamente.

L'operatore controlla i tipi seguenti di anomalie per la durata specificata:

- Modifica di livello bidirezionale
- Tendenza positiva lenta
- Tendenza negativa lenta

L'intervallo di tempo in base al quale controllare a ritroso nella cronologia rispetto all'evento corrente viene limitato usando la clausola **LIMIT DURATION**. **ANOMALYDETECTION** può facoltativamente essere limitato ai soli eventi che corrispondono a una determinata proprietà o condizione usando la clausola **WHEN**.

L'operatore può anche elaborare facoltativamente gruppi di eventi separatamente in base alla chiave specificata nella clausola **PARTITION BY**. Il training e la stima avvengono indipendentemente in ogni partizione.

## <a name="syntax"></a>Sintassi

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>Esempio di utilizzo

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`|


## <a name="arguments"></a>Argomenti

- **scalar_expression**

  Espressione scalare sulla quale viene eseguito il rilevamento delle anomalie. Si tratta di un'espressione di tipo float o bigint che restituisce un singolo valore (scalare). L'espressione con caratteri jolly **\*** non è consentita. **scalar_expression** non può contenere altre funzioni analitiche o esterne.

- **OVER ( [ partition_by_clause ] limit_duration_clause [when_clause])**

- **partition_by_clause** 

  La clausola `PARTITION BY <partition key>` divide l'apprendimento e il training tra partizioni separate. In altri termini, si userà un modello separato per ogni valore di `<partition key>` e solo gli eventi con tale valore verranno usati per l'apprendimento e il training nel modello. Ad esempio,

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  eseguirà il training e l'assegnazione di un punteggio per una sola lettura rispetto ad altre letture dello stesso sensore.

- **clausola limit_duration** DURATION(\<unità\>, \<durata\>)

  Specifica la parte di cronologia considerata a partire dall'evento corrente nel calcolo **ANOMALYDETECTION**. Per una descrizione dettagliata delle unità supportate e delle rispettive abbreviazioni, vedere DATEDIFF.

- **when_clause** 

  Specifica una condizione booleana per gli eventi considerati nel calcolo **ANOMALYDETECTION**.

## <a name="return-types"></a>Tipi restituiti

La funzione restituisce un record che contiene tutti i tre punteggi come output. Le proprietà associate ai diversi tipi di rilevatori di anomalie si chiamano:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Per estrarre i singoli valori dal record, usare la funzione **GetRecordPropertyValue**. Ad esempio: 

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 


Un'anomalia di un determinato tipo viene rilevata quando uno di questi punteggi di anomalia supera una soglia. La soglia può essere qualsiasi numero a virgola mobile \>= 0. La soglia è un compromesso tra sensibilità e attendibilità. Ad esempio, una soglia più bassa renderebbe il rilevamento più sensibile alle modifiche, generando più avvisi, mentre una soglia più alta potrebbe rendere il rilevamento meno sensibile e più attendibile, nascondendo tuttavia alcune anomalie. L'esatto valore di soglia da usare dipende dallo scenario. Non esiste un limite superiore, ma l'intervallo consigliato è compreso tra 3,25 e 5.

## <a name="algorithm"></a>Algoritmo

**ANOMALYDETECTION** usa la semantica delle finestre temporali scorrevoli. Questo significa che il calcolo viene eseguito per ogni evento introdotto nella funzione e che per l'evento viene prodotto un punteggio. Il calcolo è basato sui processo martingala scambiabili, che operano controllando se è stata modificata la distribuzione dei valori di evento. In questo caso, viene rilevata una possibile anomalia. Il punteggio restituito indica il livello di attendibilità dell'anomalia. Come ottimizzazione interna, **ANOMALYDETECTION** calcola il punteggio di anomalia di un evento in base a un valore compreso tra *d* e *2d* per gli eventi, in cui *d* è la dimensione della finestra di rilevamento specificata.

**ANOMALYDETECTION** prevede che la serie temporale di input sia uniforme. Un flusso di eventi può essere reso uniforme tramite l'aggregazione in una finestra a cascata o di salto. In scenari in cui lo spazio tra gli eventi è sempre minore della finestra di aggregazione una finestra a cascata è sufficiente per rendere uniforme la serie temporale. Quando gli spazi possono essere maggiori, vengono riempiti ripetendo l'ultimo valore tramite una finestra di salto. Entrambi questi scenari possono essere gestiti dall'esempio seguente.

## <a name="performance-guidance"></a>Linee guida sulle prestazioni

- Usare 6 unità di archiviazione per i processi. 
- Inviare eventi ad almeno un secondo uno dall'altro.
- Una query non partizionata con la funzione **ANOMALYDETECTION** può produrre risultati con una latenza di calcolo media di circa 25 ms.
- La latenza cui è soggetta una query partizionata varia leggermente con il numero di partizioni, perché il numero di calcoli è maggiore. Tuttavia, la latenza è circa la stessa del caso non partizionato per un numero totale di eventi simile su tutte le partizioni.
- Durante la lettura di dati non in tempo reale, viene rapidamente inserita una quantità elevata di dati. L'elaborazione di questi dati è attualmente molto più lenta. Si è riscontrato che in questi scenari la latenza aumenta in modo lineare in base al numero di punti dati nella finestra anziché alle dimensioni della finestra o all'intervallo di eventi stesso. Per ridurre la latenza nei casi non in tempo reale, provare a usare una finestra di dimensioni minori. In alternativa, provare ad avviare il processo dall'ora corrente. Alcuni esempi di latenza in una query non partizionata: 
    - 60 punti dati nella finestra di rilevamento possono produrre una latenza di 10 secondi con una velocità effettiva di 3 Mbps. 
    - A 600 punti dati, la latenza può raggiungere circa 80 secondi con una velocità effettiva di 0,4 Mbps.

## <a name="example"></a>Esempio

È possibile usare la query seguente per generare un avviso se viene rilevata un'anomalia.
Quando il flusso di input non è uniforme, il passaggio di aggregazione può aiutare a trasformarlo in una serie temporale uniforme. L'esempio usa **AVG**, ma il tipo specifico di aggregazione dipende dallo scenario utente. Inoltre, quando una serie temporale include spazi maggiori della finestra di aggregazione, nessun evento nella serie temporale attiverà il rilevamento delle anomalie, in base alla semantica delle finestre temporali scorrevoli. Di conseguenza, l'ipotesi di uniformità verrà annullata all'arrivo dell'evento successivo. In questi casi, è necessario individuare un modo per riempire gli spazi vuoti nella serie temporale. Un possibile approccio consiste nel considerare l'ultimo evento in ogni finestra di salto, come mostrato di seguito.

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25

## <a name="references"></a>Riferimenti

* [Anomaly Detection – Using Machine Learning to Detect Abnormalities in Time Series Data](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) (Rilevamento delle anomalie - Uso di Machine Learning per rilevare le anomalie nei dati delle serie temporali)
* [API di rilevamento delle anomalie di Machine Learning](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Rilevamento delle anomalie delle serie temporali](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

