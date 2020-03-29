---
title: Funzionalità di apprendimento automatico in Azure Data ExplorerMachine learning capability in Azure Data Explorer
description: Usare il clustering di Machine Learning per l'analisi delle cause radice in Azure Data Explorer.Use machine learning clustering for Root Cause Analysis in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769932"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Funzionalità di apprendimento automatico in Azure Data ExplorerMachine learning capability in Azure Data Explorer

Azure Data Explorer, una piattaforma di analisi dei Big Data, viene usata per monitorare l'integrità del servizio, QoS o dispositivi malfunzionanti per il comportamento anomalo usando il [rilevamento delle anomalie e](/azure/data-explorer/anomaly-detection) le funzioni di previsione delle anomalie incorporate. Una volta rilevato un modello anomalo, viene eseguita un'analisi della causa principale (RCA, Root Cause Analysis) per attenuare o risolvere l'anomalia.

Il processo di diagnosi è complesso e lungo e fatto da esperti di dominio. Il processo include il recupero e l'unione di dati aggiuntivi da origini diverse per lo stesso intervallo di tempo, la ricerca di modifiche nella distribuzione dei valori in più dimensioni, la creazione di grafici di variabili aggiuntive e altre tecniche basate sulla conoscenza del dominio e Intuizione. Poiché questi scenari di diagnosi sono comuni in Azure Data Explorer, sono disponibili plug-in di apprendimento automatico per semplificare la fase di diagnosi e ridurre la durata della RCA.

Azure Data Explorer include tre [`autocluster`](/azure/kusto/query/autoclusterplugin)plug-in di Machine Learning: , [`basket`](/azure/kusto/query/basketplugin), e [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Tutti i plug-in implementano algoritmi di clustering. I `autocluster` `basket` plugin e raggruppano un `diffpatterns` singolo set di record e il plugin raggruppa le differenze tra due set di record.

## <a name="clustering-a-single-record-set"></a>Clustering di un singolo set di record

Uno scenario comune include un set di dati selezionato da criteri specifici, ad esempio un intervallo di tempo che presenta un comportamento anomalo, letture dei dispositivi ad alta temperatura, comandi di lunga durata e utenti con spese principali. Vorremmo un modo semplice e veloce per trovare modelli comuni (segmenti) nei dati. I modelli sono un sottoinsieme del set di dati i cui record condividono gli stessi valori su più dimensioni (colonne categoriche). La query seguente compila e mostra una serie temporale di eccezioni di servizio per una settimana in contenitori di dieci minuti:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Grafico temporale delle eccezioni del servizioService exceptions timechart](media/machine-learning-clustering/service-exceptions-timechart.png)

Il numero di eccezioni del servizio è correlato al traffico complessivo del servizio. È possibile vedere chiaramente il modello giornaliero, per i giorni lavorativi dal lunedì al venerdì, con un aumento dei conteggi delle eccezioni di servizio a metà giornata e cali nei conteggi durante la notte. I conteggi bassi piatti sono visibili durante il fine settimana. I picchi di eccezione possono essere rilevati usando il rilevamento delle anomalie delle serie temporali in Azure Data Explorer.Exception spikes can be detected using [time series anomaly detection](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) in Azure Data Explorer.

Il secondo picco nei dati si verifica il martedì pomeriggio. La query seguente viene utilizzata per diagnosticare ulteriormente questo picco. Utilizzare la query per ridisegnare il grafico intorno al picco di risoluzione più alta (otto ore in contenitori di un minuto) per verificare se si tratta di un picco acuto e visualizzare i bordi.

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=)**\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Concentrati sul grafico a tempo di picco](media/machine-learning-clustering/focus-spike-timechart.png)

Vediamo un picco stretto di due minuti dalle 15:00 alle 15:02. Nella query seguente, contare le eccezioni in questa finestra di due minuti:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==)**\]**

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

Nella query seguente, esempio 20 eccezioni su 972:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Region | Unità Scale | DeploymentId                     | Analisi | Host servizio                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 1942243-19b9-4d85-9ca6-bc961861d287 |
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

### <a name="use-autocluster-for-single-record-set-clustering"></a>Utilizzare autocluster() per il clustering di set di record singoloUse autocluster() for single record set clustering

