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
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperfq1
ms.openlocfilehash: 6d05f5fa20b5deee14b1a2fada389d869d48908a
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91709088"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurare esperimenti di ML automatizzato in Python


Questa guida illustra come definire diverse impostazioni di configurazione degli esperimenti di Machine Learning automatizzato con [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true). Il processo di Machine Learning automatizzato seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. Per configurare esperimenti di Machine Learning automatizzato sono disponibili varie opzioni.

Per visualizzare esempi di esperimenti automatici di Machine Learning, vedere [esercitazione: eseguire il training di un modello di classificazione con Machine Learning automatizzato](tutorial-auto-train-models.md) o eseguire il [training di modelli con Machine Learning automatico nel cloud](how-to-auto-train-remote.md).

Opzioni di configurazione disponibili nell'apprendimento automatico:

* Selezionare il tipo di esperimento: Classificazione, Regressione o Previsione serie temporale
* Origine dati, formati, dati di recupero
* Scegliere la destinazione di calcolo, locale o remota
* Configurare le impostazioni di un esperimento di Machine Learning automatizzato
* Eseguire un esperimento di Machine Learning automatizzato
* Esplorare le metriche del modello
* Registrare e distribuire modelli

Se si preferisce un'esperienza senza codice, è anche possibile [Creare gli esperimenti di Machine Learning automatizzato in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Prerequisiti

Per questo articolo è necessario, 
* Un'area di lavoro di Azure Machine Learning. Per creare l'area di lavoro, vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

