---
title: Rilevamento anomalie delle serie temporali & previsione in Azure Data ExplorerTime series anomaly detection & forecasting in Azure Data Explorer
description: Informazioni su come analizzare i dati delle serie temporali per il rilevamento e la previsione delle anomalie usando Azure Data Explorer.Learn how to analyze time series data for anomaly detection and forecasting using Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194158"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Rilevamento e previsione delle anomalie in Azure Data Explorer

Azure Data Explorer esegue una raccolta continua di dati di telemetria da servizi cloud o dispositivi IoT.Azure Data Explorer performs on-going collection of telemetry data from cloud services or IoT devices. Questi dati vengono analizzati per varie informazioni dettagliate, ad esempio il monitoraggio dell'integrità del servizio, i processi di produzione fisici, le tendenze di utilizzo e la previsione del carico. L'analisi viene eseguita su una serie temporale di metriche selezionate per individuare un modello di deviazione della metrica rispetto al modello di base normale tipico. Azure Data Explorer contiene il supporto nativo per la creazione, la manipolazione e l'analisi di più serie temporali. Può creare e analizzare migliaia di serie temporali in pochi secondi, consentendo soluzioni di monitoraggio e flussi di lavoro quasi in tempo reale.

Questo articolo descrive in dettaglio le funzionalità di rilevamento e previsione delle anomalie delle serie temporali di Azure Data Explorer.This article details the Azure Data Explorer time series anomaly detection and forecasting capabilities. Le funzioni delle serie temporali applicabili si basano su un robusto modello di decomposizione noto, in cui ogni serie temporale originale viene scomposta in componenti stagionali, di tendenza e residui. Le anomalie vengono rilevate da outlier sul componente residuo, mentre la previsione viene eseguita estrapolando i componenti stagionali e di tendenza. L'implementazione di Azure Data Explorer migliora in modo significativo il modello di decomposizione di base mediante il rilevamento automatico della stagionalità, un'analisi outlier affidabile e un'implementazione vettorizzata per elaborare migliaia di serie temporali in pochi secondi.

## <a name="prerequisites"></a>Prerequisiti

Leggere [L'analisi di serie temporali in Azure Data Explorer](/azure/data-explorer/time-series-analysis) per una panoramica delle funzionalità delle serie temporali.

## <a name="time-series-decomposition-model"></a>Modello di scomposizione di serie temporali

L'implementazione nativa di Azure Data Explorer per la stima delle serie temporali e il rilevamento delle anomalie usa un modello di scomposizione noto. Questo modello viene applicato a una serie temporale di metriche che dovrebbero manifestare un comportamento periodico e di tendenza, ad esempio il traffico del servizio, gli heartbeat dei componenti e le misurazioni periodiche IoT per prevedere i valori delle metriche future e rilevare quelle anomale. Il presupposto di questo processo di regressione è che, ad esempio, oltre al comportamento stagionale e di tendenza precedentemente noto, la serie temporale viene distribuita in modo casuale. È quindi possibile prevedere i valori delle metriche future dai componenti stagionali e di tendenza, denominati collettivamente baseline, e ignorare la parte residua. È inoltre possibile rilevare valori anomali in base all'analisi anomala utilizzando solo la parte residua.
Per creare un modello di [`series_decompose()`](/azure/kusto/query/series-decomposefunction)scomposizione, utilizzare la funzione . La `series_decompose()` funzione prende una serie di serie temporali e scompone automaticamente ogni serie temporale ai suoi componenti stagionali, di tendenza, residui e di base. 

Ad esempio, è possibile scomporre il traffico di un servizio Web interno utilizzando la query seguente:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

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

![Decomposizione sequenza temporale](media/anomaly-detection/series-decompose-timechart.png)

* La serie temporale originale è etichettata **num** (in rosso). 
* Il processo inizia con il rilevamento automatico [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) della stagionalità utilizzando la funzione ed estrae il modello **stagionale** (in viola).
* Il modello stagionale viene sottratto dalla serie temporale originale [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) e viene eseguita una regressione lineare utilizzando la funzione per trovare il componente di **tendenza** (in azzurro).
* La funzione sottrae la tendenza e il resto è il componente **residuo** (in verde).
* Infine, la funzione aggiunge i componenti stagionali e di tendenza per generare la **linea di base** (in blu).

## <a name="time-series-anomaly-detection"></a>Rilevamento delle anomalie delle serie temporali

La [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) funzione trova punti anomali su un insieme di serie temporali. Questa funzione `series_decompose()` chiama per compilare il [`series_outliers()`](/azure/kusto/query/series-outliersfunction) modello di scomposizione e quindi viene eseguito sul componente residuo. `series_outliers()`calcola i punteggi di anomalia per ogni punto del componente residuo utilizzando il test di recinzione di Tukey. I punteggi di anomalia superiori a 1,5 o inferiori a -1,5 indicano rispettivamente un lieve aumento o diminuzione dell'anomalia. I punteggi anomalie superiori a 3,0 o inferiori a -3,0 indicano una forte anomalia. 

La query seguente consente di rilevare anomalie nel traffico dei servizi Web interni:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

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

![Rilevamento delle anomalie delle serie temporali](media/anomaly-detection/series-anomaly-detection.png)

* La serie temporale originale (in rosso). 
* La componente linea di base (stagionale e tendenza) (in blu).
* I punti anomali (in viola) sopra la serie temporale originale. I punti anomali si discostano significativamente dai valori di base previsti.

## <a name="time-series-forecasting"></a>Previsione di serie temporali

La [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) funzione stima i valori futuri di un set di serie temporali. Questa funzione `series_decompose()` chiama per compilare il modello di scomposizione e quindi, per ogni serie temporale, estrapola il componente della linea di base in futuro.

La query seguente consente di stimare il traffico del servizio Web della settimana successiva:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

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

![Previsione di serie temporali](media/anomaly-detection/series-forecasting.png)

* Metrica originale (in rosso). I valori futuri sono mancanti e impostati su 0, per impostazione predefinita.
* Estrapolare il componente della linea di base (in blu) per stimare i valori della settimana successiva.

## <a name="scalability"></a>Scalabilità

La sintassi del linguaggio di query di Azure Data Explorer consente a una singola chiamata di elaborare più serie temporali. La sua implementazione ottimizzata unica consente prestazioni veloci, che è fondamentale per il rilevamento efficace delle anomalie e la previsione quando si monitorano migliaia di contatori in scenari quasi in tempo reale.

La query seguente mostra l'elaborazione simultanea di tre serie temporali:

**\[**[**Fare clic per eseguire la query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

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

## <a name="summary"></a>Riepilogo

Questo documento descrive in dettaglio le funzioni native di Azure Data Explorer per il rilevamento e la previsione delle anomalie delle serie temporali. Ogni serie temporale originale viene scomposta in componenti stagionali, di tendenza e residui per rilevare anomalie e/o previsioni. Queste funzionalità possono essere utilizzate per scenari di monitoraggio quasi in tempo reale, ad esempio il rilevamento degli errori, la manutenzione predittiva e la previsione della domanda e del carico.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle funzionalità di Apprendimento automatico in Azure Data Explorer.Learn about [Machine learning capabilities](/azure/data-explorer/machine-learning-clustering) in Azure Data Explorer.
