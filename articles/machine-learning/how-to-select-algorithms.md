---
title: Come selezionare un algoritmo di Machine Learning
titleSuffix: Azure Machine Learning
description: Come selezionare Azure Machine Learning algoritmi per l'apprendimento supervisionato e non supervisionato in esperimenti di clustering, classificazione o regressione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: 5ad0d2c520d5347e36a9cead4ed6c5526d885ca4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319525"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Come selezionare gli algoritmi per Azure Machine Learning

Una domanda comune è "quale algoritmo di Machine Learning è necessario usare?" L'algoritmo selezionato dipende principalmente da due aspetti diversi dello scenario data science:

 - **Che cosa si vuole fare con i dati?** In particolare, qual è la domanda aziendale a cui si vuole rispondere imparando dai dati precedenti?

 - **Quali sono i requisiti dello scenario data science?** In particolare, qual è l'accuratezza, il tempo di training, la linearità, il numero di parametri e il numero di funzionalità supportate dalla soluzione?

 ![Considerazioni per la scelta degli algoritmi: cosa si vuole sapere? Quali sono i requisiti dello scenario?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Scenari aziendali e il foglio informativo sugli algoritmi di Machine Learning

Il [foglio](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) informativo sugli algoritmi di Azure Machine Learning consente di considerare la prima considerazione: **cosa si vuole fare con i dati**? Nel foglio informativo sull'algoritmo Machine Learning cercare l'attività da eseguire e quindi trovare un algoritmo di [Azure machine learning designer](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) per la soluzione di analisi predittiva. 

Machine Learning Designer fornisce un portfolio completo di algoritmi, ad esempio una [foresta delle decisioni multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [sistemi di raccomandazione](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), [regressione della rete neurale](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [rete neurale multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)e [clustering K-means](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri). Ogni algoritmo è progettato per risolvere un tipo diverso di problema di machine learning. Per un elenco completo e per informazioni sul funzionamento di ciascun algoritmo e su come ottimizzare i parametri per ottimizzare l'algoritmo, vedere la documentazione di riferimento per l' [algoritmo e il modulo di machine learning designer](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) .

> [!NOTE]
> Per scaricare il foglio informativo sugli algoritmi di Machine Learning, passare al foglio informativo sugli [algoritmi di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Oltre alle linee guida nel foglio informativo sugli algoritmi di Azure Machine Learning, tenere presente gli altri requisiti per la scelta di un algoritmo di machine learning per la soluzione. Di seguito sono riportati alcuni fattori aggiuntivi da considerare, ad esempio l'accuratezza, il tempo di training, la linearità, il numero di parametri e il numero di funzionalità.

## <a name="comparison-of-machine-learning-algorithms"></a>Confronto tra algoritmi di Machine Learning

Alcuni algoritmi di apprendimento fanno ipotesi particolari sulla struttura dei dati o sui risultati desiderati. Se è possibile trovarne uno adatto alle proprie esigenze, può fornire risultati più utili, previsioni più accurate o tempi di addestramento più rapidi.

Nella tabella seguente sono riepilogate alcune delle principali caratteristiche degli algoritmi delle famiglie di classificazione, regressione e clustering:

| **Algoritmo** | **Accuratezza** | **Tempo di formazione** | **Linearità** | **Parameters** | **Note** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Famiglia di classificazione** | | | | | |
| [Regressione logistica a due classi](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri) |Buono  |Veloci |Sì |4 | |
| [Foresta delle decisioni a due classi](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-decision-forest?WT.mc_id=docs-article-lazzeri) |Eccellente |Moderato |No |5 |Mostra i tempi di assegnazione dei punteggi più lenti. Si consiglia di non utilizzare One-vs-All Multiclass, a causa di tempi di punteggio più lenti causati da un blocco battistrada nell'accumulo di stime ad albero |
| [Albero delle decisioni con boosting a due classi](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |Eccellente |Moderato |No |6 |Footprint della memoria di grandi dimensioni |
| [Rete neurale a due classi](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-neural-network?WT.mc_id=docs-article-lazzeri) |Buono |Moderato |No |8 | |
| [Perceptron media a due classi](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-averaged-perceptron?WT.mc_id=docs-article-lazzeri) |Buono |Moderato |Sì |4 | |
| [Macchina a vettori di supporto a due classi](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) |Buono |Veloci |Sì |5 |Particolarmente valido per set di funzioni di grandi dimensioni |
| [Regressione logistica multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri) |Buono |Veloci |Sì |4 | |
| [Foresta delle decisioni multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) |Eccellente |Moderato |No |5 |Mostra i tempi di assegnazione dei punteggi più lenti |
| [Albero delle decisioni con boosting multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |Eccellente |Moderato |No |6 | Tende a migliorare l'accuratezza con un piccolo rischio di minore copertura |
| [Rete neurale multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri) |Buono |Moderato |No |8 | |
| [Una multiclasse One-vs-All](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/one-vs-all-multiclass?WT.mc_id=docs-article-lazzeri) | - | - | - | - |Visualizzare le proprietà del metodo a due classi selezionato |
| **Famiglia di regressione** | | | | | |
| [Regressione lineare](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/linear-regression?WT.mc_id=docs-article-lazzeri) |Buono |Veloci |Sì |4 | |
| [Regressione della foresta delle decisioni](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/decision-forest-regression?WT.mc_id=docs-article-lazzeri)|Eccellente |Moderato |No |5 | |
| [Regressione dell'albero delle decisioni con boosting](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/boosted-decision-tree-regression?WT.mc_id=docs-article-lazzeri) |Eccellente |Moderato |No |6 |Footprint della memoria di grandi dimensioni |
| [Regressione di rete neurale](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) |Buono |Moderato |No |8 | |
| **Famiglia di clustering** | | | | | |
| [Clustering K-means](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) |Eccellente |Moderato |Sì |8 |Algoritmo di clustering |

## <a name="requirements-for-a-data-science-scenario"></a>Requisiti per uno scenario di data science

Quando si sa cosa si vuole fare con i dati, è necessario determinare i requisiti aggiuntivi per la soluzione. 

Effettuare scelte ed eventualmente compromessi per i requisiti seguenti:

- Accuratezza
- Tempo di formazione
- Linearità
- Numero di parametri
- Numero di caratteristiche

## <a name="accuracy"></a>Accuratezza

L'accuratezza in Machine Learning consente di misurare l'efficacia di un modello come proporzione dei risultati reali al numero totale di case. In Machine Learning Designer il [modulo Evaluate Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) calcola un set di metriche di valutazione standard del settore. È possibile utilizzare questo modulo per misurare l'accuratezza di un modello sottoposto a training.

Ottenere la risposta più precisa possibile non è sempre necessario. Talvolta un'approssimazione è sufficiente, a seconda di ciò per cui si desidera utilizzarla. In tal caso, è possibile ridurre il tempo di elaborazione in modo significativo tramite l'uso di metodi più approssimativi. I metodi approssimativi tendono naturalmente anche a evitare l'overfitting.

Esistono tre modi per usare il modulo Evaluate Model:

- Generare punteggi sui dati di training per valutare il modello
- Genera punteggi sul modello, ma confronta i punteggi con i punteggi in un set di testing riservato
- Confrontare i punteggi per due modelli diversi, ma correlati, usando lo stesso set di dati

Per un elenco completo delle metriche e degli approcci che è possibile usare per valutare l'accuratezza dei modelli di Machine Learning, vedere [modulo Evaluate Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Tempo di formazione

Nell'apprendimento supervisionato, il training significa usare i dati cronologici per compilare un modello di Machine Learning che riduce al minimo gli errori. Il numero di minuti o ore necessarie per eseguire l’addestramento di un modello varia notevolmente tra gli algoritmi. Il tempo di training è spesso strettamente legato alla precisione; uno in genere accompagna l'altro. 

Inoltre, alcuni algoritmi sono più sensibili al numero di punti dati rispetto ad altri. È possibile scegliere un algoritmo specifico perché si dispone di un limite di tempo, specialmente quando il set di dati è di grandi dimensioni.

In Machine Learning Designer, la creazione e l'uso di un modello di apprendimento automatico è in genere un processo in tre passaggi:

1.  Configurare un modello, scegliendo un particolare tipo di algoritmo e definendone i parametri o gli iperparametri. 

2.  Fornire un set di dati con etichetta e con i dati compatibili con l'algoritmo. Connettere sia i dati che il modello al [modulo Train Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Al termine del training, usare il modello con training con uno dei [moduli](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) di assegnazione dei punteggi per eseguire stime sui nuovi dati.

## <a name="linearity"></a>Linearità

Linearità nelle statistiche e Machine Learning significa che esiste una relazione lineare tra una variabile e una costante nel set di dati. Gli algoritmi di classificazione lineare, ad esempio, presuppongono che le classi possano essere separate da una linea retta (o dal relativo analogo più dimensionale).

Un numero elevato di algoritmi di Machine Learning utilizza la linearità. In Azure Machine Learning Designer includono: 

- [Regressione logistica multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Regressione logistica a due classi](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Macchine a vettori di supporto](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Gli algoritmi di regressione lineare ipotizzano che le tendenze dei dati seguano una linea retta. Questo presupposto non è valido per alcuni problemi, ma per altri riduce l'accuratezza. Nonostante i loro svantaggi, gli algoritmi lineari sono popolari come prima strategia. Tendono ad essere algoritmicamente semplici e rapidi da addestrare.

![Limite di classe non lineare](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Limite di classi non lineari***: l'uso di *un algoritmo di classificazione lineare comporta una bassa precisione.*

![Dati con una tendenza non lineare](./media/how-to-select-algorithms/nonlinear-trend.png)

***Dati con una tendenza non lineare***: *l'uso di un metodo di regressione lineare genera errori molto più grandi del necessario.*

## <a name="number-of-parameters"></a>Numero di parametri

I parametri sono i pulsanti che uno scienziato dei dati deve utilizzare per configurare un algoritmo. Si tratta di numeri che influiscono sul comportamento dell'algoritmo, ad esempio la tolleranza di errore o il numero di iterazioni, oppure opzioni tra le varianti del comportamento dell'algoritmo. Il tempo di training e l'accuratezza dell'algoritmo possono a volte essere sensibili per ottenere solo le impostazioni corrette. In genere, gli algoritmi con un numero elevato di parametri richiedono la maggior parte della versione di valutazione e l'errore per trovare una combinazione valida.

In alternativa, è disponibile il [modulo Tune Model iperparametris](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) in Machine Learning Designer: l'obiettivo di questo modulo è determinare gli iperparametri ottimali per un modello di apprendimento automatico. Il modulo compila e testa più modelli usando diverse combinazioni di impostazioni. Viene confrontata la metrica su tutti i modelli per ottenere le combinazioni di impostazioni. 

Sebbene questo sia un ottimo modo per assicurarsi di avere esteso lo spazio dei parametri, il tempo necessario per il training di un modello aumenta in modo esponenziale con il numero di parametri. Il vantaggio è un numero elevato di parametri in genere indica che un algoritmo ha maggiore flessibilità. Spesso può ottenere una buona accuratezza, purché sia possibile trovare la corretta combinazione di impostazioni dei parametri.

## <a name="number-of-features"></a>Numero di caratteristiche

In Machine Learning, una funzionalità è una variabile quantificabile del fenomeno che si sta tentando di analizzare. Per alcuni tipi di dati, il numero di caratteristiche può essere molto grande rispetto al numero di punti dati. Questo accade spesso con i dati testuali o sulla genetica. 

Un numero elevato di funzionalità può impantanare alcuni algoritmi di apprendimento, rendendo il tempo di training unfeasibly lungo. Le [macchine a vettori di supporto](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) sono particolarmente adatte per gli scenari con un numero elevato di funzionalità. Per questo motivo, sono stati usati in molte applicazioni, dal recupero delle informazioni alla classificazione di testo e immagini. Le macchine a vettori di supporto possono essere usate sia per le attività di classificazione che di regressione.

La selezione delle caratteristiche si riferisce al processo di applicazione dei test statistici agli input, dato un output specificato. L'obiettivo è determinare quali colonne sono più predittive dell'output. Il [modulo Filter based feature selection](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) in Machine Learning Designer offre più algoritmi di selezione delle funzioni tra cui scegliere. Il modulo include metodi di correlazione come la correlazione di Pearson e i valori chi quadrato.

È anche possibile usare il [modulo Permutation feature](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) Importance per calcolare un set di punteggi di importanza della funzionalità per il set di dati. È quindi possibile sfruttare questi punteggi per determinare le funzionalità migliori da utilizzare in un modello.

## <a name="next-steps"></a>Passaggi successivi

 - [Altre informazioni su Azure Machine Learning Designer](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Per le descrizioni di tutti gli algoritmi di machine learning disponibili in Azure Machine Learning Designer, vedere la pagina relativa al [riferimento al modulo e all'algoritmo di machine learning designer](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - Per esplorare la relazione tra Deep Learning, Machine Learning e intelligenza artificiale, vedere [Deep Learning e Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
