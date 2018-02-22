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
ms.date: 02/12/2018
ms.author: dubansal
ms.openlocfilehash: d8762ea608afed707d41a3c0a1a8725457a0e4dc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Rilevamento delle anomalie in Analisi di flusso di Azure

> [!IMPORTANT]
> Questa funzionalità è in anteprima. Usarla per i carichi di lavoro di produzione non è consigliabile.

L'operatore **AnomalyDetection** può essere usato per rilevare diversi tipi di anomalie nei flussi di eventi. Ad esempio, una lenta diminuzione della memoria libera a lungo termine può indicare una perdita di memoria oppure il numero di richieste al servizio Web stabili all'interno di un intervallo può aumentare o diminuire drasticamente.  

L'operatore AnomalyDetection rileva tre tipi di anomalie: 

* **Modifica di livello bidirezionale**: aumento o riduzione prolungata del livello dei valori, sia verso l'alto che verso il basso. Questo valore è diverso da picchi e flessioni, che sono modifiche istantanee o di breve durata.  

* **Tendenza positiva lenta**: aumento lento della tendenza nel tempo.  

* **Tendenza negativa lenta**: diminuzione lenta della tendenza nel tempo.  

Quando si usa l'operatore AnomalyDetection è necessario specificare la clausola **Limit Duration**. Questa clausola specifica l'intervallo di tempo (la parte di cronologia considerata a partire dall'evento corrente) da considerare durante il rilevamento delle anomalie. Questo operatore può facoltativamente essere limitato ai soli eventi che corrispondono a una determinata proprietà o condizione, usando la clausola  **When** . L'operatore può anche elaborare facoltativamente gruppi di eventi separatamente in base alla chiave specificata nella clausola  **Partition by** . Il training e la stima avvengono in modo indipendente in ogni partizione. 

## <a name="syntax-for-anomalydetection-operator"></a>Sintassi dell'operatore AnomalyDetection

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Esempio di utilizzo**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argomenti

* **scalar_expression** - Espressione scalare sulla quale viene eseguito il rilevamento delle anomalie. I valori consentiti per questo parametro includono tipi di dati float o bigint che restituiscono un singolo valore (scalare). L'espressione con caratteri jolly **\*** non è consentita. Scalar expression non può contenere altre funzioni analitiche o esterne. 

* **partition_by** - La clausola `PARTITION BY <partition key>` divide l'apprendimento e il training tra partizioni separate. In altri termini, si userà un modello separato per ogni valore di `<partition key>` e solo gli eventi con tale valore verranno usati per l'apprendimento e il training nel modello. Ad esempio, la query seguente esegue il training e l'assegnazione di un punteggio per una sola lettura rispetto ad altre letture dello stesso sensore:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **limit_duration** `DURATION(<unit>, <length>)` - Questa clausola specifica l'intervallo di tempo in base a cui controllare a ritroso nella cronologia rispetto all'evento corrente durante il rilevamento delle anomalie. Per una descrizione dettagliata delle unità supportate e delle rispettive abbreviazioni, vedere [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics). 

* **when** - Questa clausola specifica una condizione booleana per gli eventi considerati nel calcolo di rilevamento anomalie.

### <a name="return-types"></a>Tipi restituiti

L'operatore AnomalyDetection restituisce un record che contiene tutti i tre punteggi come output. Le proprietà associate ai diversi tipi di rilevatori di anomalie sono:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Per estrarre i singoli valori dal record, usare la funzione **GetRecordPropertyValue**. Ad esempio: 

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Un'anomalia di un determinato tipo viene rilevata quando uno dei punteggi di anomalia supera una soglia. La soglia può essere qualsiasi numero a virgola mobile >= 0. La soglia è un compromesso tra sensibilità e attendibilità. Ad esempio, una soglia più bassa renderebbe il rilevamento più sensibile alle modifiche, generando più avvisi, mentre una soglia più alta potrebbe rendere il rilevamento meno sensibile e più attendibile, nascondendo tuttavia alcune anomalie. L'esatto valore di soglia da usare dipende dallo scenario. Non esiste un limite superiore, ma l'intervallo consigliato è compreso tra 3,25 e 5. 

## <a name="anomaly-detection-algorithm"></a>Algoritmo di rilevamento delle anomalie

* L'operatore AnomalyDetection usa un approccio di **apprendimento supervisionato** in cui non si presuppone alcun tipo di distribuzione negli eventi. In generale, in ogni momento vengono mantenuti in parallelo due modelli. Uno viene usato per l'assegnazione dei punteggi, mentre sull'altro viene eseguito il training in background. Il training dei modelli di rilevamento delle anomalie viene eseguito usando dati del flusso corrente anziché tramite un meccanismo fuori banda. La quantità di dati usati per il training dipende dalla dimensione della finestra (d) specificata dall'utente nel parametro Limit Duration. Ogni modello viene sottoposto a training per una durata degli eventi compresa tra d e 2d. Per ottenere risultati ottimali è consigliabile la presenza di almeno 50 eventi in ogni finestra. 

* L'operatore AnomalyDetection usa la **semantica delle finestre temporali scorrevoli** per il training dei modelli e l'assegnazione del punteggio agli eventi. Questo significa che per ogni evento viene eseguita la valutazione delle anomalie e viene restituito un punteggio. Il punteggio indica il livello di attendibilità dell'anomalia. 

* L'operatore AnomalyDetection offre una **garanzia di ripetibilità** che lo stesso input produca sempre lo stesso punteggio, indipendentemente dall'ora di inizio dell'output del processo. L'ora di inizio dell'output del processo rappresenta l'ora in cui il processo genera il primo evento di output. Viene impostata dall'utente sull'ora corrente, un valore personalizzato o l'ora dell'ultimo output (se il processo ha prodotto un output in precedenza). 

### <a name="training-the-models"></a>Training dei modelli 

Col passare del tempo, i modelli vengono sottoposti a training con dati diversi. Per comprendere i punteggi, è utile comprendere il meccanismo sottostante mediante il quale viene eseguito il training dei modelli. Nell'esempio, **t<sub>0</sub>** e l'**ora di inizio dell'output del processo** e **d** è la **dimensione della finestra** fornita dal parametro Limit Duration. Si supponga che il tempo sia diviso in **hop di dimensioni d**, a partire da `01/01/0001 00:00:00`. Per eseguire il training del modello e assegnare il punteggio agli eventi vengono usati i passaggi seguenti:

* Quando un processo si avvia, legge i dati a partire dall'ora t<sub>0</sub> – 2d.  
* Quando il tempo raggiunge l'hop successivo, un nuovo modello M1 viene creato e sottoposto a training.  
* Quando il tempo avanza di un altro hop, viene creato e sottoposto a training un nuovo modello M2.  
* Quando il tempo raggiunge t<sub>0</sub>, M1 viene reso attivo e inizia la restituzione del relativo punteggio.  
* Nell'hop successivo avvengono contemporaneamente tre cose:  

  * M1 non è più necessario e viene rimosso.  
  * M2 è stato sottoposto a training sufficiente e quindi viene usato per l'assegnazione del punteggio.  
  * Viene creato un nuovo modello M3 e ne inizia il training in background.  

* Per ogni hop si ripete questo ciclo, in cui il modello attivo viene rimosso, si passa al modello parallelo e viene avviato il training in background di un terzo modello. 

Riportati in un diagramma, i passaggi hanno l'aspetto seguente: 

![Training dei modelli](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Modello** | **Ora di inizio training** | **Inizio dell'uso del relativo punteggio** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* Il training del modello M1 inizia alle 11:20, ovvero l'hop successivo dopo l'inizio della lettura da parte del processo, alle 11:13. Il primo output viene generato da M1 alle 11:33, dopo il training con 13 minuti di dati. 

* Alle 11:30 inizia il training anche di un nuovo modello M2, ma il suo punteggio non viene utilizzato fino alle 11:40, ovvero dopo un training con 10 minuti di dati. 

* M3 segue lo stesso schema di M2. 

### <a name="scoring-with-the-models"></a>Assegnazione dei punteggi con i modelli 

A livello di Machine Learning, l'algoritmo di rilevamento delle anomalie calcola un valore di stranezza per ogni evento in ingresso confrontandolo con gli eventi in una finestra della cronologia. Il calcolo della stranezza è diverso per ogni tipo di anomalia.  

Esaminiamo il calcolo della stranezza in dettaglio, presupponendo l'esistenza di un set di finestre cronologiche con eventi: 

1. **Modifica di livello bidirezionale:** in base alla finestra della cronologia, viene calcolato un intervallo di funzionamento normale come [10° percentile, 90° percentile], ovvero il valore del 10° percentile come limite inferiore e il valore del 90° percentile come limite superiore. Il valore di stranezza per l'evento corrente viene calcolato come:  

   - 0 se event_value è compreso nell'intervallo di funzionamento normale  
   - event_value/90° percentile se event_value > 90° percentile  
   - 10° percentile/event_value se event_value < 10° percentile  

2. **Tendenza positiva lenta:** viene calcolata una linea di tendenza dei valori di evento nella finestra della cronologia e viene cercata una tendenza positiva all'interno di tale linea. Il valore di stranezza viene calcolato come:  

   - Coefficiente angolare, se la pendenza è positiva  
   - 0 in caso contrario 

1. **Tendenza negativa lenta:** viene calcolata una linea di tendenza dei valori di evento nella finestra della cronologia e viene cercata una tendenza negativa all'interno di tale linea. Il valore di stranezza viene calcolato come: 

   - Coefficiente angolare, se la pendenza è negativa  
   - 0 in caso contrario  

Dopo il calcolo del valore di stranezza per l'evento in ingresso, viene calcolato un valore martingala in base al valore di stranezza. Vedere il [blog su Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) per informazioni dettagliate sul modo in cui viene calcolato il valore martingala. Questo valore martingala viene restituito come punteggio dell'anomalia. Il valore martingala aumenta lentamente in risposta ai valori anomali, il che consente alla funzionalità di rilevamento di restare insensibile alle modifiche sporadiche e riduce i falsi allarmi. Ha anche una proprietà utile: 

Probabilità [esiste t tale che M<sub>t</sub> > λ ] < 1/λ, dove M<sub>t</sub> è il valore martingala nell'istante t e λ è un valore reale. Ad esempio, se viene generato un avviso quando M<sub>t</sub>> 100, la probabilità di falsi positivi è minore di 1/100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Linee guida per l'uso del rilevamento delle modifiche di livello bidirezionali 

Il rilevamento delle modifiche di livello bidirezionali può essere usato in scenari del tipo interruzione dell'alimentazione e ripristino, traffico nelle ore di punta e così via. Tuttavia, funziona in modo tale che, quando viene eseguito il training di un modello con determinati dati, un'altra modifica di livello è anomala se e soltanto se il nuovo valore è più alto del limite superiore precedente (caso di modifica di livello verso l'alto) o più basso del limite inferiore precedente (caso di modifica di livello verso il basso). Di conseguenza, un modello non dovrebbe visualizzare valori dei dati nell'intervallo del nuovo livello (verso l'alto o verso il basso) nella relativa finestra di training perché questi vengano considerati anomali. 

Quando si usa questa funzionalità di rilevamento, è necessario considerare gli aspetti seguenti: 

1. Quando la serie temporale mostra improvvisamente un aumento o una diminuzione del valore, l'operatore AnomalyDetection lo rileva. Per il rilevamento del ritorno alla normalità, tuttavia, occorre una maggiore pianificazione. Se prima dell'anomalia una serie temporale era in stato stazionario, lo scopo si può raggiungere impostando la finestra di rilevamento dell'operatore AnomalyDetection ad almeno metà della lunghezza dell'anomalia. Questo scenario presuppone che sia possibile stimare la durata minima dell'anomalia in anticipo e l'intervallo di tempo contenga eventi sufficienti per il training del modello (almeno 50 eventi). 

   Lo scenario viene illustrato nelle figure 1 e 2 di seguito usando una modifica del limite superiore (la stessa logica si applica a una modifica del limite inferiore). In entrambe le figure le forme d'onda rappresentano una modifica di livello anomala. Le linee verticali di colore arancione indicano i confini degli hop e le dimensioni hop sono uguali alle dimensioni della finestra di rilevamento specificata nell'operatore AnomalyDetection. Le linee verdi indicano le dimensioni della finestra di training. Nella figura 1, le dimensioni hop sono uguali alla durata dell'anomalia. Nella figura 2, le dimensioni hop corrispondono alla metà della durata dell'anomalia. In tutti i casi viene rilevata una modifica verso l'alto, in quanto il modello usato per l'assegnazione del punteggio è stato sottoposto a training con i dati normali. In base al modo in cui funziona il rilevamento delle modifiche di livello bidirezionali, tuttavia, è necessario escludere i valori normali dalla finestra di training usata per il modello che assegna il punteggio al ritorno alla normalità. Nella figura 1, il training del modello di assegnazione dei punteggi include alcuni eventi normali, pertanto il ritorno alla normalità non può essere rilevato. Nella figura 2 il training include solo la parte anomala, il che assicura il rilevamento del ritorno alla normalità. Per lo stesso motivo, qualsiasi valore inferiore alla metà funzionerà, mentre qualsiasi valore più grande finirà per includere alcuni eventi normali. 

   ![Rilevamento anomalie con dimensioni della finestra uguali alla durata dell'anomalia](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![Rilevamento anomalie con dimensioni della finestra uguali a metà della durata dell'anomalia](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. Nei casi in cui non è possibile prevedere la durata dell'anomalia, questa funzionalità di rilevamento opera secondo il criterio del massimo sforzo. Tuttavia, la scelta di una finestra temporale più ristretta limita i dati di training, aumentando la probabilità di rilevare il ritorno alla normalità. 

3. Nello scenario seguente, l'anomalia più lunga non viene rilevata perché la finestra di training include già un'anomalia dello stesso valore elevato. 

   ![Anomalie con le stesse dimensioni](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Query di esempio per il rilevamento di anomalie 

È possibile usare la query seguente per generare un avviso se viene rilevata un'anomalia.
Quando il flusso di input non è uniforme, il passaggio di aggregazione può aiutare a trasformarlo in una serie temporale uniforme. L'esempio usa AVG, ma il tipo specifico di aggregazione dipende dallo scenario utente. Inoltre, quando una serie temporale include spazi maggiori della finestra di aggregazione, nessun evento nella serie temporale attiva il rilevamento delle anomalie, in base alla semantica delle finestre temporali scorrevoli. Di conseguenza, l'ipotesi di uniformità viene annullata all'arrivo dell'evento successivo. In questi casi, occorre colmare i vuoti nella serie temporale. Un possibile approccio consiste nel considerare l'ultimo evento in ogni finestra di salto, come mostrato di seguito.

```sql
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
```

## <a name="performance-guidance"></a>Linee guida sulle prestazioni

* Usare sei unità di streaming per processo. 
* Inviare eventi ad almeno un secondo uno dall'altro.
* Una query non partizionata che usa l'operatore AnomalyDetection può produrre risultati con una latenza di calcolo media di circa 25 ms.
* La latenza cui è soggetta una query partizionata varia leggermente con il numero di partizioni, perché il numero di calcoli è maggiore. Tuttavia, la latenza è circa la stessa del caso non partizionato per un numero totale di eventi simile su tutte le partizioni.
* Durante la lettura di dati non in tempo reale, viene rapidamente inserita una quantità elevata di dati. L'elaborazione di questi dati è attualmente più lenta. Si è riscontrato che in questi scenari la latenza aumenta in modo lineare in base al numero di punti dati nella finestra anziché alle dimensioni della finestra o all'intervallo di eventi stesso. Per ridurre la latenza nei casi non in tempo reale, provare a usare una finestra di dimensioni minori. In alternativa, provare ad avviare il processo dall'ora corrente. Alcuni esempi di latenza in una query non partizionata: 
    - 60 punti dati nella finestra di rilevamento possono produrre una latenza di 10 secondi con una velocità effettiva di 3 Mbps. 
    - A 600 punti dati, la latenza può raggiungere circa 80 secondi con una velocità effettiva di 0,4 Mbps.

## <a name="limitations-of-the-anomalydetection-operator"></a>Limitazioni dell'operatore AnomalyDetection 

* Attualmente questo operatore non supporta il rilevamento di picchi e flessioni. I picchi e le flessioni sono modifiche di breve durata o spontanee nella serie temporale. 

* Attualmente questo operatore non gestisce gli schemi stagionali. Gli schemi stagionali sono schemi ripetuti nei dati, ad esempio un comportamento diverso del traffico Web nei fine settimana o trend di acquisto diversi durante il Black Friday, che non rappresentano anomalie bensì un modello di comportamento previsto. 

* L'operatore prevede che la serie temporale di input sia uniforme. Un flusso di eventi può essere reso uniforme tramite l'aggregazione in una finestra a cascata o di salto. In scenari in cui lo spazio tra gli eventi è sempre minore della finestra di aggregazione una finestra a cascata è sufficiente per rendere uniforme la serie temporale. Quando gli spazi possono essere maggiori, vengono riempiti ripetendo l'ultimo valore tramite una finestra di salto. 

## <a name="references"></a>Riferimenti

* [Anomaly detection – Using machine learning to detect abnormalities in time series data](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) (Rilevamento delle anomalie - Uso di Machine Learning per rilevare le anomalie nei dati delle serie temporali)
* [API di rilevamento delle anomalie di Machine Learning](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Rilevamento delle anomalie delle serie temporali](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

