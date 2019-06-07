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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: df05bd984667283b0ccc143ba14fff6b35d69144
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753167"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurare gli esperimenti di Machine Learning automatizzati in Python

In questa guida informazioni su come definire diverse impostazioni di configurazione di automatizzati esperimenti di machine learning con il [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Il processo di Machine Learning automatizzato seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. Per configurare esperimenti di Machine Learning automatizzato sono disponibili varie opzioni.

Per visualizzare esempi di Machine Learning automatizzato, vedere [Esercitazione: Eseguire il training di un modello di classificazione con Machine Learning automatizzato](tutorial-auto-train-models.md) oppure [Eseguire il training di modelli con Machine Learning automatizzato nel cloud](how-to-auto-train-remote.md).

Opzioni di configurazione disponibili nell'apprendimento automatico:

* Selezionare il tipo di esperimento: Classificazione, regressione o previsione in serie temporale
* Origine dati, formati, dati di recupero
* Scegliere la destinazione di calcolo, locale o remota
* Configurare le impostazioni di un esperimento di Machine Learning automatizzato
* Eseguire un esperimento di Machine Learning automatizzato
* Esplorare le metriche del modello
* Registrare e distribuire modelli

Se non si preferisce un'alcuna esperienza di codice, è anche possibile [automatizzati esperimenti di machine learning nel portale di Azure creare](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento

Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di machine learning da risolvere. Il processo di Machine Learning automatizzato supporta attività di tipo classificazione, regressione e previsione.

Durante il processo di automazione e ottimizzazione, il processo di Machine Learning automatizzato supporta gli algoritmi seguenti. Come utente, non è necessario specificare l'algoritmo. Sebbene gli algoritmi di rete neurale profonda siano disponibili durante il training, Machine Learning automatizzati non compila i modelli di rete neurale profonda.

classificazione | Regressione | Previsione in serie temporale
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
[Classificatore rete neurale profonda](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressore rete neurale profonda](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressore rete neurale profonda](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Funzione di classificazione lineare di rete neurale profonda](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesiano naif](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Usare la `task` parametro nel `AutoMLConfig` costruttore per specificare il tipo di esperimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Origine dati e formato
Il processo di Machine Learning automatizzato supporta dati presenti nel desktop locale o nel cloud, ad esempio Archiviazione BLOB di Azure. I dati possono essere letti in scikit-learn su formati di dati supportati. È possibile leggere i dati in:
* Matrici Numpy X (caratteristiche) e y (variabile di destinazione, nota anche come etichetta)
* Dataframe Pandas

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

Se si usa un computer remoto per eseguire l'esperimento, l'operazione di recupero di dati deve essere racchiusa in uno script python separato `get_data()`. Questo script viene eseguito nella risorsa di calcolo remota in cui viene eseguito l'esperimento di Machine Learning automatizzato. `get_data` elimina la necessità di recuperare i dati trasmessi in rete per ogni iterazione. Senza `get_data`, l'esperimento avrà esito negativo quando viene eseguito su risorse di calcolo remoto.

Di seguito è fornito un esempio di `get_data`:

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

Nell'oggetto `AutoMLConfig`, specificare il parametro `data_script` e specificare il percorso di file di script `get_data` simile al seguente:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

Lo script `get_data` può restituire:

Chiave | Type | Si escludono a vicenda con    | Descrizione
---|---|---|---
X | Dataframe Pandas o matrice Numpy | data_train, etichetta, colonne |  Tutte le funzionalità per eseguire il training con
y | Dataframe Pandas o matrice Numpy |   label   | Dati per il training con etichetta. Per la classificazione, deve essere una matrice di interi.
X_valid | Dataframe Pandas o matrice Numpy   | data_train, etichetta | _Facoltativo_ dati che costituisce il set di convalida di funzionalità. Se non specificato, X è suddiviso tra training e convalida
y_valid |   Dataframe Pandas o matrice Numpy | data_train, etichetta | _Facoltativo_ Tutte le funzionalità con cui convalidare. Se non specificato, y è suddiviso tra training e convalida
sample_weight | Dataframe Pandas o matrice Numpy |   data_train, etichetta, colonne| _Facoltativo_ un valore di ponderazione per ogni esempio. Utilizzare questa opzione quando si vuole assegnare pesi diversi per i punti dati
sample_weight_valid | Dataframe Pandas o matrice Numpy | data_train, etichetta, colonne |    _Facoltativo_ un valore di ponderazione per ogni esempio. Se non specificato, sample_weight è suddiviso tra training e convalida
data_train |    Dataframe Pandas |  X, y, X_valid, y_valid |    Tutti i dati (funzionalità + etichetta) con cui eseguire il training
label | string  | X, y, X_valid, y_valid |  Quale colonna in data_train rappresenta l'etichetta
columns | Matrice di stringhe  ||  _Facoltativo_ Elenco elementi consentiti di colonne da utilizzare per le funzionalità
cv_splits_indices   | Matrice di numeri interi ||  _Facoltativo_ Elenco di indici per dividere i dati per la convalida incrociata

## <a name="train-and-validation-data"></a>Dati di training e convalida

È possibile specificare training e convalide separati impostati tramite get_data() o direttamente nel metodo `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>Convalida incrociata K-Folds

Usare l'impostazione `n_cross_validations` per specificare il numero di convalide incrociate. Il set di dati di training verrà suddiviso in modo casuale in riduzioni `n_cross_validations` di dimensioni uguali. Durante ogni ciclo di convalida incrociata, una delle riduzioni verrà utilizzata per la convalida del modello con training eseguito sulle riduzioni restanti. Questo processo viene ripetuto per `n_cross_validations` cicli fino a quando ciascuna riduzione non viene utilizzata una sola volta come set di convalida. Il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero set di dati di training.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo Cross Validation (ripetuta sottocampionamento casuale)

Usare `validation_size` per specificare la percentuale dei dati di training da usare per la convalida e usare `n_cross_validations` per specificare il numero convalide incrociate. Durante ogni ciclo di convalida incrociata, un subset di dimensioni `validation_size` verrà selezionato in modo casuale per la convalida del modello con training eseguito sui dati restanti. Infine, il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero training set. Monte Carlo non è supportata per la serie storiche.

### <a name="custom-validation-dataset"></a>Set di dati di convalida personalizzato

Usare set di dati di convalida personalizzate se non è accettabile suddivisione casuale, in genere dati di serie temporali o dati sbilanciati. È possibile specificare il proprio set di dati di convalida. Il modello verrà valutato rispetto al set di dati di convalida specificato anziché set di dati casuali.

## <a name="compute-to-run-experiment"></a>Calcolo per eseguire l'esperimento

Successivamente, determinare dove verrà eseguito il training del modello. Un esperimento di training di Machine Learning automatizzato può essere eseguito sulle risorse di calcolo seguenti:
*   Nel computer locale, ad esempio un desktop locale o un computer portatile: in genere quando si dispone di set di dati di piccole dimensioni e si è ancora in fase di esplorazione.
*   In un computer remoto nel cloud: l'[ambiente di calcolo gestito di Azure Machine Learning](concept-compute-target.md#amlcompute) è un servizio gestito che offre la possibilità di eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure.

Visitare il [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) per notebook di esempio con destinazioni di calcolo locali e remote.

*   Un cluster Azure Databricks nella sottoscrizione di Azure. È possibile trovare informazioni dettagliate: [il programma di installazione Azure Databricks del cluster per Machine Learning automatizzata](how-to-configure-environment.md#azure-databricks)

Vedere le [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , ad esempio i notebook con Azure Databricks.

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

I tre diversi `task` i valori dei parametri determinano l'elenco degli algoritmi da applicare.  Usare i parametri `whitelist` o `blacklist` per modificare ulteriormente le iterazioni con gli algoritmi disponibili da includere o escludere. L'elenco dei modelli supportati sono reperibili sul [SupportedAlgorithms classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Metrica primaria
La principale metrica; come illustrato negli esempi precedenti determina la metrica da utilizzare durante il training del modello per l'ottimizzazione. La principale metrica che è possibile selezionare è determinata dal tipo di attività che scelto. Di seguito è riportato un elenco delle metriche disponibili.

|classificazione | Regressione | Previsione in serie temporale
|-- |-- |--
|precisione| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Pre-elaborazione dei dati & definizione delle funzionalità

In ogni automatizzato esperimento di machine learning, i dati siano [automaticamente ridimensionata e normalizzati](concept-automated-ml.md#preprocess) offrono prestazioni adeguate per gli algoritmi.  Tuttavia, è anche possibile abilitare aggiuntive pre-elaborazione/definizione delle funzionalità, ad esempio mancanti imputation valori, la codifica e le trasformazioni. [Altre informazioni su quali definizione delle funzionalità è inclusa](how-to-create-portal-experiments.md#preprocess).

Per abilitare questa definizione delle funzionalità, specificare `"preprocess": True` per il [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Previsione in serie temporale
Per tipo di attività previsione serie ora si dispone di parametri aggiuntivi da definire.
1. time_column_name - si tratta di un parametro obbligatorio che definisce il nome della colonna nella serie di data/ora di formazione su dati che lo contiene.
1. max_horizon - definisce il periodo di tempo da prevedere in base alla periodicità dei dati di training. Ad esempio se si dispone di dati di training con intervalli di tempo giornaliera, è definire la distanza orizzontale in giorni in cui eseguire il training per il modello.
1. grain_column_names - definisce il nome delle colonne che contengono dati delle serie temporali singole in dati di training. Ad esempio, se si sono previsione delle vendite di una particolare marca dall'archivio, si definirebbe marchio e archivio colonne come colonne del livello di dettaglio.

Vedere l'esempio di queste impostazioni in uso di sotto, esempio di notebook è disponibile [qui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

## <a name="run-experiment"></a>Eseguire esperimento

Avviare l'esperimento per eseguire e generare un modello. Passare il `AutoMLConfig` al metodo `submit` per generare il modello.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Le dipendenze vengono prima installate in un nuovo computer.  Potrebbero occorrere fino a 10 minuti prima che venga visualizzato l'output.
>Se si imposta `show_output` su `True`, l'output viene visualizzato nella console.

### <a name="exit-criteria"></a>Criteri uscita
Sono riportate alcune opzioni è possibile definire per completare l'esperimento.
1. Alcun criterio - se non si definisce uno chiudere i parametri che dell'esperimento continuerà fino a quando non viene definito alcun ulteriori progressi l'unità di misura primaria.
1. Numero di iterazioni - definire il numero di iterazioni per l'esperimento per l'esecuzione. È possibile facoltativo aggiungere iteration_timeout_minutes per definire un limite di tempo in minuti per ogni iterazione.
1. Chiuso dopo un periodo di tempo - usando experiment_timeout_minutes nelle impostazioni che è possibile definire la durata in minuti dovrebbe continuare un esperimento in esecuzione.
1. Uscire da dopo che è stato raggiunto un punteggio - usando experiment_exit_score che è possibile scegliere di completare l'esperimento una volta raggiunto un punteggio in base l'unità di misura primaria.

### <a name="explore-model-metrics"></a>Esplorare le metriche del modello

È possibile visualizzare i risultati di training in un widget o inline in un notebook. Per altri dettagli, vedere [Tenere traccia dei modelli e valutarli](how-to-track-experiments.md#view-run-details).

## <a name="understand-automated-ml-models"></a>Comprendere i modelli di Machine Learning automatizzati

Qualsiasi modello di prodotto usando automatizzati di Machine Learning include i passaggi seguenti:
+ Automated progettazione di funzionalità (se pre-elaborazione = True)
+ Ridimensionamento/normalizzazione e l'algoritmo con i valori hypermeter

Rendiamo trasparente per ottenere queste informazioni dall'output fitted_model da Machine Learning automatizzati.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Progettazione di funzionalità automatizzata

Visualizzare l'elenco di pre-elaborazione e [automatizzato di progettazione di funzionalità](concept-automated-ml.md#preprocess) che accade quando pre-elaborazione = True.

Considerare questo esempio:
+ Esistono 4 funzionalità input: A (numerico), (numerico) B, C (numerico), D (DateTime)
+ Funzioni numeriche C viene eliminato perché è una colonna ID con tutti i valori univoci
+ Funzioni numeriche A e B sono valori mancanti e di conseguenza vengono attribuiti con mean
+ Funzionalità di data/ora D viene trasformato in 11 funzioni progettate diversi

Usare questi 2 le API nel primo passaggio del modello adattato per altre informazioni.  Visualizzare [questo notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` restituisce un elenco di nomi di funzione progettata.

  Utilizzo:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Questo elenco include tutti i nomi di funzione progettata.

  >[!Note]
  >Per attività, usare 'timeseriestransformer' = 'previsione', usare 'datatransformer' per 'regressione' o 'classificazione' attività.

+ API 2: `get_featurization_summary()` restituisce definizione delle funzionalità riepilogo per tutte le funzionalità di input.

  Utilizzo:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Per attività, usare 'timeseriestransformer' = 'previsione', usare 'datatransformer' per 'regressione' o 'classificazione' attività.

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
   |RawFeatureName|Nome di input o colonna di funzioni dal set di dati fornito.|
   |TypeDetected|Rilevato tipo di dati della funzionalità di input.|
   |Eliminato|Indica se la funzionalità di input è stata eliminata o usata.|
   |EngineeringFeatureCount|Numero di funzioni generate tramite le trasformazioni di progettazione di funzionalità automatizzata.|
   |Trasformazioni|Elenco di trasformazioni applicate alle funzionalità per generare funzioni progettate di input.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Ridimensionamento/normalizzazione e algoritmo con i valori hypermeter:

Per comprendere i valori di ridimensionamento/normalizzazione e algoritmo/degli iperparametri per una pipeline, usare fitted_model.steps. [Altre informazioni sulla scalabilità/normalizzazione](concept-automated-ml.md#preprocess). Di seguito è riportato un output di esempio:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Per altre informazioni, usare questa funzione di supporto illustrata nella [questo notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(fitted_model)
```

Di seguito è riportato un output per una pipeline usando un algoritmo specifico (LogisticRegression con RobustScalar, in questo caso).

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

## <a name="explain-the-model-interpretability"></a>Spiegazione del modello (interpretazione)

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

È possibile visualizzare il grafico relativo all'importanza delle caratteristiche nella propria area di lavoro nel portale di Azure. Il grafico viene visualizzato anche quando si usa il widget di Jupyter in un notebook. Per altre informazioni sui grafici, vedere l'articolo sui [notebook di esempio del servizio Azure Machine Learning](samples-notebooks.md).

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![grafico relativo all'importanza delle caratteristiche](./media/how-to-configure-auto-train/feature-importance.png)

Per altre informazioni sul modo in cui una spiegazione del modello e importanza delle caratteristiche possono essere abilitati in altre aree del SDK di fuori di apprendimento automatico, vedere la [concetto](machine-learning-interpretability-explainability.md) articolo sull'interpretazione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

Altre informazioni sulle [come eseguire il training di un modello di regressione con Automated machine learning](tutorial-auto-train-models.md) oppure [come eseguire il training usando automatizzati di machine learning in una risorsa remota](how-to-auto-train-remote.md).
