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
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: ddcf97a8c7ae6000c14638a8292bc8b4f39ed87d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978340"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurare esperimenti di Machine Learning automatici in Python
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questa guida illustra come definire diverse impostazioni di configurazione degli esperimenti di Machine Learning automatici con l' [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Il processo di Machine Learning automatizzato seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. Per configurare esperimenti di Machine Learning automatizzato sono disponibili varie opzioni.

Per visualizzare esempi di esperimenti di Machine Learning automatici, vedere [esercitazione: eseguire il training di un modello di classificazione con Machine Learning automatizzato](tutorial-auto-train-models.md) o eseguire il [training di modelli con Machine Learning automatico nel cloud](how-to-auto-train-remote.md).

Opzioni di configurazione disponibili nell'apprendimento automatico:

* Selezionare il tipo di esperimento: classificazione, regressione o previsione di serie temporali
* Origine dati, formati, dati di recupero
* Scegliere la destinazione di calcolo, locale o remota
* Configurare le impostazioni di un esperimento di Machine Learning automatizzato
* Eseguire un esperimento di Machine Learning automatizzato
* Esplorare le metriche del modello
* Registrare e distribuire modelli

Se si preferisce un'esperienza senza codice, è anche possibile [creare esperimenti automatici di Machine Learning in Azure Machine Learning Studio](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento

Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di machine learning da risolvere. Il processo di Machine Learning automatizzato supporta attività di tipo classificazione, regressione e previsione. Altre informazioni sui [tipi di attività](how-to-define-task-type.md).

Durante il processo di automazione e ottimizzazione, il processo di Machine Learning automatizzato supporta gli algoritmi seguenti. Come utente, non è necessario specificare l'algoritmo.

