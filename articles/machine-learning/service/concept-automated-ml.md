---
title: Selezione e ottimizzazione dell'algoritmo di ML automatizzato
titleSuffix: Azure Machine Learning service
description: Informazioni su come il servizio Azure Machine Learning è in grado di scegliere automaticamente un algoritmo e, a partire da questo, generare un modello per consentire all'utente di risparmiare tempo. Tale scelta viene effettuata usando i parametri e i criteri specificati per selezionare l'algoritmo migliore per il modello.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 16d100256f9252b478500488c2dc5a01c7e6a0b5
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418071"
---
# <a name="what-is-automated-machine-learning"></a>Informazioni sulle funzionalità automatizzate di Machine Learning

Apprendimento automatico, noto anche come automatizzati ML, è il processo di automazione delle attività che richiedeva molto tempo e iterativa di sviluppo del modello di apprendimento automatico. Consente ai data Scientist, agli analisti e sviluppatori compilare modelli di Machine Learning con scalabilità elevata, efficienza e produttività durante tutte le qualità dei modelli di solo supporto.

Lo sviluppo di modelli di apprendimento tradizionale è un numero di risorse, che richiedono informazioni di dominio significativo e l'ora per produrre e confrontare decine di modelli. Applicare ML automatizzato quando si vuole che Azure Machine Learning per addestrare e ottimizzare un modello usando la metrica di destinazione che si specifica. Il servizio scorre quindi gli algoritmi di Machine Learning abbinati con selezioni di funzionalità, dove ogni iterazione produce un modello con un punteggio di training. Maggiore sarà il punteggio, migliore sarà il modello viene considerato "adatta" i dati.

Con automatizzati di machine learning, si sarà accelerare il tempo che necessario per ottenere modelli di Machine Learning pronti per la produzione con efficienza e semplicità eccezionali.

## <a name="when-to-use-automated-ml"></a>Quando usare automatizzati di Machine Learning

Machine Learning automatizzati rivoluziona il processo di sviluppo del modello di apprendimento e offre ai propri utenti, indipendentemente dalla loro esperienza di analisi scientifica dei dati, per identificare una pipeline di end-to-end machine learning per qualsiasi problema.

I data Scientist, agli analisti e sviluppatori per settori diversi possono usare ML automatizzati per:

+ Implementare soluzioni di machine learning senza una profonda conoscenza di programmazione
+ Risparmiare tempo e risorse
+ Usare le procedure consigliate analisi scientifica dei dati
+ Fornire agile risoluzione dei problemi

## <a name="how-automated-ml-works"></a>Funziona come automatizzati di Machine Learning

Usando **servizio di Azure Machine Learning**, è possibile progettare ed eseguire gli esperimenti di training di Machine Learning automatizzati con questi passaggi:

1. **Identificare il problema di Machine Learning** da risolvere: classificazione, le previsioni o regressione

1. **Specificare l'origine e il formato dei dati di training con etichette**: Le matrici Numpy o dataframe di Pandas

1. **Configurare la destinazione di calcolo per il training del modello**, ad esempio il [computer locale, Azure Machine Learning Calcola, le macchine virtuali remote o Azure Databricks](how-to-set-up-training-targets.md).  Informazioni su formazione automatizzato [su una risorsa remota](how-to-auto-train-remote.md).

1. **Configurare l'automatizzati di machine learning parametri** che determinano il numero di iterazioni su diversi modelli, le impostazioni degli iperparametri, advanced pre-elaborazione/definizione delle funzionalità e le metriche da esaminare durante la determinazione del modello migliore.  È possibile configurare le impostazioni per l'esperimento di training automatici [nel portale di Azure](how-to-create-portal-experiments.md) oppure [con il SDK](how-to-configure-auto-train.md).

1. **Inviare l'esecuzione di training.**

  ![Apprendimento automatico](./media/how-to-automated-ml/automl-concept-diagram2.png)

Durante il training, il servizio di Azure Machine Learning crea un numero di nelle pipeline parallele che prova diversi algoritmi e parametri. Viene arrestata quando raggiunge i criteri di uscita definiti nell'esperimento.

