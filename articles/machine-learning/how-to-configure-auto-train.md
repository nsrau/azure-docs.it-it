---
title: Creare esperimenti di Machine Learning automatizzato
titleSuffix: Azure Machine Learning
description: L'apprendimento automatico automatizzato seleziona un algoritmo per l'utente e genera un modello pronto per la distribuzione. Informazioni sulle opzioni che è possibile usare per configurare esperimenti di apprendimento automatico.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 18de50473e3dd6ca8ddda9575a247e00530032e8
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115407"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurare esperimenti di ML automatizzato in Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa guida viene illustrato come definire le varie impostazioni di configurazione degli esperimenti di apprendimento automatico automatizzati con [Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Il processo di Machine Learning automatizzato seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. Per configurare esperimenti di Machine Learning automatizzato sono disponibili varie opzioni.

Per visualizzare esempi di esperimenti di apprendimento automatico automatizzati, vedere [Esercitazione: eseguire](tutorial-auto-train-models.md) il training di un modello di classificazione con modelli automatizzati di apprendimento automatico o [Treno con apprendimento automatico automatizzato nel cloud.](how-to-auto-train-remote.md)

Opzioni di configurazione disponibili nell'apprendimento automatico:

* Selezionare il tipo di esperimento: Classificazione, Regressione o Previsione serie temporali
* Origine dati, formati, dati di recupero
* Scegliere la destinazione di calcolo, locale o remota
* Configurare le impostazioni di un esperimento di Machine Learning automatizzato
* Eseguire un esperimento di Machine Learning automatizzato
* Esplorare le metriche del modello
* Registrare e distribuire modelli

Se si preferisce un'esperienza senza codice, è anche possibile creare gli esperimenti di [apprendimento automatico automatizzati in Azure Machine Learning Studio.](how-to-use-automated-ml-for-ml-models.md)

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento

Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di machine learning da risolvere. L'apprendimento automatico supporta i tipi di attività di classificazione, regressione e previsione. Ulteriori informazioni sui [tipi di attività](how-to-define-task-type.md).

Durante il processo di automazione e ottimizzazione, il processo di Machine Learning automatizzato supporta gli algoritmi seguenti. Come utente, non è necessario specificare l'algoritmo.

