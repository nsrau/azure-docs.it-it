---
title: Eseguire il training automatico di un modello previsionale di serie temporaliAuto-train a time-series forecast model
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning per eseguire il training di un modello di regressione delle previsioni di serie temporali usando l'apprendimento automatico automatizzato.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: be3046a343e14be4a527363751081ba3f2593cd3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605883"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Eseguire il training automatico di un modello previsionale di serie temporaliAuto-train a time-series forecast model
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In this article, you learn how to train a time-series forecasting regression model using automated machine learning in Azure Machine Learning. La configurazione di un modello di previsione è simile all'impostazione di un modello di regressione standard tramite l'apprendimento automatico automatico, ma esistono alcune opzioni di configurazione e passaggi di pre-elaborazione per l'utilizzo di dati di serie temporali. Negli esempi seguenti viene illustrato come:The following examples show you how to:

* Preparare i dati per la modellazione di serie temporaliPrepare data for time series modeling
* Configurare parametri specifici di [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) serie temporali in un oggetto
* Eseguire stime con dati di serie temporaliRun predictions with time-series data

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

È possibile utilizzare ML automatizzato per combinare tecniche e approcci e ottenere una previsione di serie temporali consigliata e di alta qualità. Un esperimento automatizzato di serie temporali viene considerato come un problema di regressione multivariato. I valori delle serie temporali passati sono "pivoted" per diventare dimensioni aggiuntive per il regressore insieme ad altri predittori.

Questo approccio, a differenza dei metodi classici di serie temporali, ha il vantaggio di incorporare naturalmente più variabili contestuali e la loro relazione tra loro durante il training. Nelle applicazioni di previsione reali, più fattori possono influenzare una previsione. Ad esempio, quando si prevedono le vendite, le interazioni delle tendenze storiche, del tasso di cambio e del prezzo guidano congiuntamente il risultato delle vendite. Un ulteriore vantaggio è che tutte le recenti innovazioni nei modelli di regressione si applicano immediatamente alle previsioni.