È anche possibile esaminare le informazioni di esecuzione registrate, contenenti metriche raccolte durante l'esecuzione. L'esecuzione di training produce un oggetto serializzato di Python (`.pkl` file) che contiene il modello e dati pre-elaborazione.

Durante la compilazione del modello è automatizzata, è anche possibile [informazioni su come importanti o utili funzionalità sono](how-to-configure-auto-train.md#explain) ai modelli generati.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pre-elaborazione

In ogni automatizzato esperimento di machine learning, i dati viene pre-elaborati utilizzando i metodi predefiniti e, facoltativamente, tramite avanzate pre-elaborazione.

### <a name="automatic-preprocessing-standard"></a>Automatic pre-elaborazione (standard)

In ogni automatizzato esperimento di machine learning, i dati viene automaticamente ridimensionati o normalizzati offrono prestazioni adeguate per gli algoritmi.  Durante il training del modello, una delle seguenti tecniche scalano o normalizzano autonomamente verrà applicata a ogni modello.

|Ridimensionamento&nbsp;&&nbsp;normalizzazione| Descrizione |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizzare funzionalità rimuovendo la media e ridimensionamento in base alla varianza unitaria  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Trasforma le funzionalità da ogni funzionalità per tale colonna minimo e massimo di ridimensionamento  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Ridimensionare ogni funzionalità mediante il valore assoluto massimo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Questa funzionalità di Scaler dal relativo intervallo quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Riduzione della dimensionalità lineare utilizzando scomposizione di valori singolari dei dati, proiettare su uno spazio dimensionale inferiore |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Questa trasformazione consente di eseguire la riduzione della dimensionalità lineare mezzo di scomposizione di valori singolari troncati (SVD). Contrariamente a PCA, questo strumento di stima non allineare al centro dati prima di calcolare la scomposizione di valori singolari. Ciò significa che può funzionare in modo efficiente con matrici scipy.sparse |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Ciascun esempio (vale a dire, ogni riga della matrice dei dati) con almeno un componente diverso da zero è nuovamente in scala indipendentemente da altri esempi in modo che sua norma (l1 o l2) è uguale a uno |

### <a name="advanced-preprocessing-optional-featurization"></a>Advanced pre-elaborazione: definizione delle funzionalità facoltative

Avanzate pre-elaborazione aggiuntiva e definizione delle funzionalità sono anche disponibili, ad esempio mancanti imputation valori, la codifica e le trasformazioni. [Altre informazioni su quali definizione delle funzionalità è inclusa](how-to-create-portal-experiments.md#preprocess). Abilitare questa impostazione con:

+ Portale di Azure: Selezionando il **pre-elaborazione** casella di controllo la **impostazioni avanzate** [con questi passaggi](how-to-create-portal-experiments.md).

+ Python SDK: Che specifica `"preprocess": True` per il [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Modelli di insieme

È possibile eseguire il training di modelli di insieme usando automatizzati di machine learning con il [algoritmo di selezione Caruana insieme durante l'inizializzazione dell'insieme ordinato di](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Apprendimento dell'insieme migliora i risultati di machine learning e le prestazioni predittive combinando molti modelli rispetto all'utilizzo di modelli singoli. Come ultima iterazione di eseguire l'iterazione dell'insieme.

## <a name="training-metric-output"></a>Output di training delle metriche

Esistono diversi modi per visualizzare le metriche di accuratezza di training per ogni iterazione di esecuzione.

* Usare il widget di Jupyter.
* Usare la `get_metrics()` funzione in qualsiasi `Run` oggetto.
* Visualizzare le metriche nel portale di Azure in un esperimento.

### <a name="classification-metrics"></a>Metriche per la classificazione

Le metriche seguenti vengono salvate in ogni iterazione di esecuzione per un'attività di classificazione.

|Metrica|Descrizione|Calcolo|Parametri aggiuntivi
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
norm_macro_recall|norm_macro_recall è il recupero macro normalizzato in modo che le prestazioni causali abbiano un punteggio 0 e le prestazioni perfette abbiano un punteggio 1. Questo viene ottenuto con norm_macro_recall := (recall_score_macro - R)/(1 - R), dove R è il valore stimato di recall_score_macro per le stime casuali (ad esempio, R=0.5 per la classificazione binaria e R=(1/C) per i problemi di classificazione di classe C).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro" e quindi (recall_score_macro - R)/(1 - R), dove R è il valore stimato di recall_score_macro per le stime casuali (ad esempio, R=0.5 per la classificazione binaria e R=(1/C) per i problemi di classificazione di classe C)|
precision_score_macro|precision è la percentuale degli elementi cui è assegnata un'etichetta come una determinata classe e che si trovano effettivamente in tale classe. macro è la media aritmetica di precision per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|precision è la percentuale degli elementi cui è assegnata un'etichetta come una determinata classe e che si trovano effettivamente in tale classe. micro è il valore calcolato globalmente sommando i veri positivi e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|precision è la percentuale degli elementi cui è assegnata un'etichetta come una determinata classe e che si trovano effettivamente in tale classe. weighted è la media aritmetica della precisione per ogni classe, ponderata in base al numero di istanze vere in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|recall è la percentuale di elementi effettivamente presenti in una determinata classe cui è assegnata un'etichetta corretta. macro è la media aritmetica del recupero per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|recall è la percentuale di elementi effettivamente presenti in una determinata classe cui è assegnata un'etichetta corretta. micro è il valore calcolato globalmente sommando i veri positivi e i falsi negativi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|recall è la percentuale di elementi effettivamente presenti in una determinata classe cui è assegnata un'etichetta corretta. weighted è la media aritmetica del recupero per ogni classe, ponderata in base al numero di istanze vere in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|weighted_accuracy è l'accuratezza dove il valore di ponderazione definito per ogni esempio equivale alla proporzione delle istanze vere nella classe vera di tale esempio.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight è un vettore equivalente alla proporzione di tale classe per ogni elemento nel target|

### <a name="regression-and-forecasting-metrics"></a>Metriche per la regressione e la previsione

Le metriche seguenti vengono salvate in ogni iterazione di esecuzione per una regressione o un'attività di previsione.

|Metrica|Descrizione|Calcolo|Parametri aggiuntivi
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


## <a name="use-with-onnx-in-c-apps"></a>Uso con ONNX in C# App

Con Azure Machine Learning, è possibile usare ML automatizzati per compilare un modello Python e averlo convertito nel formato ONNX. Il runtime ONNX supporta C#, è possibile utilizzare il modello compilato automaticamente in di C# le app senza richiedere alcuna ricodifica o uno qualsiasi delle latenze di rete che presentano endpoint REST. Provare a eseguire un esempio di questo flusso [in questo notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Machine Learning automatizzati in Microsoft

Automatizzati di Machine Learning è disponibile anche in altre soluzioni Microsoft, ad esempio:

|Integrazioni|Descrizione
|------------|-----------
|ML.NET|Selezione automatica del modello e il training delle app .NET con Visual Studio e Visual Studio Code con a ML.NET automatizzati di Machine Learning (anteprima). [Altre informazioni](https://docs.microsoft.com/dotnet/machine-learning/automl-overview).
|HDIsnight|La scalabilità orizzontale ML training automatizzate su Spark nei cluster HDInsight in parallelo. [Altre informazioni](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md).
|PowerBI|Richiamare i modelli di machine learning direttamente in Power BI (anteprima). [Altre informazioni](https://docs.microsoft.com/power-bi/service-machine-learning-automated).

## <a name="next-steps"></a>Passaggi successivi

Vedere esempi e informazioni su come creare modelli tramite le funzionalità automatizzate di Machine Learning:

+ Seguire il [esercitazione: Eseguire il training automatico di un modello di classificazione con Azure Automated Machine Learning](tutorial-auto-train-models.md)

+ Configurare le impostazioni per l'esperimento di training automatico:
  + Nell'interfaccia del portale Azure [seguire questa procedura](how-to-create-portal-experiments.md).
  + Con Python SDK [seguire questa procedura](how-to-configure-auto-train.md).

+ Informazioni su come auto usando i dati delle serie temporali, train [seguire questa procedura](how-to-auto-train-forecast.md).

+ Provare [esempi di Notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
