---
title: Creare esperimenti di Machine Learning automatizzato
titleSuffix: Azure Machine Learning service
description: L'apprendimento automatico automatizzato seleziona un algoritmo per l'utente e genera un modello pronto per la distribuzione. Informazioni sulle opzioni che è possibile usare per configurare esperimenti di apprendimento automatico.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 6e29e0f89d9270a143d48cf6e85b479813e19d9d
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013652"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurare esperimenti di Machine Learning automatici in Python

Questa guida illustra come definire diverse impostazioni di configurazione degli esperimenti di Machine Learning automatici con l' [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Il processo di Machine Learning automatizzato seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. Per configurare esperimenti di Machine Learning automatizzato sono disponibili varie opzioni.

Per visualizzare esempi di Machine Learning automatizzato, vedere [Esercitazione: Eseguire il training di un modello di classificazione con Machine Learning automatizzato](tutorial-auto-train-models.md) oppure [Eseguire il training di modelli con Machine Learning automatizzato nel cloud](how-to-auto-train-remote.md).

Opzioni di configurazione disponibili nell'apprendimento automatico:

* Selezionare il tipo di esperimento: Previsione della classificazione, della regressione o delle serie temporali
* Origine dati, formati, dati di recupero
* Scegliere la destinazione di calcolo, locale o remota
* Configurare le impostazioni di un esperimento di Machine Learning automatizzato
* Eseguire un esperimento di Machine Learning automatizzato
* Esplorare le metriche del modello
* Registrare e distribuire modelli

Se si preferisce un'esperienza senza codice, è anche possibile [creare esperimenti di Machine Learning automatici nel portale di Azure](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento

Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di machine learning da risolvere. Il processo di Machine Learning automatizzato supporta attività di tipo classificazione, regressione e previsione.

Durante il processo di automazione e ottimizzazione, il processo di Machine Learning automatizzato supporta gli algoritmi seguenti. Come utente, non è necessario specificare l'algoritmo.