Classificazione | regressione | Previsione di una serie temporale
|-- |-- |--
[Regressione logistica](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)
[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC lineare](https://scikit-learn.org/stable/modules/svm.html#classification)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificazione di vettori di supporto (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificatore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificatore lineare DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesiano naif](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Regressore lineare veloce](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[ARIMA automatico](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Regressore di discesa sfumatura online](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Profeta](https://facebook.github.io/prophet/docs/quick_start.html)
|[Classificatore perceptron mediato](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Classificatore SVM lineare](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Usare il parametro `task` nel costruttore `AutoMLConfig` per specificare il tipo di esperimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Origine dati e formato

Il processo di Machine Learning automatizzato supporta dati presenti nel desktop locale o nel cloud, ad esempio Archiviazione BLOB di Azure. I dati possono essere letti in un **Dataframe Pandas** o in un **Azure Machine Learning TabularDataset**.  [Altre informazioni sui set di impostazioni](https://github.com/MicrosoftDocs/azure-docs-pr/pull/how-to-create-register-datasets.md).

Requisiti per i dati di training:
- I dati devono essere in formato tabulare.
- Il valore da stimare, ovvero la colonna di destinazione, deve trovarsi nei dati.

Negli esempi di codice seguenti viene illustrato come archiviare i dati in questi formati.

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  
  tabular_dataset = Dataset.Tabular.from_delimited_files("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv")
  train_dataset, test_dataset = tabular_dataset.random_split(percentage = 0.1, seed = 42)
  label = "Label"
  ```

* Dataframe Pandas

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    train_data, test_data = train_test_split(df, test_size = 0.1, random_state = 42)
    label = "Label"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Recuperare i dati per l'esecuzione dell'esperimento a risorse di calcolo remote

Per le esecuzioni remote, i dati di training devono essere accessibili dal calcolo remoto. La classe [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) nell'SDK espone la funzionalità per:

* trasferire facilmente i dati da file statici o origini URL nell'area di lavoro
* rendere i dati disponibili per gli script di training durante l'esecuzione di risorse di calcolo cloud

Vedere le [procedure](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) per un esempio di uso della classe `Dataset` per montare i dati nella destinazione di calcolo.

## <a name="train-and-validation-data"></a>Dati di training e convalida

È possibile specificare il training e i set di convalida distinti direttamente nel costruttore `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>Convalida incrociata K-Folds

Usare l'impostazione `n_cross_validations` per specificare il numero di convalide incrociate. Il set di dati di training verrà suddiviso in modo casuale in riduzioni `n_cross_validations` di dimensioni uguali. Durante ogni ciclo di convalida incrociata, una delle riduzioni verrà utilizzata per la convalida del modello con training eseguito sulle riduzioni restanti. Questo processo viene ripetuto per `n_cross_validations` cicli fino a quando ciascuna riduzione non viene utilizzata una sola volta come set di convalida. Il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero set di dati di training.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Convalida incrociata Monte Carlo (sottocampionamento casuale ripetuto)

Usare `validation_size` per specificare la percentuale dei dati di training da usare per la convalida e usare `n_cross_validations` per specificare il numero convalide incrociate. Durante ogni ciclo di convalida incrociata, un subset di dimensioni `validation_size` verrà selezionato in modo casuale per la convalida del modello con training eseguito sui dati restanti. Infine, il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero training set. Monte Carlo non è supportato per la previsione delle serie temporali.

### <a name="custom-validation-dataset"></a>Set di dati di convalida personalizzato

Utilizzare un set di dati di convalida personalizzato se la suddivisione casuale non è accettabile, in genere dati di serie temporali o dati sbilanciati. È possibile specificare il proprio set di dati di convalida. Il modello verrà valutato rispetto al set di dati di convalida specificato anziché set di dati casuali.

## <a name="compute-to-run-experiment"></a>Calcolo per eseguire l'esperimento

Successivamente, determinare dove verrà eseguito il training del modello. Un esperimento di training di Machine Learning automatizzato può essere eseguito sulle risorse di calcolo seguenti:
*   Nel computer locale, ad esempio un desktop locale o un computer portatile: in genere quando si dispone di set di dati di piccole dimensioni e si è ancora in fase di esplorazione.
*   In un computer remoto nel cloud: l'[ambiente di calcolo gestito di Azure Machine Learning](concept-compute-target.md#amlcompute) è un servizio gestito che offre la possibilità di eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure.

Visitare il [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) per notebook di esempio con destinazioni di calcolo locali e remote.

*   Un cluster Azure Databricks nella sottoscrizione di Azure. Per altri dettagli, vedere l'articolo [relativo all'installazione di Azure Databricks cluster per](how-to-configure-environment.md#azure-databricks) Machine Learning

Vedere il [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) per i notebook di esempio con Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurare le impostazioni di esperimento

Per configurare l'esperimento di Machine Learning automatizzato sono disponibili varie opzioni. Questi parametri vengono impostati creando un oggetto `AutoMLConfig`. Vedere la [Classe AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) per un elenco completo dei parametri.

Di seguito sono riportati alcuni esempi:

1.  Esperimento di classificazione con AUC ponderato come metrica primaria con minuti di timeout dell'esperimento impostati su 30 minuti e 2 riduzioni di convalida incrociata.

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  Di seguito è riportato un esempio di un esperimento di regressione impostato per terminare dopo 60 minuti con 5 riduzioni di convalida incrociate.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

I tre valori di parametro `task` diversi (il terzo tipo di attività è `forecasting`e usa un pool di algoritmi simile come attività `regression`) determina l'elenco dei modelli da applicare. Usare i parametri `whitelist` o `blacklist` per modificare ulteriormente le iterazioni con i modelli disponibili da includere o escludere. L'elenco dei modelli supportati è disponibile nella [classe SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedmodels?view=azure-ml-py).

### <a name="primary-metric"></a>Metrica primaria
La metrica primaria determina la metrica da utilizzare durante il training del modello per l'ottimizzazione. La metrica disponibile che è possibile selezionare è determinata dal tipo di attività scelto e la tabella seguente mostra le metriche primarie valide per ogni tipo di attività.

|Classificazione | regressione | Previsione di una serie temporale
|-- |-- |--
|precisione| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Informazioni sulle definizioni specifiche di questi dati sono disponibili in informazioni sui [risultati automatici di Machine Learning](how-to-understand-automated-ml.md).

### <a name="data-preprocessing--featurization"></a>Pre-elaborazione dei dati & conteggi

In ogni esperimento di Machine Learning automatizzato, i dati vengono [ridimensionati e normalizzati automaticamente](concept-automated-ml.md#preprocess) per aiutare *determinati* algoritmi sensibili alle funzionalità con diverse scale.  Tuttavia, è anche possibile abilitare la pre-elaborazione/conteggi aggiuntiva, ad esempio la mancata imputazione, la codifica e le trasformazioni dei valori mancanti. [Scopri di più su cosa è incluso conteggi](how-to-create-portal-experiments.md#preprocess).

Per abilitare questo conteggi, specificare `"preprocess": True` per la [classe`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> I passaggi di pre-elaborazione di Machine Learning automatizzati (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione di valori di testo nel formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di pre-elaborazione applicati durante il training vengono automaticamente applicati ai dati di input.

### <a name="time-series-forecasting"></a>Previsione di una serie temporale
L'attività `forecasting` della serie temporale richiede parametri aggiuntivi nell'oggetto di configurazione:

1. `time_column_name`: parametro obbligatorio che definisce il nome della colonna nei dati di training contenenti una serie temporale valida.
1. `max_horizon`: definisce l'intervallo di tempo che si desidera stimare in base alla periodicità dei dati di training. Se, ad esempio, si dispone di dati di training con intervalli di tempo giornalieri, si definisce la distanza in giorni per cui si desidera eseguire il training del modello.
1. `grain_column_names`: definisce il nome delle colonne che contengono singoli dati di serie temporali nei dati di training. Se, ad esempio, si prevede di prevedere le vendite di un particolare marchio per negozio, è necessario definire le colonne del negozio e del marchio come colonne di granularità. Verranno create serie temporali e previsioni separate per ogni granularità/raggruppamento. 

Per esempi delle impostazioni usate di seguito, vedere il [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

### <a name="ensemble"></a>Configurazione di ensemble

I modelli di ensemble sono abilitati per impostazione predefinita e vengono visualizzati come iterazioni di esecuzione finale in un'esecuzione automatica di machine learning. I metodi Ensemble attualmente supportati sono il voto e lo stack. Il voto viene implementato come soft-vote usando le medie ponderate e l'implementazione di stacking usa un'implementazione a 2 livelli, in cui il primo livello ha gli stessi modelli dell'insieme di voti e il secondo modello di livello viene usato per trovare la combinazione ottimale di modelli dal primo livello. Se si usano modelli ONNX **o** se è abilitata la spiegazione del modello, lo stacking verrà disabilitato e verrà usato solo il voto.

Sono disponibili più argomenti predefiniti che possono essere forniti come `kwargs` in un oggetto `AutoMLConfig` per modificare il comportamento predefinito dell'insieme dello stack.

* `stack_meta_learner_type`: il metaapprendimento è un modello di cui è stato eseguito il training sull'output dei singoli modelli di eterogeneo. I meta-Learning predefiniti sono `LogisticRegression` per le attività di classificazione (oppure `LogisticRegressionCV` se la convalida incrociata è abilitata) e `ElasticNet` per le attività di regressione/previsione (o `ElasticNetCV` se la convalida incrociata è abilitata). Questo parametro può essere una delle seguenti stringhe: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`o `LinearRegression`.
* `stack_meta_learner_train_percentage`: specifica la proporzione del set di training (quando si sceglie il tipo di training di training e di convalida) da riservare per il training del meta-Learning. Il valore predefinito è `0.2`.
* `stack_meta_learner_kwargs`: parametri facoltativi da passare all'inizializzatore del meta-Learning. Questi parametri e tipi di parametro eseguono il mirroring di quelli del costruttore del modello corrispondente e vengono trasmessi al costruttore del modello.

Il codice seguente illustra un esempio di come specificare un comportamento di ensemble personalizzato in un oggetto `AutoMLConfig`.

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

Il training di ensemble è abilitato per impostazione predefinita, ma può essere disabilitato usando il `enable_voting_ensemble` e `enable_stack_ensemble` i parametri booleani.

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

Per l'utilizzo automatico di ML si crea un oggetto `Experiment`, ovvero un oggetto denominato in un `Workspace` usato per eseguire gli esperimenti.

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
1. Nessun criterio: se non si definiscono parametri di uscita, l'esperimento continuerà fino a quando non verrà effettuato ulteriore avanzamento sulla metrica primaria.
1. Uscire dopo un periodo di tempo: l'uso di `experiment_timeout_minutes` nelle impostazioni consente di definire per quanto tempo, in minuti, un esperimento continuerà a essere eseguito.
1. Uscire dopo che è stato raggiunto un punteggio: se si usa `experiment_exit_score` l'esperimento viene completato dopo il raggiungimento di un punteggio della metrica primario.

### <a name="explore-model-metrics"></a>Esplorare le metriche del modello

È possibile visualizzare i risultati della formazione in un widget o inline se ci si trova in un notebook. Per altri dettagli, vedere [Tenere traccia dei modelli e valutarli](how-to-track-experiments.md#view-run-details).

## <a name="understand-automated-ml-models"></a>Informazioni sui modelli di Machine Learning automatizzati

Tutti i modelli prodotti con l'utilizzo automatico di ML includono i passaggi seguenti:
+ Progettazione automatica delle funzioni (se preprocess = true)
+ Ridimensionamento/normalizzazione e algoritmo con valori di iperparametri

Il risultato è trasparente per ottenere queste informazioni dall'output del fitted_model di Machine Learning automatizzato.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Progettazione automatica delle funzioni

Vedere l'elenco delle funzionalità di pre-elaborazione e di [progettazione automatica delle funzioni](concept-automated-ml.md#preprocess) che si verificano quando feauturization = auto.

Considerare questo esempio:
+ Sono disponibili 4 funzionalità di input: A (numerico), B (numerico), C (numerico), D (DateTime)
+ La funzione numerica C viene eliminata perché è una colonna ID con tutti i valori univoci
+ Le funzionalità numeriche A e B presentano valori mancanti e pertanto sono imputate dalla media
+ La funzionalità DateTime D è trasformato in 11 diverse funzionalità progettate

Usare queste 2 API nel primo passaggio del modello montato per comprendere meglio.  Vedere [questo notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` restituisce un elenco di nomi di funzionalità progettati.

  Utilizzo:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Questo elenco include tutti i nomi di funzionalità progettati.

  >[!Note]
  >Usare ' timeseriestransformer ' per task =' Forecasting '. in caso contrario, usare ' datatransformer ' per l'attività' regressione ' o ' Classification '.

+ API 2: `get_featurization_summary()` restituisce il riepilogo conteggi per tutte le funzionalità di input.

  Utilizzo:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Usare ' timeseriestransformer ' per task =' Forecasting '. in caso contrario, usare ' datatransformer ' per l'attività' regressione ' o ' Classification '.

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
   |RawFeatureName|Nome della funzionalità o della colonna di input dal set di dati specificato.|
   |TypeDetected|Tipo di dati rilevato della funzionalità di input.|
   |Dropped|Indica se la funzionalità di input è stata eliminata o utilizzata.|
   |EngineeringFeatureCount|Numero di funzionalità generate tramite trasformazioni automatiche di progettazione delle funzionalità.|
   |Trasformazioni|Elenco di trasformazioni applicate alle funzionalità di input per generare funzionalità progettate.|
   
### <a name="customize-feature-engineering"></a>Personalizzare la progettazione delle funzionalità
Per personalizzare la progettazione delle funzionalità, specificare `"feauturization":FeaturizationConfig`.

La personalizzazione supportata include:

|Personalizzazione|Definizione|
|--|--|
|Aggiornamento a scopo di colonna|Consente di eseguire l'override del tipo di funzionalità per la colonna specificata.|
|Aggiornamento parametri Transformer |Aggiornare i parametri per il trasformatore specificato. Attualmente supporta l'Imputatore e HashOneHotEncoder.|
|Rilascia colonne |Colonne da eliminare dall'oggetto trasformato.|
|Trasformatori blocco| Blocca i trasformatori da usare nel processo conteggi.|

Creare l'oggetto FeaturizationConfig usando le chiamate API:
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Ridimensionamento/normalizzazione e algoritmo con valori di iperparametri:

Per comprendere i valori di ridimensionamento, normalizzazione e algoritmo/iperparametro per una pipeline, usare fitted_model. Steps. [Altre informazioni sul ridimensionamento o la normalizzazione](concept-automated-ml.md#preprocess). Di seguito è riportato un output di esempio:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Per ottenere maggiori dettagli, usare questa funzione helper mostrata in [questo notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

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

Di seguito è riportato un esempio di output per una pipeline che usa un algoritmo specifico (LogisticRegression con RobustScalar, in questo caso).

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

<a name="explain"></a>

## <a name="model-interpretability"></a>Interpretabilità dei modelli

L'interpretazione dei modelli consente di comprendere il motivo per cui i modelli hanno eseguito stime e i valori di importanza delle funzionalità sottostanti. L'SDK include diversi pacchetti per abilitare le funzionalità di interpretazione dei modelli, sia in fase di training che di inferenza, per i modelli locali e distribuiti.

Vedere le [procedure](how-to-machine-learning-interpretability-automl.md) per esempi di codice su come abilitare le funzionalità di interpretazione in modo specifico negli esperimenti di Machine Learning automatici.

Per informazioni generali sul modo in cui è possibile abilitare le spiegazioni dei modelli e l'importanza delle funzionalità in altre aree dell'SDK al di fuori dell'apprendimento automatico automatico, vedere l'articolo relativo al [concetto](how-to-machine-learning-interpretability.md) di interpretazione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

Altre informazioni su [come eseguire il training di un modello di regressione con Machine Learning automatizzato](tutorial-auto-train-models.md) o [su come eseguire il training usando Machine Learning automatico in una risorsa remota](how-to-auto-train-remote.md).
