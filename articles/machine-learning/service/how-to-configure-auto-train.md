---
title: Configurare l'esperimento automatizzato di apprendimento automatico - Azure Machine Learning
description: L'apprendimento automatico automatizzato seleziona un algoritmo per l'utente e genera un modello pronto per la distribuzione. Informazioni sulle opzioni che è possibile usare per configurare esperimenti di apprendimento automatico.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5c75b462c3b1201eb70c1028c748def5da114b92
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823300"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>Configurare l'esperimento automatizzato di apprendimento automatico

L'apprendimento automatico automatizzato (ML automatizzato) seleziona un algoritmo e iperparametri per l'utente e genera un modello pronto per la distribuzione. È possibile scaricare il modello per personalizzarlo ulteriormente. Vi sono diverse opzioni che è possibile usare per configurare esperimenti automatizzati di apprendimento automatico. In questa guida si apprenderà come definire diverse impostazioni di configurazione.

Per visualizzare esempi di un Machine Learning automatizzato, vedere [Esercitazione: eseguire il training di un modello di classificazione di apprendimento automatico](tutorial-auto-train-models.md) oppure [Eseguire il training di modelli di apprendimento automatico nel cloud](how-to-auto-train-remote.md).

Opzioni di configurazione disponibili nell'apprendimento automatico:

* Selezionare il tipo di esperimento, ad esempio, classificazione, regressione 
* Origine dati, formati, dati di recupero
* Scegliere la destinazione di calcolo (locale o remota)
* Le impostazioni di esperimento automatizzato di Machine Learning
* Eseguire un esperimento automatizzato di Machine Learning
* Esplorare le metriche del modello
* Registrare e distribuire modelli

## <a name="select-your-experiment-type"></a>Selezionare il tipo di esperimento
Prima di iniziare l'esperimento, è necessario determinare il tipo di problema di machine learning da risolvere. L'apprendimento automatico supporta due categorie di apprendimento supervisionato: classificazione e regressione. L'apprendimento automatico supporta i seguenti algoritmi durante l'automazione e il processo di ottimizzazione. Come utente, non è necessario specificare l'algoritmo.
classificazione | Regressione
--|--
sklearn.linear_model.LogisticRegression | sklearn.linear_model.ElasticNet
sklearn.linear_model.SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes.BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes.MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model.LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model.SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm.LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm.LGBMClassifier |


## <a name="data-source-and-format"></a>Origine dati e formato
L'apprendimento automatico supporta i dati che si trovano sul desktop locale o nel cloud in un archiviazione BLOB di Azure. I dati possono essere letti in scikit-learn su formati di dati supportati. È possibile leggere i dati in 1) matrici Numpy X (funzionalità) e y (variabile di destinazione o noto anche come etichetta) o 2) dataframe Pandas. 

Esempi:

1.  Matrici Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  Dataframe Pandas

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Recuperare i dati per l'esecuzione dell'esperimento a risorse di calcolo remote

Se si usa un computer remoto per eseguire l'esperimento, l'operazione di recupero di dati deve essere racchiusa in uno script python separato `get_data()`. Questo script viene eseguito in attività di calcolo remota in cui viene eseguito l'esperimento di Machine Learning automatizzato. `get_data` elimina la necessità di recuperare i dati trasmessi in rete per ogni iterazione. Senza `get_data`, l'esperimento avrà esito negativo quando viene eseguito su risorse di calcolo remoto.

Di seguito è fornito un esempio di `get_data`:

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
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

Lo script `get_data` può restituire quanto segue:
Chiave | type |    Si escludono a vicenda con | DESCRIZIONE
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

## <a name="train-and-validation-data"></a>Dati di training e convalida

È possibile specificare training e convalide separati impostati tramite get_data() o direttamente nel metodo `AutoMLConfig`.

## <a name="cross-validation-split-options"></a>Opzioni di suddivisioni di convalida incrociata

### <a name="k-folds-cross-validation"></a>Convalida incrociata K-Folds

Usare l'impostazione `n_cross_validations` per specificare il numero di convalide incrociate. Il set di dati di training verrà suddiviso in modo casuale in riduzioni `n_cross_validations` di dimensioni uguali. Durante ogni ciclo di convalida incrociata, una delle riduzioni verrà utilizzata per la convalida del modello con training eseguito sulle riduzioni restanti. Questo processo viene ripetuto per `n_cross_validations` cicli fino a quando ciascuna riduzione non viene utilizzata una sola volta come set di convalida. Infine, il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero training set.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Monte Carlo Cross Validation (ovvero sottocampionamento casuale ripetuto)