> [!NOTE]
> Se si prevede di esportare i modelli creati da ML auto in un [modello ONNX](concept-onnx.md), solo gli algoritmi indicati con un simbolo , possono essere convertiti nel formato ONNX. Ulteriori informazioni sulla [conversione dei modelli in ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Si noti inoltre che ONNX supporta solo le attività di classificazione e regressione in questo momento. 

Classificazione | Regressione | Previsione di una serie temporale
|-- |-- |--
[Regressione logistica](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Luce GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Luce GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Incremento sfumato](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Incremento sfumato](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Vicini più vicini](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Vicini più vicini](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC lineare](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Lazo LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificazione vettoriale di supporto (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Discesa stocastica gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Alberi estremamente randomizzati](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Alberi estremamente randomizzati](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost (Xgboost)](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost (Xgboost)](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost (Xgboost)](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificatore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificatore lineare DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Regressore lineare veloce](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[ARIMA automatico](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Discesa stocastica gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Regressore discesa gradiente online](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Profeta](https://facebook.github.io/prophet/docs/quick_start.html)
|[Classificatore percepito medio](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||PrevistoTCN
|[Classificatore SVM lineare](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Utilizzare `task` il parametro nel `AutoMLConfig` costruttore per specificare il tipo di esperimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Origine dati e formato

Il processo di Machine Learning automatizzato supporta dati presenti nel desktop locale o nel cloud, ad esempio Archiviazione BLOB di Azure. I dati possono essere letti in un **DataFrame Pandas** o in un tabularE di **Azure Machine Learning.**  [Ulteriori informazioni sui dataset](how-to-create-register-datasets.md).

Requisiti per i dati di training:
- I dati devono essere in formato tabulare.
- Il valore da stimare, colonna di destinazione, deve essere nei dati.

Negli esempi di codice seguenti viene illustrato come archiviare i dati in questi formati.

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

Per le esecuzioni remote, i dati di training devono essere accessibili dal calcolo remoto. La [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) classe nell'SDK espone funzionalità a:

* trasferire facilmente i dati da file statici o origini URL nell'area di lavoro
* rendere i dati disponibili per gli script di training durante l'esecuzione su risorse di elaborazione cloud

Vedere la [procedura](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) per un esempio `Dataset` di utilizzo della classe per montare i dati nella destinazione di calcolo.

## <a name="train-and-validation-data"></a>Dati di training e convalida

È possibile specificare set di `AutoMLConfig` training e di convalida separati direttamente nel costruttore.

### <a name="k-folds-cross-validation"></a>Convalida incrociata K-Folds

Usare l'impostazione `n_cross_validations` per specificare il numero di convalide incrociate. Il set di dati di training verrà suddiviso in modo casuale in riduzioni `n_cross_validations` di dimensioni uguali. Durante ogni ciclo di convalida incrociata, una delle riduzioni verrà utilizzata per la convalida del modello con training eseguito sulle riduzioni restanti. Questo processo viene ripetuto per `n_cross_validations` cicli fino a quando ciascuna riduzione non viene utilizzata una sola volta come set di convalida. Il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero set di dati di training.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Convalida incrociata Monte Carlo (sottocampionamento casuale ripetuto)

Usare `validation_size` per specificare la percentuale dei dati di training da usare per la convalida e usare `n_cross_validations` per specificare il numero convalide incrociate. Durante ogni ciclo di convalida incrociata, un subset di dimensioni `validation_size` verrà selezionato in modo casuale per la convalida del modello con training eseguito sui dati restanti. Infine, il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero training set. Monte Carlo non è supportato per le previsioni di serie temporali.

### <a name="custom-validation-dataset"></a>Set di dati di convalida personalizzato

Utilizzare il set di dati di convalida personalizzato se la divisione casuale non è accettabile, in genere dati di serie temporali o dati sbilanciati. È possibile specificare il proprio set di dati di convalida. Il modello verrà valutato rispetto al set di dati di convalida specificato anziché set di dati casuali.

## <a name="compute-to-run-experiment"></a>Calcolo per eseguire l'esperimento

Successivamente, determinare dove verrà eseguito il training del modello. Un esperimento di training di Machine Learning automatizzato può essere eseguito sulle risorse di calcolo seguenti:
* Nel computer locale, ad esempio un desktop locale o un computer portatile: in genere quando si dispone di set di dati di piccole dimensioni e si è ancora in fase di esplorazione.
* In un computer remoto nel cloud: l'[ambiente di calcolo gestito di Azure Machine Learning](concept-compute-target.md#amlcompute) è un servizio gestito che offre la possibilità di eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure.

  Vedere questo [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) per esempi di blocchi appunti con destinazioni di elaborazione locali e remote.

* Un cluster di Azure Databricks nella sottoscrizione di Azure.An Azure Databricks cluster in your Azure subscription. Ulteriori dettagli sono disponibili qui - Configurare il [cluster Azure Databricks per ML automatizzato](how-to-configure-environment.md#azure-databricks)

  Vedere questo sito GitHub per esempi di blocchi appunti con Azure Databricks.See this [GitHub site](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) for examples of notebooks with Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurare le impostazioni di esperimento

Per configurare l'esperimento di Machine Learning automatizzato sono disponibili varie opzioni. Questi parametri vengono impostati creando un oggetto `AutoMLConfig`. Vedere la [Classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) per un elenco completo dei parametri.

Di seguito sono riportati alcuni esempi:

1. Esperimento di classificazione utilizzando AUC ponderato come metrica principale con i minuti di timeout dell'esperimento impostati su 30 minuti e 2 pieghe di convalida incrociata.

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
2. Di seguito è riportato un esempio di un esperimento di regressione impostato per terminare dopo 60 minuti con cinque pieghe incrociate di convalida.

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

I tre `task` diversi valori dei parametri `forecasting`(il terzo tipo di `regression` attività è e utilizza un pool di algoritmi simile alle attività) determinano l'elenco dei modelli da applicare. Utilizzare `whitelist` i `blacklist` parametri o per modificare ulteriormente le iterazioni con i modelli disponibili da includere o escludere. L'elenco dei modelli supportati è disponibile in [SupportedModels Class](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) for ([Classification](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [Forecasting](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)e [Regression](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Per evitare errori di timeout dell'esperimento, il `experiment_timeout_minutes` servizio di convalida di ML automatizzato richiederà che sia impostato su un minimo di 15 minuti o 60 minuti se la riga per dimensione della colonna supera 10 milioni.

### <a name="primary-metric"></a>Metrica primaria
La metrica principale determina la metrica da utilizzare durante il training del modello per l'ottimizzazione. Le metriche disponibili che è possibile selezionare sono determinate dal tipo di attività scelto e la tabella seguente mostra le metriche primarie valide per ogni tipo di attività.

|Classificazione | Regressione | Previsione di una serie temporale
|-- |-- |--
|precisione| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Per informazioni sulle definizioni specifiche di queste metriche, [vedere Comprendere i risultati dell'apprendimento automatico automatizzato.](how-to-understand-automated-ml.md)

### <a name="data-featurization"></a>Fattibilizzazione dei dati

In ogni esperimento di apprendimento automatico, i dati vengono [automaticamente ridimensionati e normalizzati](concept-automated-ml.md#preprocess) per aiutare *alcuni* algoritmi sensibili alle funzionalità su scale diverse.  Tuttavia, è anche possibile abilitare la fattibilizzazione aggiuntiva, ad esempio l'imputazione, la codifica e le trasformazioni dei valori mancanti. [Scopri di più su quale fattibilizzazione è inclusa](how-to-use-automated-ml-for-ml-models.md#featurization).

Quando si configurano gli esperimenti, `featurization`è possibile abilitare l'impostazione avanzata . Nella tabella seguente vengono illustrate le impostazioni [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)accettate per la featurization nella classe .

|Configurazione della featurizzazione | Descrizione |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica che deve essere utilizzato un passaggio di featurizzazione personalizzato. [Scopri come personalizzare la featurizzazione.](how-to-configure-auto-train.md#customize-feature-engineering)|
|`"featurization": 'off'`| Indica che la fase di featurizzazione non deve essere eseguita automaticamente.|
|`"featurization": 'auto'`| Indica che, come parte della pre-elaborazione, i guardrail di dati e i passaggi di [featurizzazione](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) vengono eseguiti automaticamente.|

> [!NOTE]
> I passaggi automatizzati di fattibilizzazione dell'apprendimento automatico (la normalizzazione delle funzionalità, la gestione dei dati mancanti, la conversione del testo in numeri e così via) diventano parte del modello sottostante. Quando si usa il modello per le stime, gli stessi passaggi di featurizzazione applicati durante il training vengono applicati automaticamente ai dati di input.

### <a name="time-series-forecasting"></a>Previsione di una serie temporale
L'attività `forecasting` di serie temporale richiede parametri aggiuntivi nell'oggetto di configurazione:The time series task requires additional parameters in the configuration object:

1. `time_column_name`: parametro obbligatorio che definisce il nome della colonna nei dati di training contenenti una serie temporale valida.
1. `max_horizon`: definisce il periodo di tempo che si desidera stimare in base alla periodicità dei dati di training. Ad esempio, se si dispone di dati di training con valori di tempo giornalieri, è possibile definire la distanza in giorni per cui si desidera eseguire il training del modello.
1. `grain_column_names`: definisce il nome delle colonne che contengono singoli dati di serie temporali nei dati di training. Ad esempio, se si prevede le vendite di un determinato marchio per punto vendita, è necessario definire le colonne del punto vendita e del marchio come colonne di granulosità. Verranno create serie temporali e previsioni separate per ogni granello/raggruppamento. 

Per esempi delle impostazioni utilizzate di seguito, vedere il [blocco appunti di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Configurazione dell'ensemble

I modelli Ensemble sono abilitati per impostazione predefinita e vengono visualizzati come iterazioni di esecuzione finali in un'esecuzione automatica dell'apprendimento automatico. I metodi di ensemble attualmente supportati sono il voto e l'impilamento. La votazione viene implementata come soft-voting utilizzando medie ponderate e l'implementazione di impilamento utilizza un'implementazione a due livelli, in cui il primo livello ha gli stessi modelli dell'ensemble di voto e il secondo modello di livello viene utilizzato per trovare la combinazione ottimale dei modelli dal primo livello. Se si utilizzano modelli ONNX **o** è attivata la spiegazione dei modelli, l'impilamento verrà disabilitato e verrà utilizzata solo la votazione.

Esistono più argomenti predefiniti che `kwargs` possono `AutoMLConfig` essere forniti come in un oggetto per modificare il comportamento predefinito dell'insieme di stack.

* `stack_meta_learner_type`: il meta-allievo è un modello sottoposto a training sull'output dei singoli modelli eterogenei. I meta-allievi `LogisticRegression` predefiniti sono `LogisticRegressionCV` per le attività di `ElasticNet` classificazione (o se `ElasticNetCV` la convalida incrociata è abilitata) e per le attività di regressione/previsione (o se la convalida incrociata è abilitata). Questo parametro può essere una `LogisticRegression` `LogisticRegressionCV`delle `LightGBMClassifier` `ElasticNet`stringhe `ElasticNetCV` `LightGBMRegressor`seguenti: `LinearRegression`, , , , , o .
* `stack_meta_learner_train_percentage`: specifica la proporzione del set di formazione (quando si sceglie il formato e il tipo di formazione di convalida) da riservare alla formazione del meta-studente. Il valore predefinito è `0.2`.
* `stack_meta_learner_kwargs`: parametri facoltativi da passare all'inizializzatore del meta-learner. Questi parametri e tipi di parametro rispecchiano i parametri e i tipi di parametro dal costruttore del modello corrispondente e vengono inoltrati al costruttore del modello.

Il codice seguente mostra un esempio di `AutoMLConfig` specifica del comportamento dell'insieme personalizzato in un oggetto.

```python
ensemble_settings = {
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

La formazione dell'ensemble è abilitata per `enable_voting_ensemble` impostazione predefinita, ma può essere disabilitata utilizzando i parametri booleani e `enable_stack_ensemble` .

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

Per la ML automatizzata, si crea un `Experiment` oggetto, che è un oggetto denominato in un `Workspace` oggetto usato per eseguire esperimenti.

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

### <a name="exit-criteria"></a>Criteri di uscita
Sono disponibili alcune opzioni che è possibile definire per terminare l'esperimento.
1. Nessun criterio: se non si definiscono parametri di uscita, l'esperimento continuerà fino a quando non verranno compiuti ulteriori progressi sulla metrica principale.
1. Esci dopo un `experiment_timeout_minutes` certo periodo di tempo: l'uso nelle impostazioni consente di definire il tempo in minuti che deve continuare l'esecuzione di un esperimento.
1. Esci dopo il raggiungimento di un punteggio: l'uso `experiment_exit_score` completerà l'esperimento dopo che è stato raggiunto un punteggio metrico primario.

### <a name="explore-model-metrics"></a>Esplorare le metriche del modello

È possibile visualizzare i risultati della formazione in un widget o in linea se ci si trova in un blocco appunti. Per altri dettagli, vedere [Tenere traccia dei modelli e valutarli](how-to-track-experiments.md#view-run-details).

## <a name="understand-automated-ml-models"></a>Comprendere i modelli ML automatizzati

Qualsiasi modello prodotto utilizzando ML automatizzato include i seguenti passaggi:
+ Progettazione di funzionalità `"featurization": 'auto'`automatizzate (se )
+ Scalabilità/normalizzazione e algoritmo con valori di iperparametri

Lo rendiamo trasparente per ottenere queste informazioni dal fitted_model output da ML automatizzato.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Progettazione automatizzata delle funzionalità

Vedere l'elenco della pre-elaborazione e `"featurization": 'auto'`della [progettazione automatica delle funzionalità](concept-automated-ml.md#preprocess) che si verifica quando .

Considerare questo esempio:
+ Sono disponibili quattro funzionalità di input: A (numerico), B (numerico), C (numerico), D (DateTime)
+ La caratteristica numerica C viene eliminata perché è una colonna ID con tutti i valori univoci
+ Le caratteristiche numeriche A e B hanno valori mancanti e quindi sono imputate dalla media
+ La funzione DateTime D è featurizzata in 11 diverse funzionalità ingegnerizzate

Usa queste 2 API sul primo passaggio del modello adattato per ottenere di più.  Vedere [questo blocco appunti di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` restituisce un elenco di nomi di funzionalità progettate.

  Utilizzo:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Questo elenco include tutti i nomi delle funzioni progettate.

  >[!Note]
  >Utilizzare 'timeseriestransformer' per l'attività 'forecasting', altrimenti utilizzare 'datatransformer' per 'regressione' o 'classificazione' attività.

+ API 2: `get_featurization_summary()` restituisce il riepilogo della featurization per tutte le funzionalità di input.

  Utilizzo:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Utilizzare 'timeseriestransformer' per l'attività 'forecasting', altrimenti utilizzare 'datatransformer' per 'regressione' o 'classificazione' attività.

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
   |Nomefunzionalità non elaborato|Nome della feature/colonna di input dal set di dati fornito.|
   |TypeDetected|Tipo di dati rilevato della funzionalità di input.|
   |Dropped|Indica se la feature di input è stata eliminata o utilizzata.|
   |EngineeringFeatureCount (Esempio di funzionalità EngineeringFeatureCount)|Numero di feature generate tramite trasformazioni automatizzate di progettazione delle funzionalità.|
   |Trasformazioni|Elenco di trasformazioni applicate alle funzioni di input per generare feature progettate.|
   
### <a name="customize-feature-engineering"></a>Personalizzare la progettazione delle funzionalità
Per personalizzare la `"featurization": FeaturizationConfig`progettazione delle funzionalità, specificare .

La personalizzazione supportata include:

|Personalizzazione|Definizione|
|--|--|
|Aggiornamento dello scopo della colonna|Eseguire l'override del tipo di feature per la colonna specificata.|
|Aggiornamento dei parametri del trasformatore |Aggiornare i parametri per il trasformatore specificato. Attualmente supporta Imputer (media, più frequente & mediana) e HashOneHotEncoder.|
|Colonne di rilascio |Colonne da eliminare dall'essere featurizzati.|
|Trasformatori a blocchi| Trasformatori a blocchi da utilizzare nel processo di featurization.|

Creare l'oggetto FeaturizationConfig utilizzando le chiamate API:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Scala/Normalizzazione e algoritmo con valori di iperparametri:

Per comprendere i valori di algoritmi/iperparametri di scalabilità per una pipeline, usare fitted_model.steps. [Ulteriori informazioni sul ridimensionamento/normalizzazione](concept-automated-ml.md#preprocess). Di seguito è riportato un output di esempio:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Per ulteriori dettagli, usare questa funzione di supporto illustrata in [questo blocco appunti di esempio.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)

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


print_model(fitted_model)
```

L'output di esempio seguente è per una pipeline che usa un algoritmo specifico (LogisticRegression with RobustScalar, in questo caso).

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

### <a name="predict-class-probability"></a>Prevedere la probabilità della classePredict class probability

I modelli prodotti utilizzando ML automatizzato dispongono tutti di oggetti wrapper che rispecchiano la funzionalità della classe di origine open source. La maggior parte degli oggetti wrapper del `predict_proba()` modello di classificazione restituiti da ML automatizzato implementa la funzione, che accetta un campione di dati di tipo matrice o di matrice di tipo matrice delle feature (valori X) e restituisce una matrice n-dimensionale di ogni campione e la relativa probabilità di classe.

Supponendo di aver recuperato il miglior modello di corsa e `predict_proba()` montato utilizzando le stesse `X_test` chiamate dall'alto, è possibile chiamare direttamente dal modello montato, fornendo un campione nel formato appropriato a seconda del tipo di modello.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se il modello sottostante `predict_proba()` non supporta la funzione o il formato non è corretto, verrà generata un'eccezione specifica della classe del modello. Vedere il [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) documenti di riferimento per esempi di come questa funzione viene implementata per diversi tipi di modello.

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretabilità dei modelli

L'interpretabilità dei modelli consente di comprendere il motivo per cui i modelli hanno eseguito stime e i valori di importanza della feature sottostante. L'SDK include vari pacchetti per abilitare le funzionalità di interpretabilità del modello, sia in fase di training che di inferenza, per i modelli locali e distribuiti.

Scopri le [procedure](how-to-machine-learning-interpretability-automl.md) per gli esempi di codice su come abilitare le funzionalità di interpretabilità in modo specifico all'interno di esperimenti automatizzati di apprendimento automatico.

Per informazioni generali su come è possibile abilitare le spiegazioni dei modelli e l'importanza delle funzionalità in altre aree dell'SDK al di fuori dell'apprendimento automatico, vedere l'articolo [sul concetto](how-to-machine-learning-interpretability.md) sull'interpretabilità.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

Altre informazioni su come eseguire il training di un modello di [regressione con l'apprendimento automatico automatizzato](tutorial-auto-train-models.md) o [su come eseguire il training usando l'apprendimento automatico automatico in una risorsa remota.](how-to-auto-train-remote.md)
