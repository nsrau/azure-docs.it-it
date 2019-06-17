---
title: Rilevamento di anomalie in serie temporali e le previsioni in Esplora dati di Azure
description: Informazioni su come analizzare i dati di serie temporali per il rilevamento delle anomalie e le previsioni con Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: f40350129a12c7865051bcae80b74b6f9c069179
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233536"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Rilevamento di anomalie e le previsioni in Esplora dati di Azure

Esplora dati Azure esegue la raccolta continua di dati di telemetria da dispositivi IoT o servizi cloud. Questi dati viene analizzati per varie informazioni dettagliate, ad esempio il monitoraggio dell'integrità del servizio, i processi di produzione fisica, le tendenze di utilizzo e previsione di carico. L'analisi viene eseguita in serie temporale di metriche selezionate per individuare un modello di deviazione della metrica rispetto al relativo modello tipico normale di base. Esplora i dati di Azure include supporto nativo per la creazione, modifica e analisi di più serie temporali. È possibile creare e analizzare migliaia di serie temporali in pochi secondi, l'abilitazione quasi in tempo reale, monitoraggio di soluzioni e flussi di lavoro.

Questo articolo illustra in dettaglio le Esplora dati di Azure ora serie anomalie rilevamento e la previsione della funzionalità. Le funzioni di serie temporali applicabili si basano su un modello di scomposizione noto affidabile, in cui ogni serie temporale originale viene scomposto stagionale, tendenza e componenti residui. Le anomalie vengono rilevate mediante gli outlier per il componente rimanente, mentre la previsione è eseguita estrapolando gli stagionali e componenti di tendenza. L'implementazione di Esplora dati di Azure migliora in modo significativo il modello di scomposizione di base dal rilevamento della stagionalità automatico, analisi degli outlier affidabile e implementazione vettorizzata scritta per elaborare migliaia di serie temporale in pochi secondi.

## <a name="prerequisites"></a>Prerequisiti

Lettura [tempo di analisi delle serie in Esplora dati di Azure](/azure/data-explorer/time-series-analysis) per una panoramica delle funzionalità di time series.

## <a name="time-series-decomposition-model"></a>Modello di scomposizione di time series

Implementazione nativa di Azure Esplora dati per la stima basata su serie temporali e rilevamento delle anomalie Usa un modello di scomposizione noto. Questo modello viene applicato alle serie temporali delle metriche prevede del manifesto periodico e il comportamento di tendenza, quali il traffico del servizio, gli heartbeat componente e IoT misurazioni periodiche per stimare valori futuri di metrica e rilevare quelle anomale. Il presupposto di questo processo di regressione che è diverso da nota in precedenza stagionale e il comportamento di tendenza, il tempo di serie viene distribuito in modo casuale. È quindi possibile prevedere i futuri valori delle metriche dallo stagionale e componenti di tendenza, collettivamente denominate linea di base e ignorare la parte rimanente. È anche possibile rilevare valori anomali basati sull'analisi degli outlier utilizzando solo la parte rimanente.
Per creare un modello di scomposizione, usare la funzione [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction). Il `series_decompose()` funzione accetta un set di serie temporali e automaticamente scompone ogni serie temporale relativo stagionali, tendenza, residual e dei componenti di base. 

Ad esempio, è possibile scomporre il traffico di un servizio web interno utilizzando la query seguente:

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

* La serie temporale originale viene etichettata **num** (in rosso). 
* Il processo viene avviato per il rilevamento automatico di stagionalità usando la funzione [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) ed estrae i **stagionali** modello (in viola).
* Il modello stagionale viene sottratto dalla serie temporale originale e una regressione lineare viene eseguita utilizzando la funzione [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) per trovare il **tendenza** componente (in blu chiaro).
* La funzione sottrae la tendenza e il resto è il **residuo** componente (in verde).
* Infine, la funzione aggiunge lo stagionale e delle tendenze di componenti per generare il **baseline** (in blu).

## <a name="time-series-anomaly-detection"></a>Rilevamento di anomalie in serie temporali

La funzione [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) trova anomali punti su un set di serie temporale. Questa funzione chiama `series_decompose()` per compilare il modello di scomposizione e quindi viene eseguito [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) sul componente residuo. `series_outliers()` Calcola i punteggi delle anomalie per ogni punto del componente residuo usando il test di Tukey limite. Punteggi delle anomalie sopra 1.5 o di sotto di -1,5 indicano un aumento delle anomalie blanda o rifiutare rispettivamente. I punteggi di anomalia sopra 3.0 o sotto -3.0 indicano un'anomalia sicura. 

La query seguente consente di rilevare le anomalie del traffico del servizio web interni:

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

![Rilevamento di anomalie in serie temporali](media/anomaly-detection/series-anomaly-detection.png)

* La serie temporale originale (in rosso). 
* La linea di base (stagionale + tendenza) componente (in blu).
* I punti anomali (in viola) nella parte superiore della serie temporale originale. I punti di anomali in modo significativo si discostano dai valori di base prevista.

## <a name="time-series-forecasting"></a>Previsione in serie temporale

La funzione [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) consente di stimare valori futuri di un set di serie temporale. Questa funzione chiama `series_decompose()` per compilare il modello e quindi, per ogni serie temporale, la scomposizione Estrapola il componente della linea di base nel futuro.

La query seguente consente di stimare il traffico del servizio web della prossima settimana:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Previsione in serie temporale](media/anomaly-detection/series-forecasting.png)

* Metrica originale (in rosso). I valori futuri risultano mancanti e impostato su 0, per impostazione predefinita.
* Estrapolare il componente della linea di base (in blu) per stimare i valori della settimana successiva.

## <a name="scalability"></a>Scalabilità

Sintassi del linguaggio query Esplora dati Azure consente una singola chiamata per l'elaborazione di più serie temporali. L'implementazione ottimizzato univoco consente per garantire prestazioni elevate, che è importante per il rilevamento delle anomalie efficace e le previsioni quando si monitorino migliaia di contatori in scenari in tempo reale quasi.

La query seguente illustra l'elaborazione di serie temporale tre contemporaneamente:

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

![Scalabilità di serie ora](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Riepilogo

Questo documento descrive le funzioni native di Esplora dati di Azure per rilevamento anomalie di serie temporali e previsione. Ogni serie temporale originale viene scomposto in componenti stagionali, tendenza e residuo per rilevare le anomalie e/o di previsione. Queste funzionalità possono essere usate per quasi scenari di monitoraggio in tempo reale, come rilevamento degli errori, la manutenzione predittiva e la domanda e carico di previsione.

## <a name="next-steps"></a>Passaggi successivi

Scopri [di Machine learning funzionalità](/azure/data-explorer/machine-learning-clustering) in Esplora dati di Azure.
