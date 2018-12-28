---
title: Creare esperimenti di Machine Learning automatizzato
titleSuffix: Azure Machine Learning service
description: Il processo di Machine Learning automatizzato seleziona un algoritmo per l'utente e genera un modello pronto per la distribuzione. Informazioni sulle opzioni che è possibile usare per configurare esperimenti di Machine Learning automatizzato.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3dedf5de1ac2c88a9a00fd5f62e0663b840c0fd9
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438524"
---
# <a name="configure-automated-machine-learning-experiments"></a>Configurare esperimenti di Machine Learning automatizzato

Il processo di Machine Learning automatizzato seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. Per configurare esperimenti di Machine Learning automatizzato sono disponibili varie opzioni. In questa guida si apprenderà come definire le diverse impostazioni di configurazione.

Per visualizzare esempi di Machine Learning automatizzato, vedere [Esercitazione: Eseguire il training di un modello di classificazione con Machine Learning automatizzato](tutorial-auto-train-models.md) oppure [Eseguire il training di modelli con Machine Learning automatizzato nel cloud](how-to-auto-train-remote.md).

Opzioni di configurazione disponibili per il processo di Machine Learning automatizzato:

* Selezionare il tipo di esperimento: Classificazione, Regressione o Previsione
* Origine dati, formati, recupero di dati
* Scegliere la destinazione di calcolo, locale o remota
* Configurare le impostazioni di un esperimento di Machine Learning automatizzato
* Eseguire un esperimento di Machine Learning automatizzato
* Esplorare le metriche del modello
* Registrare e distribuire il modello

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento
Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di Machine Learning da risolvere. Il processo di Machine Learning automatizzato supporta attività di tipo classificazione, regressione e previsione. 

Mentre le funzionalità di Machine Learning automatizzato sono disponibili a livello generale, quella relativa alla **previsione è ancora in anteprima pubblica**.

Durante il processo di automazione e ottimizzazione, il processo di Machine Learning automatizzato supporta gli algoritmi seguenti. Come utente, non è necessario specificare l'algoritmo.

