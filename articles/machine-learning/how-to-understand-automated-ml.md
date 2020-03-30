---
title: Comprendere i risultati automatizzati di ML
titleSuffix: Azure Machine Learning
description: Scopri come visualizzare e comprendere i grafici e le metriche per ogni esecuzione di apprendimento automatico automatizzato.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283459"
---
# <a name="understand-automated-machine-learning-results"></a>Comprendere i risultati di un Machine Learning automatizzato
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come visualizzare e comprendere i grafici e le metriche per ogni esecuzione di apprendimento automatico automatizzato. 

Altre informazioni su:
+ [Metriche, grafici e curve per i modelli di classificazione](#classification)
+ [Metriche, grafici e grafici per i modelli di regressione](#regression)
+ [Interpretabilità del modello e importanza delle caratteristiche](#explain-model)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Creare un esperimento per l'esecuzione dell'apprendimento automatico, con l'SDK o in Azure Machine Learning Studio.Create an experiment for your automated machine learning run, either with the SDK, or in Azure Machine Learning studio.

    * Utilizzare l'SDK per compilare un modello di [classificazione](how-to-auto-train-remote.md) o un [modello di regressioneUse](tutorial-auto-train-models.md) the SDK to build a classification model or regression model
    * Usare [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) per creare un modello di classificazione o regressione caricando i dati appropriati.

## <a name="view-the-run"></a>Visualizza la corsa

Dopo aver eseguito un esperimento di apprendimento automatico, è possibile trovare una cronologia delle esecuzioni nell'area di lavoro di Machine Learning.After running an automated machine learning experiment, a history of the runs can be found in your machine learning workspace. 

1. Passa all'area di lavoro.

1. Nel pannello sinistro dell'area di lavoro, selezionate **Esperimenti.**

   ![Screenshot del menu Esperimenti](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Nell'elenco degli esperimenti selezionare quello che si desidera esplorare.

   [![Elenco degli esperimenti](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Nella tabella inferiore selezionare **esegui**.

   Esecuzione dell'esperimento ) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. In Modelli selezionare il **nome dell'algoritmo** per il modello che si desidera esplorare ulteriormente.

   [![Modello dell'esperimento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Questi stessi risultati vengono visualizzati anche `RunDetails`durante un'esecuzione quando si utilizza il [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification-results"></a><a name="classification"></a>Risultati della classificazione

Le metriche e i grafici seguenti sono disponibili per ogni modello di classificazione creato usando le funzionalità di apprendimento automatico automatizzate di Azure Machine LearningThe e metrics and charts are available for every classification model that you build using the automated machine learning capabilities of Azure Machine Learning

+ [Metriche](#classification-metrics)
+ [Matrice di confusione](#confusion-matrix)
+ [Grafico di richiamo di precisione](#precision-recall-chart)
+ [ROC (Receiver Operating Characteristics)](#roc)
+ [Curva di accuratezza](#lift-curve)
+ [Curva del guadagno](#gains-curve)
+ [Tracciato di calibrazione](#calibration-plot)

### <a name="classification-metrics"></a>Metriche per la classificazione

Le metriche seguenti vengono salvate in ogni iterazione di esecuzione per un'attività di classificazione.

Metrica|Descrizione|Calcolo|Parametri aggiuntivi
--|--|--|--
AUC_Macro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Macro è la media aritmetica dell'area AUC per ogni classe.  | [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Micro viene calcolato globalmente combinando i veri positivi e falsi positivi di ogni classe.| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC è l'area sottesa alla curva ROC (Receiver Operating Characteristic). Ponderata è la media aritmetica del punteggio per ogni classe, ponderata in base al numero di istanze true in ogni classe.| [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
precisione|accuracy è la percentuale di etichette stimate che corrispondono esattamente alle etichette vere. |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |nessuno|
average_precision_score_macro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. Macro è la media aritmetica del punteggio di precisione medio di ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. Micro viene calcolato globalmente combinando i veri positivi e i falsi positivi ad ogni taglio.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. Ponderata è la media aritmetica del punteggio medio di precisione per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|balanced_accuracy è la media aritmetica del recupero per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|f1_score è la media armonica di precisione e recupero. Macro è la media aritmetica del punteggio F1 per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|f1_score è la media armonica di precisione e recupero. Micro viene calcolato globalmente contando i veri positivi totali, i falsi negativi e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|f1_score è la media armonica di precisione e recupero. weighted è la media calcolata in base alla frequenza di F1_score per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Si tratta della funzione di perdita utilizzata nella regressione logistica (multinomiale) e nelle relative estensioni, ad esempio reti neurali, definita come la probabilità di log negativa delle etichette reali date le stime di un classificatore probabilistico. Per un singolo campione con {0,1} etichetta reale yt in e probabilità stimata yp che yt è 1, la perdita di log è -log P(yt&#124;yp) -(yt log(yp) - (1 - yt) log(1 - yp)).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|nessuno|
norm_macro_recall|norm_macro_recall è il recupero macro normalizzato in modo che le prestazioni causali abbiano un punteggio 0 e le prestazioni perfette abbiano un punteggio 1. Ciò si ottiene norm_macro_recall (recall_score_macro - R)/(1 - R), dove R è il valore previsto di recall_score_macro per le stime casuali (ad esempio, R .0.5 per la classificazione binaria e R' (1/C) per i problemi di classificazione di classe C).|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|media - "macro" |
precision_score_macro|Precisione è la percentuale di elementi con stima positiva che sono etichettati correttamente. Macro è la media aritmetica di precisione per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precisione è la percentuale di elementi con stima positiva che sono etichettati correttamente. Micro viene calcolato globalmente contando i veri positivi totali e i falsi positivi.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precisione è la percentuale di elementi con stima positiva che sono etichettati correttamente. Ponderata è la media aritmetica di precisione per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Richiamo è la percentuale di elementi etichettati correttamente di una determinata classe. Macro è la media aritmetica di richiamo per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Richiamo è la percentuale di elementi etichettati correttamente di una determinata classe. Micro viene calcolato globalmente contando i veri positivi totali, i falsi negativi e i falsi positivi|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Richiamo è la percentuale di elementi etichettati correttamente di una determinata classe. Ponderata è la media aritmetica di richiamo per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|La precisione ponderata è la precisione in cui il peso assegnato a ogni esempio è uguale alla proporzione di istanze true nella classe true di tale esempio.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight è un vettore equivalente alla proporzione di tale classe per ogni elemento nel target|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Matrice di confusione

#### <a name="what-is-a-confusion-matrix"></a>Che cos'è una matrice di confusione?
Una matrice di confusione viene usata per descrivere le prestazioni di un modello di classificazione. Ogni riga visualizza le istanze della classe true o actual nel set di dati e ogni colonna rappresenta le istanze della classe prevista dal modello. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Cosa fa la ML automatizzata con la matrice di confusione?
Per i problemi di classificazione, Azure Machine Learning offre automaticamente una matrice di confusione per ogni modello creato. Per ogni matrice di confusione, ML automatizzato mostrerà la frequenza di ogni etichetta stimata (colonna) confrontata con l'etichetta reale (riga). Più scuro è il colore, maggiore è il conteggio in quella particolare parte della matrice. 

#### <a name="what-does-a-good-model-look-like"></a>Che aspetto ha un buon modello?
Viene confrontato il valore effettivo del set di dati con i valori stimati del modello. Per questo motivo, i modelli di apprendimento automatico hanno una maggiore precisione se il modello ha la maggior parte dei suoi valori lungo la diagonale, ovvero il modello ha previsto il valore corretto. Se un modello presenta uno squilibrio di classe, la matrice di confusione consentirà di rilevare un modello di parte.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Esempio 1: Un modello di classificazione con scarsa precisioneExample 1: A classification model with poor accuracy
![Un modello di classificazione con scarsa precisione](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Esempio 2: Un modello di classificazione con alta precisioneExample 2: A classification model with high accuracy 
![Un modello di classificazione con alta precisione](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Esempio 3: Un modello di classificazione con alta precisione e alta distorsione nelle stime del modelloExample 3: A classification model with high accuracy and high bias in model predictions
![Un modello di classificazione con alta precisione e alta distorsione nelle stime del modello](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Grafico di precisione-recupero
#### <a name="what-is-a-precision-recall-chart"></a>Che cos'è un grafico di richiamo di precisione?
La curva di richiamo di precisione mostra la relazione tra precisione e richiamo da un modello. Il termine precisione rappresenta la capacità di un modello di etichettare correttamente tutte le istanze. Il termine "recupero" indica la capacità di un algoritmo di classificazione di trovare tutte le istanze di una determinata etichetta.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Cosa fa la ML automatizzata con il grafico di richiamo di precisione?

Con questo grafico è possibile confrontare le curve di precisione-recupero per ogni modello al fine di determinare quale modello ha una relazione accettabile tra precisione e recupero per uno specifico problema aziendale. Questo grafico mostra la media macro di precisione-recupero, la media micro di precisione-recupero e il valore di precisione-recupero associato a tutte le classi per un modello. 

La macromedia calcolerà la metrica indipendentemente da ogni classe e quindi prenderà la media, trattando tutte le classi allo stesso modo. Tuttavia, la micromedia aggregherà i contributi di tutte le classi per calcolare la media. La micromedia è preferibile se è presente uno squilibrio di classe nel set di dati.

#### <a name="what-does-a-good-model-look-like"></a>Che aspetto ha un buon modello?
A seconda dell'obiettivo del problema aziendale, la curva di precisione-recall ideale potrebbe variare. Alcuni esempi sono riportati di seguito

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Esempio 1: Un modello di classificazione con bassa precisione e basso richiamo
![Un modello di classificazione con bassa precisione e basso richiamo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Esempio 2: Un modello di classificazione con una precisione del 100% e un richiamo al 100% 
![Un modello di classificazione ad alta precisione e richiamo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Grafico ROC

#### <a name="what-is-a-roc-chart"></a>Che cos'è un grafico ROC?
ROC (Receiver Operating Characteristics) è un tracciato che mette a confronto le etichette classificate correttamente con quelle classificate in modo errato per un determinato modello. La curva ROC può essere meno informativa quando si esegue il training di modelli su set di dati con elevata distorsione perché non visualizza le etichette false positive.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Cosa fa la ML automatizzata con il grafico ROC?
L'ML automatizzato genera il Macro Average Precision-Recall, il Micro Average Precision-Recall e il richiamo di precisione associato a tutte le classi per un modello. 

La macromedia calcolerà la metrica indipendentemente da ogni classe e quindi prenderà la media, trattando tutte le classi allo stesso modo. Tuttavia, la micromedia aggregherà i contributi di tutte le classi per calcolare la media. La micromedia è preferibile se è presente uno squilibrio di classe nel set di dati.

#### <a name="what-does-a-good-model-look-like"></a>Che aspetto ha un buon modello?
Idealmente, il modello avrà più vicino al 100% vero tasso positivo e più vicino al tasso di falsi positivi dello 0%. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Esempio 1: un modello di classificazione con etichette vere basse ed etichette false alteExample 1: A classification model with low true labels and high false labels
![Modello di classificazione con etichette vere basse ed etichette false alte](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Esempio 2: Un modello di classificazione con etichette true alte e etichette false basseExample 2: A classification model with high true labels and low false labels
![un modello di classificazione con etichette vere alte e etichette false basse](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Grafico di accuratezza
#### <a name="what-is-a-lift-chart"></a>Cos'è un grafico di sollevamento?
I grafici di accuratezza vengono usati per valutare le prestazioni di un modello di classificazione. Mostra quanto meglio ci si può aspettare di fare con il modello generato rispetto a senza un modello in termini di precisione.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Cosa fa la ML automatizzata con la tabella di risalita?
È possibile confrontare l'accuratezza del modello creato automaticamente con Azure Machine Learning rispetto al modello di base per visualizzare il guadagno di valore di tale modello.
#### <a name="what-does-a-good-model-look-like"></a>Che aspetto ha un buon modello?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Esempio 1: un modello di classificazione che fa peggio di un modello di selezione casualeExample 1: A classification model that does worse than a random selection model
![Un modello di classificazione che fa peggio di un modello di selezione casuale](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Esempio 2: un modello di classificazione con prestazioni migliori rispetto a un modello di selezione casualeExample 2: A classification model that perform better than a random selection model
![Un modello di classificazione che offre prestazioni migliori](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Grafico dei guadagni
#### <a name="what-is-a-gains-chart"></a>Che cos'è un grafico dei guadagni?

Un grafico del guadagno valuta le prestazioni di un modello di classificazione in base alle singole porzioni dei dati. Per ogni percentile del set di dati, mostra il miglioramento che ci si può aspettare di ottenere rispetto a un modello di selezione casuale.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Cosa fa ML automatizzato fare con il grafico guadagni?
L'uso del grafico del guadagno cumulativo consente di scegliere il limite di classificazione usando un valore percentuale corrispondente al guadagno che si vuole ottenere dal modello. Queste informazioni offrono un modo alternativo di esaminare i risultati nel grafico di accuratezza associato.

#### <a name="what-does-a-good-model-look-like"></a>Che aspetto ha un buon modello?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Esempio 1: un modello di classificazione con un guadagno minimoExample 1: A classification model with minimal gain
![un modello di classificazione con guadagno minimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Esempio 2: Un modello di classificazione con un guadagno significativoExample 2: A classification model with significant gain
![Un modello di classificazione con un guadagno significativo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Grafico di calibrazione

#### <a name="what-is-a-calibration-chart"></a>Che cos'è un grafico di calibrazione?
Un tracciato di calibrazione viene usato per visualizzare il grado di fiducia di un modello predittivo. Ciò si fa mostrando la relazione tra la probabilità prevista e la probabilità effettiva, dove "probabilità" rappresenta la probabilità che una particolare istanza appartenga sotto una certa etichetta.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Cosa fa la ML automatizzata con il grafico di calibrazione?
Per tutti i problemi di classificazione, è possibile esaminare la linea di calibrazione per media micro, media macro e ogni classe in un determinato modello predittivo.

La macromedia calcolerà la metrica indipendentemente da ogni classe e quindi prenderà la media, trattando tutte le classi allo stesso modo. Tuttavia, la micromedia aggregherà i contributi di tutte le classi per calcolare la media. 
#### <a name="what-does-a-good-model-look-like"></a>Che aspetto ha un buon modello?
 Un modello ben calibrato è allineato con la linea y-x, dove è ragionevolmente fiducioso nelle sue previsioni. Un modello con eccessiva fiducia è allineato alla linea y=0, dove la probabilità stimata è presente ma non esiste alcuna probabilità effettiva. 


##### <a name="example-1-a-well-calibrated-model"></a>Esempio 1: Un modello ben calibrato
![ modello più ben calibrato](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Esempio 2: un modello troppo sicuro di séExample 2: An over-confident model
![Un modello troppo sicuro di sé](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Risultati della regressione

Le metriche e i grafici seguenti sono disponibili per ogni modello di regressione creato usando le funzionalità di apprendimento automatico di AzureThee following metrics and charts are available for every regression model that you build using the automated machine learning capabilities of Azure Machine Learning

+ [Metriche](#reg-metrics)
+ [Predicted vs. True](#pvt)
+ [Istogramma dei valori residui](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Metriche di regressione

Le metriche seguenti vengono salvate in ogni iterazione di esecuzione per un'attività di regressione o previsione.

|Metrica|Descrizione|Calcolo|Parametri aggiuntivi
--|--|--|--|
explained_variance|explained_variance è la proporzione in base alla quale un modello matematico tiene conto della variazione di un determinato set di dati. Si tratta della riduzione percentuale della varianza dei dati originali rispetto alla varianza degli errori. Quando la media degli errori è 0, la varianza è perfettamente spiegata.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|nessuno|
r2_score|R2 è il coefficiente di determinazione o la riduzione percentuale di errori quadratici rispetto a un modello di base che restituisce la media. |[Calcolo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|nessuno|
spearman_correlation|La correlazione di Spearman è una misura non parametrica della monotonicità della relazione tra due set di dati. A differenza della correlazione di Pearson, quella di Spearman non presuppone che entrambi i set di dati siano normalmente distribuiti. Come altri coefficienti di correlazione, questo coefficiente varia da -1 a + 1, con 0 che implica l'assenza di correlazione. Le correlazioni con coefficiente -1 o + 1 implicano una relazione monotonica esatta. In caso di correlazione positiva, un incremento di x corrisponde a un incremento di y. In caso di correlazione negativa, un incremento di x corrisponde a un decremento di y.|[Calcolo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|nessuno|
mean_absolute_error|mean_absolute_error è il valore stimato del valore assoluto della differenza tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|nessuno|
normalized_mean_absolute_error|normalized_median_absolute_error è l'errore assoluto medio diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividere per l'intervallo dei dati|
median_absolute_error|median_absolute_error è il valore mediano di tutte le differenze assolute tra il target e la stima. Questa perdita è significativa per gli outlier.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|nessuno|
normalized_median_absolute_error|normalized_median_absolute_error è l'errore assoluto mediano diviso per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_error|root_mean_squared_error è la radice quadrata della differenza quadratica stimata tra il target e la stima.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|nessuno|
normalized_root_mean_squared_error|normalized_root_mean_squared_error è la radice dell'errore quadratico medio divisa per l'intervallo dei dati.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividere per l'intervallo dei dati|
root_mean_squared_log_error|root_mean_squared_log_error è la radice quadrata dell'errore logaritmico quadratico stimato.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|nessuno|
normalized_root_mean_squared_log_error|normalized_root_mean_squared_log_error è la radice dell'errore logaritmico quadratico medio divisa per l'intervallo dei dati|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividere per l'intervallo dei dati|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Grafico Predicted vs True
#### <a name="what-is-a-predicted-vs-true-chart"></a>Che cos'è un grafico Predicted vs True?
Predicted vs True mostra la relazione tra un valore stimato e il relativo valore true correlato per un problema di regressione. Può essere usato per misurare le prestazioni di un modello poiché la vicinanza dei valori stimati alla linea y=x è direttamente proporzionale all'accuratezza di un modello predittivo.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Cosa fa la ML automatizzata con il grafico Predicted vs.
Dopo ogni esecuzione, è possibile visualizzare un grafico del valore stimato rispetto al valore vero per ogni modello di regressione. Per proteggere la privacy dei dati, i valori sono raggruppati in contenitori e la dimensione di ogni contenitore viene visualizzata in un grafico a barre nella parte inferiore dell'area del grafico. È possibile confrontare il modello predittivo, con l'area più chiara indicante i margini di errore, rispetto al valore ideale che dovrebbe raggiungere il modello.

#### <a name="what-does-a-good-model-look-like"></a>Che aspetto ha un buon modello?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Esempio 1: Un modello di classificazione con bassa precisioneExample 1: A classification model with low accuracy
![Un modello di regressione con bassa precisione nelle stime](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Esempio 2: Un modello di regressione con precisione elevataExample 2: A regression model with high accuracy 
[![Un modello di regressione con elevata precisione nelle proprie previsioni](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Istogramma dei residui grafico
#### <a name="what-is-a-residuals-chart"></a>Cos'è un grafico residua?
Un valore residuo rappresenta la differenza tra un valore y osservato e il valore y stimato. Per mostrare un margine di errore con distorsione ridotta, l'istogramma dei valori residui deve avere la forma di una curva a campana, con il centro vicino allo 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Cosa fa la ML automatizzata con il grafico dei residui?
La ML automatizzata fornisce automaticamente un grafico residui per mostrare la distribuzione degli errori nelle stime.
#### <a name="what-does-a-good-model-look-like"></a>Che aspetto ha un buon modello?
Un buon modello avrà in genere una curva a campana o errori intorno allo zero.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Esempio 1: Un modello di regressione con distorsione nei relativi erroriExample 1: A regression model with bias in its errors
![Modello di regressione SA con distorsione nei relativi errori](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Esempio 2: Un modello di regressione con una distribuzione più uniforme degli erroriExample 2: A regression model with more even distribution of errors
![Un modello di regressione con una distribuzione più uniforme degli errori](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>Interpretabilità del modello e importanza delle caratteristiche
Il machine Learning ML fornisce un dashboard di interpretabilità dell'apprendimento automatico per le esecuzioni.
Per altre informazioni sull'abilitazione delle funzionalità di interpretabilità, vedere le [procedure](how-to-machine-learning-interpretability-automl.md) per abilitare l'interpretabilità negli esperimenti automatici di ML.

## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni su ml automatizzato in Azure Machine Learning.Learn more about [automated ml](concept-automated-ml.md) in Azure Machine Learning.
+ Provare i blocchi appunti di esempio [Spiegazione modello di Machine Learning automatizzato.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
