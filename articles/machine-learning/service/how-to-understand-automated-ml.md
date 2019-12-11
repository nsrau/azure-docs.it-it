---
title: Informazioni sui risultati automatici di ML
titleSuffix: Azure Machine Learning
description: Informazioni su come visualizzare e comprendere i grafici e le metriche per ognuna delle esecuzioni automatiche di machine learning.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 81f17de7627658b756edd19438a80fb32add859d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974088"
---
# <a name="understand-automated-machine-learning-results"></a>Informazioni sui risultati automatici di Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come visualizzare e comprendere i grafici e le metriche per ognuna delle esecuzioni automatiche di machine learning. 

Altre informazioni su:
+ [Metriche, grafici e curve per i modelli di classificazione](#classification)
+ [Metriche, grafici e grafici per i modelli di regressione](#regression)
+ [Interpretazione dei modelli e importanza delle funzionalità](#explain-model)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Creare un esperimento per l'esecuzione automatica di Machine Learning, con l'SDK o in Azure Machine Learning Studio.

    * Usare l'SDK per compilare un modello di [classificazione](how-to-auto-train-remote.md) o un [modello di regressione](tutorial-auto-train-models.md)
    * Usare [Azure Machine Learning Studio](how-to-create-portal-experiments.md) per creare un modello di classificazione o regressione caricando i dati appropriati.

## <a name="view-the-run"></a>Visualizza l'esecuzione

Dopo l'esecuzione di un esperimento di Machine Learning automatizzato, è possibile trovare una cronologia delle esecuzioni nell'area di lavoro di machine learning. 

1. Passa all'area di lavoro.

1. Nel riquadro sinistro dell'area di lavoro selezionare **esperimenti**.

   ![Screenshot del menu Esperimenti](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Nell'elenco degli esperimenti selezionare quello che si desidera esplorare.

   [![elenco degli esperimenti](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Nella tabella inferiore selezionare l' **esecuzione**.

   [esecuzione dell'esperimento![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Nei modelli selezionare il nome dell' **algoritmo** per il modello che si desidera esplorare ulteriormente.

   [modello di esperimento di ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Gli stessi risultati vengono visualizzati anche durante un'esecuzione quando si usa il `RunDetails`[widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Risultati della classificazione

Le metriche e i grafici seguenti sono disponibili per ogni modello di classificazione compilato usando le funzionalità automatiche di Machine Learning di Azure Machine Learning

+ [Metriche](#classification-metrics)
+ [Matrice di confusione](#confusion-matrix)
+ [Grafico di precisione-recupero](#precision-recall-chart)
+ [ROC (Receiver Operating Characteristics)](#roc)
+ [Curva di accuratezza](#lift-curve)
+ [Curva del guadagno](#gains-curve)
+ [Tracciato di calibrazione](#calibration-plot)

### <a name="classification-metrics"></a>Metriche per la classificazione

Le metriche seguenti vengono salvate in ogni iterazione di esecuzione per un'attività di classificazione.

|Metrica|Description|Calcolo|Parametri aggiuntivi
--|--|--|--|
AUC_Macro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Macro è la media aritmetica dell'area AUC per ogni classe.  | [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Micro viene calcolato a livello globale combinando i veri positivi e i falsi positivi da ogni classe.| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Ponderato è la media aritmetica del punteggio per ogni classe, ponderata in base al numero di istanze true in ogni classe.| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
precisione|accuracy è la percentuale di etichette stimate che corrispondono esattamente alle etichette vere. |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nessuno|
average_precision_score_macro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. Macro è la media aritmetica del Punteggio di precisione media di ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. Micro viene calcolato a livello globale combinando i veri positivi e i falsi positivi a ogni cutoff.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. Ponderata è la media aritmetica del Punteggio di precisione media per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|balanced_accuracy è la media aritmetica del recupero per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|f1_score è la media armonica di precisione e recupero. Macro è la media aritmetica del Punteggio F1 per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|f1_score è la media armonica di precisione e recupero. Micro viene calcolato a livello globale contando i veri positivi totali, i falsi negativi e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|f1_score è la media armonica di precisione e recupero. weighted è la media calcolata in base alla frequenza di F1_score per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Questa è la funzione di perdita usata per la regressione logistica (multinomiale) e le relative estensioni, ad esempio le reti neurali, definita come probabilità logaritmica negativa delle etichette vere date le stime del classificatore probabilistico. Per un singolo esempio con true label YT in {0,1} e probabilità stimata YP che YT = 1, la perdita di log è-log P&#124;(YT YP) =-(log di YT (YP) + (1-YT) log (1-YP).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nessuno|
norm_macro_recall|norm_macro_recall è il recupero macro normalizzato in modo che le prestazioni causali abbiano un punteggio 0 e le prestazioni perfette abbiano un punteggio 1. Questa operazione viene eseguita da norm_macro_recall: = (recall_score_macro-R)/(1-R), dove R è il valore previsto di recall_score_macro per le stime casuali (ad esempio, R = 0,5 per la classificazione binaria e R = (1/C) per i problemi di classificazione di classe C).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "macro" |
precision_score_macro|La precisione è la percentuale di elementi stimati positivamente etichettati correttamente. La macro è la media aritmetica di precisione per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|La precisione è la percentuale di elementi stimati positivamente etichettati correttamente. Micro viene calcolato a livello globale contando i veri positivi totali e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|La precisione è la percentuale di elementi stimati positivamente etichettati correttamente. Ponderato è la media aritmetica di precisione per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Richiama è la percentuale di elementi con etichetta corretta di una determinata classe. Macro è la media aritmetica da richiamare per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Richiama è la percentuale di elementi con etichetta corretta di una determinata classe. Micro viene calcolato a livello globale contando il totale dei veri positivi, falsi negativi e falsi positivi|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Richiama è la percentuale di elementi con etichetta corretta di una determinata classe. Ponderato è la media aritmetica di richiamo per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|L'accuratezza ponderata è l'accuratezza con cui il peso assegnato a ogni esempio è uguale alla proporzione di istanze true nella classe true di tale esempio.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight è un vettore equivalente alla proporzione di tale classe per ogni elemento nel target|
<a name="confusion-matrix"></a>
### <a name="confusion-matrix"></a>Matrice di confusione
#### <a name="what-is-a-confusion-matrix"></a>Che cos'è una matrice di confusione?
Una matrice di confusione viene usata per descrivere le prestazioni di un modello di classificazione. Ogni riga visualizza le istanze della classe true o Actual nel set di dati e ogni colonna rappresenta le istanze della classe stimate dal modello. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Che cosa comporta l'esecuzione automatica di ML con la matrice di confusione?
Per i problemi di classificazione, Azure Machine Learning offre automaticamente una matrice di confusione per ogni modello creato. Per ogni matrice di confusione, Machine Learning automatizzata Visualizza la frequenza di ogni etichetta stimata (colonna) rispetto all'etichetta true (riga). Più scuro è il colore, maggiore è il conteggio in quella particolare parte della matrice. 

#### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
Si sta confrontando il valore effettivo del set di dati con i valori stimati forniti dal modello. Per questo motivo, i modelli di apprendimento automatico hanno una maggiore precisione se il modello contiene la maggior parte dei valori lungo la diagonale, ovvero il modello ha stimato il valore corretto. Se un modello presenta uno squilibrio della classe, la matrice di confusione consente di rilevare un modello di distorsione.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Esempio 1: modello di classificazione con accuratezza scarsa
![Un modello di classificazione con una scarsa accuratezza](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Esempio 2: un modello di classificazione con accuratezza elevata 
![Un modello di classificazione con accuratezza elevata](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Esempio 3: un modello di classificazione con accuratezza elevata e distorsione elevata nelle stime dei modelli
![Modello di classificazione con accuratezza elevata e distorsione elevata nelle stime dei modelli](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Grafico di precisione-recupero
#### <a name="what-is-a-precision-recall-chart"></a>Che cos'è un grafico di richiamo della precisione?
La curva di richiamo della precisione Mostra la relazione tra precisione e richiamo da un modello. Il termine precisione rappresenta la possibilità per un modello di etichettare correttamente tutte le istanze. Il termine "recupero" indica la capacità di un algoritmo di classificazione di trovare tutte le istanze di una determinata etichetta.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Che cosa comporta l'esecuzione automatica di Machine Learning con il grafico di richiamo della precisione?

Con questo grafico è possibile confrontare le curve di precisione-recupero per ogni modello al fine di determinare quale modello ha una relazione accettabile tra precisione e recupero per uno specifico problema aziendale. Questo grafico mostra la media macro di precisione-recupero, la media micro di precisione-recupero e il valore di precisione-recupero associato a tutte le classi per un modello. 

Con la media macro la metrica viene calcolata in modo indipendente da ogni classe e quindi viene accettata la media, considerando ugualmente tutte le classi. Tuttavia, la micro-media aggrega i contributi di tutte le classi per calcolare la media. Micro-Average è preferibile se nel set di dati è presente un squilibrio di classe.

#### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
A seconda dell'obiettivo del problema aziendale, la curva di richiamo di precisione ideale potrebbe variare. Di seguito sono riportati alcuni esempi

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Esempio 1: modello di classificazione con precisione bassa e richiamo ridotto
![Modello di classificazione con precisione bassa e richiamo minimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Esempio 2: un modello di classificazione con precisione di ~ 100% e ~ 100% Call 
![un modello di classificazione con precisione elevata e richiamo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Grafico ROC

#### <a name="what-is-a-roc-chart"></a>Che cos'è un grafico ROC?
ROC (Receiver Operating Characteristics) è un tracciato che mette a confronto le etichette classificate correttamente con quelle classificate in modo errato per un determinato modello. La curva ROC può essere meno informativa quando si esegue il training di modelli su set di dati con elevata distorsione perché non visualizza le etichette false positive.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Che cosa comporta l'esecuzione automatica di ML con il grafico ROC?
La tecnologia Machine Learning Machine Learning genera la precisione media della macro, il richiamo della precisione media e il richiamo della precisione associato a tutte le classi per un modello. 

Con la media macro la metrica viene calcolata in modo indipendente da ogni classe e quindi viene accettata la media, considerando ugualmente tutte le classi. Tuttavia, la micro-media aggrega i contributi di tutte le classi per calcolare la media. Micro-Average è preferibile se nel set di dati è presente un squilibrio di classe.

#### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
Idealmente, il modello sarà più vicino al 100% del vero positivo e più vicino al tasso di falsi positivi 0%. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Esempio 1: modello di classificazione con etichette true basse e etichette false elevate
![Modello di classificazione con etichette true basse e etichette false elevate](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Esempio 2: modello di classificazione con etichette true e etichette false basse
![un modello di classificazione con etichette reali elevate e etichette false basse](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Grafico di accuratezza
#### <a name="what-is-a-lift-chart"></a>Che cos'è un grafico di accuratezza?
I grafici di accuratezza vengono usati per valutare le prestazioni di un modello di classificazione. Mostra quanto meglio si può prevedere con il modello generato rispetto a senza un modello in termini di accuratezza.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Che cosa comporta l'esecuzione automatica di ML con il grafico di accuratezza?
È possibile confrontare l'accuratezza del modello creato automaticamente con Azure Machine Learning rispetto al modello di base per visualizzare il guadagno di valore di tale modello.
#### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Esempio 1: modello di classificazione più difettoso rispetto a un modello di selezione casuale
![Modello di classificazione più difettoso rispetto a un modello di selezione casuale](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Esempio 2: modello di classificazione che garantisce prestazioni migliori rispetto a un modello di selezione casuale
![un modello di classificazione che garantisce prestazioni migliori](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Acquisisci grafico
#### <a name="what-is-a-gains-chart"></a>Che cos'è un grafico guadagni?

Un grafico del guadagno valuta le prestazioni di un modello di classificazione in base alle singole porzioni dei dati. Per ogni percentile del set di dati, mostra il miglioramento che ci si può aspettare di ottenere rispetto a un modello di selezione casuale.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Che cosa comporta l'esecuzione automatica di Machine Learning con il grafico dei guadagni?
L'uso del grafico del guadagno cumulativo consente di scegliere il limite di classificazione usando un valore percentuale corrispondente al guadagno che si vuole ottenere dal modello. Queste informazioni offrono un modo alternativo di esaminare i risultati nel grafico di accuratezza associato.

#### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Esempio 1: modello di classificazione con guadagno minimo
![un modello di classificazione con un guadagno minimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Esempio 2: un modello di classificazione con un miglioramento significativo
![un modello di classificazione con un notevole miglioramento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Grafico di calibrazione

#### <a name="what-is-a-calibration-chart"></a>Che cos'è un grafico di calibrazione?
Un tracciato di calibrazione viene usato per visualizzare il grado di fiducia di un modello predittivo. A tale scopo, il tracciato mostra la relazione tra probabilità stimata e probabilità effettiva, dove per "probabilità" si intende la possibilità che una particolare istanza appartenga a una determinata etichetta.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Che cosa comporta l'esecuzione automatica di ML con il grafico di calibrazione?
Per tutti i problemi di classificazione, è possibile esaminare la linea di calibrazione per media micro, media macro e ogni classe in un determinato modello predittivo.

Con la media macro la metrica viene calcolata in modo indipendente da ogni classe e quindi viene accettata la media, considerando ugualmente tutte le classi. Tuttavia, la micro-media aggrega i contributi di tutte le classi per calcolare la media. 
#### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
 Un modello ben calibrato è allineato alla riga y = x, in cui è ragionevolmente affidabile nelle stime. Un modello con eccessiva fiducia è allineato alla linea y=0, dove la probabilità stimata è presente ma non esiste alcuna probabilità effettiva. 


##### <a name="example-1-a-well-calibrated-model"></a>Esempio 1: modello ben calibrato
![ modello più ben calibrato](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Esempio 2: modello con sicurezza eccessiva
![Un modello con sicurezza eccessiva](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Risultati regressione

Le metriche e i grafici seguenti sono disponibili per ogni modello di regressione compilato usando le funzionalità automatiche di Machine Learning di Azure Machine Learning

+ [Metriche](#reg-metrics)
+ [Confronto tra stimato e true](#pvt)
+ [Istogramma dei valori residui](#histo)


### <a name="reg-metrics"></a>Metriche di regressione

Le metriche seguenti vengono salvate in ogni iterazione di esecuzione per un'attività di regressione o previsione.

|Metrica|Description|Calcolo|Parametri aggiuntivi
--|--|--|--|
explained_variance|explained_variance è la proporzione in base alla quale un modello matematico tiene conto della variazione di un determinato set di dati. Si tratta della riduzione percentuale della varianza dei dati originali rispetto alla varianza degli errori. Quando la media degli errori è 0, la varianza è perfettamente spiegata.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nessuno|
r2_score|R2 è il coefficiente di determinazione o la riduzione percentuale di errori quadratici rispetto a un modello di base che restituisce la media. |[Calcolo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nessuno|
spearman_correlation|La correlazione di Spearman è una misura non parametrica della monotonicità della relazione tra due set di dati. A differenza della correlazione di Pearson, quella di Spearman non presuppone che entrambi i set di dati siano normalmente distribuiti. Come altri coefficienti di correlazione, questo coefficiente varia da -1 a + 1, con 0 che implica l'assenza di correlazione. Le correlazioni con coefficiente -1 o + 1 implicano una relazione monotonica esatta. In caso di correlazione positiva, un incremento di x corrisponde a un incremento di y. In caso di correlazione negativa, un incremento di x corrisponde a un decremento di y.|[Calcolo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nessuno|
mean_absolute_error|mean_absolute_error è il valore stimato del valore assoluto della differenza tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nessuno|
normalized_mean_absolute_error|normalized_median_absolute_error è l'errore assoluto medio diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividere per l'intervallo dei dati|
median_absolute_error|median_absolute_error è il valore mediano di tutte le differenze assolute tra il target e la stima. Questa perdita è significativa per gli outlier.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nessuno|
normalized_median_absolute_error|normalized_median_absolute_error è l'errore assoluto mediano diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_error|root_mean_squared_error è la radice quadrata della differenza quadratica stimata tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nessuno|
normalized_root_mean_squared_error|normalized_root_mean_squared_error è la radice dell'errore quadratico medio divisa per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_log_error|root_mean_squared_log_error è la radice quadrata dell'errore logaritmico quadratico stimato.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nessuno|
normalized_root_mean_squared_log_error|normalized_root_mean_squared_log_error è la radice dell'errore logaritmico quadratico medio divisa per l'intervallo dei dati|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividere per l'intervallo dei dati|

### <a name="pvt"></a>Grafico stimato rispetto al vero
#### <a name="what-is-a-predicted-vs-true-chart"></a>Che cos'è un grafico stimato rispetto al vero?
Stimato rispetto a true Mostra la relazione tra un valore stimato e il relativo valore true correlato per un problema di regressione. Può essere usato per misurare le prestazioni di un modello poiché la vicinanza dei valori stimati alla linea y=x è direttamente proporzionale all'accuratezza di un modello predittivo.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Che cosa comporta l'esecuzione automatica di Machine Learning con il grafico stimato rispetto a quello vero?
Dopo ogni esecuzione, è possibile visualizzare un grafico del valore stimato rispetto al valore vero per ogni modello di regressione. Per proteggere la privacy dei dati, i valori sono raggruppati in contenitori e la dimensione di ogni contenitore viene visualizzata in un grafico a barre nella parte inferiore dell'area del grafico. È possibile confrontare il modello predittivo, con l'area più chiara indicante i margini di errore, rispetto al valore ideale che dovrebbe raggiungere il modello.

#### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Esempio 1: modello di classificazione con precisione bassa
![Modello di regressione con precisione bassa nelle stime](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Esempio 2: modello di regressione con accuratezza elevata 
[![un modello di regressione con accuratezza elevata nelle stime](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Istogramma del grafico residui
#### <a name="what-is-a-residuals-chart"></a>Che cos'è un grafico residuo?
Un valore residuo rappresenta la differenza tra un valore y osservato e il valore y stimato. Per mostrare un margine di errore con distorsione ridotta, l'istogramma dei valori residui deve avere la forma di una curva a campana, con il centro vicino allo 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Che cosa comporta l'esecuzione automatica di ML con il grafico dei residui?
Automatizzato ML fornisce automaticamente un grafico dei residui per mostrare la distribuzione degli errori nelle stime.
#### <a name="what-does-a-good-model-look-like"></a>Qual è l'aspetto di un modello valido?
Un modello valido in genere avrà una curva a campana o errori intorno allo zero.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Esempio 1: modello di regressione con distorsione nei relativi errori
![Modello di regressione SA con distorsione nei relativi errori](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Esempio 2: un modello di regressione con una distribuzione più uniforme degli errori
![Modello di regressione con una distribuzione più uniforme degli errori](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Interpretazione dei modelli e importanza delle funzionalità
Automatizzato ML fornisce un dashboard di interpretazione di machine learning per le esecuzioni.
Per altre informazioni sull'abilitazione delle funzionalità di interpretazione, vedere le [procedure per](how-to-machine-learning-interpretability-automl.md) l'abilitazione dell'interpretazione negli esperimenti di Machine Learning automatici.

## <a name="next-steps"></a>Passaggi successivi

+ Scopri [di più su Machine](concept-automated-ml.md) learning in Azure Machine Learning.
+ Provare la [spiegazione automatica del modello di Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) dei notebook di esempio.
