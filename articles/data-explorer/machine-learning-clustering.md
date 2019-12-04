---
title: Funzionalità di Machine Learning in Azure Esplora dati
description: Usare il clustering di machine learning per l'analisi delle cause principali in Azure Esplora dati.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769932"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Funzionalità di Machine Learning in Azure Esplora dati

Azure Esplora dati, una piattaforma di analisi dei Big data, viene usato per monitorare l'integrità dei servizi, QoS o i dispositivi malfunzionanti per un comportamento anomalo usando le funzioni predefinite di [rilevamento delle anomalie e previsioni](/azure/data-explorer/anomaly-detection) . Una volta rilevato un modello anomalo, viene eseguita un'analisi della causa radice (RCA) per attenuare o risolvere l'anomalia.

Il processo di diagnosi è complesso e lungo e realizzato dagli esperti di dominio. Il processo include il recupero e l'aggiunta di dati aggiuntivi da origini diverse per lo stesso intervallo di tempo, la ricerca di modifiche nella distribuzione di valori in più dimensioni, la creazione di grafici di variabili aggiuntive e altre tecniche basate sulle informazioni di dominio e intuizione. Poiché questi scenari di diagnosi sono comuni in Azure Esplora dati, sono disponibili plug-in di machine learning per semplificare la fase di diagnosi e ridurre la durata della RCA.

Azure Esplora dati prevede tre plug-in Machine Learning: [`autocluster`](/azure/kusto/query/autoclusterplugin), [`basket`](/azure/kusto/query/basketplugin)e [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Tutti i plug-in implementano gli algoritmi di clustering. I plug-in `autocluster` e `basket` raggruppano un singolo set di record e il plug-in `diffpatterns` cluster le differenze tra due set di record.

## <a name="clustering-a-single-record-set"></a>Clustering di un singolo set di record

Uno scenario comune include un set di dati selezionato da criteri specifici, ad esempio un intervallo di tempo che mostra un comportamento anomalo, letture di dispositivi ad alta temperatura, comandi con lunga durata e utenti di spesa principali. Si vuole un modo semplice e veloce per trovare i modelli comuni (segmenti) nei dati. I modelli sono un subset del set di dati i cui record condividono gli stessi valori in più dimensioni (colonne categoriche). La query seguente compila e Mostra una serie temporale di eccezioni del servizio in una settimana in contenitori di dieci minuti:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Eccezioni del servizio Diagramma temporale](media/machine-learning-clustering/service-exceptions-timechart.png)

Il numero di eccezioni del servizio è correlato al traffico globale del servizio. È possibile visualizzare chiaramente il modello giornaliero, per i giorni lavorativi da lunedì a venerdì, con un incremento dei conteggi delle eccezioni del servizio a metà giornata, ed Elimina i conteggi durante la notte. I conteggi bassi Flat sono visibili nel fine settimana. I picchi di eccezione possono essere rilevati usando il [rilevamento di anomalie della serie temporale](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) in Azure Esplora dati.

Il secondo picco nei dati viene eseguito il martedì pomeriggio. La query seguente viene usata per diagnosticare ulteriormente questo picco. Usare la query per ricreare il grafico intorno al picco con una risoluzione superiore (otto ore in contenitori di un minuto) per verificare se si tratta di un picco acuto e visualizzarne i bordi.

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=) **\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Concentrati su Spike Diagramma temporale](media/machine-learning-clustering/focus-spike-timechart.png)

Viene visualizzato un picco di due minuti limitato da 15:00 a 15:02. Nella query seguente, contare le eccezioni in questa finestra di due minuti:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==) **\]**

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

Nella query seguente, campionare 20 eccezioni su 972:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Area geografica | ScaleUnit | DeploymentId                     | Punto | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | Scus   | aggiornamento software 3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | Scus   | aggiornamento software 3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | Scus   | aggiornamento software 3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | Scus   | aggiornamento software 3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | Scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | Scus   | aggiornamento software 3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Usare AutoCluster () per il clustering di set di record singolo

Anche se sono presenti meno di mille eccezioni, è ancora difficile trovare segmenti comuni, in quanto sono presenti più valori in ogni colonna. È possibile usare [`autocluster()`](/azure/kusto/query/autoclusterplugin) plug-in per estrarre immediatamente un piccolo elenco di segmenti comuni e individuare i cluster interessanti nei due minuti del picco, come illustrato nella query seguente:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Conteggio | Percentuale | Area geografica | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | Scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | Scus | aggiornamento software 3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | UEO | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

