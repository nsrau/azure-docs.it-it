---
title: Eseguire il training automatico di un modello di previsione delle serie temporali
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning per eseguire il training di un modello di regressione di previsione di serie temporali usando Machine Learning automatizzato.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 05d658c052c5bc12f49d957bb29ad085c269c57b
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137365"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Eseguire il training automatico di un modello di previsione delle serie temporali
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come configurare ed eseguire il training di un modello di regressione di previsione di serie temporali usando Machine Learning automatizzato in Azure Machine Learning. 

La configurazione di un modello di previsione è simile alla configurazione di un modello di regressione standard usando Machine Learning automatizzato, ma sono disponibili alcune opzioni di configurazione e passaggi di pre-elaborazione per l'uso di dati di serie temporali. 

È possibile, ad esempio, [configurare](#config) il modo in cui la previsione dovrebbe estendersi (orizzonte di previsione), nonché i ritardi e altro ancora. Machine Learning Machine Learning apprende un singolo modello, ma spesso con rami internamente, per tutti gli elementi del set di dati e gli orizzonti di stima. Sono pertanto disponibili più dati per stimare i parametri del modello e la generalizzazione per la serie non visibile diventa possibile.

Gli esempi seguenti illustrano come:

* Preparare i dati per la modellazione delle serie temporali
* Configurare specifici parametri della serie temporale in un [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) oggetto
* Eseguire stime con dati di serie temporali

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

A differenza dei metodi di serie temporali classici, in i valori delle serie temporali di ML automatizzati vengono trasformati tramite pivot per diventare dimensioni aggiuntive per il regressore insieme ad altri predittori. Questo approccio incorpora più variabili contestuali e la relazione reciproca durante il training. Poiché più fattori possono influenzare una previsione, questo metodo si Allinea correttamente con scenari di previsione reali. Ad esempio, quando si prevedono le vendite, le interazioni delle tendenze cronologiche, il tasso di cambio e il prezzo comportano congiuntamente il risultato delle vendite. 

Le funzionalità estratte dai dati di training svolgono un ruolo fondamentale. Inoltre, Automated Machine Learning esegue i passaggi di pre-elaborazione standard e genera ulteriori funzionalità della serie temporale per acquisire gli effetti stagionali e ottimizzare la precisione predittiva

## <a name="time-series-and-deep-learning-models"></a>Modelli di serie temporali e Deep Learning


L'apprendimento avanzato di Machine Learning consente di prevedere i dati univariati e multivariati della serie temporale.

I modelli di apprendimento avanzato hanno tre funzionalità intrinseche:
1. Possono imparare da mapping arbitrari da input a output
1. Supportano più input e output
1. Possono estrarre automaticamente i modelli nei dati di input che si estendono su lunghe sequenze

Dato che i dati più grandi, i modelli di apprendimento avanzato, ad esempio ForecastTCN di Microsoft, possono migliorare i punteggi del modello risultante. Informazioni su come [configurare l'esperimento per l'apprendimento](#configure-a-dnn-enable-forecasting-experiment)avanzato.

Automatizzato ML fornisce agli utenti sia i modelli nativi che i modelli di apprendimento avanzato come parte del sistema di raccomandazione. 


Modelli| Description | Vantaggi
----|----|---
Profeta (anteprima)|Il profeta funziona meglio con le serie temporali con effetti stagionali forti e diverse stagioni di dati cronologici. | Accurato & rapido, affidabile per outlier, dati mancanti e modifiche radicali nelle serie temporali.
ARIMA automatica (anteprima)|La media mobili integrata (ARIMA, regressione) garantisce prestazioni ottimali quando i dati sono stazionari. Ciò significa che le proprietà statistiche come la media e la varianza sono costanti sull'intero set. Se, ad esempio, si capovolge una moneta, la probabilità di ottenere le testine è pari al 50%, indipendentemente dal fatto che venga invertita oggi, domani o il prossimo anno.| Ideale per le serie univariate, perché i valori precedenti vengono usati per stimare i valori futuri.
ForecastTCN (anteprima)| ForecastTCN è un modello di rete neurale progettato per affrontare le attività di previsione più complesse, acquisendo le tendenze locali e globali non lineari nei dati, nonché le relazioni tra le serie temporali.|In grado di sfruttare le tendenze complesse nei dati e di ridimensionare immediatamente il maggior numero di set di dati.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per creare l'area di lavoro, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).
* Questo articolo presuppone una conoscenza di base della configurazione di un esperimento di Machine Learning automatizzato. Seguire l' [esercitazione](tutorial-auto-train-models.md) o le [procedure](how-to-configure-auto-train.md) per visualizzare i modelli di progettazione degli esperimenti automatici di base di machine learning.

## <a name="preparing-data"></a>Preparazione dei dati

La differenza più importante tra un tipo di attività di regressione previsione e un tipo di attività di regressione all'interno di Machine Learning automatizzato consiste nell'includere una funzionalità nei dati che rappresenta una serie temporale valida. Una serie temporale regolare presenta una frequenza ben definita e coerente e presenta un valore in ogni punto di esempio in un intervallo di tempo continuo. Si consideri lo snapshot seguente `sample.csv`di un file.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Questo set di dati è un semplice esempio di dati di vendita giornalieri per una società che dispone di due archivi diversi, A e B. Inoltre, esiste `week_of_year` una funzionalità per che consentirà al modello di rilevare la stagionalità settimanale. Il campo `day_datetime` rappresenta una serie temporale pulita con frequenza giornaliera e il campo `sales_quantity` è la colonna di destinazione per l'esecuzione delle stime. Leggere i dati in un frame di dati Pandas, quindi `to_datetime` usare la funzione per assicurarsi che la serie `datetime` temporale sia un tipo.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In questo caso, i dati sono già ordinati in ordine crescente in base al `day_datetime`campo orario. Tuttavia, quando si configura un esperimento, verificare che la colonna di tempo desiderata venga ordinata in ordine crescente per compilare una serie temporale valida. Si supponga che i dati contengano 1.000 record e creino una suddivisione deterministica nei dati per creare set di dati di training e di test. Identificare il nome della colonna Label e impostarlo su Label. In questo esempio, l'etichetta sarà `sales_quantity`. Quindi separare il campo Label da `test_data` per formare il `test_target` set.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Quando si esegue il training di un modello per la previsione dei valori futuri, assicurarsi che tutte le funzionalità usate nel training possano essere usate quando si eseguono stime per l'orizzonte previsto. Ad esempio, durante la creazione di una previsione della domanda, inclusa una funzionalità per il prezzo azionario corrente, potrebbe aumentare notevolmente la precisione di training. Tuttavia, se si intende prevedere con un orizzonte lungo, potrebbe non essere possibile stimare in modo accurato i valori azionari futuri corrispondenti ai punti di serie temporali futuri e l'accuratezza del modello potrebbe soffrire.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Dati di training e convalida
È possibile specificare set di training e di convalida distinti direttamente `AutoMLConfig` nel costruttore.

### <a name="rolling-origin-cross-validation"></a>Convalida incrociata di origine in sequenza
Per la previsione delle serie temporali, la convalida incrociata di origine (ROCV) viene usata per suddividere le serie temporali in modo temporale coerente. ROCV divide la serie in dati di training e di convalida usando un punto di origine. Se si scorre l'origine nel tempo, vengono generate le riduzioni di convalida incrociata.  

![testo alternativo](./media/how-to-auto-train-forecast/ROCV.svg)

Questa strategia consente di mantenere l'integrità dei dati delle serie temporali ed eliminare il rischio di perdita dei dati. ROCV viene usato automaticamente per le attività di previsione passando i dati di training e di convalida insieme e impostando il numero di riduzioni `n_cross_validations`di convalida incrociata usando. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
Altre informazioni su [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Configurare ed eseguire l'esperimento

Per le attività di previsione, Machine Learning automatizzato USA operazioni di pre-elaborazione e di stima specifiche per i dati delle serie temporali. Verranno eseguiti i seguenti passaggi di pre-elaborazione:

* Rilevare la frequenza di campionamento della serie temporale (ad esempio, ogni ora, ogni giorno, ogni settimana) e creare nuovi record per i punti di tempo mancanti per rendere la serie continua.
* Imputare i valori mancanti nell'oggetto di destinazione (tramite il caricamento in diretta) e nelle colonne della funzionalità (usando i valori della colonna mediana)
* Creazione di funzionalità basate su granularità per consentire effetti fissi su diverse serie
* Creazione di funzionalità basate sul tempo per facilitare l'apprendimento di modelli stagionali
* Codificare variabili categoriche in quantità numeriche

L' [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) oggetto definisce le impostazioni e i dati necessari per un'attività automatica di machine learning. Analogamente a un problema di regressione, si definiscono parametri di training standard come il tipo di attività, il numero di iterazioni, i dati di training e il numero di convalide incrociate. Per le attività di previsione, è necessario impostare parametri aggiuntivi che interessano l'esperimento. La tabella seguente illustra ogni parametro e il relativo utilizzo.

| Nome&nbsp;parametro | Description | Obbligatoria |
|-------|-------|-------|
|`time_column_name`|Utilizzato per specificare la colonna DateTime nei dati di input utilizzati per compilare la serie temporale e dedurre la relativa frequenza.|✓|
|`grain_column_names`|Nome/i che definisce i singoli gruppi di serie nei dati di input. Se la granularità non è definita, si presuppone che il set di dati sia una serie temporale.||
|`max_horizon`|Definisce l'orizzonte di previsione massimo desiderato in unità di frequenza di serie temporali. Le unità sono basate sull'intervallo di tempo dei dati di training, ad esempio, ogni settimana, che il Forecaster deve prevedere.|✓|
|`target_lags`|Numero di righe in base ai valori di destinazione in base alla frequenza dei dati. Il ritardo è rappresentato come un elenco o un singolo Integer. È consigliabile usare lag quando la relazione tra le variabili indipendenti e la variabile dipendente non corrisponde o non è correlata per impostazione predefinita. Ad esempio, quando si tenta di prevedere la richiesta di un prodotto, la richiesta in ogni mese può dipendere dal prezzo di prodotti specifici 3 mesi prima. In questo esempio, è possibile che si desideri ritardare la destinazione (richiesta) negativamente di 3 mesi, in modo che il modello sia in grado di eseguire il training sulla relazione corretta.||
|`target_rolling_window_size`|*n* periodi cronologici da usare per generare valori previsti, <= dimensioni del set di training. Se omesso, *n* è la dimensione massima del set di training. Specificare questo parametro quando si desidera considerare solo una certa quantità di cronologia durante il training del modello.||
|`enable_dnn`|Abilitare DNN di previsione.||

Per ulteriori informazioni, vedere la [documentazione di riferimento](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Creare le impostazioni della serie temporale come oggetto Dictionary. Impostare sul `time_column_name` `day_datetime` campo nel set di dati. Definire il `grain_column_names` parametro per assicurarsi che vengano creati **due gruppi di serie temporali distinti** per i dati. uno per i negozi A e B. Infine, impostare `max_horizon` su 50 per stimare l'intero set di test. Impostare una finestra di previsione su 10 periodi `target_rolling_window_size`con e specificare un singolo ritardo sui valori di destinazione per due punti avanti con il `target_lags` parametro. È consigliabile impostare `max_horizon` `target_rolling_window_size` e `target_lags` su "auto" per rilevare automaticamente questi valori. Nell'esempio seguente sono state usate le impostazioni "auto" per questi parametri. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> I passaggi di pre-elaborazione di Machine Learning automatizzati (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione di valori di testo nel formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di pre-elaborazione applicati durante il training vengono automaticamente applicati ai dati di input.

Definendo `grain_column_names` nel frammento di codice precedente, AutoML creerà due gruppi di serie temporali distinti, noti anche come più serie temporali. Se non è definito alcun oggetto Grain, AutoML presuppone che il set di dati sia una singola serie temporale. Per ulteriori informazioni sulle singole serie temporali, vedere la [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

A questo punto, `AutoMLConfig` creare un oggetto standard `forecasting` , specificando il tipo di attività e inviare l'esperimento. Al termine del modello, recuperare l'iterazione di esecuzione migliore.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Vedere i [notebook di esempio di previsione](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) per esempi di codice dettagliati sulla configurazione di previsione avanzata, tra cui:

* [rilevamento festività e conteggi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [convalida incrociata di origine in sequenza](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [ritardi configurabili](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [funzionalità di aggregazione della finestra in sequenza](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Configurare un esperimento di abilitazione della previsione di DNN

> [!NOTE]
> Il supporto di DNN per la previsione nei Machine Learning automatici è in anteprima e non è supportato per le esecuzioni locali.

Per sfruttare DNN per la previsione, è necessario impostare il `enable_dnn` parametro in AutoMLConfig su true. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Altre informazioni su [AutoMLConfig](#configure-and-run-experiment).

In alternativa, è possibile selezionare l' `Enable deep learning` opzione in studio.
![testo alternativo](./media/how-to-auto-train-forecast/enable_dnn.png)

È consigliabile usare un cluster di calcolo AML con SKU GPU e almeno due nodi come destinazione di calcolo. Per consentire un tempo sufficiente per il completamento del training DNN, è consigliabile impostare il timeout dell'esperimento su un minimo di un paio di ore.
Per altre informazioni sulle dimensioni delle macchine virtuali e di calcolo di AML che includono GPU, vedere la documentazione di [calcolo AML](how-to-set-up-training-targets.md#amlcompute) e le [dimensioni delle macchine virtuali ottimizzate per GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Visualizzare il [notebook della previsione di produzione delle bevande](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) per un esempio di codice dettagliato con DNN.

### <a name="target-rolling-window-aggregation"></a>Aggregazione finestra in sequenza di destinazione
Spesso le informazioni migliori che un Forecaster può avere è il valore recente della destinazione. La creazione di statistiche cumulative della destinazione può aumentare l'accuratezza delle stime. Le aggregazioni della finestra di destinazione in sequenza consentono di aggiungere un'aggregazione in sequenza di valori di dati come funzionalità. Per abilitare le finestre in sequenza di `target_rolling_window_size` destinazione impostare sulla dimensione della finestra Integer desiderata. 

Un esempio può essere visualizzato quando si stima la domanda di energia. È possibile aggiungere una funzionalità della finestra in sequenza di tre giorni per tenere conto delle modifiche termiche degli spazi riscaldati. Nell'esempio seguente è stata creata questa finestra di dimensioni tre impostando `target_rolling_window_size=3` nel `AutoMLConfig` costruttore. La tabella mostra la progettazione delle funzionalità che si verifica quando viene applicata l'aggregazione di finestre. Le colonne per Minimum, Maximum e Sum vengono generate in una finestra temporale scorrevole di tre in base alle impostazioni definite. Ogni riga ha una nuova funzionalità calcolata, nel caso del timestamp per l'8 settembre 2017 4:00 i valori massimo, minimo e Sum vengono calcolati usando i valori della richiesta per l'8 settembre 2017 1:00-3:00. Questa finestra di tre turni per popolare i dati per le righe rimanenti.

![testo alternativo](./media/how-to-auto-train-forecast/target-roll.svg)

La generazione e l'utilizzo di queste funzionalità aggiuntive come dati contestuali aggiuntivi facilitano l'accuratezza del modello di training.

Visualizzare un esempio di codice Python sfruttando la [funzionalità di aggregazione della finestra in sequenza di destinazione](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="view-feature-engineering-summary"></a>Visualizza riepilogo Progettazione funzionalità

Per i tipi di attività delle serie temporali in automazione automatica, è possibile visualizzare i dettagli del processo di progettazione delle funzionalità. Il codice seguente mostra ogni funzionalità non elaborata con gli attributi seguenti:

* Nome della funzionalità non elaborata
* Numero di funzionalità progettate da questa funzionalità non elaborata
* Tipo rilevato
* Indica se la funzionalità è stata eliminata
* Elenco di trasformazioni di funzionalità per la funzionalità RAW

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Previsione con il modello migliore

Utilizzare l'iterazione del modello migliore per prevedere i valori per il set di dati di test.

La `forecast()` funzione deve essere utilizzata al posto `predict()`di, in modo da consentire le specifiche di quando le stime devono essere avviate. Nell'esempio seguente vengono innanzitutto sostituiti tutti i valori in `y_pred` con `NaN`. In questo caso, l'origine della previsione sarà alla fine dei dati di training, come in genere quando si usa `predict()`. Tuttavia, se si sostituisce solo la seconda metà di `y_pred` con `NaN`, la funzione lascia i valori numerici nella prima metà non modificati, ma prevede i `NaN` valori nella seconda metà. La funzione restituisce i valori previsti e le funzionalità allineate.

È anche possibile usare il `forecast_destination` parametro nella `forecast()` funzione per prevedere i valori fino a una data specificata.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcolare valori RMSE (radice errore quadratico medio) tra i `actual_labels` valori effettivi e i valori previsti in `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Ora che è stata determinata l'accuratezza del modello complessiva, il passaggio successivo più realistico consiste nell'usare il modello per prevedere valori futuri sconosciuti. Fornire un set di dati nello stesso formato del set `test_data` di test ma con DateTime futuri e il set di stime risultante corrisponde ai valori previsti per ogni passaggio della serie temporale. Si supponga che gli ultimi record di serie temporali nel set di dati siano per 12/31/2018. Per prevedere la domanda del giorno successivo (o il numero di periodi in cui è necessario prevedere, <`max_horizon`=), creare un singolo record della serie temporale per ogni negozio per 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Ripetere i passaggi necessari per caricare i dati futuri in un dataframe, quindi eseguire `best_run.predict(test_data)` per stimare i valori futuri.

> [!NOTE]
> Non è possibile stimare i valori per il `max_horizon`numero di periodi maggiore di. È necessario rieseguire il training del modello con un orizzonte più ampio per stimare i valori futuri oltre l'orizzonte corrente.

## <a name="next-steps"></a>Passaggi successivi

* Seguire l' [esercitazione](tutorial-auto-train-models.md) per imparare a creare esperimenti con Machine Learning automatizzato.
* Visualizzare la documentazione [di riferimento di Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
