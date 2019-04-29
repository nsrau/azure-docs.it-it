---
title: Analizzare i dati di serie temporali usando Esplora dati di Azure
description: Informazioni su come analizzare i dati di serie temporali nel cloud usando Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 8492f736e64366802b3601f9b5fc8bd1d9b6ea79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827440"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analisi di serie temporali in Esplora dati di Azure

Esplora dati di Azure esegue la raccolta continua di dati di telemetria da servizi cloud o dispositivi IoT. Questi dati possono essere analizzati per ottenere varie informazioni dettagliate, ad esempio per il monitoraggio dell'integrità dei servizi, dei processi di produzione e delle tendenze di utilizzo. L'analisi viene eseguita su serie temporali di metriche selezionate per individuare eventuali deviazioni rispetto al tipico modello di base.
Esplora dati di Azure contiene supporto nativo per la creazione, la modifica e l'analisi di più serie temporali. In questo argomento si apprenderà come usare Esplora dati di Azure per creare e analizzare **migliaia di serie temporali in pochi secondi**, eseguendo così il monitoraggio di soluzioni e flussi di lavoro quasi in tempo reale.

## <a name="time-series-creation"></a>Creazione di serie temporali

In questa sezione si creerà un ampio set di serie temporali regolari in modo semplice e intuitivo usando l'operatore `make-series` e specificando eventuali valori mancanti in base alle esigenze.
Il primo passaggio nell'analisi delle serie temporali consiste nel partizionare la tabella dei dati di telemetria originale e trasformarla in un set di serie temporali. La tabella contiene in genere una colonna di timestamp, dimensioni contestuali e metriche facoltative. Le dimensioni vengono usate per partizionare i dati. L'obiettivo è quello di creare migliaia di serie temporali per ogni partizione a intervalli regolari.

La tabella di input *demo_make_series1* contiene 600.000 record del traffico di servizi Web arbitrario. Usare il comando seguente per creare un campione di 10 record:

```kusto
demo_make_series1 | take 10 
```

La tabella risultante contiene una colonna di timestamp e tre colonne di dimensioni contestuali, ma non sono incluse metriche:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | TimeStamp | BrowserVer | OsVer | Paese |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Regno Unito |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Regno Unito |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Regno Unito |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | Repubblica di Lituania |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | India |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Regno Unito |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Paesi Bassi |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Regno Unito |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | India |

Poiché non sono presenti metriche, è possibile creare solo un set di serie temporali che rappresenta il traffico totale, partizionato in base al sistema operativo, usando la query seguente:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Usare l'operatore [`make-series`](/azure/kusto/query/make-seriesoperator) per creare un set di tre serie temporali, dove:
    - `num=count()`: la serie temporale del traffico
    - `range(min_t, max_t, 1h)`: la serie temporale viene creata in base a intervalli di un'ora nell'intero periodo di tempo (il timestamp meno recente e quello più recente dei record della tabella)
    - `default=0`: specificare il metodo di riempimento per gli intervalli mancanti per creare serie temporali regolari oppure usare [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction), [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction), [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) e [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) per eventuali modifiche
    - `byOsVer`: partizionare in base al sistema operativo
- L'effettiva struttura di dati di una serie temporale è una matrice numerica del valore aggregato per ogni intervallo temporale. Per la visualizzazione viene usato `render timechart`.

Nella tabella precedente sono presenti tre partizioni. È possibile creare una serie temporale distinta: Windows 10 (rosso), 7 (blu) e 8.1 (verde), come illustrato nel grafico:

![Partizioni di serie temporali](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Funzioni di analisi delle serie temporali

In questa sezione si eseguiranno tipiche funzioni di elaborazione delle serie temporali.
Dopo la creazione di un set di serie temporali, in Esplora dati di Azure è disponibile un numero crescente di funzioni di elaborazione e analisi di tali serie, che è possibile trovare nella [documentazione relativa alle serie temporali](/azure/kusto/query/machine-learning-and-tsa). Di seguito sono descritte alcune funzioni rappresentative per l'elaborazione e l'analisi di serie temporali.

### <a name="filtering"></a>Filtri

L'applicazione di filtri è una pratica comune nell'elaborazione dei segnali ed è utile per le attività di elaborazione delle serie temporali, ad esempio lo smorzamento di un segnale di disturbo o il rilevamento di modifiche.
- Sono presenti due funzioni di filtro generiche:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Applicazione del filtro FIR. Usato per il calcolo semplice della media mobile e della differenziazione della serie temporale per il rilevamento di modifiche.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): Applicazione del filtro IIR. Usato per lo smorzamento esponenziale e la somma cumulativa.
- `Extend` viene usato per estendere il set di serie temporali aggiungendo alla query una nuova serie a media mobile costituita da cinque intervalli (denominata *ma_num*):

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Applicazione di filtri alle serie temporali](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Analisi di regressione

Esplora dati di Azure supporta l'analisi di regressione lineare segmentata per stimare la tendenza delle serie temporali.
- Usare [series_fit_line()](/azure/kusto/query/series-fit-linefunction) per individuare la linea che meglio si adatta a una serie temporale per il rilevamento della tendenza generale.
- Usare [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) per rilevare le variazioni della tendenza, rispetto alla linea di base, che sono utili negli scenari di monitoraggio.

Esempio delle funzioni `series_fit_line()` e `series_fit_2lines()` in una query di serie temporale:

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regressione della serie temporale](media/time-series-analysis/time-series-regression.png)