Classificazione | Regressione | Previsione
|-- |-- |--
[Regressione logistica](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Bayesiano naif](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Classificazione a vettori di supporto C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)
[SVC lineare](https://scikit-learn.org/stable/modules/svm.html#classification)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|


## <a name="data-source-and-format"></a>Origine dati e formato
Il processo di Machine Learning automatizzato supporta dati presenti nel desktop locale o nel cloud, ad esempio Archiviazione BLOB di Azure. I dati possono essere letti in base ai formati supportati da scikit-learn. È possibile leggere i dati in:
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

Chiave | Tipo |    Si esclude a vicenda con | Descrizione
---|---|---|---
X | Dataframe Pandas o matrice Numpy | data_train, label, columns |  Tutte le funzionalità per eseguire il training con
y | Dataframe Pandas o matrice Numpy |   label   | Dati per il training con etichetta. Per la classificazione, deve essere una matrice di interi.
X_valid | Dataframe Pandas o matrice Numpy   | data_train, label | _Facoltativo_ Tutte le funzionalità con cui convalidare. Se non specificato, X è suddiviso tra training e convalida
y_valid |   Dataframe Pandas o matrice Numpy | data_train, label | _Facoltativo_ Tutte le funzionalità con cui convalidare. Se non specificato, y è suddiviso tra training e convalida
sample_weight | Dataframe Pandas o matrice Numpy |   data_train, label, columns| _Facoltativo_ un valore di ponderazione per ogni esempio. Utilizzare questa opzione quando si vuole assegnare pesi diversi per i punti dati 
sample_weight_valid | Dataframe Pandas o matrice Numpy | data_train, label, columns |    _Facoltativo_ un valore di ponderazione per ogni esempio. Se non specificato, sample_weight è suddiviso tra training e convalida
data_train |    Dataframe Pandas |  X, y, X_valid, y_valid |    Tutti i dati (funzionalità + etichetta) con cui eseguire il training
label | stringa  | X, y, X_valid, y_valid |  Quale colonna in data_train rappresenta l'etichetta
columns | Matrice di stringhe  ||  _Facoltativo_ Elenco elementi consentiti di colonne da utilizzare per le funzionalità
cv_splits_indices   | Matrice di numeri interi ||  _Facoltativo_ Elenco di indici per dividere i dati per la convalida incrociata

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Caricare e preparare i dati con Data Prep SDK
Gli esperimenti di Machine Learning automatizzato supportano il caricamento dei dati e le trasformazioni con Data Prep SDK. Questo SDK offre la possibilità di

>* Caricare i dati da numerosi tipi di file con inferenza dei parametri di analisi (codifica, separatore, intestazioni)
>* Convertire i tipi mediante inferenza durante il caricamento dei file
>* Usufruire delle connessioni per Microsoft SQL Server e Azure Data Lake Storage
>* Aggiungere una colonna tramite un'espressione
>* Attribuire i valori mancanti
>* Derivare le colonne in base a un esempio
>* Applicare filtri
>* Eseguire trasformazioni Python personalizzate

Per informazioni su Data Prep SDK,vedere [Come preparare i dati per la modellazione](how-to-load-data.md). Di seguito è riportato un esempio di caricamento dei dati tramite Data Prep SDK. 
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

Usare `validation_size` per specificare la percentuale dei dati di training da usare per la convalida e usare `n_cross_validations` per specificare il numero convalide incrociate. Durante ogni ciclo di convalida incrociata, un subset di dimensioni `validation_size` verrà selezionato in modo casuale per la convalida del modello con training eseguito sui dati restanti. Infine, il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero training set.

### <a name="custom-validation-dataset"></a>Set di dati di convalida personalizzato

Usare set di dati di convalida personalizzati se la suddivisione casuale non è accettabile (in genere dati di time series o dati sbilanciati). È possibile specificare il proprio set di dati di convalida. Il modello verrà valutato rispetto al set di dati di convalida specificato anziché set di dati casuali.

## <a name="compute-to-run-experiment"></a>Calcolo per eseguire l'esperimento

Successivamente, determinare dove verrà eseguito il training del modello. Un esperimento di training di Machine Learning automatizzato può essere eseguito sulle risorse di calcolo seguenti:
*   Nel computer locale, ad esempio un desktop locale o un computer portatile: in genere quando si dispone di set di dati di piccole dimensioni e si è ancora in fase di esplorazione.
*   In un computer remoto nel cloud: l'[ambiente di calcolo gestito di Azure Machine Learning](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) è un servizio gestito che offre la possibilità di eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure.

Visitare il [sito GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl) per notebook di esempio con destinazioni di calcolo locali e remote.

<a name='configure-experiment'/>

## <a name="configure-your-experiment-settings"></a>Configurare le impostazioni dell'esperimento

Per configurare l'esperimento di Machine Learning automatizzato sono disponibili varie opzioni. Questi parametri vengono impostati creando un oggetto `AutoMLConfig`.

Di seguito sono riportati alcuni esempi:

1.  Esperimento di classificazione tramite AUC ponderato come metrica primaria con un tempo massimo di 12.000 secondi per ogni iterazione, con il termine dell'esperimento allo scadere di 50 iterazioni e 2 riduzioni di convalida incrociata.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  Di seguito è riportato un esempio di un set di esperimento di regressione da terminare allo scadere di 100 iterazioni, con ogni iterazione della durata fino a 600 secondi con 5 riduzioni di convalida incrociata.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

Questa tabella elenca le impostazioni dei parametri disponibili per l'esperimento e i relativi valori predefiniti.

Proprietà |  Descrizione | Valore predefinito
--|--|--
`task`  |Specificare il tipo di problema di machine learning. I valori consentiti sono <li>Classificazione</li><li>Regressione</li><li>Previsione</li>    | Nessuno |
`primary_metric` |Metrica che si desidera ottimizzare nella compilazione del modello. Se ad esempio si specifica accuracy come primary_metric, il processo di Machine Learning automatizzato cerca di trovare un modello con la massima accuratezza. È possibile specificare un solo valore di primary_metric per ogni esperimento. I valori consentiti sono <br/>**Classificazione**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Regressione**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score  </li> | Per la classificazione: precisione <br/>Per la regressione: spearman_correlation <br/> |
`experiment_exit_score` |   È possibile impostare un valore target per primary_metric. Una volta trovato un modello che soddisfa il target di primary_metric, il processo di Machine Learning automatizzato arresta le iterazioni e l'esperimento termina. Se questo valore non è impostato (impostazione predefinita), l'esperimento di Machine Learning automatizzato continuerà a eseguire il numero di iterazioni specificate in iterations. Accetta un valore double. Se il target non viene mai raggiunto, il processo di Machine Learning automatizzato continua finché non raggiunge il numero di iterazioni specificate in iterations.| Nessuno
`iterations` |Numero massimo di iterazioni. Ogni iterazione è uguale a un processo di training risultante in una pipeline. Una pipeline è una pre-elaborazione dei dati e il modello. Per ottenere un modello di alta qualità usarne almeno 250.    | 100
`max_concurrent_iterations`|    Numero massimo di iterazioni da eseguire in parallelo. Questa impostazione funziona solo per il calcolo remoto.|   1
`max_cores_per_iteration`   | Indica il numero di core nella destinazione di calcolo che verrà utilizzato per eseguire il training di una singola pipeline. Se l'algoritmo può sfruttare più core, ciò aumenta le prestazioni in un computer multicore. È possibile impostarlo su -1 per usare tutte le memorie centrali disponibili nel computer.|  1
`iteration_timeout_minutes` |   Limita la quantità di tempo (minuti) che impiega una determinata iterazione. Se un'iterazione supera la quantità specificata, viene annullata quell'iterazione. Se non impostata, l'iterazione continua a essere eseguita fino al termine. |   Nessuno
`n_cross_validations`   |Numero di suddivisioni di convalida incrociata| Nessuno
`validation_size`   |Dimensione della convalida impostata come percentuale dell'esempio di tutti i training.|  Nessuno
`preprocess` | True/False <br/>True permette all'esperimento di eseguire la pre-elaborazione dell'input. Di seguito è indicato un subset di pre-elaborazione<li>Dati mancanti: attribuisce i dati mancanti: numerici con media, testo con le maggiori occorrenze </li><li>Valori di categoria: se il tipo di dati è numerico e il numero di valori univoci è inferiore al 5%, converte in codifica one-hot </li><li>E così via, per l'elenco completo verificare [il repository di GitHub](https://aka.ms/aml-notebooks)</li><br/>Nota: se i dati sono di tipo sparse è possibile usare preprocess = true |  False | 
`blacklist_models`  | L'esperimento di Machine Learning automatizzato ha molti algoritmi diversi che prova ad applicare. Configurare il processo in modo da escludere determinati algoritmi dall'esperimento. È utile se si è a conoscenza del fatto che uno o più algoritmi non funzionano correttamente per il set di dati. L'esclusione di algoritmi può far risparmiare risorse di calcolo e tempi di training.<br/>Valori consentiti per la classificazione<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>Valori consentiti per la regressione<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>Valori consentiti per la previsione<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|   Nessuno
`whitelist_models`  | L'esperimento di Machine Learning automatizzato ha molti algoritmi diversi che prova ad applicare. Configurare il processo in modo da includere determinati algoritmi per l'esperimento. È utile se si è a conoscenza del fatto che uno o più algoritmi funzionano correttamente per il set di dati. <br/>Valori consentiti per la classificazione<br/><li>LogisticRegression</li><li>SGD</li><li>MultinomialNaiveBayes</li><li>BernoulliNaiveBayes</li><li>SVM</li><li>LinearSVM</li><li>KNN</li><li>DecisionTree</li><li>RandomForest</li><li>ExtremeRandomTrees</li><li>LightGBM</li><li>GradientBoosting</li><li>TensorFlowDNN</li><li>TensorFlowLinearClassifier</li><br/>Valori consentiti per la regressione<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li><br/>Valori consentiti per la previsione<br/><li>ElasticNet</li><li>GradientBoosting</li><li>DecisionTree</li><li>KNN</li><li>LassoLars</li><li>SGD </li><li>RandomForest</li><li>ExtremeRandomTree</li><li>LightGBM</li><li>TensorFlowLinearRegressor</li><li>TensorFlowDNN</li></li>|  Nessuno
`verbosity` |Controlla il livello di registrazione con INFO, il più dettagliato, e CRITICAL, il meno dettagliato. Il livello Verbosity accetta gli stessi valori definiti nel pacchetto di registrazione Python. I valori consentiti sono i seguenti:<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | Tutte le caratteristiche con cui eseguire il training |  Nessuno
`y` |   Dati delle etichette con cui eseguire il training. Per la classificazione, deve essere una matrice di interi.|  Nessuno
`X_valid`|_Facoltativo_ Tutte le caratteristiche con cui eseguire la convalida. Se non specificato, X è suddiviso tra training e convalida |   Nessuno
`y_valid`   |_Facoltativo_ Tutti i dati delle etichette con cui eseguire la convalida. Se non specificato, y è suddiviso tra training e convalida    | Nessuno
`sample_weight` |   _Facoltativo_ Un valore di ponderazione per ogni esempio. Utilizzare questa opzione quando si vuole assegnare pesi diversi per i punti dati |   Nessuno
`sample_weight_valid`   |   _Facoltativo_ Un valore di ponderazione per ogni esempio. Se non specificato, sample_weight è suddiviso tra training e convalida   | Nessuno
`run_configuration` |   Oggetto RunConfiguration.  Utilizzato per le esecuzioni remote. |Nessuno
`data_script`  |    Percorso di un file che contiene il metodo get_data.  Utilizzato per le esecuzioni remote.   |Nessuno
`model_explainability` | _Facoltativo_ True/False <br/>  True consente all'esperimento di applicare l'importanza delle caratteristiche per ogni iterazione. È anche possibile usare il metodo explain_model() in un'iterazione specifica per abilitare l'importanza delle caratteristiche su richiesta per tale iterazione al termine dell'esperimento. | False
`enable_ensembling`|Flag per consentire un'iterazione di insieme al termine di tutte le altre iterazioni.| True  
`ensemble_iterations`|Numero di iterazioni durante le quali si sceglie una pipeline adattata come parte dell'insieme finale.| 15
`experiment_timeout_minutes`| Limita la quantità di tempo (minuti) che può impiegare l'esecuzione dell'intero esperimento. | Nessuno

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

## <a name="run-experiment"></a>Eseguire l'esperimento

Avviare l'esperimento per eseguire e generare un modello. Passare l'oggetto `AutoMLConfig` al metodo `submit` per generare il modello.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Le dipendenze vengono prima installate in un nuovo computer.  Potrebbero occorrere fino a 10 minuti prima che venga visualizzato l'output.
>Se si imposta `show_output` su `True`, l'output viene visualizzato nella console.


## <a name="explore-model-metrics"></a>Esplorare le metriche del modello
È possibile visualizzare i risultati in un widget o inline in un notebook. Per altri dettagli, vedere [Tenere traccia dei modelli e valutarli](how-to-track-experiments.md#view-run-details).


### <a name="classification-metrics"></a>Metriche per la classificazione
In ogni iterazione per un'attività di classificazione vengono salvate le metriche seguenti.

|Metrica primaria|Descrizione|Calcolo|Parametri aggiuntivi
--|--|--|--|--|
AUC_Macro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Macro è la media aritmetica dell'area AUC per ogni classe.  | [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Micro è il valore calcolato globalmente combinando i veri positivi e i falsi positivi da ogni classe.| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Weighted è la media aritmetica del punteggio per ogni classe, ponderata in base al numero di istanze vere in ogni classe.| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|accuracy è la percentuale di etichette stimate che corrispondono esattamente alle etichette vere. |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nessuno|
average_precision_score_macro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. macro è la media aritmetica del punteggio di precisione media di ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. micro è il valore calcolato globalmente combinando i veri positivi e i falsi positivi in corrispondenza di ogni limite.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. weighted è la media aritmetica del punteggio di precisione media per ogni classe, ponderata in base al numero di istanze vere in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|balanced_accuracy è la media aritmetica del recupero per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|f1_score è la media armonica di precisione e recupero. macro è la media aritmetica di f1_score per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|f1_score è la media armonica di precisione e recupero. micro è il valore calcolato globalmente sommando i veri positivi, i falsi negativi e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|f1_score è la media armonica di precisione e recupero. weighted è la media calcolata in base alla frequenza di F1_score per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Questa è la funzione di perdita usata per la regressione logistica (multinomiale) e le relative estensioni, ad esempio le reti neurali, definita come probabilità logaritmica negativa delle etichette vere date le stime del classificatore probabilistico. Per un singolo campione con etichetta vera yt in {0,1} e una probabilità stimata yp tale che yt = 1, la perdita logaritmica è -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nessuno|
norm_macro_recall|norm_macro_recall è il recupero macro normalizzato in modo che le prestazioni causali abbiano un punteggio 0 e le prestazioni perfette abbiano un punteggio 1. Questo viene ottenuto con norm_macro_recall := (recall_score_macro - R)/(1 - R), dove R è il valore stimato di recall_score_macro per le stime casuali (ad esempio, R=0.5 per la classificazione binaria e R=(1/C) per i problemi di classificazione di classe C).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average = "macro" e quindi (recall_score_macro - R)/(1 - R), dove R è il valore stimato di recall_score_macro per le stime casuali (ad esempio, R=0.5 per la classificazione binaria e R=(1/C) per i problemi di classificazione di classe C)|
precision_score_macro|precision è la percentuale degli elementi cui è assegnata un'etichetta come una determinata classe e che si trovano effettivamente in tale classe. macro è la media aritmetica di precision per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|precision è la percentuale degli elementi cui è assegnata un'etichetta come una determinata classe e che si trovano effettivamente in tale classe. micro è il valore calcolato globalmente sommando i veri positivi e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|precision è la percentuale degli elementi cui è assegnata un'etichetta come una determinata classe e che si trovano effettivamente in tale classe. weighted è la media aritmetica della precisione per ogni classe, ponderata in base al numero di istanze vere in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|recall è la percentuale di elementi effettivamente presenti in una determinata classe cui è assegnata un'etichetta corretta. macro è la media aritmetica del recupero per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|recall è la percentuale di elementi effettivamente presenti in una determinata classe cui è assegnata un'etichetta corretta. micro è il valore calcolato globalmente sommando i veri positivi e i falsi negativi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|recall è la percentuale di elementi effettivamente presenti in una determinata classe cui è assegnata un'etichetta corretta. weighted è la media aritmetica del recupero per ogni classe, ponderata in base al numero di istanze vere in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|weighted_accuracy è l'accuratezza dove il valore di ponderazione definito per ogni esempio equivale alla proporzione delle istanze vere nella classe vera di tale esempio.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight è un vettore equivalente alla proporzione di tale classe per ogni elemento nel target|

### <a name="regression-and-forecasting-metrics"></a>Metriche per la regressione e la previsione
In ogni iterazione per un'attività di regressione o previsione vengono salvate le metriche seguenti.

|Metrica primaria|Descrizione|Calcolo|Parametri aggiuntivi
--|--|--|--|--|
explained_variance|explained_variance è la proporzione in base alla quale un modello matematico tiene conto della variazione di un determinato set di dati. Si tratta della riduzione percentuale della varianza dei dati originali rispetto alla varianza degli errori. Quando la media degli errori è 0, la varianza è perfettamente spiegata.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nessuno|
r2_score|R2 è il coefficiente di determinazione o la riduzione percentuale di errori quadratici rispetto a un modello di base che restituisce la media. Quando la media degli errori è 0, la varianza è perfettamente spiegata.|[Calcolo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nessuno|
spearman_correlation|La correlazione di Spearman è una misura non parametrica della monotonicità della relazione tra due set di dati. A differenza della correlazione di Pearson, quella di Spearman non presuppone che entrambi i set di dati siano normalmente distribuiti. Come altri coefficienti di correlazione, questo coefficiente varia da -1 a + 1, con 0 che implica l'assenza di correlazione. Le correlazioni con coefficiente -1 o + 1 implicano una relazione monotonica esatta. In caso di correlazione positiva, un incremento di x corrisponde a un incremento di y. In caso di correlazione negativa, un incremento di x corrisponde a un decremento di y.|[Calcolo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nessuno|
mean_absolute_error|mean_absolute_error è il valore stimato del valore assoluto della differenza tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nessuno|
normalized_mean_absolute_error|normalized_median_absolute_error è l'errore assoluto medio diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividere per l'intervallo dei dati|
median_absolute_error|median_absolute_error è il valore mediano di tutte le differenze assolute tra il target e la stima. Questa perdita è significativa per gli outlier.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nessuno|
normalized_median_absolute_error|normalized_median_absolute_error è l'errore assoluto mediano diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_error|root_mean_squared_error è la radice quadrata della differenza quadratica stimata tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nessuno|
normalized_root_mean_squared_error|normalized_root_mean_squared_error è la radice dell'errore quadratico medio divisa per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_log_error|root_mean_squared_log_error è la radice quadrata dell'errore logaritmico quadratico stimato.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nessuno|
normalized_root_mean_squared_log_error|normalized_root_mean_squared_log_error è la radice dell'errore logaritmico quadratico medio divisa per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividere per l'intervallo dei dati|

## <a name="explain-the-model"></a>Spiegare il modello

Mentre le funzionalità di Machine Learning automatizzato sono disponibili a livello generale, quella relativa alla **spiegabilità del modello è ancora in anteprima pubblica**.

Il processo di Machine Learning automatizzato consente di riconoscere l'importanza delle caratteristiche.  Durante il training, è possibile ottenere l'importanza delle caratteristiche a livello globale per il modello.  Per gli scenari di classificazione, è anche possibile ottenere l'importanza delle caratteristiche a livello di classe.  Per ottenere l'importanza delle caratteristiche è necessario specificare un set di dati di convalida (X_valid).

È possibile generare l'importanza delle caratteristiche in due modi.

*   Al termine di un esperimento, è possibile usare il metodo `explain_model` su qualsiasi iterazione.

    ```
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

    ```
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

    ```
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

È possibile visualizzare il grafico relativo all'importanza delle caratteristiche nella propria area di lavoro nel portale di Azure. Il grafico viene visualizzato anche quando si usa il widget di Jupyter in un notebook. Per altre informazioni sui grafici, vedere l'articolo sui [notebook di esempio di Azure Machine Learning](samples-notebooks.md).

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![grafico relativo all'importanza delle caratteristiche](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

Altre informazioni su [come eseguire il training di un modello di classificazione con Machine Learning automatizzato](tutorial-auto-train-models.md) oppure [come eseguire il training con Machine Learning automatizzato su una risorsa remota](how-to-auto-train-remote.md). 
