---
title: Rilevamento anomalie in Analisi di flusso di Azure
description: Questo articolo descrive come rilevare le anomalie usando Analisi di flusso di Azure e Azure Machine Learning insieme.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525533"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Rilevamento anomalie in Analisi di flusso di Azure

Analisi di flusso di Azure, disponibile sia nel cloud che in Azure IoT Edge, offre funzionalità di rilevamento anomalie integrate basate su Machine Learning, che possono essere usate per monitorare le due anomalie più frequenti: temporanee e persistenti. Le funzioni **AnomalyDetection_SpikeAndDip** e **AnomalyDetection_ChangePoint** consentono di eseguire il rilevamento anomalie direttamente nel processo di Analisi di flusso.

I modelli di Machine Learning presuppongono una serie temporale con campionamento uniforme. Se la serie temporale non è uniforme, è possibile inserire un passaggio di aggregazione con una finestra a cascata prima di chiamare il rilevamento anomalie.

Le operazioni di apprendimento automatico non supportano le tendenze di stagionalità o le correlazioni multivariate in questo momento.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Rilevamento delle anomalie tramite l'apprendimento automatico in Analisi di flusso di AzureAnomaly detection using machine learning in Azure Stream Analytics

Il video seguente illustra come rilevare un'anomalia in tempo reale usando le funzioni di apprendimento automatico in Analisi di flusso di Azure.The following video demonstrates how to detect an anomaly in real time using machine learning functions in Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Comportamento del modello

Generalmente l'accuratezza del modello migliora se la finestra temporale scorrevole contiene più dati. I dati nella finestra temporale scorrevole specificata vengono trattati come parte del normale intervallo di valori per quell'intervallo di tempo. Il modello considera solo la cronologia eventi sulla finestra temporale scorrevole per verificare se l'evento corrente è anomalo. Man mano che la finestra temporale scorrevole scorre, i vecchi valori vengono rimossi dal training del modello.

Le funzioni operano stabilendo un valore normale certo basato su quanto rilevato fino a quel momento. Gli outlier vengono identificati mediante un confronto con il valore normale stabilito, entro il livello di attendibilità. Le dimensioni della finestra devono essere basate sul numero minimo di eventi necessari per eseguire il training del modello per il comportamento normale in modo che, quando si verifica un'anomalia, sia in grado di riconoscerla.

Il tempo di risposta del modello aumenta con le dimensioni della cronologia perché deve essere confrontato con un numero maggiore di eventi passati. Per assicurare prestazioni migliori, è consigliabile includere solo il numero di eventi necessario.

La presenza di interruzioni nella serie temporale può dipendere dal fatto che il modello non riceve eventi in determinati periodi di tempo. Questa situazione viene gestita da Analisi di flusso usando la logica di imputazione. Le dimensioni della cronologia, nonché la durata, per la stessa finestra temporale scorrevole vengono usate per calcolare la frequenza media con cui si prevede che arrivino gli eventi.

