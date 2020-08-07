---
title: Creare esperimenti di Machine Learning automatizzato
titleSuffix: Azure Machine Learning
description: L'apprendimento automatico automatizzato seleziona un algoritmo per l'utente e genera un modello pronto per la distribuzione. Informazioni sulle opzioni che è possibile usare per configurare esperimenti di apprendimento automatico.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 05/20/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 025d3b1e0ce2f46cc689d74fe659facf026dc215
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852498"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurare esperimenti di ML automatizzato in Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questa guida illustra come definire diverse impostazioni di configurazione degli esperimenti di Machine Learning automatizzato con [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Il processo di Machine Learning automatizzato seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. Per configurare esperimenti di Machine Learning automatizzato sono disponibili varie opzioni.

Per visualizzare esempi di Machine Learning automatizzato, vedere [Esercitazione: Eseguire il training di un modello di classificazione con Machine Learning automatizzato](tutorial-auto-train-models.md) oppure [Eseguire il training di modelli con Machine Learning automatizzato nel cloud](how-to-auto-train-remote.md).

Opzioni di configurazione disponibili nell'apprendimento automatico:

* Selezionare il tipo di esperimento: Classificazione, Regressione o Previsione serie temporale
* Origine dati, formati, dati di recupero
* Scegliere la destinazione di calcolo, locale o remota
* Configurare le impostazioni di un esperimento di Machine Learning automatizzato
* Eseguire un esperimento di Machine Learning automatizzato
* Esplorare le metriche del modello
* Registrare e distribuire modelli