- Blu: serie temporale originale
- Verde: linea adattata
- Rosso: due linee adattate

> [!NOTE]
> La funzione ha rilevato correttamente il punto di variazione del livello.

### <a name="seasonality-detection"></a>Rilevamento della stagionalità

Molte metriche seguono modelli stagionali (periodici). Il traffico degli utenti dei servizi cloud contiene in genere modelli giornalieri e settimanali che sono più alti nella parte centrale della giornata lavorativa e più bassi di notte o durante il fine settimana. I sensori IoT eseguono misurazioni in base a intervalli periodici. Le misure fisiche, ad esempio temperatura, pressione o umidità, possono mostrare anche un comportamento stagionale.

L'esempio seguente applica il rilevamento della stagionalità al traffico di un servizio Web nel corso di un mese (intervalli di 2 ore):

```kusto
demo_series3
| render timechart 
```

![Stagionalità della serie temporale](media/time-series-analysis/time-series-seasonality.png)

- Usare [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) per rilevare automaticamente i periodi nella serie temporale. 
- Usare [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) se si è conoscenza del fatto che una metrica dovrebbe avere specifici periodi distinti e si vuole verificare che esistano effettivamente.

> [!NOTE]
> Se non esistono specifici periodi distinti è presente un'anomalia.

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | periodi | scores | days |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

La funzione rileva la stagionalità giornaliera e settimanale. La prima ha un punteggio minore rispetto alla seconda perché i giorni del fine settimana sono diversi dai normali giorni della settimana.

### <a name="element-wise-functions"></a>Funzioni relative a elementi

È possibile eseguire operazioni aritmetiche e logiche su una serie temporale. Usando [series_subtract()](/azure/kusto/query/series-subtractfunction) si può calcolare una serie temporale residua, ovvero la differenza tra una metrica originale non elaborata e una metrica smorzata e cercare eventuali anomalie nel segnale residuo:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Operazioni su una serie temporale](media/time-series-analysis/time-series-operations.png)

- Blu: serie temporale originale
- Rosso: serie temporale smorzata
- Verde: serie temporale residua

## <a name="time-series-workflow-at-scale"></a>Flusso di lavoro di serie temporali su larga scala

L'esempio seguente illustra come queste funzioni possono essere eseguite su larga scala su migliaia di serie temporali in secondi per il rilevamento di anomalie. Per visualizzare alcuni record di telemetria di esempio di una metrica di conteggio delle letture di un servizio di database in un periodo di quattro giorni, eseguire la query seguente:

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | TIMESTAMP | Loc | anonOp | DB | DataRead |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

E statistiche semplici:

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

La creazione di una serie temporale con intervalli di un'ora della metrica di lettura (quattro giorni totali * 24 ore = 96 punti) ha come risultato una fluttuazione del modello normale:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Serie temporale su larga scala](media/time-series-analysis/time-series-at-scale.png)

Il comportamento sopra illustrato è fuorviante perché la singola serie temporale normale risulta dall'aggregazione di migliaia di istanze diverse che possono avere modelli anomali. È pertanto opportuno creare una serie temporale per ogni istanza. Un'istanza è definita da Loc (località), anonOp (operazione) e DB (computer specifico).

Quante serie temporali è possibile creare?

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Conteggio |
|   | 18339 |

A questo punto, si crea un set di 18339 serie temporali della metrica di conteggio delle letture. Si aggiunge la clausola `by` all'istruzione make-series, si applica la regressione lineare e si selezionano le prime due serie temporali che risultano avere la tendenza decrescente più significativa:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Prime due serie temporali](media/time-series-analysis/time-series-top-2.png)

Visualizzare le istanze:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Loc | Op | DB | slope |
|   | Loc 15 | 37 | 1151 | -102743.910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

In meno di due minuti, Esplora dati di Azure ha analizzato quasi 20.000 serie temporali e ha rilevato due serie temporali anomale in cui il conteggio delle letture è calato improvvisamente.

Queste funzionalità avanzate, unite alle prestazioni elevate di Esplora dati di Azure, offrono una soluzione potente ed esclusiva per l'analisi di serie temporali.
