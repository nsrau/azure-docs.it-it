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
ms.openlocfilehash: 88c0aea851bcf70206b5f68d7865c487441905f6
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329908"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Rilevamento anomalie in Analisi di flusso di Azure

Analisi di flusso di Azure, disponibile sia nel cloud che in Azure IoT Edge, offre funzionalità di rilevamento anomalie integrate basate su Machine Learning, che possono essere usate per monitorare le due anomalie più frequenti: temporanee e persistenti. Le funzioni **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint** consentono di eseguire il rilevamento anomalie direttamente nel processo di Analisi di flusso.

I modelli di Machine Learning presuppongono una serie temporale con campionamento uniforme. Se la serie temporale non è uniforme, è possibile inserire un passaggio di aggregazione con una finestra a cascata prima di chiamare il rilevamento anomalie.

Le operazioni di machine learning non supportano le tendenze di stagionalità o correlazioni con più varianti in questo momento.

## <a name="model-accuracy-and-performance"></a>Accuratezza e prestazioni del modello

Generalmente l'accuratezza del modello migliora se la finestra temporale scorrevole contiene più dati. I dati nella finestra temporale scorrevole specificata vengono trattati come parte del normale intervallo di valori per quell'intervallo di tempo. Il modello considera solo la cronologia eventi sulla finestra temporale scorrevole per verificare se l'evento corrente è anomalo. Man mano che la finestra temporale scorrevole scorre, i vecchi valori vengono rimossi dal training del modello.

Le funzioni operano stabilendo un valore normale certo basato su quanto rilevato fino a quel momento. Gli outlier vengono identificati mediante un confronto con il valore normale stabilito, entro il livello di attendibilità. Le dimensioni della finestra devono essere basate sul numero minimo di eventi necessari per eseguire il training del modello per il comportamento normale in modo che, quando si verifica un'anomalia, sia in grado di riconoscerla.

Tempo di risposta del modello aumenta con dimensioni cronologia perché è necessario eseguire il confronto con un numero maggiore di eventi passati. Per assicurare prestazioni migliori, è consigliabile includere solo il numero di eventi necessario.

La presenza di interruzioni nella serie temporale può dipendere dal fatto che il modello non riceve eventi in determinati periodi di tempo. Questa situazione viene gestita da Stream Analitica usando imputation logica. Le dimensioni della cronologia, nonché la durata, per la stessa finestra temporale scorrevole vengono usate per calcolare la frequenza media con cui si prevede che arrivino gli eventi.

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

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Rilevamento delle anomalie con machine learning in Azure Stream Analitica

Il video seguente illustra come rilevare un'anomalia in tempo reale usando funzioni di machine learning in Azure Stream Analitica. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

