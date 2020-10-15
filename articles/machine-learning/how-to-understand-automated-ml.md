---
title: Valuta i risultati dell'esperimento AutoML
titleSuffix: Azure Machine Learning
description: Informazioni su come visualizzare e valutare i grafici e le metriche per ogni esecuzione dell'esperimento di Machine Learning automatizzato.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 10/09/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2
ms.openlocfilehash: d27c65938d10f9061961ebb585327bc77d8b2859
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092461"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Valuta i risultati dell'esperimento di Machine Learning automatizzato

Questo articolo illustra come visualizzare e valutare i risultati degli esperimenti di Machine Learning, AutoML e automatizzati. Questi esperimenti sono costituiti da più esecuzioni, in cui ogni esecuzione crea un modello. Per semplificare la valutazione di ogni modello, AutoML genera automaticamente le metriche delle prestazioni e i grafici specifici del tipo di esperimento. 

Ad esempio, AutoML fornisce grafici diversi per i modelli di classificazione e regressione. 

|Classificazione|Regressione
|---|---|
|<li> [Matrice di confusione](#confusion-matrix) <li>[Grafico di richiamo della precisione](#precision-recall-chart) <li> [ROC (Receiver Operating Characteristics)](#roc) <li> [Curva di accuratezza](#lift-curve)<li> [Curva del guadagno](#gains-curve)<li> [Tracciato di calibrazione](#calibration-plot) | <li> [Confronto tra stimato e true](#pvt) <li> [Istogramma dei valori residui](#histo)|

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Creare un esperimento per l'esecuzione automatica di Machine Learning, con l'SDK o in Azure Machine Learning Studio.

    * Usare l'SDK per compilare un modello di [classificazione](how-to-auto-train-remote.md) o un [modello di regressione](tutorial-auto-train-models.md)
    * Usare [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) per creare un modello di classificazione o regressione caricando i dati appropriati.

## <a name="view-run-results"></a>Visualizza risultati esecuzione

Al termine dell'esperimento di Machine Learning automatizzato, è possibile trovare una cronologia delle esecuzioni nell'area di lavoro di Machine Learning tramite il [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 

Per gli esperimenti SDK, è possibile visualizzare gli stessi risultati durante un'esecuzione quando si usa il `RunDetails` [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true).

I passaggi e l'animazione seguenti illustrano come visualizzare la cronologia di esecuzione e le metriche delle prestazioni e i grafici di un modello specifico in studio.

![Passaggi per visualizzare la cronologia di esecuzione e le metriche delle prestazioni del modello e i grafici](./media/how-to-understand-automated-ml/view-run-metrics-ui.gif)

Per visualizzare la cronologia di esecuzione e le metriche delle prestazioni del modello e i grafici in studio: 

1. [Accedere a Studio](https://ml.azure.com/) e passare all'area di lavoro.
1. Nel riquadro sinistro dell'area di lavoro selezionare **esecuzioni**.
1. Nell'elenco degli esperimenti selezionare quello che si desidera esplorare.
1. Nella tabella inferiore selezionare l' **esecuzione**.
1. Nella scheda **modelli** selezionare il nome dell' **algoritmo** per il modello che si desidera esplorare.
1. Nella scheda **metriche** selezionare le metriche e i grafici che si desidera valutare per il modello. 


<a name="classification"></a> 

## <a name="classification-performance-metrics"></a>Metriche delle prestazioni di classificazione

La tabella seguente riepiloga le metriche delle prestazioni del modello calcolate da AutoML per ogni modello di classificazione generato per l'esperimento. 

Metrica|Descrizione|Calcolo|Parametri aggiuntivi
--|--|--|--
AUC_macro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Macro è la media aritmetica dell'area AUC per ogni classe.  | [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_micro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Micro viene calcolato a livello globale combinando i veri positivi e i falsi positivi da ogni classe.| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_weighted  | AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Ponderato è la media aritmetica del punteggio per ogni classe, ponderata in base al numero di istanze true in ogni classe.| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
precisione|accuracy è la percentuale di etichette stimate che corrispondono esattamente alle etichette vere. |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nessuno|
average_precision_score_macro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. Macro è la media aritmetica del Punteggio di precisione media di ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. Micro viene calcolato a livello globale combinando i veri positivi e i falsi positivi a ogni cutoff.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. Ponderata è la media aritmetica del Punteggio di precisione media per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|balanced_accuracy è la media aritmetica del recupero per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|f1_score è la media armonica di precisione e recupero. Macro è la media aritmetica del Punteggio F1 per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|f1_score è la media armonica di precisione e recupero. Micro viene calcolato a livello globale contando i veri positivi totali, i falsi negativi e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|f1_score è la media armonica di precisione e recupero. weighted è la media calcolata in base alla frequenza di F1_score per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Si tratta della funzione di perdita usata nella regressione logistica (Multinomial) e nelle estensioni di esso, ad esempio le reti neurali, definita come la probabilità di log negativa delle etichette vere, date le stime di un classificatore probabilistico. Per un singolo esempio con true label YT in {0,1} e la probabilità stimata YP che YT = 1, il log loss è-log P (yt&#124;YP) =-(log di YT (YP) + (1-YT) log (1-YP).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nessuno|
norm_macro_recall|norm_macro_recall è il recupero macro normalizzato in modo che le prestazioni causali abbiano un punteggio 0 e le prestazioni perfette abbiano un punteggio 1. Questa operazione viene eseguita da norm_macro_recall: = (recall_score_macro-R)/(1-R), dove R è il valore previsto di recall_score_macro per le stime casuali (ad esempio, R = 0,5 per la classificazione binaria e R = (1/C) per i problemi di classificazione di classe C).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "macro" |
precision_score_macro|La precisione è la percentuale di elementi stimati positivamente etichettati correttamente. La macro è la media aritmetica di precisione per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|La precisione è la percentuale di elementi stimati positivamente etichettati correttamente. Micro viene calcolato a livello globale contando i veri positivi totali e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|La precisione è la percentuale di elementi stimati positivamente etichettati correttamente. Ponderato è la media aritmetica di precisione per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Richiama è la percentuale di elementi con etichetta corretta di una determinata classe. Macro è la media aritmetica da richiamare per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Richiama è la percentuale di elementi con etichetta corretta di una determinata classe. Micro viene calcolato a livello globale contando il totale dei veri positivi, falsi negativi e falsi positivi|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Richiama è la percentuale di elementi con etichetta corretta di una determinata classe. Ponderato è la media aritmetica di richiamo per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|L'accuratezza ponderata è l'accuratezza con cui il peso assegnato a ogni esempio è uguale alla proporzione di istanze true nella classe true di tale esempio.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight è un vettore equivalente alla proporzione di tale classe per ogni elemento nel target|

### <a name="binary-vs-multiclass-metrics"></a>Metriche binarie e multiclasse

AutoML non distingue tra le metriche binarie e multiclasse. Le stesse metriche di convalida vengono segnalate se un set di dati ha due classi o più di due classi. Tuttavia, alcune metriche sono destinate alla classificazione multiclasse. Quando vengono applicati a un set di dati binario, queste metriche non considerano alcuna classe come la `true` classe, come si può immaginare. Le metriche che sono chiaramente destinate per la multiclasse sono con suffisso `micro` , `macro` o `weighted` . Gli esempi sono `average_precision_score` ,, `f1_score` `precision_score` , `recall_score` e `AUC` .

Ad esempio, invece di calcolare il richiamo come `tp / (tp + fn)` , la media della multiclasse richiama ( `micro` , `macro` o `weighted` ) la media su entrambe le classi di un set di dati di classificazione binario. Questa operazione equivale a calcolare il richiamo per la `true` classe e la `false` classe separatamente e quindi a prendere la media dei due.

## <a name="confusion-matrix"></a>Matrice di confusione

Una matrice di confusione descrive le prestazioni di un modello di classificazione. Ogni riga visualizza le istanze della classe true o Actual nel set di dati e ogni colonna rappresenta le istanze della classe stimate dal modello. 

Per ogni matrice di confusione, il ML automatico Mostra la frequenza di ogni etichetta stimata (colonna) rispetto all'etichetta true (riga). Più scuro è il colore, maggiore è il conteggio in quella particolare parte della matrice. 

### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?

Una matrice di confusione confronta il valore effettivo del set di dati con i valori stimati che il modello ha assegnato. Per questo motivo, i modelli di apprendimento automatico hanno una maggiore precisione se il modello contiene la maggior parte dei valori lungo la diagonale, ovvero il modello ha stimato il valore corretto. Se un modello presenta uno squilibrio della classe, la matrice di confusione consente di rilevare un modello di distorsione.

#### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Esempio 1: modello di classificazione con accuratezza scarsa
![Un modello di classificazione con una scarsa accuratezza](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

#### <a name="example-2-a-classification-model-with-high-accuracy"></a>Esempio 2: un modello di classificazione con accuratezza elevata 
![Un modello di classificazione con accuratezza elevata](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Esempio 3: un modello di classificazione con accuratezza elevata e distorsione elevata nelle stime dei modelli
![Modello di classificazione con accuratezza elevata e distorsione elevata nelle stime dei modelli](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>

## <a name="precision-recall-chart"></a>Grafico di precisione-recupero

La curva di richiamo della precisione Mostra la relazione tra precisione e richiamo da un modello. Il termine precisione rappresenta la possibilità per un modello di etichettare correttamente tutte le istanze. Il termine "recupero" indica la capacità di un algoritmo di classificazione di trovare tutte le istanze di una determinata etichetta.

Con questo grafico è possibile confrontare le curve di precisione-recupero per ogni modello al fine di determinare quale modello ha una relazione accettabile tra precisione e recupero per uno specifico problema aziendale. Questo grafico mostra la media macro di precisione-recupero, la media micro di precisione-recupero e il valore di precisione-recupero associato a tutte le classi per un modello. 

La **media macro** calcola la metrica indipendentemente da ogni classe e quindi accetta la media, considerando equamente tutte le classi. Tuttavia, la **micro-media** aggrega i contributi di tutte le classi per calcolare la media. Micro-Average è preferibile se nel set di dati è presente un squilibrio di classe.

### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
A seconda dell'obiettivo del problema aziendale, la curva di richiamo di precisione ideale potrebbe variare. 

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Esempio 1: modello di classificazione con precisione bassa e richiamo ridotto
![Modello di classificazione con precisione bassa e richiamo minimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Esempio 2: un modello di classificazione con precisione di ~ 100% e ~ 100% Call 
![Un modello di classificazione con precisione elevata e richiamo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

<a name="roc"></a>

## <a name="roc-chart"></a>Grafico ROC

La caratteristica operativa del ricevitore (o ROC) è un tracciato delle etichette classificate correttamente rispetto alle etichette classificate in modo errato per un determinato modello. La curva ROC può essere meno informativa quando si esegue il training dei modelli nei set di DataSet con un elevato squilibrio della classe, poiché la classe di maggioranza può escludere il contributo dalle classi minoritarie.

È possibile visualizzare l'area sotto il grafico ROC come proporzione degli esempi classificati correttamente. Un utente avanzato del grafico ROC potrebbe trovarsi oltre l'area sotto la curva e ottenere un'intuizione per le normali tariffe positive e false come funzione della soglia di classificazione o del limite della decisione.

### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
Curva ROC che si avvicina all'angolo superiore sinistro con un tasso di 100% true positivo e 0% falso positivo sarà il modello migliore. Un modello casuale verrebbe visualizzato come una linea piatta dalla parte inferiore sinistra all'angolo superiore destro. Peggiori di Random si DIP sotto la riga y = x.

#### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Esempio 1: modello di classificazione con etichette true basse e etichette false elevate
![Modello di classificazione con etichette true basse e etichette false elevate](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

#### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Esempio 2: modello di classificazione con etichette true e etichette false basse

![modello di classificazione con etichette true elevate e etichette false basse](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)


<a name="lift-curve"></a>

## <a name="lift-chart"></a>Grafico di accuratezza

I grafici di accuratezza valutano le prestazioni dei modelli di classificazione. Un grafico di accuratezza Mostra il numero di esecuzioni migliori rispetto a un modello casuale. In questo modo si ottengono le prestazioni relative che prendono in considerazione il fatto che la classificazione risulta più difficile quando si aumenta il numero di classi. Un modello casuale stima erroneamente una frazione più elevata di campioni da un set di dati con dieci classi rispetto a un set di dati con due classi.

È possibile confrontare l'accuratezza del modello compilato automaticamente con Azure Machine Learning alla baseline (modello casuale) per visualizzare il valore di tale modello specifico.

### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?

Una curva di accuratezza più elevata, ovvero maggiore è il modello che precede la linea di base, indica un modello con prestazioni migliori. 

#### <a name="example-1-a-classification-model-that-performs-poorly-compared-to-a-random-selection-model"></a>Esempio 1: modello di classificazione eseguito in modo non corretto rispetto a un modello di selezione casuale
![Modello di classificazione più difettoso rispetto a un modello di selezione casuale](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

#### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Esempio 2: modello di classificazione che garantisce prestazioni migliori rispetto a un modello di selezione casuale
![Modello di classificazione che garantisce prestazioni migliori](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

<a name="gains-curve"></a>

## <a name="cumulative-gains-chart"></a>Grafico guadagni cumulativi

Un grafico guadagni cumulativi valuta le prestazioni di un modello di classificazione per ogni parte dei dati. Per ogni percentile del set di dati, nel grafico viene mostrato il numero di campioni classificati accuratamente rispetto a un modello sempre errato. Queste informazioni offrono un modo alternativo di esaminare i risultati nel grafico di accuratezza associato.

Il grafico guadagni cumulativi consente di scegliere il limite di classificazione usando una percentuale corrispondente al guadagno desiderato dal modello. È possibile confrontare il grafico guadagni cumulativi con la linea di base (modello errato) per visualizzare la percentuale di campioni classificati correttamente a ogni percentile di confidenza.

#### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?

Analogamente a un grafico di accuratezza, maggiore sarà la curva dei guadagni cumulativi sopra la linea di base, migliore sarà l'esecuzione del modello. Inoltre, la curva dei guadagni cumulativi più vicina è nell'angolo superiore sinistro del grafo, maggiore è il raggiungimento del modello rispetto alla linea di base. 

##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Esempio 1: modello di classificazione con guadagno minimo
![un modello di classificazione con un guadagno minimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Esempio 2: un modello di classificazione con un miglioramento significativo
![Un modello di classificazione con un notevole miglioramento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

<a name="calibration-plot"></a>

## <a name="calibration-chart"></a>Grafico di calibrazione

Un tracciato di calibrazione Visualizza la confidenza di un modello predittivo. Questa operazione viene eseguita mostrando la relazione tra la probabilità stimata e la probabilità effettiva, dove "probabilità" rappresenta la probabilità che una determinata istanza appartenga a un'etichetta.

Per tutti i problemi di classificazione, è possibile esaminare la linea di calibrazione per media micro, media macro e ogni classe in un determinato modello predittivo.

La **media macro** calcola la metrica indipendentemente da ogni classe e quindi accetta la media, considerando equamente tutte le classi. Tuttavia, la **micro-media** aggrega i contributi di tutte le classi per calcolare la media. 

### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
Un modello ben calibrato è allineato alla riga y = x, in cui viene corretta la stima della probabilità che i campioni appartengano a ogni classe. Un modello con un livello di attendibilità superiore stima le probabilità quasi pari a zero e uno, raramente incerto sulla classe di ciascun campione.

#### <a name="example-1-a-well-calibrated-model"></a>Esempio 1: modello ben calibrato
![ modello più ben calibrato](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

#### <a name="example-2-an-over-confident-model"></a>Esempio 2: modello con sicurezza eccessiva
![Un modello con sicurezza eccessiva](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)


<a name="regression"></a> 

## <a name="regression-performance-metrics"></a>Metriche delle prestazioni di regressione

La tabella seguente riepiloga le metriche delle prestazioni del modello calcolate da AutoML per ogni modello di regressione o previsione generato per l'esperimento. 

|Metrica|Descrizione|Calcolo|Parametri aggiuntivi
--|--|--|--|
explained_variance|explained_variance è la proporzione in base alla quale un modello matematico tiene conto della variazione di un determinato set di dati. Si tratta della riduzione percentuale della varianza dei dati originali rispetto alla varianza degli errori. Quando la media degli errori è 0, è uguale al coefficiente di determinazione (vedere r2_score di seguito).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nessuno|
r2_score|R ^ 2 è il coefficiente di determinazione o la riduzione percentuale di errori quadratici rispetto a un modello di base che restituisce la media. |[Calcolo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nessuno|
spearman_correlation|La correlazione di Spearman è una misura non parametrica della monotonicità della relazione tra due set di dati. A differenza della correlazione di Pearson, quella di Spearman non presuppone che entrambi i set di dati siano normalmente distribuiti. Come altri coefficienti di correlazione, questo coefficiente varia da -1 a + 1, con 0 che implica l'assenza di correlazione. Le correlazioni con coefficiente -1 o + 1 implicano una relazione monotonica esatta. In caso di correlazione positiva, un incremento di x corrisponde a un incremento di y. In caso di correlazione negativa, un incremento di x corrisponde a un decremento di y.|[Calcolo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nessuno|
mean_absolute_error|mean_absolute_error è il valore stimato del valore assoluto della differenza tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nessuno|
normalized_mean_absolute_error|normalized_median_absolute_error è l'errore assoluto medio diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividere per l'intervallo dei dati|
median_absolute_error|median_absolute_error è il valore mediano di tutte le differenze assolute tra il target e la stima. Questa perdita è significativa per gli outlier.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nessuno|
normalized_median_absolute_error|normalized_median_absolute_error è l'errore assoluto mediano diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_error|root_mean_squared_error è la radice quadrata della differenza quadratica stimata tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nessuno|
normalized_root_mean_squared_error|normalized_root_mean_squared_error è la radice dell'errore quadratico medio divisa per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_log_error|root_mean_squared_log_error è la radice quadrata dell'errore logaritmico quadratico stimato.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nessuno|
normalized_root_mean_squared_log_error|normalized_root_mean_squared_log_error è la radice dell'errore logaritmico quadratico medio divisa per l'intervallo dei dati|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividere per l'intervallo dei dati|

<a name="pvt"></a>

## <a name="predicted-vs-true-chart"></a>Grafico stimato rispetto al vero

Stimato rispetto a true Mostra la relazione tra un valore stimato e il relativo valore true correlato per un problema di regressione. 

Dopo ogni esecuzione, è possibile visualizzare un grafico del valore stimato rispetto al valore vero per ogni modello di regressione. Per proteggere la privacy dei dati, i valori sono raggruppati in contenitori e la dimensione di ogni contenitore viene visualizzata in un grafico a barre nella parte inferiore dell'area del grafico. È possibile confrontare il modello predittivo, con l'area più chiara indicante i margini di errore, rispetto al valore ideale che dovrebbe raggiungere il modello.

### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
Può essere usato per misurare le prestazioni di un modello poiché la vicinanza dei valori stimati alla linea y=x è direttamente proporzionale all'accuratezza di un modello predittivo.

#### <a name="example-1-a-classification-model-with-low-accuracy"></a>Esempio 1: modello di classificazione con precisione bassa
![Modello di regressione con precisione bassa nelle stime](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

#### <a name="example-2-a-regression-model-with-high-accuracy"></a>Esempio 2: modello di regressione con accuratezza elevata 
![Modello di regressione con accuratezza elevata nelle stime](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a> 

## <a name="histogram-of-residuals-chart"></a>Istogramma del grafico residui

Automatizzato ML fornisce automaticamente un grafico residuo per mostrare la distribuzione degli errori nelle stime di un modello di regressione. Un residuo è la differenza tra la stima e il valore effettivo ( `y_pred - y_true` ). 

### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
Per visualizzare un margine di errore con una distorsione bassa, l'istogramma dei residui deve essere plasmato come una curva a campana, centrata intorno allo zero.

#### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Esempio 1: modello di regressione con distorsione nei relativi errori
![Modello di regressione SA con distorsione nei relativi errori](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

#### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Esempio 2: un modello di regressione con una distribuzione più uniforme degli errori
![Modello di regressione con una distribuzione più uniforme degli errori](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

<a name="explain-model"></a>

## <a name="model-interpretability-and-feature-importance"></a>Interpretazione dei modelli e importanza delle funzionalità
Automatizzato ML fornisce un dashboard di interpretazione di machine learning per le esecuzioni.

Per altre informazioni sull'abilitazione delle funzionalità di interpretazione, vedere [interpretazione: spiegazione del modello in Machine Learning automatico](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> Il modello ForecastTCN non è attualmente supportato dal client di spiegazione. Questo modello non restituisce un dashboard di spiegazione se viene restituito come modello migliore e non supporta l'esecuzione di spiegazioni su richiesta.

## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni sulle funzionalità di [Machine Learning automatizzato](concept-automated-ml.md) in Azure Machine Learning.
+ Provare i notebook di esempio per la [spiegazione automatica del modello di Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