Se si preferisce un'esperienza senza codice, è anche possibile [Creare gli esperimenti di Machine Learning automatizzato in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento

Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di machine learning da risolvere. Il Machine Learning automatizzato supporta attività di tipo classificazione, regressione e previsione. Altre informazioni sui [tipi di attività](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Durante il processo di automazione e ottimizzazione, il processo di Machine Learning automatizzato supporta gli algoritmi seguenti. Come utente, non è necessario specificare l'algoritmo.

> [!NOTE]
> Se si prevede di esportare i modelli di Machine Learning automatizzato creati in un [modello ONNX](concept-onnx.md), solo gli algoritmi indicati con * possono essere converti nel formato ONNX. Altre informazioni sulla [conversione di modelli in ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Si noti anche che in questo momento ONNX supporta solo le attività di classificazione e regressione. 

Classificazione | Regressione | Previsione serie temporale
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Support Vector Classification (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Averaged Perceptron Classifier](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)|[Online Gradient Descent Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Fast Linear Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Linear SVM Classifier](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)*||

Usare il parametro `task` nel costruttore `AutoMLConfig` per specificare il tipo di esperimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Origine dati e formato

Il processo di Machine Learning automatizzato supporta dati presenti nel desktop locale o nel cloud, ad esempio Archiviazione BLOB di Azure. I dati possono essere letti in un **dataframe Pandas** o in un **TabularDataset di Azure Machine Learning**.  [Altre informazioni sui set di dati](how-to-create-register-datasets.md).

Requisiti per i dati di training:
- I dati devono essere in formato tabulare.
- Il valore da stimare, la colonna di destinazione, deve essere presente nei dati.

Gli esempi di codice seguenti illustrano come archiviare i dati in questi formati.

* TabularDataset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Dataframe Pandas

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Recuperare i dati per l'esecuzione dell'esperimento a risorse di calcolo remote

Per le esecuzioni remote, i dati di training devono essere accessibili dal calcolo remoto. La classe [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) nell'SDK espone la funzionalità per:

* trasferire facilmente i dati da file statici o origini URL nell'area di lavoro
* rendere i dati disponibili per gli script di training durante l'esecuzione in risorse di calcolo sul cloud

Per un esempio dell'uso della classe `Dataset` per montare i dati nella destinazione di calcolo, vedere la [procedura](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets).

## <a name="train-and-validation-data"></a>Dati di training e convalida

È possibile specificare set di training e di convalida distinti direttamente nel `AutoMLConfig` costruttore con le opzioni seguenti. Altre informazioni su [come configurare le suddivisioni dei dati e la convalida incrociata](how-to-configure-cross-validation-data-splits.md) per gli esperimenti AutoML. 

### <a name="k-folds-cross-validation"></a>Convalida incrociata K-Folds

Usare l'impostazione `n_cross_validations` per specificare il numero di convalide incrociate. Il set di dati di training verrà suddiviso in modo casuale in riduzioni `n_cross_validations` di dimensioni uguali. Durante ogni ciclo di convalida incrociata, una delle riduzioni verrà utilizzata per la convalida del modello con training eseguito sulle riduzioni restanti. Questo processo viene ripetuto per `n_cross_validations` cicli fino a quando ciascuna riduzione non viene utilizzata una sola volta come set di convalida. Il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero set di dati di training.

Altre informazioni sul modo in cui il Machine Learning automatizzato applica la convalida incrociata per [impedire l'overfitting dei modelli](concept-manage-ml-pitfalls.md#prevent-over-fitting).

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Convalida incrociata Monte Carlo (sottocampionamento casuale ripetuto)

Usare `validation_size` per specificare la percentuale dei dati di training da usare per la convalida e usare `n_cross_validations` per specificare il numero convalide incrociate. Durante ogni ciclo di convalida incrociata, un subset di dimensioni `validation_size` verrà selezionato in modo casuale per la convalida del modello con training eseguito sui dati restanti. Infine, il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero training set. La convalida incrociata Monte Carlo non è supportata per la previsione di serie temporale.

### <a name="custom-validation-dataset"></a>Set di dati di convalida personalizzato

Usare set di dati di convalida personalizzati se la suddivisione casuale non è accettabile (in genere dati di serie temporale o dati sbilanciati). È possibile specificare il proprio set di dati di convalida. Il modello verrà valutato rispetto al set di dati di convalida specificato anziché set di dati casuali. Altre informazioni su [come configurare un set di convalida personalizzato con l'SDK](how-to-configure-cross-validation-data-splits.md#provide-validation-data).

## <a name="compute-to-run-experiment"></a>Calcolo per eseguire l'esperimento

Successivamente, determinare dove verrà eseguito il training del modello. Un esperimento di training di Machine Learning automatizzato può essere eseguito sulle risorse di calcolo seguenti:
* Nel computer locale, ad esempio un desktop locale o un computer portatile: in genere quando si dispone di set di dati di piccole dimensioni e si è ancora in fase di esplorazione.
* In un computer remoto nel cloud: l'[ambiente di calcolo gestito di Azure Machine Learning](concept-compute-target.md#amlcompute) è un servizio gestito che offre la possibilità di eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure. 

  Visitare il [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) per esempi di notebook con destinazioni di calcolo locali e remote.

* Un cluster Azure Databricks nella sottoscrizione di Azure. Per informazioni dettagliate, vedere [Configurare un cluster Azure Databricks per il Machine Learning automatizzato](how-to-configure-environment.md#azure-databricks)

  Visitare il [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) per esempi di notebook con Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurare le impostazioni di esperimento

Per configurare l'esperimento di Machine Learning automatizzato sono disponibili varie opzioni. Questi parametri vengono impostati creando un oggetto `AutoMLConfig`. Vedere la [Classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) per un elenco completo dei parametri.

Di seguito sono riportati alcuni esempi:

1. Esperimento di classificazione con area sotto la curva (AUC) ponderata come metrica primaria con minuti di timeout dell'esperimento impostati su 30 minuti e due riduzioni di convalida incrociata.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Di seguito è riportato un esempio di esperimento di regressione impostato per terminare dopo 60 minuti con cinque riduzioni di convalida incrociata.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

I tre valori di parametro `task` diversi (il terzo tipo di attività è `forecasting` e usa un pool di algoritmi simile come attività `regression`) determina l'elenco dei modelli da applicare. Usare i parametri `whitelist` o `blacklist` per modificare ulteriormente le iterazioni con i modelli disponibili da includere o escludere. L'elenco dei modelli supportati è disponibile in [Classe SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) per le classi [Classification](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [Forecasting](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting) e [Regression](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression).

Per evitare errori di timeout degli esperimenti, il servizio di convalida del Machine Learning automatizzato richiede che `experiment_timeout_minutes` sia impostato su un minimo di 15 minuti o 60 minuti se le dimensioni riga per colonna superano 10 milioni.

### <a name="primary-metric"></a>Metrica primaria
La metrica primaria determina la metrica da usare durante il training del modello per l'ottimizzazione. Le metriche disponibili che è possibile selezionare sono determinate dal tipo di attività selezionato. La tabella seguente mostra le metriche primarie valide per ogni tipo di attività.

|Classificazione | Regressione | Previsione serie temporale
|-- |-- |--
|precisione| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Per informazioni sulle definizioni specifiche di queste metriche, vedere [Risultati del Machine Learning automatizzato](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Definizione delle funzionalità dei dati

In ogni esperimento di Machine Learning automatizzato i dati vengono [dimensionati e normalizzati automaticamente](how-to-configure-auto-features.md#) per *determinati* algoritmi sensibili a funzionalità su scale diverse.  Tuttavia, è anche possibile abilitare definizioni delle funzionalità aggiuntive, ad esempio l'imputazione di valori, la codifica e le trasformazioni dei valori mancanti.

Quando si configurano gli esperimenti nell' `AutoMLConfig` oggetto, è possibile abilitare o disabilitare l'impostazione `featurization` . La tabella seguente illustra le impostazioni accettate per la definizione delle funzionalità nella [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Configurazione della definizione delle funzionalità | Descrizione |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indica che i passaggi di [protezione dati e definizione delle funzionalità](how-to-configure-auto-features.md#featurization) vengono eseguiti automaticamente come parte della pre-elaborazione. **Impostazione predefinita**|
|`"featurization": 'off'`| Indica che la definizione delle funzionalità non deve essere eseguita automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica che deve essere usata la definizione delle funzionalità personalizzata. [Informazioni su come personalizzare la definizione delle funzionalità](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> I passaggi di definizione delle funzionalità di Machine Learning automatizzato (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione dei valori di testo in formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di definizione delle funzionalità applicati durante il training vengono automaticamente applicati ai dati di input.

### <a name="time-series-forecasting"></a>Previsione serie temporale
L'attività `forecasting` della serie temporale richiede parametri aggiuntivi nell'oggetto di configurazione:

1. `time_column_name`: parametro obbligatorio che definisce il nome della colonna nei dati di training contenenti una serie temporale valida.
1. `forecast_horizon`: Definisce il numero di periodi in cui si vuole prevedere la previsione. L'orizzonte Integer è in unità della frequenza timeseries. Se, ad esempio, si dispone di dati di training con frequenza giornaliera, è possibile definire il periodo di tempo in cui si desidera eseguire il training del modello.
1. `time_series_id_column_names`: Definisce le colonne che identificano in modo univoco la serie temporale nei dati che hanno più righe con lo stesso timestamp. Se, ad esempio, si prevede di prevedere le vendite di un particolare marchio per negozio, è necessario definire le colonne Store e brand come identificatori di serie temporali. Verranno create previsioni separate per ogni raggruppamento. Se gli identificatori delle serie temporali non sono definiti, si presuppone che il set di dati sia una serie temporale.

Per esempi delle impostazioni usate di seguito, vedere il [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as time series identifiers for training.
time_series_id_column_names = ['Store', 'Brand']
nseries = data.groupby(time_series_id_column_names).ngroups

# View the number of time series data with defined time series identifiers
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'time_series_id_column_names': time_series_id_column_names,
    'drop_column_names': ['logQuantity'],
    'forecast_horizon': n_test_periods
}

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble-configuration"></a><a name="ensemble"></a> Configurazione Ensemble

I modelli Ensemble sono abilitati per impostazione predefinita e vengono visualizzati come iterazioni dell'esecuzione finale nelle esecuzioni di Machine Learning automatizzato. I metodi Ensemble attualmente supportati sono votazione e stacking. La votazione viene implementata come votazione software usando medie ponderate, mentre l'implementazione stacking usa un'implementazione a due livelli in cui il primo livello ha gli stessi modelli Ensemble della votazione e il secondo livello viene usato per trovare la combinazione ottimale dei modelli del primo livello. Se si usano modelli ONNX **o** è abilitata la spiegabilità del modello abilitata, lo stacking verrà disabilitato e verrà usata solo la votazione.

Esistono più argomenti predefiniti che possono essere specificati come `kwargs` in un oggetto `AutoMLConfig` per modificare il comportamento Ensemble predefinito.

* `ensemble_download_models_timeout_sec`: Durante la generazione dei modelli **VotingEnsemble** e **StackEnsemble** vengono scaricati più modelli adattati delle esecuzioni figlio precedenti. Se si verifica questo errore: `AutoMLEnsembleException: Could not find any models for running ensembling`, potrebbe essere necessario fornire più tempo per il download dei modelli. Il valore predefinito è 300 secondi per il download di questi modelli in parallelo e non è previsto un limite massimo di timeout. Se è necessario più tempo, configurare questo parametro con un valore superiore a 300 secondi. 

  > [!NOTE]
  >  Se viene raggiunto il timeout e sono stati scaricati modelli, l'Ensemble procede con il numero di modelli scaricati. Non è necessario che tutti i modelli vengano scaricati per terminare prima del timeout.

I parametri seguenti si applicano solo ai modelli **StackEnsemble**: 

* `stack_meta_learner_type`: il meta-apprendimento è un modello di cui è stato eseguito il training nell'output dei singoli modelli eterogenei. I modelli di meta-apprendimento predefiniti sono `LogisticRegression` per le attività di classificazione (oppure `LogisticRegressionCV` se è abilitata la convalida incrociata) e `ElasticNet` per le attività di regressione o previsione (oppure `ElasticNetCV` se è abilitata la convalida incrociata). Questo parametro può essere una delle stringhe seguenti: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` o `LinearRegression`.

* `stack_meta_learner_train_percentage`: specifica la proporzione del set di training (quando si sceglie il tipo training e convalida) da riservare per il training del meta-apprendimento. Il valore predefinito è `0.2`. 

* `stack_meta_learner_kwargs`: parametri facoltativi da passare all'inizializzatore del meta-apprendimento. Questi parametri e tipi di parametro rispecchiano i parametri e i tipi di parametro del costruttore del modello corrispondente e vengono trasmessi al costruttore del modello.

Il codice seguente mostra un esempio di specifica di un comportamento Ensemble personalizzato in un oggetto `AutoMLConfig`.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Il training Ensemble è abilitato per impostazione predefinita, ma può essere disabilitato usando i parametri booleani `enable_voting_ensemble` e `enable_stack_ensemble`.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Eseguire esperimento

Per il Machine Learning automatizzato, si crea un oggetto `Experiment` che è un oggetto denominato in una `Workspace` usata per eseguire gli esperimenti.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Avviare l'esperimento per eseguire e generare un modello. Passare il `AutoMLConfig` al metodo `submit` per generare il modello.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Le dipendenze vengono prima installate in un nuovo computer.  Potrebbero occorrere fino a 10 minuti prima che venga visualizzato l'output.
>Se si imposta `show_output` su `True`, l'output viene visualizzato nella console.

### <a name="exit-criteria"></a><a name="exit"></a> Criteri uscita

È possibile definire alcune opzioni per terminare l'esperimento.
1. Nessun criterio: se non si definiscono parametri di uscita, l'esperimento continuerà fino a quando non si verificherà un ulteriore avanzamento sulla metrica primaria.
1. Uscita dopo un intervallo di tempo: l'uso di `experiment_timeout_minutes` nelle impostazioni consente di definire la durata in minuti dell'esecuzione di un esperimento.
1. Uscita dopo il raggiungimento di un determinato punteggio: l'uso di `experiment_exit_score` completerà l'esperimento dopo che è stato raggiunto il punteggio di una metrica primaria.

### <a name="explore-model-metrics"></a>Esplorare le metriche del modello

È possibile visualizzare i risultati del training in un widget o inline in un notebook. Per altri dettagli, vedere [Tenere traccia dei modelli e valutarli](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs).

Per informazioni dettagliate su come scaricare o registrare un modello per la distribuzione in un servizio Web, vedere [come e dove distribuire un modello](how-to-deploy-and-where.md).

## <a name="understand-automated-ml-models"></a>Modelli di Machine Learning automatizzato

Tutti i modelli prodotti con l'utilizzo del Machine Learning automatizzato includono i passaggi seguenti:
+ Definizione delle funzionalità automatizzata (se `"featurization": 'auto'`)
+ Ridimensionamento o normalizzazione e algoritmo con valori di iperparametri

La visualizzazione di queste informazioni dall'output fitted_model del Machine Learning automatizzato è trasparente.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Definizione delle funzionalità automatizzata

Vedere l'elenco della pre-elaborazione e della [definizione delle funzionalità automatizzata]() che si verifica quando `"featurization": 'auto'`.

Considerare questo esempio:
+ Sono disponibili quattro funzionalità di input: A (numerica), B (numerica), C (numerica), D (datetime)
+ La funzionalità numerica C viene eliminata poiché è una colonna ID con tutti i valori univoci
+ Le funzionalità numeriche A e B presentano valori mancanti e vengono quindi attribuite in base alla media
+ La funzionalità datetime D viene trasformata in 11 diverse funzionalità definite

Usare le due API seguenti nel primo passaggio del modello adattato per ottenere altre informazioni.  Vedere [questo notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` restituisce un elenco dei nomi delle funzionalità definite.

  Utilizzo:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Questo elenco include tutti i nomi delle funzionalità definite.

  >[!Note]
  >Usare 'timeseriestransformer' per task='forecasting' oppure usare 'datatransformer' per l'attività 'regression' o 'classification'.

+ API 2: `get_featurization_summary()` restituisce un riepilogo della definizione delle funzionalità per tutte le funzionalità di input.

  Utilizzo:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Usare 'timeseriestransformer' per task='forecasting' oppure usare 'datatransformer' per l'attività 'regression' o 'classification'.

  Output:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Dove:

   |Output|Definizione|
   |----|--------|
   |RawFeatureName|Nome della funzionalità o della colonna di input del set di dati specificato.|
   |TypeDetected|Tipo di dati rilevato della funzionalità di input.|
   |Dropped|Indica se la funzionalità di input è stata eliminata o usata.|
   |EngineeringFeatureCount|Numero di funzionalità generate tramite trasformazioni della definizione delle funzionalità automatizzata.|
   |Trasformazioni|Elenco delle trasformazioni applicate alle funzionalità di input per generare le funzionalità definite.|
### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Ridimensionamento o normalizzazione e algoritmo con valori di iperparametri:

Per comprendere il ridimensionamento o la normalizzazione e l'algoritmo o i valori di iperparametri di una pipeline, usare fitted_model.steps. [Altre informazioni sul ridimensionamento o la normalizzazione](how-to-configure-auto-features.md). Di seguito è riportato un output di esempio:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Per ottenere altri dettagli, usare la funzione helper seguente: 

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(model)
```

L'output di esempio seguente è relativo a una pipeline che usa un algoritmo specifico (in questo caso LogisticRegression con RobustScalar).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Stimare la probabilità della classe

I modelli prodotti con il Machine Learning automatizzato hanno tutti oggetti wrapper che rispecchiano la funzionalità della classe di origine open source. La maggior parte degli oggetti wrapper del modello di classificazione restituiti dal Machine Learning automatizzato implementa la funzione `predict_proba()` che accetta dati di esempio di tipo matrice o matrice sparsa delle funzionalità (valori X) e restituisce una matrice n-dimensionale di ogni esempio e la probabilità della rispettiva classe.

Si supponga di avere recuperato l'esecuzione migliore e il modello adattato usando le stesse chiamate precedenti, è possibile chiamare `predict_proba()` direttamente dal modello adattato specificando un `X_test` di esempio nel formato appropriato, a seconda del tipo di modello.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se il modello sottostante non supporta la funzione `predict_proba()` o il formato non è corretto, verrà generata un'eccezione specifica della classe del modello. Per esempi relativi all'implementazione di questa funzione per i diversi tipi di modello, vedere la documentazione di riferimento di [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html).

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretabilità dei modelli

L'interpretabilità dei modelli consente di comprendere il motivo per cui i modelli hanno eseguito stime e i valori di importanza delle funzionalità sottostanti. L'SDK include diversi pacchetti per abilitare le funzionalità di interpretabilità dei modelli, sia in fase di training che di inferenza, per i modelli locali e distribuiti.

Per esempi di codice su come abilitare le funzionalità di interpretabilità in modo specifico negli esperimenti di Machine Learning automatizzato, vedere la [procedura](how-to-machine-learning-interpretability-automl.md).

Per informazioni generali su come abilitare le spiegazioni dei modelli e l'importanza delle funzionalità in altre aree dell'SDK al di fuori del Machine Learning automatizzato, vedere l'articolo relativo al [concetto](how-to-machine-learning-interpretability.md) di interpretabilità.

> [!NOTE]
> Il modello ForecastTCN non è attualmente supportato dal client di spiegazione. Questo modello non restituirà un dashboard di spiegazione se viene restituito come modello migliore e non supporta l'esecuzione di spiegazioni su richiesta.

## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

+ Altre informazioni su [come eseguire il training di un modello di regressione con il Machine Learning automatizzato](tutorial-auto-train-models.md) oppure [come eseguire il training con il Machine Learning automatizzato in una risorsa remota](how-to-auto-train-remote.md).
+ Informazioni su come eseguire il training di più modelli con il Machine Learning automatizzato nell'[Acceleratore di soluzioni molti modelli](https://aka.ms/many-models).
