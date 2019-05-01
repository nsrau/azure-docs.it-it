---
title: Machine learning funzionalità in Esplora dati di Azure
description: Usare il clustering per l'analisi causa radice in Esplora dati di Azure machine learning.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 2358cb2ea411a0077f34798183da30bd32ae067b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925135"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Machine learning funzionalità in Esplora dati di Azure

Esplora dati Azure, una piattaforma analitica di Big Data, viene usato per monitorare l'integrità dei servizi, QoS o dispositivi che non funziona correttamente per l'utilizzo predefinito di comportamenti anomali [rilevamento di anomalie e le previsioni](/azure/data-explorer/anomaly-detection) funzioni. Quando viene rilevato un modello anomalo, viene eseguita un'analisi (della causa radice) per mitigare o correggere le anomalie.

Il processo di diagnosi è complesse e lunghe e done da esperti del dominio. Il processo prevede il recupero e sull'aggiunta di altri dati da origini diverse per l'intervallo di tempo stesso, alla ricerca di modifiche alla distribuzione dei valori in più dimensioni, creazione di grafici di altre variabili, e altre tecniche basate su informazioni di dominio e intuizione. Poiché questi scenari di diagnosi sono comuni in Esplora dati di Azure, i plug-in di machine learning sono disponibili per rendere più semplice la fase di diagnosi e abbreviare la durata della finestra di RCA.

Esplora i dati di Azure ha tre plug-in Machine Learning: [ `autocluster` ](/azure/kusto/query/autoclusterplugin), [ `basket` ](/azure/kusto/query/basketplugin), e [ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin). Tutti i plug-in implementano gli algoritmi di clustering. Il `autocluster` e `basket` plug-in cluster a un singolo set di record e `diffpatterns` plug-in cluster le differenze tra due set di record.

## <a name="clustering-a-single-record-set"></a>Clustering di un set di record singolo

Uno scenario comune include un set di dati selezionato da un criterio specifico, ad esempio l'intervallo di tempo che esibisce comportamenti anomali, temperatura elevata in dispositivi, i comandi di lunga durata e superiore agli utenti di spesa. Vorremmo un modo rapido e semplice per individuare modelli comuni (segmenti) nei dati. I modelli sono un subset del set di dati i cui record condividono gli stessi valori in più dimensioni (colonne categoriche). La query seguente si basa e Mostra una serie temporale di eccezioni di servizio più di una settimana nel bin 10 minuti:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Diagramma temporale delle eccezioni di servizio](media/machine-learning-clustering/service-exceptions-timechart.png)

Il conteggio delle eccezioni del servizio mette in correlazione con il traffico del servizio. È possibile visualizzare il criterio giornaliero, in modo chiaro per giorni lavorativi da lunedì a venerdì, con l'aumento del servizio eccezione conta a mezzogiorno e scende nei conteggi durante la notte. Conteggi flat bassi sono visibili durante il fine settimana. I picchi di eccezione possono essere rilevati tramite [ora rilevamento anomalie delle serie](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) in Esplora dati di Azure.

Nel pomeriggio martedì si verifica il picco secondo nei dati. La query seguente viene usata per isolare ulteriormente il picco. Usare la query per ridisegnare il grafico per il picco risoluzione più alta (otto ore in bin di un minuto) per verificare se si tratta di un picco acuto e visualizzare i bordi.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Lo stato attivo sul diagramma temporale di picco](media/machine-learning-clustering/focus-spike-timechart.png)

Viene illustrato un picco di due minuti narrow da 15:00 per 15:02. Nella query seguente, il conteggio delle eccezioni in questa finestra di due minuti:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Conteggio |
|---------|
|972    |

Nella query seguente, campionare 20 eccezioni all'esterno di 972:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Region | ScaleUnit | DeploymentId                     | Punto di analisi | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Usare `autocluster()` per singolo record, impostare il clustering

Anche se sono presenti meno di mille eccezioni, è comunque difficile trovare i segmenti comuni, perché sono presenti più valori in ogni colonna. È possibile usare [ `autocluster()` ](/azure/kusto/query/autoclusterplugin) plug-in per estrarre un breve elenco di segmenti comuni e trovare le interessanti immediatamente cluster entro due minuti del Raccoglitore come illustrato nella query seguente:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Conteggio | Percentuale | Region | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

