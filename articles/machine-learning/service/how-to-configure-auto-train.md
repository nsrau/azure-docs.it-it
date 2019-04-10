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
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 5aa9a60c624e1bfaa1570d02bfd1a421fcab3301
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358300"
---
# <a name="configure-automated-machine-learning-experiments"></a>Configurare esperimenti di Machine Learning automatizzato

Il processo di Machine Learning automatizzato seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. Per configurare esperimenti di Machine Learning automatizzato sono disponibili varie opzioni. In questa guida si apprenderà come definire le diverse impostazioni di configurazione.

Per visualizzare esempi di Machine Learning automatizzato, vedere [Esercitazione: Eseguire il training di un modello di classificazione con Machine Learning automatizzato](tutorial-auto-train-models.md) oppure [Eseguire il training di modelli con Machine Learning automatizzato nel cloud](how-to-auto-train-remote.md).

Opzioni di configurazione disponibili nell'apprendimento automatico:

* Selezionare il tipo di esperimento: Classificazione, regressione o previsione in serie temporale
* Origine dati, formati, dati di recupero
* Scegliere la destinazione di calcolo, locale o remota
* Configurare le impostazioni di un esperimento di Machine Learning automatizzato
* Eseguire un esperimento di Machine Learning automatizzato
* Esplorare le metriche del modello
* Registrare e distribuire modelli

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento
Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di machine learning da risolvere. Il processo di Machine Learning automatizzato supporta attività di tipo classificazione, regressione e previsione.

Durante il processo di automazione e ottimizzazione, il processo di Machine Learning automatizzato supporta gli algoritmi seguenti. Come utente, non è necessario specificare l'algoritmo. Sebbene gli algoritmi di rete neurale profonda siano disponibili durante il training, Machine Learning automatizzati non compila i modelli di rete neurale profonda.