classificazione | Regressione | Previsione delle serie temporali
|-- |-- |--
[Regressione logistica](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)
[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC lineare](https://scikit-learn.org/stable/modules/svm.html#classification)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificazione a vettori di supporto C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificatore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificatore lineare DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesiano naif](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Usare il `task` parametro `AutoMLConfig` nel costruttore per specificare il tipo di esperimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Origine dati e formato
Il processo di Machine Learning automatizzato supporta dati presenti nel desktop locale o nel cloud, ad esempio Archiviazione BLOB di Azure. I dati possono essere letti in scikit-learn su formati di dati supportati. È possibile leggere i dati in:
* Matrici Numpy X (caratteristiche) e y (variabile di destinazione, nota anche come etichetta)
* Dataframe Pandas

>[!Important]
> Requisiti per i dati di training:
>* I dati devono essere in formato tabulare.
>* Il valore che si desidera stimare (colonna di destinazione) deve essere presente nei dati.

Esempi:

*   Matrici Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Dataframe Pandas

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Recuperare i dati per l'esecuzione dell'esperimento a risorse di calcolo remote

Per le esecuzioni remote, è necessario rendere i dati accessibili dal calcolo remoto. A tale scopo, è possibile caricare i dati nell'archivio dati.

Di seguito è riportato un esempio `datastore`di utilizzo di:

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>Definire i riferimenti dprep

Definire X e y come riferimento dprep, che verrà passato all'oggetto Machine Learning `AutoMLConfig` automatico simile al seguente:

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Dati di training e convalida

È possibile specificare il training e il `AutoMLConfig` set di convalida distinti direttamente nel metodo.

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

1.  Esperimento di classificazione tramite AUC ponderato come metrica primaria con un tempo massimo di 12.000 secondi per ogni iterazione, con il termine dell'esperimento allo scadere di 50 iterazioni e 2 riduzioni di convalida incrociata.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Di seguito è riportato un esempio di un set di esperimento di regressione da terminare allo scadere di 100 iterazioni, con ogni iterazione della durata fino a 600 secondi con 5 riduzioni di convalida incrociata.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

I tre valori `task` di parametro diversi determinano l'elenco di algoritmi da applicare.  Usare i parametri `whitelist` o `blacklist` per modificare ulteriormente le iterazioni con gli algoritmi disponibili da includere o escludere. L'elenco dei modelli supportati è disponibile nella [classe SupportedAlgorithms](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Metrica primaria
Metrica primaria; come illustrato negli esempi precedenti, determina la metrica da usare durante il training del modello per l'ottimizzazione. La metrica primaria che è possibile selezionare è determinata dal tipo di attività scelto. Di seguito è riportato un elenco delle metriche disponibili.

|classificazione | Regressione | Previsione delle serie temporali
|-- |-- |--
|precisione| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Pre-elaborazione dei dati & conteggi

In ogni esperimento di Machine Learning automatizzato, i dati vengono [ridimensionati e normalizzati automaticamente](concept-automated-ml.md#preprocess) per consentire agli algoritmi di ottenere risultati ottimali.  Tuttavia, è anche possibile abilitare la pre-elaborazione/conteggi aggiuntiva, ad esempio la mancata imputazione, la codifica e le trasformazioni dei valori mancanti. [Scopri di più su cosa è incluso conteggi](how-to-create-portal-experiments.md#preprocess).

Per abilitare questo conteggi, specificare `"preprocess": True` per la [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> I passaggi di pre-elaborazione automatizzati di Machine Learning, ovvero la normalizzazione delle funzionalità, la gestione dei dati mancanti, la conversione di testo in numeri e così via, diventano parte del modello sottostante. Quando si usa il modello per le stime, le stesse fasi di pre-elaborazione applicate durante il training vengono applicate automaticamente ai dati di input.

### <a name="time-series-forecasting"></a>Previsione delle serie temporali
Per il tipo di attività di previsione delle serie temporali sono disponibili parametri aggiuntivi da definire.
1. time_column_name: parametro obbligatorio che definisce il nome della colonna nei dati di training contenenti la serie di data/ora.
1. max_horizon: definisce l'intervallo di tempo che si desidera stimare in base alla periodicità dei dati di training. Se, ad esempio, si dispone di dati di training con intervalli di tempo giornalieri, si definisce la distanza in giorni per cui si desidera eseguire il training del modello.
1. grain_column_names: definisce il nome delle colonne che contengono singoli dati di serie temporali nei dati di training. Se, ad esempio, si prevede di prevedere le vendite di un particolare marchio per negozio, è necessario definire le colonne del negozio e del marchio come colonne di granularità.

Vedere l'esempio di queste impostazioni usato di seguito. l'esempio di notebook è disponibile [qui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Configurazione di ensemble

I modelli di ensemble sono abilitati per impostazione predefinita e vengono visualizzati come iterazioni di esecuzione finale in un'esecuzione automatica di machine learning. I metodi Ensemble attualmente supportati sono il voto e lo stack. Il voto viene implementato come soft-vote usando le medie ponderate e l'implementazione di stacking usa un'implementazione a 2 livelli, in cui il primo livello ha gli stessi modelli dell'insieme di voti e il secondo modello di livello viene usato per trovare la combinazione ottimale di modelli dal primo livello. Se si usano modelli ONNX **o** se è abilitata la spiegazione del modello, lo stacking verrà disabilitato e verrà usato solo il voto.

Sono disponibili più argomenti predefiniti che possono essere forniti come `kwargs` in un `AutoMLConfig` oggetto per modificare il comportamento predefinito dell'insieme dello stack.

* `stack_meta_learner_type`: il metaapprendimento è un modello di cui è stato eseguito il training sull'output dei singoli modelli di eterogeneo. I meta-Learning predefiniti sono `LogisticRegression` per le attività di classificazione `LogisticRegressionCV` (o se la convalida incrociata `ElasticNet` è abilitata) e per le attività di `ElasticNetCV` regressione/previsione (o se la convalida incrociata è abilitata). Questo parametro può essere una `LogisticRegression`delle stringhe seguenti: `LightGBMClassifier`, `LogisticRegressionCV`,, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`o `LinearRegression`.
* `stack_meta_learner_train_percentage`: specifica la proporzione del set di training (quando si sceglie il tipo di training di training e di convalida) da riservare per il training del meta-Learning. Il valore predefinito è `0.2`.
* `stack_meta_learner_kwargs`: parametri facoltativi da passare all'inizializzatore del meta-Learning. Questi parametri e tipi di parametro eseguono il mirroring di quelli del costruttore del modello corrispondente e vengono trasmessi al costruttore del modello.

Il codice seguente illustra un esempio di come specificare un comportamento di ensemble `AutoMLConfig` personalizzato in un oggetto.

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
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Il training di ensemble è abilitato per impostazione predefinita, ma può essere disabilitato `enable_voting_ensemble` usando `enable_stack_ensemble` i parametri booleani e.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Eseguire esperimento

Per l'utilizzo automatico di ml `Experiment` si crea un oggetto, ovvero un oggetto denominato `Workspace` in un oggetto usato per eseguire gli esperimenti.

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
Sono disponibili alcune opzioni che è possibile definire per completare l'esperimento.
1. Nessun criterio: se non si definiscono parametri di uscita, l'esperimento continuerà fino a quando non verrà effettuato ulteriore avanzamento sulla metrica primaria.
1. Numero di iterazioni: si definisce il numero di iterazioni per l'esecuzione dell'esperimento. È possibile aggiungere facoltativamente iteration_timeout_minutes per definire un limite di tempo in minuti per ogni iterazione.
1. Uscire dopo un periodo di tempo: usando experiment_timeout_minutes nelle impostazioni è possibile definire per quanto tempo, in minuti, un esperimento continuerà a essere eseguito.
1. Uscire dopo che è stato raggiunto un punteggio: usando experiment_exit_score è possibile scegliere di completare l'esperimento dopo che è stato raggiunto un punteggio in base alla metrica primaria.

### <a name="explore-model-metrics"></a>Esplorare le metriche del modello

È possibile visualizzare i risultati della formazione in un widget o inline se ci si trova in un notebook. Per altri dettagli, vedere [Tenere traccia dei modelli e valutarli](how-to-track-experiments.md#view-run-details).

## <a name="understand-automated-ml-models"></a>Informazioni sui modelli di Machine Learning automatizzati

Tutti i modelli prodotti con l'utilizzo automatico di ML includono i passaggi seguenti:
+ Progettazione automatica delle funzioni (se preprocess = true)
+ Ridimensionamento/normalizzazione e algoritmo con valori di ipermetria

Lo facciamo trasparente per ottenere queste informazioni dall'output di fitted_model da Machine Learning automatico.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Progettazione automatica delle funzioni

Vedere l'elenco delle funzionalità di pre-elaborazione e di [progettazione automatica delle funzioni](concept-automated-ml.md#preprocess) che si verificano quando pre-elaborazione = true.

Si consideri l'esempio seguente:
+ Sono disponibili 4 funzionalità di input: A (numerico), B (numerico), C (numerico), D (DateTime)
+ La funzione numerica C viene eliminata perché è una colonna ID con tutti i valori univoci
+ Le funzionalità numeriche A e B presentano valori mancanti e pertanto vengono imputate per mezzo
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
   |Eliminato|Indica se la funzionalità di input è stata eliminata o utilizzata.|
   |EngineeringFeatureCount|Numero di funzionalità generate tramite trasformazioni automatiche di progettazione delle funzionalità.|
   |Trasformazioni|Elenco di trasformazioni applicate alle funzionalità di input per generare funzionalità progettate.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Ridimensionamento/normalizzazione e algoritmo con valori di ipermetria:

Per comprendere i valori di ridimensionamento, normalizzazione e algoritmo/iperparametro per una pipeline, usare fitted_model. Steps. [Altre informazioni sul ridimensionamento o](concept-automated-ml.md#preprocess)la normalizzazione. Di seguito è riportato un output di esempio:

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

## <a name="explain-the-model-interpretability"></a>Descrizione del modello (interpretazione)

Il processo di Machine Learning automatizzato consente di riconoscere l'importanza delle caratteristiche.  Durante il training, è possibile ottenere l'importanza delle caratteristiche a livello globale per il modello.  Per gli scenari di classificazione, è anche possibile ottenere l'importanza delle caratteristiche a livello di classe.  Per ottenere l'importanza delle caratteristiche è necessario specificare un set di dati di convalida (X_valid).

È possibile generare l'importanza delle caratteristiche in due modi.

*   Al termine di un esperimento, è possibile usare il metodo `explain_model` su qualsiasi iterazione.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Per visualizzare l'importanza delle caratteristiche per tutte le iterazioni, impostare il flag `model_explainability` su `True` in AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Dopo aver eseguito questa operazione, è possibile usare il metodo retrieve_model_explanation per recuperare l'importanza delle caratteristiche per un'iterazione specifica.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

È possibile visualizzare il grafico relativo all'importanza delle caratteristiche nella propria area di lavoro nel portale di Azure. Visualizzare l'URL utilizzando l'oggetto Run:

```
automl_run.get_portal_url()
```

È possibile visualizzare il grafico relativo all'importanza delle caratteristiche nella propria area di lavoro nel portale di Azure. Il grafico viene visualizzato anche quando si usa `RunDetails` il [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) in un notebook. Per altre informazioni sui grafici, vedere l'articolo relativo ai [risultati automatici di Machine Learning](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![grafico relativo all'importanza delle caratteristiche](./media/how-to-configure-auto-train/feature-importance.png)

Per altre informazioni su come è possibile abilitare le spiegazioni dei modelli e l'importanza delle funzionalità in altre aree dell'SDK al di fuori dell'apprendimento automatico automatico, vedere l'articolo relativo al [concetto](machine-learning-interpretability-explainability.md) di interpretazione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

Altre informazioni su [come eseguire il training di un modello di regressione con Machine Learning automatizzato](tutorial-auto-train-models.md) o [su come eseguire il training usando Machine Learning automatico in una risorsa remota](how-to-auto-train-remote.md).
