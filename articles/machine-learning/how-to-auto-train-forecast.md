---
title: Eseguire il training automatico di un modello di previsione di una serie temporale
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning per eseguire il training di un modello di regressione di previsione di una serie temporale usando Machine Learning automatizzato.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1, automl
ms.date: 08/20/2020
ms.openlocfilehash: 57b54fbe20df4eb74ee17c7b5ac83d773114463b
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437372"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Eseguire il training automatico di un modello di previsione di una serie temporale


Questo articolo illustra come configurare ed eseguire il training di un modello di regressione di previsione di serie temporali usando Machine Learning automatizzato, AutoML, in [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

A tale scopo, è necessario: 

> [!div class="checklist"]
> * Preparare i dati per la modellazione delle serie temporali.
> * Configurare specifici parametri della serie temporale in un [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) oggetto.
> * Eseguire stime con dati di serie temporali.

Per un'esperienza di codice ridotta, vedere [Esercitazione: Prevedere la domanda con Machine Learning automatizzato](tutorial-automated-ml-forecast.md) per un esempio di previsione di una serie temporale usando Machine Learning automatizzato in [Azure Machine Learning Studio](https://ml.azure.com/).

A differenza dei metodi di serie temporali classici, in Machine Learning, i valori della serie temporale precedenti sono "pivoted" per diventare dimensioni aggiuntive per il regressore insieme ad altri predittori. Questo approccio incorpora più variabili contestuali e la relazione reciproca durante il training. Poiché più fattori possono influenzare una previsione, questo metodo si allinea perfettamente con scenari di previsione reali. Ad esempio, quando si prevedono le vendite, le interazioni delle tendenze cronologiche, il tasso di cambio e il prezzo comportano congiuntamente il risultato delle vendite. 

## <a name="prerequisites"></a>Prerequisiti

Per questo articolo è necessario, 

* Un'area di lavoro di Azure Machine Learning. Per creare l'area di lavoro, vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

* Questo articolo presuppone una certa familiarità con la configurazione di un esperimento di Machine Learning automatizzato. Seguire l' [esercitazione](tutorial-auto-train-models.md) o le [procedure](how-to-configure-auto-train.md) per visualizzare i modelli di progettazione dell'esperimento automatizzato di machine learning.

## <a name="preparing-data"></a>Preparazione dei dati

La differenza più importante tra un tipo di attività di regressione previsione e un tipo di attività di regressione all'interno di AutoML è l'inclusione di una funzionalità nei dati che rappresenta una serie temporale valida. Una serie temporale regolare presenta una frequenza ben definita e coerente e un valore in ogni punto di esempio in un intervallo di tempo continuo. 

Si consideri lo snapshot seguente di un file `sample.csv`.
Questo set di dati è costituito da dati di vendita giornalieri per una società con due archivi diversi, A e B. 

Sono inoltre disponibili funzionalità per

 *  `week_of_year`: consente al modello di rilevare la stagionalità settimanale.
* `day_datetime`: rappresenta una serie temporale pulita con frequenza giornaliera.
* `sales_quantity`: colonna di destinazione per l'esecuzione di stime. 

```output
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
```


Leggere i dati in un frame di dati Pandas e quindi usare la funzione `to_datetime` per assicurarsi che la serie temporale sia di tipo `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In questo caso, i dati sono già ordinati in ordine crescente in base al campo dell'ora `day_datetime`. Tuttavia, quando si configura un esperimento, verificare che la colonna temporale desiderata venga ordinata in ordine crescente per compilare una serie temporale valida. 

Il codice seguente, 
* Presuppone che i dati contengano 1.000 record e crei una suddivisione deterministica nei dati per creare set di dati di training e di test. 
* Identifica la colonna di etichetta come `sales_quantity` .
* Separa il campo etichetta da `test_data` per formare il `test_target` set.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> Quando si esegue il training di un modello per la previsione di valori futuri, assicurarsi che tutte le funzionalità usate nel training possano essere usate quando si eseguono stime per l'orizzonte previsto. <br> <br>Ad esempio, durante la creazione di una previsione della richiesta, includere una funzionalità per il prezzo dei titoli azionari corrente potrebbe aumentare notevolmente l'accuratezza del training. Se, tuttavia, si vogliono effettuare previsioni con un orizzonte lungo, potrebbe non essere possibile stimare in modo accurato i valori dei titoli azionari futuri corrispondenti ai punti futuri della serie temporale e l'accuratezza del modello potrebbe risentirne.

<a name="config"></a>

## <a name="training-and-validation-data"></a>Dati di training e di convalida

È possibile specificare set di training e di convalida distinti direttamente nell' `AutoMLConfig` oggetto.   Altre informazioni sull'oggetto [AutoMLConfig](#configure-experiment).

Per la previsione della serie temporale, per impostazione predefinita viene usata solo la **convalida incrociata di origine (ROCV) in sequenza** . Passare i dati di training e di convalida insieme e impostare il numero di riduzioni di convalida incrociata con il `n_cross_validations` parametro in `AutoMLConfig` . La convalida incrociata dell'origine in sequenza divide la serie in dati di training e convalida usando un punto di origine. Scorrere l'origine nel tempo comporta la generazione di riduzioni della convalida incrociata. Questa strategia consente di mantenere l'integrità dei dati delle serie temporali ed Elimina il rischio di perdita dei dati

![convalida incrociata di origine in sequenza](./media/how-to-auto-train-forecast/ROCV.svg)

È anche possibile importare i propri dati di convalida. per altre informazioni, vedere [configurare le suddivisioni dei dati e la convalida incrociata in AutoML](how-to-configure-cross-validation-data-splits.md#provide-validation-data).


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

Altre informazioni sul modo in cui AutoML applica la convalida incrociata per [prevenire i modelli di overfitting](concept-manage-ml-pitfalls.md#prevent-over-fitting).

## <a name="configure-experiment"></a>Configurare l'esperimento

L'oggetto [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) definisce le impostazioni e i dati necessari per un'attività di Machine Learning automatizzato. La configurazione di un modello di previsione è simile alla configurazione di un modello di regressione standard, ma alcuni modelli, le opzioni di configurazione e i passaggi di conteggi esistono in modo specifico per i dati delle serie temporali. 

### <a name="supported-models"></a>Modelli supportati
Il Machine Learning automatico tenta di creare automaticamente modelli e algoritmi diversi come parte del processo di creazione e ottimizzazione del modello. Come utente, non è necessario specificare l'algoritmo. Per gli esperimenti di previsione, le serie temporali native e i modelli di apprendimento avanzato fanno parte del sistema di raccomandazione. Nella tabella seguente viene riepilogato Questo subset di modelli. 

>[!Tip]
> I modelli di regressione tradizionali vengono inoltre testati come parte del sistema di raccomandazione per la previsione degli esperimenti. Per l'elenco completo dei modelli, vedere la [tabella del modello supportata](how-to-configure-auto-train.md#supported-models) . 

Modelli| Descrizione | Vantaggi
----|----|---
Prophet (anteprima)|Il modello Prophet funziona meglio con le serie temporali con effetti stagionali forti e diverse stagioni di dati cronologici. Per utilizzare questo modello, installarlo localmente utilizzando `pip install fbprophet` . | Accuratezza, rapidità, affidabilità per outlier, dati mancanti e modifiche radicali nella serie temporale.
Auto-ARIMA (anteprima)|La media mobili integrata di regressione automatica (ARIMA) garantisce prestazioni ottimali quando i dati sono stazionari. Ciò significa che le proprietà statistiche, come la media e la varianza, sono costanti per l'intero set. Se, ad esempio, si capovolge una moneta, la probabilità di ottenere le testine è pari al 50%, indipendentemente dal fatto che si Capovolgi oggi, domani o il prossimo anno.| Ideale per le serie univariate, perché i valori precedenti vengono usati per stimare i valori futuri.
ForecastTCN (anteprima)| ForecastTCN è un modello di rete neurale progettato per affrontare le attività di previsione più complesse, acquisendo le tendenze locali e globali non lineari nei dati, nonché le relazioni tra le serie temporali.|Capacità di sfruttare le tendenze complesse nei dati e di ridimensionare immediatamente nel set di dati di dimensioni maggiori.

### <a name="configuration-settings"></a>Impostazioni di configurazione

Analogamente a un problema di regressione, si definiscono parametri di training standard, come il tipo di attività, il numero di iterazioni, i dati di training e il numero di convalide incrociate. Per le attività di previsione, è necessario impostare parametri aggiuntivi che interessano l'esperimento. 

Nella tabella seguente vengono riepilogati questi parametri aggiuntivi. Per informazioni sui modelli di progettazione della sintassi, vedere la [documentazione di riferimento della classe ForecastingParameter](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) .

| Nome&nbsp;parametro | Descrizione | Obbligatoria |
|-------|-------|-------|
|`time_column_name`|Usato per specificare la colonna datetime nei dati di input usati per compilare la serie temporale e dedurne la frequenza.|✓|
|`forecast_horizon`|Definisce il numero di periodi in cui si desidera prevedere la previsione. L'orizzonte è in unità della frequenza della serie temporale. Le unità si basano sull'intervallo temporale dei dati di training, ad esempio mensile o settimanale, che il modulo di previsione deve prevedere.|✓|
|`enable_dnn`|[Abilitare DNN di previsione]().||
|`time_series_id_column_names`|Il nome o i nomi di colonna utilizzati per identificare in modo univoco la serie temporale nei dati che hanno più righe con lo stesso timestamp. Se gli identificatori delle serie temporali non sono definiti, si presuppone che il set di dati sia una serie temporale. Per altre informazioni sulle singole serie temporali, vedere [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).||
|`freq`| Frequenza del set di dati della serie temporale. Questo parametro rappresenta il periodo in cui si prevede che gli eventi si verifichino, ad esempio giornaliero, settimanale, annuale e così via. La frequenza deve essere un [alias di offset Pandas](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects).||
|`target_lags`|Numero di righe riferite al ritardo dei valori di destinazione in base alla frequenza dei dati. Il ritardo è rappresentato come un elenco o un singolo Integer. È consigliabile usare il ritardo quando la relazione tra le variabili indipendenti e la variabile dipendente non corrisponde o non è correlata per impostazione predefinita. ||
|`feature_lags`| Le funzionalità da ritardare verranno automaticamente decise da Machine Learning automatiche quando `target_lags` vengono impostate e `feature_lags` sono impostate su `auto` . L'abilitazione di ritardi delle funzionalità può contribuire a migliorare la precisione. I ritardi delle funzionalità sono disabilitati per impostazione predefinita. ||
|`target_rolling_window_size`|*n* periodi cronologici da usare per generare valori previsti, < = dimensioni del set di training. Se omesso, *n* è la dimensione massima del set di training. Specificare questo parametro quando si vuole considerare solo una certa quantità di dati cronologici durante il training del modello. Altre informazioni sull' [aggregazione delle finestre in sequenza di destinazione](#target-rolling-window-aggregation).||
|`short_series_handling_config`| Consente la gestione rapida delle serie temporali per evitare errori durante il training a causa di dati insufficienti. La gestione delle serie brevi è impostata su per `auto` impostazione predefinita. Altre informazioni sulla [gestione della serie breve](#short-series-handling).|


Il codice seguente, 
* Sfrutta la [`ForecastingParameters`](https://docs.microsoft.com/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) classe per definire i parametri di previsione per il training dell'esperimento
* Imposta l'oggetto sul `time_column_name` `day_datetime` campo nel set di dati. 
* Definisce il `time_series_id_column_names` parametro per `"store"` . In questo modo si garantisce che vengano creati **due gruppi di serie temporali distinti** per i dati. uno per i negozi A e B.
* Imposta `forecast_horizon` su 50 per stimare l'intero set di test. 
* Imposta una finestra di previsione su 10 punti con `target_rolling_window_size`
* Specifica un singolo ritardo sui valori di destinazione per due periodi più avanti con il `target_lags` parametro. 
* Imposta `target_lags` sull'impostazione "auto" consigliata, che consente di rilevare automaticamente questo valore.

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

Questi `forecasting_parameters` vengono quindi passati nell'oggetto standard `AutoMLConfig` insieme al tipo di `forecasting` attività, alla metrica primaria, ai criteri di uscita e ai dati di training. 

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
                             **forecasting_parameters)
```

### <a name="featurization-steps"></a>Procedura conteggi

In ogni esperimento di Machine Learning automatizzato, le tecniche di ridimensionamento automatico e di normalizzazione vengono applicate ai dati per impostazione predefinita. Queste tecniche sono tipi di **conteggi** che aiutano *determinati* algoritmi sensibili alle funzionalità su scale diverse. Altre informazioni sui passaggi predefiniti di conteggi in [conteggi in AutoML](how-to-configure-auto-features.md#automatic-featurization)

Tuttavia, i passaggi seguenti vengono eseguiti solo per i `forecasting` tipi di attività:

* Rilevare la frequenza di campionamento della serie temporale (ad esempio, ogni ora, giorno, settimana) e creare nuovi record per i momenti mancanti per rendere la serie continua
* Imputare i valori mancanti nell'oggetto di destinazione (tramite il caricamento in diretta) e nelle colonne di funzioni (usando i valori della colonna mediana)
* Creazione di funzionalità basate su identificatori di serie temporali per consentire effetti fissi su diverse serie
* Creare funzionalità basate sul tempo per facilitare l'apprendimento di modelli stagionali
* Codificare variabili categoriche su quantità numeriche

Per ottenere un riepilogo delle funzionalità create come risultato di questi passaggi, vedere [conteggi Transparency](how-to-configure-auto-features.md#featurization-transparency)

> [!NOTE]
> I passaggi di definizione delle funzionalità di Machine Learning automatizzato (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione dei valori di testo in formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di definizione delle funzionalità applicati durante il training vengono automaticamente applicati ai dati di input.

#### <a name="customize-featurization"></a>Personalizzare conteggi

È anche possibile personalizzare le impostazioni di conteggi per assicurarsi che i dati e le funzionalità usati per il training del modello ML provochino stime pertinenti. 

Le personalizzazioni supportate per le `forecasting` attività includono:

|Personalizzazione|Definizione|
|--|--|
|**Aggiornamento dello scopo della colonna**|Eseguire l'override del tipo di funzionalità rilevato automaticamente per la colonna specificata.|
|**Aggiornamento dei parametri del trasformatore** |Aggiornare i parametri per il trasformatore specificato. Attualmente supporta l' *imputatore* (fill_value e mediana).|
|**Eliminazione delle colonne** |Specifica le colonne da eliminare dall'trasformato.|

Per personalizzare featurizations con l'SDK, specificare `"featurization": FeaturizationConfig` nell' `AutoMLConfig` oggetto. Altre informazioni sulle [featurizations personalizzate](how-to-configure-auto-features.md#customize-featurization).

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

Se si usa Azure Machine Learning Studio per l'esperimento, vedere [come personalizzare conteggi in studio](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

## <a name="optional-configurations"></a>Configurazioni facoltative

Sono disponibili configurazioni aggiuntive facoltative per le attività di previsione, ad esempio l'abilitazione dell'apprendimento avanzato e la specifica di un'aggregazione di finestre in sequenza. 

### <a name="enable-deep-learning"></a>Abilita Deep Learning

> [!NOTE]
> Il supporto di DNN per la previsione nei Machine Learning automatici è in **Anteprima** e non è supportato per le esecuzioni locali.

È anche possibile sfruttare l'apprendimento avanzato con reti neurali profonde, DNN, per migliorare i punteggi del modello. Il Deep Learning di Machine Learning automatizzato consente di prevedere i dati univariati e multivariati della serie temporale.

I modelli di Deep Learning hanno tre funzionalità intrinseche:
1. Possono imparare da mapping arbitrari da input a output
1. Supportano più input e output
1. Possono estrarre automaticamente i modelli nei dati di input che si estendono su lunghe sequenze. 

Per abilitare l'apprendimento avanzato, impostare `enable_dnn=True` nell' `AutoMLConfig` oggetto.

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> Quando si Abilita DNN per gli esperimenti creati con l'SDK, le [spiegazioni migliori del modello](how-to-machine-learning-interpretability-automl.md) sono disabilitate.

Per abilitare DNN per un esperimento AutoML creato in Azure Machine Learning Studio, vedere le [impostazioni del tipo di attività in studio How-to](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

Per un esempio di codice dettagliato sull'uso delle reti neurali profonde, vedere il [notebook relativo alle previsioni della produzione di bevande](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

### <a name="target-rolling-window-aggregation"></a>Aggregazioni della finestra mobile di destinazione
Spesso le informazioni migliori a disposizione di un modulo di previsione sono date dal valore recente della destinazione.  Le aggregazioni della finestra di destinazione in sequenza consentono di aggiungere un'aggregazione in sequenza di valori di dati come funzionalità. La generazione e l'uso di queste funzionalità aggiuntive come ulteriori dati contestuali facilitano l'accuratezza del modello di training.

Si immagini, ad esempio, di voler stimare la domanda di energia. Potrebbe essere necessario aggiungere una funzionalità finestra in sequenza di tre giorni per tenere conto delle modifiche termiche degli spazi riscaldati. In questo esempio, creare questa finestra impostando `target_rolling_window_size= 3` nel `AutoMLConfig` costruttore. 

La tabella mostra la progettazione delle funzionalità risultante che si verifica quando viene applicata l'aggregazione di finestre. Le colonne per **Minimum, Maximum** e **Sum** vengono generate in una finestra temporale scorrevole di tre in base alle impostazioni definite. Ogni riga ha una nuova funzionalità calcolata, nel caso del timestamp per l'8 settembre 2017 4:00 i valori massimo, minimo e Sum vengono calcolati usando i **valori della richiesta** per l'8 settembre 2017 1:00-3:00. Questa finestra su base tre scorre in avanti per popolare i dati per le righe rimanenti.

![finestra in sequenza di destinazione](./media/how-to-auto-train-forecast/target-roll.svg)

Visualizzare un esempio di codice Python sfruttando la [funzionalità di aggregazione della finestra mobile di destinazione](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="short-series-handling"></a>Gestione di serie brevi

Automatizzato ML considera una serie temporale una **serie breve** se non sono presenti punti dati sufficienti per condurre le fasi di training e convalida dello sviluppo del modello. Il numero di punti dati varia per ogni esperimento e dipende dalla max_horizon, dal numero di divisioni della convalida incrociata e dalla lunghezza del modello Lookback, che è il massimo della cronologia necessario per costruire le funzionalità della serie temporale. Per il calcolo esatto, vedere la [documentazione di riferimento short_series_handling_config](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py#short-series-handling-configuration).

Machine Learning Machine Learning offre una gestione delle serie brevi per impostazione predefinita con il `short_series_handling_config` parametro nell' `ForecastingParameters` oggetto. 

Per abilitare la gestione delle serie brevi, `freq` è necessario definire anche il parametro. Per modificare il comportamento predefinito, `short_series_handling_config = auto` aggiornare il `short_series_handling_config` parametro nell' `ForecastingParameter` oggetto.  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_config='auto',
                                            freq = '7',
                                            target_lags='auto')
```
Nella tabella seguente sono riepilogate le impostazioni disponibili per `short_series_handling_config` .
 
|Impostazione|Descrizione
|---|---
|`auto`| Di seguito è riportato il comportamento predefinito per la gestione delle serie brevi <li> *Se tutte le serie sono brevi*, riempire i dati. <br> <li> *Se non tutte le serie sono brevi*, eliminare la serie breve. 
|`pad`| Se `short_series_handling_config = pad` , Machine Learning automatico aggiunge valori fittizi a ogni serie breve trovata. Di seguito sono elencati i tipi di colonna e gli elementi con cui vengono aggiunti: <li>Colonne oggetto con NaNs <li> Colonne numeriche con 0 <li> Colonne booleane/logiche con false <li> La colonna di destinazione viene riempita con valori casuali con una media di zero e una deviazione standard pari a 1. 
|`drop`| Se `short_series_handling_config = drop` , machine learning elimina la serie breve e non verrà usato per il training o la stima. Le stime per queste serie restituiranno NaN.
|`None`| Nessuna serie viene riempita o eliminata

>[!WARNING]
>La spaziatura interna può influisca sull'accuratezza del modello risultante, dal momento che vengono introdotti dati artificiali solo per superare il training senza errori. <br> <br> Se molte serie sono brevi, è possibile che si verifichino alcuni effetti sui risultati della spiegazione

## <a name="run-the-experiment"></a>Eseguire l'esperimento 

Quando l'oggetto è `AutoMLConfig` pronto, è possibile inviare l'esperimento. Al termine del modello, recuperare l'iterazione di esecuzione migliore.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```
 
## <a name="forecasting-with-best-model"></a>Eseguire previsioni con il modello migliore

Usare l'iterazione del modello migliore per prevedere i valori per il set di dati di test.

La `forecast()` funzione consente le specifiche di quando le stime devono essere avviate, a differenza di `predict()` , che viene in genere usato per le attività di classificazione e regressione.

Nell'esempio seguente vengono innanzitutto sostituiti tutti i valori di `y_pred` con `NaN`. In questo caso, l'origine della previsione sarà alla fine dei dati di training. Se, tuttavia, si sostituisce solo la seconda metà di `y_pred` con `NaN`, la funzione lascia i valori numerici nella prima metà non modificati, ma prevede i valori `NaN` nella seconda metà. La funzione restituisce sia i valori previsti, che le funzionalità allineate.

È anche possibile usare il parametro `forecast_destination` nella funzione `forecast()` per prevedere i valori fino a una data specificata.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcolare la radice dell'errore quadratico medio (valori RMSE) tra i `actual_labels` valori effettivi e i valori previsti in `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Ora che è stata determinata l'accuratezza complessiva del modello, il passaggio successivo più realistico consiste nell'uso del modello per prevedere valori futuri sconosciuti. 

Fornire un set di dati nello stesso formato del set di test `test_data`, ma con datetime futuri e il set di stime risultante, corrisponde ai valori previsti per ogni passaggio della serie temporale. Si supponga che gli ultimi record della serie temporale nel set di dati si riferiscano al giorno 31/12/2018. Per prevedere la richiesta del giorno successivo (o il numero di periodi che è necessario prevedere, < = `forecast_horizon`), creare un singolo record della serie temporale per ogni negozio per il giorno 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Ripetere i passaggi necessari per caricare i dati futuri in un dataframe e quindi eseguire `best_run.predict(test_data)` per stimare i valori futuri.

> [!NOTE]
> Non è possibile stimare i valori per un numero di periodi maggiore di `forecast_horizon`. È necessario rieseguire il training del modello con un orizzonte più ampio per stimare i valori futuri oltre l'orizzonte corrente.


## <a name="example-notebooks"></a>Notebook di esempio
Per esempi di codice dettagliati sulla configurazione di previsione avanzata, vedere i [notebook di esempio della previsione](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning), tra cui:

* [Rilevamento festività e definizione delle funzionalità](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [Convalida incrociata dell'origine in sequenza](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Ritardi configurabili](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [Funzionalità di aggregazione della finestra mobile](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Rete neurale profonda](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).
* Informazioni sull' [interpretazione: spiegazione del modello in Machine Learning automatizzato (anteprima)](how-to-machine-learning-interpretability-automl.md). 
* Informazioni su come eseguire il training di più modelli con AutoML nell' [acceleratore della soluzione many Models](https://aka.ms/many-models).
* Seguire l' [esercitazione](tutorial-auto-train-models.md) per un esempio end-to-end per la creazione di esperimenti con Machine Learning automatizzato.