Anche se ci sono meno di mille eccezioni, è ancora difficile trovare segmenti comuni, in quanto sono presenti più valori in ogni colonna. È possibile [`autocluster()`](/azure/kusto/query/autoclusterplugin) utilizzare plugin per estrarre istantaneamente un piccolo elenco di segmenti comuni e trovare i cluster interessanti entro i due minuti del picco come si vede nella seguente query:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Conteggio | Percentuale | Region | Unità Scale | DeploymentId | Host servizio |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | Ueo | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Dai risultati precedenti si può vedere che il segmento più dominante contiene il 65,74% dei record di eccezione totali e condivide quattro dimensioni. Il segmento successivo è molto meno comune, contiene solo il 9,67% dei record e condivide tre dimensioni. Gli altri segmenti sono ancora meno comuni. 

Autocluster utilizza un algoritmo proprietario per l'estrazione di più dimensioni ed estrazione di segmenti interessanti. "Interessante" significa che ogni segmento ha una copertura significativa sia del set di record che del set di funzionalità. Anche i segmenti sono diversi, il che significa che ognuno è significativamente diverso dagli altri. Uno o più di questi segmenti possono essere rilevanti per il processo RCA. Per ridurre al minimo la revisione e la valutazione dei segmenti, il cluster automatico estrae solo un elenco di segmenti di piccole dimensioni.

### <a name="use-basket-for-single-record-set-clustering"></a>Utilizzare basket() per il clustering di set di record singoloUse basket() for single record set clustering

È inoltre possibile [`basket()`](/azure/kusto/query/basketplugin) utilizzare il plugin come si vede nella seguente query:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Conteggio | Percentuale | Region | Unità Scale | DeploymentId | Analisi | Host servizio |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | Ueo | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Basket implementa l'algoritmo Apriori per il data mining del set di articoli ed estrae tutti i segmenti la cui copertura del set di record è superiore a una soglia (valore predefinito 5%). Puoi vedere che più segmenti sono stati estratti con segmenti simili (ad esempio, segmenti 0,1 o 2,3).

Entrambi i plugin sono potenti e facili da usare, ma la loro limitazione significativa è che raggruppano un singolo record impostato in modo non supervisionato (senza etichette). Non è quindi chiaro se i modelli estratti caratterizzano il set di record selezionato (i record anomali) o il set di record globale.

## <a name="clustering-the-difference-between-two-records-sets"></a>Clustering della differenza tra due set di record

Il [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) plugin supera la `autocluster` `basket`limitazione di e . `Diffpatterns`accetta due set di record ed estrae i segmenti principali diversi tra di loro. Un set contiene in genere il set di `autocluster` record `basket`anomalo analizzato (uno analizzato da e ). L'altro set contiene il set di record di riferimento (linea di base). 

Nella query seguente, `diffpatterns` utilizziamo per trovare cluster interessanti all'interno dei due minuti del picco, che sono diversi dai cluster all'interno della linea di base. Definiamo la finestra della linea di base come gli otto minuti prima delle 15:00 (quando il picco è iniziato). È inoltre necessario estendere da una colonna binaria (AB) specificando se un record specifico appartiene alla linea di base o al set anomalo. `Diffpatterns`implementa un algoritmo di apprendimento supervisionato, in cui le due etichette di classe sono state generate dal flag anomalo rispetto al flag di base (AB).

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA)**\]**

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

| SegmentId | CountA | CountB | PercentA | PercentB | PercentualeDiffAB | Region | Unità Scale | DeploymentId | Analisi |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | Ueo | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Il segmento più dominante è lo `autocluster`stesso segmento estratto da , la sua copertura sulla finestra anomala di due minuti è del 65,74%. Ma la sua copertura sulla finestra di base di otto minuti è solo 1.7%. La differenza è del 64,04%. Questa differenza sembra essere correlata al picco anomalo. È possibile verificare questo presupposto suddividendo il grafico originale nei record appartenenti a questo segmento problematico rispetto agli altri segmenti, come illustrato nella query seguente:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Validating 'diffpattern' segment timechart](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Questo grafico ci permette di vedere che il picco il martedì pomeriggio è `diffpatterns` stato a causa delle eccezioni da questo segmento specifico, scoperto utilizzando il plugin.

## <a name="summary"></a>Riepilogo

I plug-in di Azure Data Explorer Machine Learning sono utili per molti scenari. L'algoritmo `autocluster` di apprendimento e `basket` implementare non supervisionato e sono facili da usare. `Diffpatterns`implementa l'algoritmo di apprendimento supervisionato e, sebbene più complesso, è più potente nell'estrazione di segmenti di differenziazione per RCA.

Questi plugin vengono utilizzati in modo interattivo in scenari ad hoc e in automatico servizi di monitoraggio quasi in tempo reale. In Azure Data Explorer il rilevamento delle anomalie delle serie temporali è seguito da un processo di diagnosi altamente ottimizzato per soddisfare gli standard di prestazioni necessari.