È possibile [configurare](#config) la distanza nel futuro che la previsione deve estendere (l'orizzonte di previsione), nonché ritardi e altro ancora. L'infrastruttura ML automatizzata apprende un singolo modello ramificato internamente per tutti gli elementi nel set di dati e negli orizzonti di stima. Diventa quindi possibile un numero maggiore di dati per stimare i parametri del modello e la generalizzazione in serie invisibili.

Le funzionalità estratte dai dati di training svolgono un ruolo fondamentale. Inoltre, ML automatizzato esegue procedure di pre-elaborazione standard e genera funzionalità aggiuntive di serie temporali per catturare gli effetti stagionali e massimizzare la precisione predittiva.

## <a name="time-series-and-deep-learning-models"></a>Modelli di serie temporali e Deep Learning


L'ML automatizzato fornisce agli utenti sia modelli di serie temporali native che modelli di deep learning come parte del sistema di raccomandazione. Questi studenti includono:
+ Profeta
+ ARIMA automatico
+ PrevistoTCN

Il deep learning automatizzato di ML consente di prevedere dati di serie temporali univariati e multivariati.

I modelli di deep learning hanno tre funzionalità intrinseche:Deep learning models have three intrinsic capabilities:
1. Possono imparare da mapping arbitrari dagli input agli output
1. Supportano più ingressi e uscite
1. Possono estrarre automaticamente i modelli nei dati di input che si estendono su sequenze lunghe

Dati più grandi, i modelli di deep learning, ad esempio ForecastTCN di Microsoft, possono migliorare i punteggi del modello risultante. 

Gli allievi nativi di serie temporali sono anche forniti come parte di ML automatizzato. Profeta funziona meglio con serie temporali che hanno forti effetti stagionali e diverse stagioni di dati storici. Profeta è preciso & veloce, robusto agli outlier, dati mancanti e cambiamenti drammatici nella tua serie temporale. 

AutoRegressive Integrated Moving Average (ARIMA) è un metodo statistico popolare per la previsione di serie temporali. Questa tecnica di previsione è comunemente utilizzata in scenari di previsione a breve termine in cui i dati mostrano tendenze come i cicli, che possono essere imprevedibili e difficili da modellare o prevedere. Auto-ARIMA trasforma i dati in dati fissi per ottenere risultati coerenti e affidabili.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per creare l'area di lavoro, vedere [Creare un'area](how-to-manage-workspace.md)di lavoro di Azure Machine Learning.
* Questo articolo presuppone una familiarità di base con la configurazione di un esperimento di apprendimento automatico. Seguire [l'esercitazione](tutorial-auto-train-models.md) o [le procedure](how-to-configure-auto-train.md) per visualizzare i modelli di progettazione di esperimenti di apprendimento automatico automatizzati di base.

## <a name="preparing-data"></a>Preparazione dei dati

La differenza più importante tra un tipo di attività di regressione di previsione e un tipo di attività di regressione all'interno dell'apprendimento automatico consiste nell'inclusione di una funzionalità nei dati che rappresenta una serie temporale valida. Una serie temporale regolare ha una frequenza ben definita e coerente e ha un valore in ogni punto campione in un intervallo di tempo continuo. Si consideri la `sample.csv`seguente istantanea di un file .

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

Questo set di dati è un semplice esempio di dati di vendita giornalieri per una società `week_of_year` che dispone di due punti vendita diversi, A e B. Inoltre, è disponibile una funzionalità per che consentirà al modello di rilevare la stagionalità settimanale. Il `day_datetime` campo rappresenta una serie temporale pulita `sales_quantity` con frequenza giornaliera e il campo è la colonna di destinazione per l'esecuzione delle stime. Leggere i dati in un frame di `to_datetime` dati Pandas, quindi `datetime` utilizzare la funzione per assicurarsi che la serie temporale sia un tipo.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In questo caso, i dati sono già ordinati `day_datetime`in ordine crescente in base al campo temporale . Tuttavia, quando si imposta un esperimento, assicurarsi che la colonna temporale desiderata sia ordinata in ordine crescente per creare una serie temporale valida. Si supponga che i dati contengano 1.000 record e che si verifichi una divisione deterministica nei dati per creare set di dati di training e test. Identificare il nome della colonna dell'etichetta e impostarlo su label. In questo esempio, l'etichetta sarà `sales_quantity`. Quindi separare il `test_data` campo `test_target` etichetta da per formare il set.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Quando si esegue il training di un modello per la previsione dei valori futuri, assicurarsi che tutte le funzionalità usate nel training possano essere usate durante l'esecuzione delle stime per l'orizzonte previsto. Ad esempio, quando si crea una previsione della domanda, l'inclusione di una funzionalità per il prezzo corrente delle azioni potrebbe aumentare enormemente l'accuratezza dell'allenamento. Tuttavia, se si intende prevedere con un orizzonte lungo, potrebbe non essere possibile prevedere con precisione i valori azionari futuri corrispondenti ai punti di serie temporali futuri e l'accuratezza del modello potrebbe risentirne.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Configurare ed eseguire l'esperimento

Per le attività di previsione, l'apprendimento automatico usa i passaggi di pre-elaborazione e stima specifici dei dati di serie temporali. Verranno eseguiti i seguenti passaggi di pre-elaborazione:

* Rileva la frequenza dei campioni di serie temporali (ad esempio, oraria, giornaliera, settimanale) e crea nuovi record per i punti temporali assenti per rendere la serie continua.
* Impute valori mancanti nella destinazione (tramite forward-fill) e colonne di entità geografiche (utilizzando valori di colonna mediani)
* Creare funzioni basate su granuloper abilitare effetti fissi in diverse serie
* Creare funzionalità basate sul tempo per facilitare l'apprendimento dei modelli stagionali
* Codificare le variabili di categoria in quantità numeriche

L'oggetto [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) definisce le impostazioni e i dati necessari per un'attività di apprendimento automatico. Analogamente a un problema di regressione, si definiscono parametri di training standard come il tipo di attività, il numero di iterazioni, i dati di training e il numero di convalide incrociate. Per le attività di previsione, è necessario impostare parametri aggiuntivi che influiscono sull'esperimento. Nella tabella seguente vengono illustrati ogni parametro e il relativo utilizzo.

| Nome&nbsp;parametro | Descrizione | Obbligatoria |
|-------|-------|-------|
|`time_column_name`|Utilizzato per specificare la colonna datetime nei dati di input utilizzati per la creazione della serie temporale e la relativa frequenza.|✓|
|`grain_column_names`|Nome/i che definisce i singoli gruppi di serie nei dati di input. Se il grano non è definito, si presuppone che il set di dati sia una serie temporale.||
|`max_horizon`|Definisce l'orizzonte di previsione massimo desiderato in unità di frequenza della serie temporale. Le unità si basano sull'intervallo di tempo dei dati di training, ad esempio mensile, settimanale che l'addetto alle previsioni deve prevedere.|✓|
|`target_lags`|Numero di righe in ritardo tra i valori di destinazione in base alla frequenza dei dati. Il ritardo è rappresentato come un elenco o un singolo numero intero. Il ritardo deve essere utilizzato quando la relazione tra le variabili indipendenti e le variabili dipendenti non corrisponde o correla per impostazione predefinita. Ad esempio, quando si tenta di prevedere la domanda di un prodotto, la domanda in un mese può dipendere dal prezzo di materie prime specifiche 3 mesi prima. In questo esempio, è possibile ritardare negativamente l'obiettivo (domanda) di 3 mesi in modo che il modello eseguo la relazione corretta.||
|`target_rolling_window_size`|*n* periodi storici da utilizzare per generare valori previsti, <: dimensioni del set di training. Se omesso, *n* è la dimensione completa del set di training. Specificare questo parametro quando si desidera considerare solo una certa quantità di cronologia durante il training del modello.||
|`enable_dnn`|Abilitare i DN di previsione.||

Per ulteriori informazioni, vedere la [documentazione di riferimento.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)

Creare le impostazioni della serie temporale come oggetto dizionario. Impostare `time_column_name` il `day_datetime` campo sul campo nel set di dati. Definire `grain_column_names` il parametro per garantire che vengano creati due gruppi di **serie temporali separati** per i dati; uno per il negozio A e `max_horizon` B. Infine, impostare il a 50 al fine di prevedere per l'intero set di test. Impostare una finestra di `target_rolling_window_size`previsione su 10 periodi con e specificare `target_lags` un singolo ritardo sui valori di destinazione per due periodi precedenti con il parametro . Si consiglia di `max_horizon` `target_rolling_window_size` impostare , e `target_lags` su "auto" che rileverà automaticamente questi valori per voi. Nell'esempio seguente, le impostazioni "auto" sono state utilizzate per questi parametri. 

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

Definendo `grain_column_names` il nel frammento di codice precedente, AutoML creerà due gruppi di serie temporali separati, noti anche come serie temporale multiple. Se non è definita alcuna granularità, AutoML presupporrà che il set di dati sia una singola serie temporale. Per ulteriori informazioni sulle singole serie temporali, vedere la [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Creare ora `AutoMLConfig` un oggetto standard, specificando il `forecasting` tipo di attività e inviare l'esperimento. Al termine del modello, recuperare l'iterazione di esecuzione migliore.

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

Vedere i blocchi appunti di esempio di previsione per esempi di codice dettagliati della configurazione di previsione avanzata, tra cui:See the [forecasting sample notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) for detailed code examples of advanced forecasting configuration including:

* [rilevamento e featurizzazione delle vacanze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [convalida incrociata dell'origine continua](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [ritardi configurabili](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [funzionalità di aggregazione per finestre in sequenza](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Configurare un esperimento di previsione DNNConfigure a DNN enable Forecasting experiment

> [!NOTE]
> Il supporto DNN per le previsioni in Automated Machine Learning è in anteprima e non è supportato per le esecuzioni locali.

Per sfruttare i DNN per la previsione, `enable_dnn` è necessario impostare il parametro in AutoMLConfig su true. 

È consigliabile usare un cluster AML Compute con SKU GPU e almeno due nodi come destinazione di calcolo. Per concedere tempo sufficiente per il completamento del training DNN, è consigliabile impostare il timeout dell'esperimento su un minimo di un paio d'ore.
Per altre informazioni sul calcolo AML e sulle dimensioni delle macchine virtuali che includono GPU, vedere la documentazione di [AML Compute](how-to-set-up-training-targets.md#amlcompute) e la documentazione sulle [dimensioni delle macchine virtuali ottimizzate per GPU.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)

Visualizzare il [blocco appunti Previsione produzione bevande](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) per un esempio di codice dettagliato sfruttando DNN.

### <a name="view-feature-engineering-summary"></a>Visualizzare il riepilogo della progettazione delle funzionalità

Per i tipi di attività di serie temporali nell'apprendimento automatico automatico, è possibile visualizzare i dettagli del processo di progettazione delle funzionalità. Il codice seguente mostra ogni funzionalità non elaborata insieme agli attributi seguenti:The following code shows each raw feature along with the following attributes:

* Nome della funzione non elaborata
* Numero di funzioni ingegnerizzate formate da questa funzione grezza
* Tipo rilevato
* Se la funzionalità è stata eliminata
* Elenco delle trasformazioni di entità geografiche per la feature non elaborata

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Previsione con il miglior modello

Usare l'iterazione del modello migliore per prevedere i valori per il set di dati di test.

La `forecast()` funzione deve essere `predict()`utilizzata al posto di , in questo modo le specifiche di quando le stime devono iniziare. Nell'esempio seguente, prima si `y_pred` sostituiscono tutti i valori in con `NaN`. In questo caso, l'origine prevista sarà alla fine dei `predict()`dati di training, come sarebbe normalmente quando si usa . Tuttavia, se si sostituisse solo la seconda metà di `y_pred` con `NaN`, la funzione `NaN` lascerà i valori numerici nella prima metà non modificati, ma prevede i valori nella seconda metà. La funzione restituisce sia i valori previsti che le feature allineate.

È inoltre possibile `forecast_destination` utilizzare `forecast()` il parametro nella funzione per prevedere i valori fino a una data specificata.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcolare RMSE (errore al quadrato `actual_labels` principale) tra i valori `predict_labels`effettivi e i valori previsti in .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Ora che è stata determinata l'accuratezza complessiva del modello, il passaggio successivo più realistico consiste nell'utilizzare il modello per prevedere valori futuri sconosciuti. Fornire un set di dati nello `test_data` stesso formato del set di test, ma con datetime futuri e il set di stima risultante è i valori previsti per ogni passaggio della serie temporale. Si supponga che gli ultimi record di serie temporali nel set di dati siano stati per 31/12/2018. Per prevedere la domanda per il giorno successivo (o il `max_horizon`numero di periodi che è necessario prevedere, <) creare un singolo record di serie temporali per ogni punto vendita per 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Ripetere i passaggi necessari per caricare i dati `best_run.predict(test_data)` futuri in un frame di dati e quindi eseguire per stimare i valori futuri.

> [!NOTE]
> Non è possibile prevedere valori per `max_horizon`numero di periodi maggiori di . Il modello deve essere ri-addestrato con un orizzonte più ampio per prevedere i valori futuri oltre l'orizzonte corrente.

## <a name="next-steps"></a>Passaggi successivi

* Seguire [l'esercitazione](tutorial-auto-train-models.md) per informazioni su come creare esperimenti con l'apprendimento automatico automatizzato.
* Visualizzare la documentazione di riferimento di [Azure Machine Learning SDK for Python.View](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) the Azure Machine Learning SDK for Python reference documentation.