Usare `validation_size` per specificare la percentuale dei dati di training da usare per la convalida e usare `n_cross_validations` per specificare il numero convalide incrociate. Durante ogni ciclo di convalida incrociata, un subset di dimensioni `validation_size` verrà selezionato in modo casuale per la convalida del modello con training eseguito sui dati restanti. Infine, il punteggio medio di tutti i cicli `n_cross_validations` verrà segnalato e il modello corrispondente verrà sottoposto nuovamente a training sull'intero training set.

### <a name="custom-validation-dataset"></a>Set di dati di convalida personalizzato

Usare set di dati di convalida personalizzati se la suddivisione casuale non è accettabile (in genere dati di time series o dati sbilanciati). Con questa impostazione, è possibile specificare il proprio set di dati di convalida. Il modello verrà valutato rispetto al set di dati di convalida specificato anziché set di dati casuali.

## <a name="compute-to-run-experiment"></a>Calcolo per eseguire l'esperimento

Successivamente, determinare dove verrà eseguito il training del modello. Un esperimento di training di Machine Learning automatico viene eseguito su una destinazione di calcolo che si possiede e gestisce. 

Le opzioni di calcolo supportate sono:
1.  Nel computer locale, ad esempio un desktop locale o un computer portatile: in genere quando si dispone di set di dati di piccole dimensioni e si è ancora in fase di esplorazione.
2.  Un computer remoto nel cloud - [Data Science Virtual Machine di Azure](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) su cui è in esecuzione Linux: si dispone di un set di dati di grandi dimensioni e si desidera aumentare le prestazioni in un computer di grandi dimensioni che è disponibile nel cloud di Azure. 
3.  Cluster Batch per intelligenza artificiale: un cluster gestito che è possibile configurare per la scalabilità orizzontale ed eseguire le iterazioni di Machine Learning automatizzato in parallelo. 

<a name='configure-experiment'/>
## <a name="configure-your-experiment-settings"></a>Configurare le impostazioni di esperimento

Vi sono diverse opzioni che è possibile usare per configurare esperimenti automatizzati di apprendimento automatico. Questi parametri vengono impostati creando un oggetto `AutoMLConfig`.

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