Un generatore di anomalie disponibile [qui](https://aka.ms/asaanomalygenerator) può essere utilizzato per alimentare un hub Iot con dati con diversi modelli di anomalia. Un lavoro ASA può essere impostato con queste funzioni di rilevamento delle anomalie per leggere da questo Hub Iot e rilevare le anomalie.

## <a name="spike-and-dip"></a>Picchi e flessioni

Le anomalie temporanee in un flusso di eventi di una serie temporale sono note come picchi e flessioni. Questi picchi e flessioni possono essere monitorati usando l'operatore basato su Machine Learning [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Esempio di anomalia con picchi e flessioni](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Se nella stessa finestra temporale scorrevole un secondo picco è inferiore al primo, il punteggio calcolato del picco inferiore non è probabilmente abbastanza significativo rispetto al punteggio del primo picco all'interno del livello di attendibilità specificato. Si può provare a diminuire il livello di confidenza del modello per rilevare tali anomalie. Se però si inizia a ricevere troppi avvisi, è possibile usare un intervallo di attendibilità più elevato.

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

Le prestazioni di questi modelli dipendono dalle dimensioni della cronologia, dalla durata della finestra, dal carico di eventi e dall'utilizzo del partizionamento a livello di funzione. In questa sezione vengono illustrate queste configurazioni e vengono forniti esempi su come supportare i tassi di inserimento di eventi 1K, 5K e 10K al secondo.

* **Dimensione della cronologia** - Questi modelli vengono eseguiti in modo lineare con **le dimensioni della cronologia.** Più lunga è la dimensione della storia, più a lungo i modelli richiedono per segnare un nuovo evento. Questo perché i modelli confrontano il nuovo evento con ciascuno degli eventi passati nel buffer della cronologia.
* **Durata della finestra:** la **durata della finestra** deve riflettere il tempo necessario per ricevere il tempo necessario per ricevere il tempo specificato dalla dimensione della cronologia. Senza numerosi eventi nella finestra, Analisi di flusso di Azure imputerebbe i valori mancanti. Di conseguenza, l'utilizzo della CPU è una funzione della dimensione della cronologia.
* **Caricamento evento:** maggiore è il carico di **eventi,** maggiore sarà il lavoro eseguito dai modelli, con un impatto sull'utilizzo della CPU. Il processo può essere scalato orizzontalmente rendendolo in modo imbarazzante parallelo, presupponendo che la logica di business utilizzi più partizioni di input.
* **Partizionamento** - a livello di funzione Il ```PARTITION BY``` **partizionamento** a livello di funzione viene eseguito utilizzando la chiamata alla funzione di rilevamento delle anomalie. Questo tipo di partizionamento aggiunge un sovraccarico, in quanto lo stato deve essere mantenuto per più modelli contemporaneamente. Il partizionamento a livello di funzione viene usato in scenari come il partizionamento a livello di dispositivo.

### <a name="relationship"></a>Relazione
Le dimensioni della cronologia, la durata della finestra e il caricamento totale dell'evento sono correlati nel modo seguente:The history size, window duration, and total event load are related in the following way:

windowDuration (in ms) - 1000 - historySize / (Total Input Events Per Sec / Input Partition Count)

Quando si partiziona la funzione in base a deviceId, aggiungere "PARTITION BY deviceId" alla chiamata di funzione di rilevamento anomalie.

### <a name="observations"></a>Osservazioni
La tabella seguente include le osservazioni della velocità effettiva per un singolo nodo (6 SU) per il caso non partizionato:The following table includes the throughput observations for a single node (6 SU) for the non-partitioned case:

| Dimensione della cronologia (eventi) | Durata della finestra (ms) | Totale eventi di input al secondo |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6000 | 10,910 | 1.100 |

La tabella seguente include le osservazioni della velocità effettiva per un singolo nodo (6 SU) per il caso partizionato:The following table includes the throughput observations for a single node (6 SU) for the partitioned case:

| Dimensione della cronologia (eventi) | Durata della finestra (ms) | Totale eventi di input al secondo | Conteggio dispositivi |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1.100 | 10 |
| 600 | 10,910 | 1.100 | 10 |
| 6000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6000 | 2,181,819 | <550 | 100 |

Il codice di esempio per eseguire le configurazioni non partizionate precedente si trova nel repository Streaming At Scale di Azure Samples.Sample code to run the non-partitioned configurations above is located in the [Streaming At Scale repo](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) of Azure Samples. Il codice crea un processo di analisi di flusso senza partizionamento a livello di funzione, che usa l'hub eventi come input e output. Il carico di input viene generato tramite client di test. Ogni evento di input è un documento json da 1 KB. Gli eventi simulano un dispositivo IoT che invia dati JSON (per un massimo di 1K dispositivi). Le dimensioni della cronologia, la durata della finestra e il caricamento totale dell'evento sono variati in 2 partizioni di input.

> [!Note]
> Per una stima più accurata, personalizzare gli esempi in base allo scenario.

### <a name="identifying-bottlenecks"></a>Identificazione dei colli di bottiglia
Usare il riquadro Metriche nel processo di Analisi di flusso di Azure per identificare i colli di bottiglia nella pipeline. Esaminare gli eventi di **input/output** per la velocità effettiva e ["Ritardo filigrana"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) o **Eventi backlogged** per verificare se il processo tiene il passo con la frequenza di input. Per le metriche dell'Hub eventi, cercare **Richieste limitate** e regolare di conseguenza le unità di soglia. Per le metriche cosmos DB, esaminare **Max consumato RU/s per intervallo** di chiavi di partizione in Velocità effettiva per assicurarsi che gli intervalli di chiavi di partizione siano utilizzati in modo uniforme. Per il database SQL di Azure, monitorare **L'I/O del log** e **la CPU**.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi di flusso di AzureGet started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Guida di riferimento al linguaggio di query di Analisi di flusso di AzureAzure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

