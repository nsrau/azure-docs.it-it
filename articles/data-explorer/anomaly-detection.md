---
title: Rilevamento delle anomalie delle serie temporali e previsione in Azure Esplora dati
description: Informazioni su come analizzare i dati delle serie temporali per il rilevamento delle anomalie e la previsione con Azure Esplora dati.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 0e06569a3a6948836201b333501bf2de0416d4ca
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766039"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Rilevamento delle anomalie e previsioni in Azure Esplora dati

Azure Esplora dati esegue una raccolta in corso di dati di telemetria da servizi cloud o dispositivi Internet. Questi dati vengono analizzati per diversi approfondimenti, ad esempio il monitoraggio dell'integrità dei servizi, i processi di produzione fisici, le tendenze di utilizzo e la previsione di carico. L'analisi viene eseguita in serie temporali di metriche selezionate per individuare un modello di deviazione della metrica rispetto al tipico modello di base normale. Azure Esplora dati contiene il supporto nativo per la creazione, la manipolazione e l'analisi di più serie temporali. Può creare e analizzare migliaia di serie temporali in pochi secondi, abilitando flussi di lavoro e soluzioni di monitoraggio quasi in tempo reale.

Questo articolo illustra in dettaglio le funzionalità di previsione e rilevamento delle anomalie della serie temporale di Azure Esplora dati. Le funzioni della serie temporale applicabili sono basate su un modello di scomposizione ben noto, in cui ogni serie temporale originale viene scomposta in componenti stagionali, di tendenza e residui. Le anomalie vengono rilevate dagli outlier sul componente residuo, mentre la previsione viene eseguita estrapolando i componenti stagionali e di tendenza. L'implementazione di Azure Esplora dati migliora significativamente il modello di scomposizione di base tramite il rilevamento automatico della stagionalità, l'analisi avanzata degli outlier e l'implementazione vettoriale per elaborare migliaia di serie temporali in pochi secondi.

## <a name="prerequisites"></a>Prerequisiti

Per una panoramica delle funzionalità della serie temporale, vedere [analisi delle serie temporali in Azure Esplora dati](/azure/data-explorer/time-series-analysis) .

## <a name="time-series-decomposition-model"></a>Modello di scomposizione delle serie temporali

L'implementazione nativa di Azure Esplora dati per la stima basata su serie temporali e il rilevamento di anomalie usa un modello di scomposizione noto. Questo modello viene applicato a serie temporali di metriche previste per il comportamento periodico e di tendenza del manifesto, ad esempio il traffico del servizio, gli heartbeat dei componenti e le misurazioni periodiche degli stessi per prevedere i valori delle metriche future e rilevare quelli anomali. Il presupposto di questo processo di regressione è che, oltre al comportamento noto in precedenza stagionale e tendenza, la serie temporale viene distribuita in modo casuale. È quindi possibile prevedere i valori delle metriche future dai componenti stagionali e di tendenza, denominati collettivamente baseline e ignorare la parte residua. È anche possibile rilevare i valori anomali in base all'analisi outlier usando solo la parte residua.
Per creare un modello di scomposizione, utilizzare la funzione [`series_decompose()`](/azure/kusto/query/series-decomposefunction). La funzione `series_decompose()` accetta un set di serie temporali e scompone automaticamente ogni serie temporale ai componenti stagionali, di tendenza, residui e di base. 

È possibile, ad esempio, scomporre il traffico di un servizio Web interno utilizzando la query seguente:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Scomposizione di serie temporali](media/anomaly-detection/series-decompose-timechart.png)

* La serie temporale originale è denominata **num** (in rosso). 
* Il processo inizia con il rilevamento automatico della stagionalità usando la funzione [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) ed estrae il modello **stagionale** (in viola).
* Il modello stagionale viene sottratto dalla serie temporale originale e viene eseguita una regressione lineare usando la funzione [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) per trovare il componente **tendenza** (in blu chiaro).
* La funzione sottrae la tendenza e il resto è il componente **residuo** (in verde).
* Infine, la funzione aggiunge i componenti stagionali e di tendenza per generare la **baseline** (in blu).

## <a name="time-series-anomaly-detection"></a>Rilevamento anomalie della serie temporale

La funzione [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) trova punti anomali in un set di serie temporali. Questa funzione chiama `series_decompose()` per compilare il modello di scomposizione e quindi esegue [`series_outliers()`](/azure/kusto/query/series-outliersfunction) sul componente residuo. `series_outliers()` calcola i punteggi di anomalie per ogni punto del componente residuo usando il test di recinzione di Tukey. I punteggi di anomalie sopra 1,5 o inferiori a 1,5 indicano rispettivamente un incremento o un calo di anomalie. I punteggi di anomalie sopra 3,0 o inferiori a 3,0 indicano un'anomalia forte. 

La query seguente consente di rilevare le anomalie nel traffico del servizio Web interno:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Rilevamento anomalie della serie temporale](media/anomaly-detection/series-anomaly-detection.png)

* La serie temporale originale (in rosso). 
* Componente Baseline (Season + trend) (in blu).
* Punti anomali (in viola) all'inizio della serie temporale originale. I punti anomali si deviano in modo significativo rispetto ai valori di base previsti.

## <a name="time-series-forecasting"></a>Previsione delle serie temporali

La funzione [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) stima i valori futuri di un set di serie temporali. Questa funzione chiama `series_decompose()` per compilare il modello di scomposizione e quindi, per ogni serie temporale, estrapola il componente di base in futuro.

La query seguente consente di stimare il traffico dei servizi Web della settimana successiva:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Previsione delle serie temporali](media/anomaly-detection/series-forecasting.png)

* Metrica originale (in rosso). Per impostazione predefinita, i valori futuri risultano mancanti e vengono impostati su 0.
* Estrapolare il componente Baseline (in blu) per stimare i valori della settimana successiva.

## <a name="scalability"></a>Scalabilità

La sintassi del linguaggio di query di Azure Esplora dati consente a una singola chiamata di elaborare più serie temporali. La sua implementazione ottimizzata univoca consente prestazioni veloci, che è fondamentale per il rilevamento e la previsione di anomalie durante il monitoraggio di migliaia di contatori negli scenari quasi in tempo reale.

Nella query seguente viene illustrata l'elaborazione di tre serie temporali simultaneamente:

**\[** [**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Scalabilità delle serie temporali](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Summary

Questo documento descrive in dettaglio le funzioni native di Azure Esplora dati per il rilevamento e la previsione di anomalie della serie temporale. Ogni serie temporale originale è scomposta in componenti stagionali, di tendenza e residui per il rilevamento di anomalie e/o previsioni. Queste funzionalità possono essere usate per scenari di monitoraggio quasi in tempo reale, ad esempio il rilevamento di errori, la manutenzione predittiva e la previsione del carico e della richiesta.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [funzionalità di Machine Learning](/azure/data-explorer/machine-learning-clustering) in Azure Esplora dati.