* Azure Machine Learning Python SDK installato.
    Per installare l'SDK, è possibile eseguire una delle due 
    * Creare un'istanza di calcolo, che installa automaticamente l'SDK ed è preconfigurata per i flussi di lavoro ML. Per altre informazioni, vedere [creare e gestire un'istanza di calcolo Azure Machine Learning](how-to-create-manage-compute-instance.md) . 

    * [Installare l'SDK manualmente](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). Assicurarsi di includere l' `automl` ulteriore. 

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento

Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di machine learning da risolvere. Machine Learning automatizzato supporta i tipi di attività `classification` , `regression` e `forecasting` . Altre informazioni sui [tipi di attività](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Il codice seguente usa il `task` parametro nel `AutoMLConfig` costruttore per specificare il tipo di esperimento come `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Origine dati e formato

Il processo di Machine Learning automatizzato supporta dati presenti nel desktop locale o nel cloud, ad esempio Archiviazione BLOB di Azure. I dati possono essere letti in un **dataframe Pandas** o in un **TabularDataset di Azure Machine Learning**. [Altre informazioni sui set di dati](how-to-create-register-datasets.md).

Requisiti per i dati di training:
- I dati devono essere in formato tabulare.
- Il valore da stimare, la colonna di destinazione, deve essere presente nei dati.

**Per gli esperimenti remoti**, i dati di training devono essere accessibili dal calcolo remoto. AutoML accetta [Azure Machine Learning TabularDatasets](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) solo quando si lavora su un calcolo remoto. 

Azure Machine Learning i set di impostazioni espongono la funzionalità per:

* Trasferire facilmente i dati da file statici o origini URL nell'area di lavoro.
* Rendere i dati disponibili per gli script di training durante l'esecuzione di risorse di calcolo cloud. Per un esempio dell'uso della classe per montare i dati nella destinazione di calcolo remota, vedere [come eseguire il training con i set](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) di `Dataset` dati.

Il codice seguente crea un TabularDataset da un URL Web. Vedere [creare un TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) per esempi di codice su come creare set di dati da altre origini, ad esempio file e archivi dati locali.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Per gli esperimenti di calcolo locali**, è consigliabile usare i dataframe di Pandas per velocizzare i tempi di elaborazione.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Dati di training, convalida e test

È possibile specificare **set di training e di convalida** distinti direttamente nel `AutoMLConfig` costruttore. Altre informazioni su [come configurare le suddivisioni dei dati e la convalida incrociata](how-to-configure-cross-validation-data-splits.md) per gli esperimenti AutoML. 

Se non si specifica in modo esplicito `validation_data` un `n_cross_validation` parametro o, AutoML applica le tecniche predefinite per determinare la modalità di esecuzione della convalida. Questa determinazione dipende dal numero di righe nel set di dati assegnato al `training_data` parametro. 

|&nbsp;Dimensioni dati di training &nbsp;| Tecnica di convalida |
|---|-----|
|**Maggiore &nbsp; di &nbsp; 20.000 &nbsp; righe**| Viene applicata la suddivisione dei dati di Training/convalida. Il valore predefinito prevede il 10% del set di dati di training iniziale come set di convalida. A sua volta, il set di convalida viene usato per il calcolo delle metriche.
|**Inferiore &nbsp; a &nbsp; 20.000 &nbsp; righe**| Viene applicato l'approccio per la convalida incrociata. Il numero predefinito di riduzioni dipende dal numero di righe. <br> **Se il set di dati è inferiore a 1.000 righe**, vengono utilizzate 10 riduzioni. <br> **Se le righe sono comprese tra 1.000 e 20.000**, vengono usate tre riduzioni.

A questo punto, è necessario fornire i propri **dati di test** per la valutazione del modello. Per un esempio di codice relativo all'introduzione dei propri dati di test per la valutazione del modello, vedere la sezione **test** di [questo notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Calcolo per eseguire l'esperimento

Successivamente, determinare dove verrà eseguito il training del modello. Un esperimento di training di Machine Learning automatizzato può essere eseguito sulle seguenti opzioni di calcolo. Scopri i [vantaggi e gli svantaggi delle opzioni di calcolo locali e remote](concept-automated-ml.md#local-remote) . 

* Il computer **locale** , ad esempio un desktop o un portatile locale, in genere quando si dispone di un set di dati di piccole dimensioni e si è ancora in fase di esplorazione. Vedere [questo notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) per un esempio di calcolo locale. 
 
* Un computer **remoto** nel cloud: [Azure Machine Learning calcolo gestito](concept-compute-target.md#amlcompute) è un servizio gestito che consente di eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure. 

    Vedere [questo notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) per un esempio remoto con Azure Machine Learning calcolo gestito. 

* Un **cluster Azure Databricks** nella sottoscrizione di Azure. Per altri dettagli, vedere l'articolo [relativo alla configurazione Azure Databricks cluster per](how-to-configure-environment.md#aml-databricks)Machine Learning automatico. Visitare il [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) per esempi di notebook con Azure Databricks.

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
       blocked_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
1. L'esempio seguente è un esperimento di regressione impostato per terminare dopo 60 minuti con cinque riduzioni incrociate di convalida.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      allowed_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```


1. Le attività di previsione richiedono un'installazione aggiuntiva. per altre informazioni, vedere l'articolo relativo al [training automatico di un modello di previsione delle serie temporali](how-to-auto-train-forecast.md) . 

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
    
### <a name="supported-models"></a>Modelli supportati

Il Machine Learning automatizzato tenta diversi modelli e algoritmi durante il processo di automazione e ottimizzazione. Come utente, non è necessario specificare l'algoritmo. 

I tre `task` valori di parametro diversi determinano l'elenco di algoritmi, o modelli, da applicare. Usare i parametri `allowed_models` o `blocked_models` per modificare ulteriormente le iterazioni con i modelli disponibili da includere o escludere. 

Nella tabella seguente sono riepilogati i modelli supportati in base al tipo di attività. 

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
[Averaged Perceptron Classifier](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest&preserve-view=true)|[Online Gradient Descent Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest&preserve-view=true) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Fast Linear Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest&preserve-view=true)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastic Gradient Descent (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Linear SVM Classifier](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest&preserve-view=true)*||

### <a name="primary-metric"></a>Metrica primaria
Il `primary metric` parametro determina la metrica da utilizzare durante il training del modello per l'ottimizzazione. Le metriche disponibili che è possibile selezionare sono determinate dal tipo di attività selezionato. La tabella seguente mostra le metriche primarie valide per ogni tipo di attività.

Per informazioni sulle definizioni specifiche di queste metriche, vedere [Risultati del Machine Learning automatizzato](how-to-understand-automated-ml.md).

|Classificazione | Regressione | Previsione serie temporale
|--|--|--
|precisione| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>Definizione delle funzionalità dei dati

In ogni esperimento di Machine Learning automatizzato i dati vengono dimensionati e normalizzati automaticamente per *determinati* algoritmi sensibili a funzionalità su scale diverse. Questo ridimensionamento e normalizzazione viene definito conteggi. Per altri dettagli ed esempi di codice, vedere [conteggi in AutoML](how-to-configure-auto-features.md#) . 

Quando si configurano gli esperimenti nell' `AutoMLConfig` oggetto, è possibile abilitare o disabilitare l'impostazione `featurization` . Nella tabella seguente vengono illustrate le impostazioni accettate per conteggi nell' [oggetto AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Configurazione della definizione delle funzionalità | Descrizione |
| ------------- | ------------- |
|`"featurization": 'auto'`| Indica che i passaggi di [protezione dati e definizione delle funzionalità](how-to-configure-auto-features.md#featurization) vengono eseguiti automaticamente come parte della pre-elaborazione. **Impostazione predefinita**.|
|`"featurization": 'off'`| Indica che il passaggio conteggi non deve essere eseguito automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indica che deve essere usata la definizione delle funzionalità personalizzata. [Informazioni su come personalizzare la definizione delle funzionalità](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> I passaggi di definizione delle funzionalità di Machine Learning automatizzato (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione dei valori di testo in formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di definizione delle funzionalità applicati durante il training vengono automaticamente applicati ai dati di input.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> Configurazione Ensemble

I modelli di ensemble sono abilitati per impostazione predefinita e vengono visualizzati come iterazioni di esecuzione finale in un'esecuzione AutoML. Attualmente sono supportati **VotingEnsemble** e **StackEnsemble** . 

Il voto implementa il voto soft che usa medie ponderate. L'implementazione di stacking usa un'implementazione a due livelli, in cui il primo livello ha gli stessi modelli dell'insieme di voti e il secondo modello di livello viene usato per trovare la combinazione ottimale dei modelli dal primo livello. 

Se si usano modelli ONNX **o** se è abilitata la spiegazione del modello, lo stacking è disabilitato e viene usato solo il voto.

Il training di ensemble può essere disabilitato usando i `enable_voting_ensemble` `enable_stack_ensemble` parametri booleani e.

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

Per modificare il comportamento dell'insieme predefinito, sono presenti più argomenti predefiniti che possono essere forniti come `kwargs` in un `AutoMLConfig` oggetto.

> [!IMPORTANT]
>  I parametri seguenti non sono parametri espliciti della classe AutoMLConfig. 

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

 <a name="exit"></a> 

### <a name="exit-criteria"></a>Criteri uscita

È possibile definire alcune opzioni per terminare l'esperimento.

|Criteri| description
|----|----
Nessun &nbsp; criterio | Se non si definiscono parametri di uscita, l'esperimento continua fino a quando non viene eseguito alcun ulteriore avanzamento sulla metrica primaria.
Dopo &nbsp; un &nbsp; periodo &nbsp; di &nbsp; tempo| Usare `experiment_timeout_minutes` nelle impostazioni per definire per quanto tempo, in minuti, l'esperimento continuerà a essere eseguito. <br><br> Per evitare errori di timeout dell'esperimento, è necessario un minimo di 15 minuti o 60 minuti se le dimensioni della riga per colonna superano 10 milioni.
&nbsp; &nbsp; È &nbsp; stato raggiunto un &nbsp; Punteggio| Usare `experiment_exit_score` completa l'esperimento dopo che è stato raggiunto un punteggio della metrica primaria specificato.

## <a name="explore-models-and-metrics"></a>Esplora i modelli e le metriche

È possibile visualizzare i risultati del training in un widget o inline in un notebook. Per altri dettagli, vedere [Tenere traccia dei modelli e valutarli](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs).

Vedere [informazioni sui risultati automatici di Machine Learning](how-to-understand-automated-ml.md) per le definizioni e gli esempi dei grafici delle prestazioni e delle metriche disponibili per ogni esecuzione. 

Per ottenere un riepilogo di conteggi e comprendere quali funzionalità sono state aggiunte a un particolare modello, vedere la pagina relativa alla [trasparenza di conteggi](how-to-configure-auto-features.md#featurization-transparency). 

## <a name="register-and-deploy-models"></a>Registrare e distribuire modelli

Per informazioni dettagliate su come scaricare o registrare un modello per la distribuzione in un servizio Web, vedere [come e dove distribuire un modello](how-to-deploy-and-where.md).

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

+ Informazioni su come eseguire il training di più modelli con AutoML nell' [acceleratore della soluzione many Models](https://aka.ms/many-models).