È possibile visualizzare nei risultati della precedenti che il segmento più dominante contiene 65.74% dei record di eccezione totale e condivide i quattro dimensioni. Il segmento successivo è molto meno comune, contiene solo 9.67% dei record e condivide tre dimensioni. Altri segmenti sono ancora meno comuni. 

Autocluster Usa un algoritmo proprietario per più dimensioni di data mining e l'estrazione dei segmenti interessanti. "Interessanti" significa che ogni segmento è assicurata sia il set di record e set di funzionalità. I segmenti anche diverse, vale a dire che ognuno di essi è significativamente diversa dagli altri. Potrebbe essere rilevante per il processo di RCA uno o più di questi segmenti. Per ridurre al minimo la valutazione e revisione del segmento, autocluster estrae solo un elenco di segmenti di piccole dimensioni.

### <a name="use-basket-for-single-record-set-clustering"></a>Usare `basket()` per singolo record, impostare il clustering

È anche possibile usare la [ `basket()` ](/azure/kusto/query/basketplugin) plug-in come illustrato nella query seguente:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Conteggio | Percentuale | Region | ScaleUnit | DeploymentId | Punto di analisi | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Basket implementa l'algoritmo Apriori per elemento set data mining e consente di estrarre tutti i segmenti di cui la copertura del set di record è superiore al limite (valore predefinito è % 5). È possibile vedere che sono stati estratti più segmenti con visi simili (ad esempio, i segmenti 0,1 o 2,3).

Entrambi i plug-in sono potenti e facile da usare, ma il limite significativo è dovuto al fatto che è un singolo record impostato in modo non supervisionato (con nessuna etichetta) del cluster. Di conseguenza risulta poco chiaro se gli schemi estratti caratterizzano il set di record selezionato (i record anomali) o il set di record globale.

## <a name="clustering-the-difference-between-two-records-sets"></a>La differenza tra set di due record di clustering

Il [ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin) plug-in consente di superare la limitazione del `autocluster` e `basket`. `Diffpatterns` accetta due set di record ed estrae i segmenti principali che sono diversi tra di essi. Un unico set contiene in genere il set in fase di analisi di record anomalie (uno analizzate dai `autocluster` e `basket`). L'altro set contiene il set di record di riferimento (base). 

Nella query seguente, usiamo `diffpatterns` nell'individuare cluster interessanti entro due minuti del picco, che sono diverso rispetto ai cluster all'interno della linea di base. Si definisce la finestra della linea di base come otto minuti prima 15:00 (quando il picco avviato). Dobbiamo inoltre estendere da una colonna binaria (AB) che specifica se un record specifico appartiene alla linea di base o al set di anomalie. `Diffpatterns` implementa un algoritmo di apprendimento supervisionato, in cui le etichette delle due classi generate dalle anomalie e il flag della linea di base (AB).

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | Region | ScaleUnit | DeploymentId | Punto di analisi |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Il segmento più dominante è la stessa che è stato estratto da `autocluster`, il code coverage nella finestra di anomala di due minuti è anche 65.74%. Ma la copertura nella finestra di otto minuti della linea di base è solo % 1.7. La differenza è 64.04%. Questa differenza sembra essere correlato al picco anomalo. È possibile verificare questa ipotesi suddividendo il grafico originale nei record che appartengono a questo segmento problematico e altri segmenti come illustrato nella query seguente:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![La convalida del diagramma temporale segmento 'diffpattern'](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Questo grafico consente di vedere che il picco nel pomeriggio martedì era a causa di eccezioni da questo segmento specifico, individuati tramite il `diffpatterns` plug-in.

## <a name="summary"></a>Riepilogo

I plug-in Azure Data Explorer Machine Learning sono utili per molti scenari. Il `autocluster` e `basket` implementare l'algoritmo di apprendimento non supervisionato e sono semplici da utilizzare. `Diffpatterns` implementa con supervisione algoritmo di apprendimento automatico e, anche se più complesse, è più potente l'estrazione di segmenti di differenziazione per RCA.

Questi plug-in vengono utilizzati in modo interattivo in scenari ad hoc e in automatico in prossimità di servizi di monitoraggio in tempo reale. In Esplora dati di Azure, rilevamento di anomalie in serie temporali è seguito da un processo di diagnostica che è altamente ottimizzato in modo da soddisfare gli standard di prestazioni necessarie.
