---
title: Rilevamento anomalie in Analisi di flusso di Azure
description: Questo articolo descrive come rilevare le anomalie usando Analisi di flusso di Azure e Azure Machine Learning insieme.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e2fd226f1c605821f0fd595832b2cbe26d994fb4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612346"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Rilevamento anomalie in Analisi di flusso di Azure

Analisi di flusso di Azure, disponibile sia nel cloud che in Azure IoT Edge, offre funzionalità di rilevamento anomalie integrate basate su Machine Learning, che possono essere usate per monitorare le due anomalie più frequenti: temporanee e persistenti. Le funzioni **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint** consentono di eseguire il rilevamento anomalie direttamente nel processo di Analisi di flusso.

I modelli di Machine Learning presuppongono una serie temporale con campionamento uniforme. Se la serie temporale non è uniforme, è possibile inserire un passaggio di aggregazione con una finestra a cascata prima di chiamare il rilevamento anomalie.

Le operazioni di machine learning non supportano le tendenze di stagionalità o correlazioni con più varianti in questo momento.

## <a name="model-behavior"></a>Comportamento del modello

Generalmente l'accuratezza del modello migliora se la finestra temporale scorrevole contiene più dati. I dati nella finestra temporale scorrevole specificata vengono trattati come parte del normale intervallo di valori per quell'intervallo di tempo. Il modello considera solo la cronologia eventi sulla finestra temporale scorrevole per verificare se l'evento corrente è anomalo. Man mano che la finestra temporale scorrevole scorre, i vecchi valori vengono rimossi dal training del modello.

Le funzioni operano stabilendo un valore normale certo basato su quanto rilevato fino a quel momento. Gli outlier vengono identificati mediante un confronto con il valore normale stabilito, entro il livello di attendibilità. Le dimensioni della finestra devono essere basate sul numero minimo di eventi necessari per eseguire il training del modello per il comportamento normale in modo che, quando si verifica un'anomalia, sia in grado di riconoscerla.

Tempo di risposta del modello aumenta con dimensioni cronologia perché è necessario eseguire il confronto con un numero maggiore di eventi passati. Per assicurare prestazioni migliori, è consigliabile includere solo il numero di eventi necessario.

La presenza di interruzioni nella serie temporale può dipendere dal fatto che il modello non riceve eventi in determinati periodi di tempo. Questa situazione viene gestita da Stream Analitica usando imputation logica. Le dimensioni della cronologia, nonché la durata, per la stessa finestra temporale scorrevole vengono usate per calcolare la frequenza media con cui si prevede che arrivino gli eventi.