classificazione | Regressione | Previsione in serie temporale
|-- |-- |--
[Regressione logistica](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Boosting a gradienti](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Boosting a gradienti](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Boosting a gradienti](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)
[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC lineare](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS lazo](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS lazo](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificazione di vettori di supporto di C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Gradiente Stocastica (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Gradiente Stocastica (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Strutture ad albero molto casuale](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Strutture ad albero molto casuale](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Strutture ad albero molto casuale](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificazione di rete neurale profonda](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressore rete neurale profonda](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressore rete neurale profonda](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Funzione di classificazione lineare di rete neurale profonda](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Gradiente Stocastica (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|


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
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Recuperare i dati per l'esecuzione dell'esperimento a risorse di calcolo remote

Se si usa un computer remoto per eseguire l'esperimento, l'operazione di recupero di dati deve essere racchiusa in uno script python separato `get_data()`. Questo script viene eseguito nella risorsa di calcolo remota in cui viene eseguito l'esperimento di Machine Learning automatizzato. `get_data` Elimina la necessità di recuperare i dati trasmessi in rete per ogni iterazione. Senza `get_data`, l'esperimento avrà esito negativo quando viene eseguito su risorse di calcolo remoto.

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

`get_data` lo script può restituire:

Chiave | Type | Si escludono a vicenda con    | DESCRIZIONE
---|---|---|---
X | Dataframe Pandas o matrice Numpy | data_train, etichetta, colonne |  Tutte le funzionalità per eseguire il training con
y | Dataframe Pandas o matrice Numpy |   label   | Dati per il training con etichetta. Per la classificazione, deve essere una matrice di interi.
X_valid | Dataframe Pandas o matrice Numpy   | data_train, etichetta | _Facoltativo_ Tutte le funzionalità con cui convalidare. Se non specificato, X è suddiviso tra training e convalida
y_valid |   Dataframe Pandas o matrice Numpy | data_train, etichetta | _Facoltativo_ Tutte le funzionalità con cui convalidare. Se non specificato, y è suddiviso tra training e convalida
sample_weight | Dataframe Pandas o matrice Numpy |   data_train, etichetta, colonne| _Facoltativo_ un valore di ponderazione per ogni esempio. Utilizzare questa opzione quando si vuole assegnare pesi diversi per i punti dati
sample_weight_valid | Dataframe Pandas o matrice Numpy | data_train, etichetta, colonne |    _Facoltativo_ un valore di ponderazione per ogni esempio. Se non specificato, sample_weight è suddiviso tra training e convalida
data_train |    Dataframe Pandas |  X, y, X_valid, y_valid |    Tutti i dati (funzionalità + etichetta) con cui eseguire il training
label | stringa  | X, y, X_valid, y_valid |  Quale colonna in data_train rappresenta l'etichetta
columns | Matrice di stringhe  ||  _Facoltativo_ Elenco elementi consentiti di colonne da utilizzare per le funzionalità
cv_splits_indices   | Matrice di numeri interi ||  _Facoltativo_ Elenco di indici per dividere i dati per la convalida incrociata

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Caricare e preparare i dati con Data Prep SDK
Gli esperimenti di Machine Learning automatizzato supportano il caricamento dei dati e le trasformazioni con Data Prep SDK. Questo SDK offre la possibilità di

>* Caricamento da numerosi tipi di file con inferenza dei parametri di analisi (codifica, separatore, intestazioni)
>* Conversione del tipo mediante l'inferenza durante il caricamento dei file
>* Supporto delle connessioni per Microsoft SQL Server e Azure Data Lake Storage
>* Aggiungere una colonna tramite un'espressione
>* Attribuire i valori mancanti
>* Derivare le colonne in base a un esempio
>* Filtri
>* Trasformazioni di Python personalizzate

Per informazioni su Data Prep SDK,vedere [Come preparare i dati per la modellazione](how-to-load-data.md).
Di seguito è riportato un esempio di caricamento dei dati tramite Data Prep SDK.
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>Dati di training e convalida

È possibile specificare training e convalide separati impostati tramite get_data() o direttamente nel metodo `AutoMLConfig`.

## <a name="cross-validation-split-options"></a>Opzioni di suddivisioni di convalida incrociata

### <a name="k-folds-cross-validation"></a>Convalida incrociata K-Folds

Usare l'impostazione `n_cross_validations` per specificare il numero di convalide incrociate. Il set di dati di training verrà suddiviso in modo casuale in riduzioni `n_cross_validations` di dimensioni uguali. Durante ogni ciclo di convalida incrociata, una delle riduzioni verrà utilizzata per la convalida del modello con training eseguito sulle riduzioni restanti. Questo processo viene ripetuto per `n_cross_validations` cicli fino a quando ciascuna riduzione non viene utilizzata una sola volta come set di convalida. Il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero set di dati di training. 

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Monte Carlo Cross Validation (ovvero sottocampionamento casuale ripetuto)

Usare `validation_size` per specificare la percentuale dei dati di training da usare per la convalida e usare `n_cross_validations` per specificare il numero convalide incrociate. Durante ogni ciclo di convalida incrociata, un subset di dimensioni `validation_size` verrà selezionato in modo casuale per la convalida del modello con training eseguito sui dati restanti. Infine, il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero training set. Monte Carlo non è supportata per la serie storiche.

### <a name="custom-validation-dataset"></a>Set di dati di convalida personalizzato

Usare set di dati di convalida personalizzate se non è accettabile suddivisione casuale, in genere dati di serie temporali o dati sbilanciati. È possibile specificare il proprio set di dati di convalida. Il modello verrà valutato rispetto al set di dati di convalida specificato anziché set di dati casuali.

## <a name="compute-to-run-experiment"></a>Calcolo per eseguire l'esperimento

Successivamente, determinare dove verrà eseguito il training del modello. Un esperimento di training di Machine Learning automatizzato può essere eseguito sulle risorse di calcolo seguenti:
*   Nel computer locale, ad esempio un desktop locale o un computer portatile: in genere quando si dispone di set di dati di piccole dimensioni e si è ancora in fase di esplorazione.
*   In un computer remoto nel cloud: l'[ambiente di calcolo gestito di Azure Machine Learning](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) è un servizio gestito che offre la possibilità di eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure.

Visitare il [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl) per notebook di esempio con destinazioni di calcolo locali e remote.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurare le impostazioni di esperimento

Per configurare l'esperimento di Machine Learning automatizzato sono disponibili varie opzioni. Questi parametri vengono impostati creando un oggetto `AutoMLConfig`. Vedere la [Classe AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) per un elenco completo dei parametri.  

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

I tre diversi `task` i valori dei parametri determinano l'elenco degli algoritmi da applicare.  Usare i parametri `whitelist` o `blacklist` per modificare ulteriormente le iterazioni con gli algoritmi disponibili da includere o escludere. L'elenco dei modelli supportati sono reperibili nel [SupportedAlgorithms classe](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py)

## <a name="primary-metric"></a>Metrica primaria
La principale metrica; come illustrato negli esempi precedenti determina la metrica da utilizzare durante il training del modello per l'ottimizzazione. La principale metrica che è possibile selezionare è determinata dal tipo di attività che scelto. Di seguito è riportato un elenco delle metriche disponibili.

|classificazione | Regressione | Previsione in serie temporale
|-- |-- |--
|precisione| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

## <a name="data-pre-processing-and-featurization"></a>Definizione delle caratteristiche e pre-elaborazione dei dati

Se si usa `preprocess=True`, i passaggi seguenti di pre-elaborazione dei dati vengono eseguiti automaticamente:
1.  Eliminazione delle caratteristiche con elevata cardinalità o senza varianza
    * Vengono eliminate le caratteristiche senza informazioni utili dai set di training e convalida. Queste includono le caratteristiche con tutti i valori mancanti, con lo stesso valore in tutte le righe o con cardinalità molto elevata, ad esempio hash, ID o GUID.
1.  Attribuzione di valori mancanti
    *   Per le caratteristiche numeriche, vengono attribuiti i valori mancanti con la media dei valori nella colonna.
    *   Per le caratteristiche di categoria, vengono attribuiti i valori mancanti con il valore più frequente.
1.  Generazione di caratteristiche aggiuntive
    * Per le caratteristiche di tipo DateTime: anno, mese, giorno, giorno della settimana, giorno dell'anno, trimestre, settimana dell'anno, ora, minuti, secondi.
    * Per le caratteristiche di tipo Text: frequenza del termine in base alla vettorializzazione di unigrammi, digrammi e trigrammi di parole.
1.  Trasformazioni e codifiche
    * Le caratteristiche numeriche con un numero molto ridotto di valori univoci vengono trasformate in caratteristiche di categoria.
    * A seconda della cardinalità delle caratteristiche di categoria, viene eseguita la codifica delle etichette o la codifica one-hot (hashing).

## <a name="time-series-forecasting"></a>Previsione in serie temporale
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
                             enable_ensembling=False,
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

## <a name="exit-criteria"></a>Criteri uscita 
Sono riportate alcune opzioni è possibile definire per completare l'esperimento.
1. Alcun criterio - se non si definisce uno chiudere i parametri che dell'esperimento continuerà fino a quando non viene definito alcun ulteriori progressi l'unità di misura primaria. 
1. Numero di iterazioni - definire il numero di iterazioni per l'esperimento per l'esecuzione. È possibile facoltativo aggiungere iteration_timeout_minutes per definire un limite di tempo in minuti per ogni iterazione.
1. Chiuso dopo un periodo di tempo - usando experiment_timeout_minutes nelle impostazioni che è possibile definire la durata in minuti dovrebbe continuare un esperimento in esecuzione.
1. Uscire da dopo che è stato raggiunto un punteggio - usando experiment_exit_score che è possibile scegliere di completare il experiement dopo che è stato raggiunto un punteggio in base l'unità di misura primaria.

## <a name="explore-model-metrics"></a>Esplorare le metriche del modello
È possibile visualizzare i risultati in un widget o inline in un notebook. Per altri dettagli, vedere [Tenere traccia dei modelli e valutarli](how-to-track-experiments.md#view-run-details).


### <a name="classification-metrics"></a>Metriche per la classificazione
In ogni iterazione per un'attività di classificazione vengono salvate le metriche seguenti.

|Metrica|DESCRIZIONE|Calcolo|Parametri aggiuntivi
--|--|--|--|
AUC_Macro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Macro è la media aritmetica dell'area AUC per ogni classe.  | [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Micro è il valore calcolato globalmente combinando i veri positivi e i falsi positivi da ogni classe| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Weighted è la media aritmetica del punteggio per ogni classe, ponderata in base al numero di istanze vere in ogni classe.| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
precisione|accuracy è la percentuale di etichette stimate che corrispondono esattamente alle etichette vere. |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nessuna|
average_precision_score_macro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. macro è la media aritmetica del punteggio di precisione media di ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. micro è il valore calcolato globalmente combinando i veri positivi e i falsi positivi in corrispondenza di ogni limite.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. weighted è la media aritmetica del punteggio di precisione media per ogni classe, ponderata in base al numero di istanze vere in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|balanced_accuracy è la media aritmetica del recupero per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|f1_score è la media armonica di precisione e recupero. macro è la media aritmetica di f1_score per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|f1_score è la media armonica di precisione e recupero. micro è il valore calcolato globalmente sommando i veri positivi, i falsi negativi e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|f1_score è la media armonica di precisione e recupero. weighted è la media calcolata in base alla frequenza di F1_score per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Questa è la funzione di perdita usata per la regressione logistica (multinomiale) e le relative estensioni, ad esempio le reti neurali, definita come probabilità logaritmica negativa delle etichette vere date le stime del classificatore probabilistico. Per un singolo campione con etichetta vera yt in {0,1} e una probabilità stimata yp tale che yt = 1, la perdita logaritmica è -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nessuna|
norm_macro_recall|norm_macro_recall è il recupero macro normalizzato in modo che le prestazioni causali abbiano un punteggio 0 e le prestazioni perfette abbiano un punteggio 1. Questo viene ottenuto con norm_macro_recall := (recall_score_macro - R)/(1 - R), dove R è il valore stimato di recall_score_macro per le stime casuali (ad esempio, R=0.5 per la classificazione binaria e R=(1/C) per i problemi di classificazione di classe C).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average = "macro" e quindi (recall_score_macro - R)/(1 - R), dove R è il valore stimato di recall_score_macro per le stime casuali (ad esempio, R=0.5 per la classificazione binaria e R=(1/C) per i problemi di classificazione di classe C)|
precision_score_macro|precision è la percentuale degli elementi cui è assegnata un'etichetta come una determinata classe e che si trovano effettivamente in tale classe. macro è la media aritmetica di precision per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|precision è la percentuale degli elementi cui è assegnata un'etichetta come una determinata classe e che si trovano effettivamente in tale classe. micro è il valore calcolato globalmente sommando i veri positivi e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|precision è la percentuale degli elementi cui è assegnata un'etichetta come una determinata classe e che si trovano effettivamente in tale classe. weighted è la media aritmetica della precisione per ogni classe, ponderata in base al numero di istanze vere in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|recall è la percentuale di elementi effettivamente presenti in una determinata classe cui è assegnata un'etichetta corretta. macro è la media aritmetica del recupero per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|recall è la percentuale di elementi effettivamente presenti in una determinata classe cui è assegnata un'etichetta corretta. micro è il valore calcolato globalmente sommando i veri positivi e i falsi negativi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|recall è la percentuale di elementi effettivamente presenti in una determinata classe cui è assegnata un'etichetta corretta. weighted è la media aritmetica del recupero per ogni classe, ponderata in base al numero di istanze vere in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|weighted_accuracy è l'accuratezza dove il valore di ponderazione definito per ogni esempio equivale alla proporzione delle istanze vere nella classe vera di tale esempio.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight è un vettore equivalente alla proporzione di tale classe per ogni elemento nel target|

### <a name="regression-and-time-series-forecasting-metrics"></a>Le metriche di previsione in serie la regressione e ora
In ogni iterazione per un'attività di regressione o previsione vengono salvate le metriche seguenti.

|Metrica|DESCRIZIONE|Calcolo|Parametri aggiuntivi
--|--|--|--|
explained_variance|explained_variance è la proporzione in base alla quale un modello matematico tiene conto della variazione di un determinato set di dati. Si tratta della riduzione percentuale della varianza dei dati originali rispetto alla varianza degli errori. Quando la media degli errori è 0, la varianza è perfettamente spiegata.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nessuna|
r2_score|R2 è il coefficiente di determinazione o la riduzione percentuale di errori quadratici rispetto a un modello di base che restituisce la media. Quando la media degli errori è 0, la varianza è perfettamente spiegata.|[Calcolo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nessuna|
spearman_correlation|La correlazione di Spearman è una misura non parametrica della monotonicità della relazione tra due set di dati. A differenza della correlazione di Pearson, quella di Spearman non presuppone che entrambi i set di dati siano normalmente distribuiti. Come altri coefficienti di correlazione, questo coefficiente varia da -1 a + 1, con 0 che implica l'assenza di correlazione. Le correlazioni con coefficiente -1 o + 1 implicano una relazione monotonica esatta. In caso di correlazione positiva, un incremento di x corrisponde a un incremento di y. In caso di correlazione negativa, un incremento di x corrisponde a un decremento di y.|[Calcolo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nessuna|
mean_absolute_error|mean_absolute_error è il valore stimato del valore assoluto della differenza tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nessuna|
normalized_mean_absolute_error|normalized_median_absolute_error è l'errore assoluto medio diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividere per l'intervallo dei dati|
median_absolute_error|median_absolute_error è il valore mediano di tutte le differenze assolute tra il target e la stima. Questa perdita è significativa per gli outlier.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nessuna|
normalized_median_absolute_error|normalized_median_absolute_error è l'errore assoluto mediano diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_error|root_mean_squared_error è la radice quadrata della differenza quadratica stimata tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nessuna|
normalized_root_mean_squared_error|normalized_root_mean_squared_error è la radice dell'errore quadratico medio divisa per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_log_error|root_mean_squared_log_error è la radice quadrata dell'errore logaritmico quadratico stimato.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nessuna|
normalized_root_mean_squared_log_error|normalized_root_mean_squared_log_error è la radice dell'errore logaritmico quadratico medio divisa per l'intervallo dei dati|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividere per l'intervallo dei dati|

## <a name="explain-the-model"></a>Spiegare il modello

Mentre le funzionalità di Machine Learning automatizzato sono disponibili a livello generale, quella relativa alla **spiegabilità del modello è ancora in anteprima pubblica**.

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

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![grafico relativo all'importanza delle caratteristiche](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

Altre informazioni sulle [come eseguire il training di un modello di regressione con Automated machine learning](tutorial-auto-train-models.md) oppure [come eseguire il training usando automatizzati di machine learning in una risorsa remota](how-to-auto-train-remote.md).