È possibile vedere nei risultati precedenti che il segmento più dominante contiene il 65,74% dei record di eccezione totali e condivide quattro dimensioni. Il segmento successivo è molto meno comune, contiene solo il 9,67% dei record e condivide tre dimensioni. Gli altri segmenti sono ancora meno comuni. 

AutoCluster usa un algoritmo proprietario per il data mining di più dimensioni ed estrae segmenti interessanti. "Interessante" significa che ogni segmento presenta una copertura significativa del set di record e delle funzionalità impostate. I segmenti sono anche divergenti, vale a dire che ognuno è significativamente diverso dagli altri. Uno o più di questi segmenti potrebbero essere rilevanti per il processo RCA. Per ridurre al minimo la revisione e la valutazione del segmento, AutoCluster estrae solo un elenco di segmenti di piccole dimensioni.

### <a name="use-basket-for-single-record-set-clustering"></a>Usare basket () per il clustering di set di record singolo

È anche possibile usare il plug-in [`basket()`](/azure/kusto/query/basketplugin) come illustrato nella query seguente:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Conteggio | Percentuale | Area geografica | ScaleUnit | DeploymentId | Punto | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | Scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | Scus | aggiornamento software 3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | Scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | UEO | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Basket implementa l'algoritmo Apriori per il data mining del set di elementi ed estrae tutti i segmenti la cui copertura del set di record è superiore a una soglia (valore predefinito 5%). È possibile osservare che sono stati estratti più segmenti con quelli simili (ad esempio, segmenti 0, 1 o 2, 3).

Entrambi i plug-in sono potenti e facili da utilizzare, ma la loro significativa limitazione consiste nel fatto che raggruppano un singolo set di record in modo non supervisionato (senza etichette). Non è pertanto chiaro se i modelli estratti caratterizzano il set di record selezionato (record anomali) o il set di record globale.

## <a name="clustering-the-difference-between-two-records-sets"></a>Clustering della differenza tra due set di record

Il plug-in [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) il limite di `autocluster` e `basket`. `Diffpatterns` accetta due set di record ed estrae i segmenti principali che sono diversi tra loro. Un set contiene in genere il set di record anomalo esaminato (uno analizzato da `autocluster` e `basket`). L'altro set contiene il set di record di riferimento (baseline). 

Nella query seguente viene usato `diffpatterns` per trovare cluster interessanti nei due minuti del picco, che sono diversi dai cluster all'interno della linea di base. Si definisce la finestra di base come otto minuti prima del 15:00 (all'avvio del picco). È anche necessario estendere da una colonna binaria (AB) che specifica se un record specifico appartiene alla baseline o al set anomalo. `Diffpatterns` implementa un algoritmo di apprendimento supervisionato, in cui le due etichette di classe sono state generate da anomalie rispetto al flag Baseline (AB).

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA) **\]**

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

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | Area geografica | ScaleUnit | DeploymentId | Punto |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65,74 | 1.7 | 64,04 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17,18 | 44,16 | 26,97 | Scus |  |  |  |
| 2 | 92 | 356 | 9,47 | 28,9 | 19,43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27,27 | 18,01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8,44 | 25,81 | 17,38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5,66 | 20,45 | 14,8 | UEO | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5,86 | 16,56 | 10,69 |  |  |  |  |

Il segmento più dominante è lo stesso che è stato Estratto da `autocluster`, anche la relativa copertura sulla finestra anomala di due minuti è pari al 65,74%. Tuttavia, la copertura sulla finestra di base di otto minuti è solo del 1,7%. La differenza è pari al 64,04%. Questa differenza sembra essere correlata al picco anomalo. È possibile verificare questa ipotesi suddividendo il grafico originale nei record appartenenti a questo segmento problematico rispetto agli altri segmenti, come illustrato nella query seguente:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Convalida del segmento ' diffpattern ' Diagramma temporale](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Questo grafico consente di vedere che il picco del martedì pomeriggio è dovuto a eccezioni da questo segmento specifico, individuate con il plug-in `diffpatterns`.

## <a name="summary"></a>Summary

I plug-in Azure Esplora dati Machine Learning sono utili per molti scenari. I `autocluster` e `basket` implementano un algoritmo di apprendimento non supervisionato e sono facili da usare. `Diffpatterns` implementa l'algoritmo di apprendimento supervisionato e, sebbene più complesso, è più potente nell'estrazione dei segmenti di differenziazione per RCA.

Questi plug-in vengono usati in modo interattivo negli scenari ad hoc e nei servizi di monitoraggio automatici quasi in tempo reale. In Azure Esplora dati il rilevamento delle anomalie della serie temporale è seguito da un processo di diagnosi altamente ottimizzato per soddisfare gli standard di prestazioni necessari.