Proprietà |  DESCRIZIONE | Default Value
--|--|--
`task`  |Specificare il tipo di problema di machine learning. I valori consentiti sono <li>classificazione</li><li>Regressione</li>    | Nessuna |
`primary_metric` |Metrica che si desidera ottimizzare nella compilazione del modello. Ad esempio, se si specifica come precisione primary_metric, l'apprendimento automatico cerca di trovare un modello con la precisione massima. È possibile specificare solo un primary_metric per ogni esperimento. I valori consentiti sono <br/>**Classificazione**:<br/><li> precisione  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Regressione**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | Per la classificazione: precisione <br/>Per la regressione: spearman_correlation <br/> |
`exit_score` |  È possibile impostare un valore di destinazione per il primary_metric. Dopo aver trovato un modello che soddisfa la destinazione primary_metric, l'apprendimento automatico arresterà l'iterazione e termina l'esperimento. Se questo valore non è impostato (impostazione predefinita), l'esperimento di Machine Learning automatizzato continuerà a eseguire il numero di iterazioni specificate in iterazioni. Accetta un valore double. Se la destinazione non viene mai raggiunta, il machine learning automatizzato continuerà fino a quando non raggiunge il numero di iterazioni specificate in iterazioni.|   Nessuna
`iterations` |Numero massimo di iterazioni. Ogni iterazione è uguale a un processo di training risultante in una pipeline. Una pipeline è una pre-elaborazione dei dati e il modello. Per ottenere un modello di alta qualità usarne 250 o più | 100
`Concurrent_iterations`|    Numero massimo di iterazioni da eseguire in parallelo. Questa impostazione funziona solo per il calcolo remoto.|    1
`max_cores_per_iteration`   | Indica il numero di core nella destinazione di calcolo che verrà utilizzato per eseguire il training di una singola pipeline. Se l'algoritmo può sfruttare più core, ciò aumenta le prestazioni in un computer multicore. È possibile impostarlo su -1 per usare tutte le memorie centrali disponibili nel computer.|  1
`max_time_sec` |    Limita la quantità di tempo (secondi) che richiede un'iterazione specifica. Se un'iterazione supera la quantità specificata, viene annullata quell'iterazione. Se non impostata, l'iterazione continua a essere eseguita fino al termine. |   Nessuna
`n_cross_validations`   |Numero di suddivisioni di convalida incrociata| Nessuna
`validation_size`   |Dimensione della convalida impostata come percentuale dell'esempio di tutti i training.|  Nessuna
`preprocess` | True/False <br/>True permette all'esperimento di eseguire la pre-elaborazione dell'input. Di seguito è indicato un subset di pre-elaborazione<li>Dati mancanti: attribuisce i dati mancanti - numerici con media, testo con la maggior parte dell'occorrenza </li><li>Valori categorici: se il tipo di dati è numerico e il numero di valori univoci è inferiore al 5%, converte in codifica one-hot </li><li>E così via, per l'elenco completo verificare [il repository di GitHub](https://aka.ms/aml-notebooks)</li><br/>Nota: se i dati sono di tipo sparso è possibile utilizzare la pre-elaborazione = true |  False | 
`blacklist_algos`   | L'esperimento di Machine Learning automatizzato ha molti algoritmi diversi che prova. Configurare il Machine Learning automatizzato per escludere determinati algoritmi dall'esperimento. È utile se si è consapevoli del fatto che gli algoritmi non funzionano correttamente per il set di dati. L'esclusione di algoritmi può far risparmiare risorse di calcolo e tempi di training.<br/>Valori consentiti per la classificazione<br/><li>Regressione logistica</li><li>Classificatore SGD</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>alberi aggiuntivi</li><li>boosting a gradienti</li><li>lgbm_classifier</li><br/>Valori consentiti per la regressione<br/><li>Net elastico</li><li>Regressore con boosting a gradienti</li><li>Regressore DT</li><li>Regressore kNN</li><li>Lars lasso</li><li>Regressore SGD</li><li>Regressore RF</li><li>regressore alberi aggiuntivi</li>|   Nessuna
`verbosity` |Controlla il livello di registrazione con INFO che è il più verboso e CRITICAL che è il minore.<br/>I valori consentiti sono i seguenti:<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | Tutte le funzionalità per eseguire il training con |  Nessuna
`y` |   Dati per il training con etichetta. Per la classificazione, deve essere una matrice di interi.|  Nessuna
`X_valid`|_Facoltativo_ Tutte le funzionalità con cui convalidare. Se non specificato, X è suddiviso tra training e convalida |   Nessuna
`y_valid`   |_Facoltativo_ Tutte le funzionalità con cui convalidare. Se non specificato, y è suddiviso tra training e convalida    | Nessuna
`sample_weight` |   _Facoltativo_ un valore di ponderazione per ogni esempio. Utilizzare questa opzione quando si vuole assegnare pesi diversi per i punti dati |   Nessuna
`sample_weight_valid`   |   _Facoltativo_ un valore di ponderazione per ogni esempio. Se non specificato, sample_weight è suddiviso tra training e convalida   | Nessuna
`run_configuration` |   Oggetto RunConfiguration.  Utilizzato per le esecuzioni remote. |Nessuna
`data_script`  |    Percorso di un file che contiene il metodo get_data.  Utilizzato per le esecuzioni remote.   |Nessuna


## <a name="run-experiment"></a>Eseguire esperimento

Successivamente, è possibile avviare l'esperimento per eseguire e generare un modello. Passare il `AutoMLConfig` al metodo `submit` per generare il modello.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Le dipendenze sono prima installate in una nuova Data Science Virtual Machine.  Potrebbero occorrere fino a 10 minuti prima che venga visualizzato l'output.
>Impostando `show_output` su True, l'output viene visualizzato nella console.


## <a name="explore-model-metrics"></a>Esplorare le metriche del modello
È possibile visualizzare i risultati in un widget o inline in un notebook. Vedere i dettagli nella sezione "Monitoraggio e valutazione dei modelli". (assicurarsi che il contenuto di Azure Machine Learning contenga le informazioni rilevanti per il Machine Learning automatizzato)

Le metriche seguenti vengono salvate in ogni iterazione
* AUC_macro
* AUC_micro
* AUC_weighted
* precisione
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

Altre informazioni su [come eseguire il training di un modello di classificazione con Machine Learning automatizzato](tutorial-auto-train-models.md) oppure [come eseguire il training con Machine Learning automatizzato su una risorsa remota](how-to-auto-train-remote.md). 