Un generatore di anomalie disponibile [qui](https://aka.ms/asaanomalygenerator) può essere utilizzato per feed un Iot Hub con i dati con i modelli di anomalie diversi. Un processo ASA può essere impostato con queste funzioni di rilevamento delle anomalie per leggere da questo Iot Hub e rilevare le anomalie.

## <a name="spike-and-dip"></a>Picchi e flessioni

Le anomalie temporanee in un flusso di eventi di una serie temporale sono note come picchi e flessioni. Questi picchi e flessioni possono essere monitorati usando l'operatore basato su Machine Learning [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Esempio di anomalia con picchi e flessioni](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Se nella stessa finestra temporale scorrevole un secondo picco è inferiore al primo, il punteggio calcolato del picco inferiore non è probabilmente abbastanza significativo rispetto al punteggio del primo picco all'interno del livello di attendibilità specificato. È possibile provare a diminuire il livello di confidenza del modello per rilevare queste anomalie. Se però si inizia a ricevere troppi avvisi, è possibile usare un intervallo di attendibilità più elevato.

La query di esempio seguente presuppone una frequenza di input uniforme di un evento al secondo in una finestra temporale scorrevole di 2 minuti con una cronologia di 120 eventi. L'istruzione SELECT finale estrae e restituisce il punteggio e lo stato dell'anomalia con un livello di attendibilità del 95%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Punto di modifica

Le anomalie persistenti in un flusso di eventi di una serie temporale sono modifiche nella distribuzione dei valori del flusso di eventi, come cambi di livello e tendenze. In Analisi di flusso queste anomalie vengono rilevate mediante l'operatore basato su Machine Learning [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics).

Le modifiche persistenti durano molto più a lungo dei picchi e le flessioni e possono indicare eventi molto gravi. Non sono generalmente visibili a occhio nudo, ma possono essere rilevate con l'operatore **AnomalyDetection_ChangePoint**.

L'immagine seguente è un esempio di cambio di livello:

![Esempio di anomalia con cambio di livello](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

L'immagine seguente è un esempio di cambio di tendenza:

![Esempio di anomalia con cambio di tendenza](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

La query di esempio seguente presuppone una frequenza di input uniforme di un evento al secondo in una finestra temporale scorrevole di 20 minuti con una cronologia di 1200 eventi. L'istruzione SELECT finale estrae e restituisce il punteggio e lo stato dell'anomalia con un livello di attendibilità dell'80%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Caratteristiche delle prestazioni

Le prestazioni di questi modelli dipende dalla dimensione della cronologia della durata della finestra, il caricamento di eventi, e se viene utilizzato il partizionamento a livello di funzione. In questa sezione vengono illustrate queste configurazioni e fornisce esempi su come supportare le frequenze di inserimento di 1 KB, pari a 5 KB e 10 k rpm eventi al secondo.

* **Dimensioni della cronologia** -questi modelli di eseguono in modo lineare con **dimensioni cronologia**. Più a lungo la dimensione della cronologia, la forma estesa i modelli accettano per assegnare un punteggio un nuovo evento. Questo avviene perché i modelli di confrontare il nuovo evento con ognuno degli ultimi eventi nel buffer.
* **Durata della finestra** - il **durata intervallo di** deve riflettere il tempo necessario per ricevere tutti gli eventi come specificato dalle dimensioni della cronologia. Senza che molti eventi nella finestra di, Azure Stream Analitica sarebbe attribuire valori mancanti. Di conseguenza, il consumo della CPU è una funzione delle dimensioni della cronologia.
* **Caricamento di eventi** : maggiore di **caricamento di eventi**, più lavoro che viene eseguita dai modelli, che influisce sul consumo di CPU. Il processo può essere aumentato, rendendo perfettamente parallela, presupponendo che è opportuno per la logica di business da utilizzare le partizioni di input.
* **Partizionamento a livello di funzione** - **partizionamento a livello di funzione** viene eseguita usando ```PARTITION BY``` entro la chiamata di funzione di rilevamento delle anomalie. Questo tipo di partizionamento aggiunge un sovraccarico, come lo stato deve essere mantenuto per più modelli nello stesso momento. Partizionamento a livello di funzione viene usato in scenari come il partizionamento a livello di dispositivo.

### <a name="relationship"></a>Relazione
Le dimensioni della cronologia, durata della finestra e carico totale di eventi sono correlati nel modo seguente:

windowDuration (in ms) = 1000 * historySize / (totale eventi al secondo / numero di partizioni di Input)

Quando la funzione di partizionamento da ID dispositivo, aggiungere "PARTITION BY deviceId" alla chiamata di funzione di rilevamento delle anomalie.

### <a name="observations"></a>Osservazioni
Nella tabella seguente include le osservazioni di velocità effettiva per un singolo nodo (6 unità di ricerca) per il caso senza partizionamento:

| Dimensioni della cronologia (eventi) | Durata finestra (ms) | Eventi di input totali / sec |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6000 | 10,910 | 1,100 |

Nella tabella seguente include le osservazioni di velocità effettiva per un singolo nodo (6 unità di ricerca) per il case partizionato:

| Dimensioni della cronologia (eventi) | Durata finestra (ms) | Eventi di input totali / sec | Numero di dispositivi |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6000 | 2,181,819 | <550 | 100 |

Codice di esempio per eseguire le configurazioni sopra non partizionata si trova nella [repository di Streaming a scalabilità](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) degli esempi di Azure. Il codice crea un processo di analitica di flusso con nessuna funzione a livello di partizionamento, che usa Hub eventi come input e output. Viene generato il carico di input tramite client di test. Ogni evento di input è un documento json da 1KB. Gli eventi di simulano un dispositivo IoT l'invio di dati JSON (per i dispositivi K fino a 1). Le dimensioni della cronologia, durata della finestra e carico totale di eventi sono diversi in 2 partizioni di input.

> [!Note]
> Per una stima più accurata, personalizzare gli esempi per adattarlo allo scenario.

### <a name="identifying-bottlenecks"></a>Identificare i colli di bottiglia
Utilizzare il riquadro metriche nel processo di Azure Stream Analitica per identificare i colli di bottiglia nella pipeline. Revisione **eventi di Input/Output** per la velocità effettiva e ["Ritardo della filigrana"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) oppure **eventi backlog sta** per vedere se il processo è stato aggiornato con la frequenza di input. Per le metriche di Hub eventi, cercare **richieste limitate** e modificare di conseguenza le unità di soglia. Per le metriche di Cosmos DB, esaminare **numero massimo di unità richiesta al secondo utilizzate per ogni intervallo di chiavi di partizione** sotto la velocità effettiva per verificare gli intervalli di chiavi di partizione vengono utilizzati in modo uniforme. Per il database SQL di Azure, monitorare **i/o Log** e **CPU**.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Rilevamento delle anomalie con machine learning in Azure Stream Analitica

Il video seguente illustra come rilevare un'anomalia in tempo reale usando funzioni di machine learning in Azure Stream Analitica. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

