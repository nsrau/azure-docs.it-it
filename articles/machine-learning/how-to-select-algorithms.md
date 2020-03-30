---
title: Come selezionare un algoritmo di apprendimento automaticoHow to select a machine learning algorithm
titleSuffix: Azure Machine Learning
description: Come selezionare gli algoritmi di Azure Machine Learning per l'apprendimento supervisionato e senza supervisione negli esperimenti di clustering, classificazione o regressione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328664"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Come selezionare gli algoritmi per Azure Machine LearningHow to select algorithms for Azure Machine Learning

Una domanda comune è "Quale algoritmo di apprendimento automatico è consigliabile usare?" L'algoritmo selezionato dipende principalmente da due aspetti diversi dello scenario di data science:The algorithm you select depends primarily on two different aspects of your data science scenario:

 - **Cosa vuoi fare con i tuoi dati?** In particolare, qual è la domanda aziendale a cui vuoi rispondere imparando dai tuoi dati passati?

 - **Quali sono i requisiti dello scenario di data science?** In particolare, qual è la precisione, il tempo di allenamento, la linearità, il numero di parametri e il numero di funzionalità supportate dalla soluzione?

 ![Considerazioni per la scelta degli algoritmi: cosa si vuole sapere? Quali sono i requisiti dello scenario?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Scenari aziendali e foglio di trucco dell'algoritmo di Machine Learning

Azure [Machine Learning Algorithm Cheat Sheet](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) ti aiuta con la prima considerazione: Cosa **vuoi fare con i tuoi dati?** Nel foglio Cheat dell'algoritmo di Machine Learning cercare l'attività desiderata e quindi trovare un algoritmo di [progettazione](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) di Azure Machine Learning per la soluzione di analisi predittiva. 

Progettazione di Machine Learning fornisce un portafoglio completo di algoritmi, ad esempio [Multiclass Decision Forest](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [Sistemi di raccomandazione](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), [Regressione della rete neurale](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [Multiclass Neural Network](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)e [K-Means Clustering](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri). Ogni algoritmo è progettato per risolvere un diverso tipo di problema di apprendimento automatico. Vedere [l'algoritmo di progettazione di Machine Learning e il riferimento](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) al modulo per un elenco completo insieme alla documentazione sul funzionamento di ogni algoritmo e su come ottimizzare i parametri per ottimizzare l'algoritmo.

> [!NOTE]
> Per scaricare il foglio cheat dell'algoritmo di apprendimento automatico, passare a [Azure Machine Learning algorithm cheat sheet](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Oltre alle indicazioni contenute nel foglio cheat dell'algoritmo di Azure Machine Learning, tenere presente altri requisiti quando si sceglie un algoritmo di apprendimento automatico per la soluzione. Di seguito sono riportati altri fattori da considerare, ad esempio l'accuratezza, il tempo di allenamento, la linearità, il numero di parametri e il numero di feature.

## <a name="additional-requirements-for-a-data-science-scenario"></a>Requisiti aggiuntivi per uno scenario di data scienceAdditional requirements for a data science scenario

Una volta che si conosce cosa si desidera fare con i dati, è necessario determinare i requisiti aggiuntivi per la soluzione. 

Effettuare scelte ed eventualmente compromessi per i seguenti requisiti:

- Accuratezza
- Tempo di formazione
- Linearità
- Numero di parametri
- Numero di caratteristiche

## <a name="accuracy"></a>Accuratezza

L'accuratezza nell'apprendimento automatico misura l'efficacia di un modello come percentuale di risultati reali rispetto ai casi totali. In Progettazione Machine Learning, il [modulo Valuta modello](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) calcola un set di metriche di valutazione standard del settore. È possibile utilizzare questo modulo per misurare l'accuratezza di un modello sottoposto a training.

Ottenere la risposta più accurata possibile non è sempre necessario. Talvolta un'approssimazione è sufficiente, a seconda di ciò per cui si desidera utilizzarla. Se questo è il caso, si può essere in grado di ridurre drasticamente il tempo di elaborazione da attaccare con metodi più approssimativi. Anche i metodi approssimativi tendono naturalmente a evitare l'overfitting.

Esistono tre modi per utilizzare il modulo Valuta modello:

- Generare punteggi sui dati di training per valutare il modello
- Generare punteggi sul modello, ma confrontarli con i punteggi in un set di test riservato
- Confronta i punteggi per due modelli diversi ma correlati, usando lo stesso set di dati

Per un elenco completo delle metriche e degli approcci che è possibile utilizzare per valutare l'accuratezza dei modelli di apprendimento automatico, vedere [Valutare il modulo del modello](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Tempo di formazione

Nell'apprendimento supervisionato, il training significa usare i dati cronologici per creare un modello di apprendimento automatico che riduca al minimo gli errori. Il numero di minuti o ore necessarie per eseguire l’addestramento di un modello varia notevolmente tra gli algoritmi. Il tempo di allenamento è spesso strettamente legato alla precisione; uno di solito accompagna l'altro. 

Inoltre, alcuni algoritmi sono più sensibili al numero di punti dati rispetto ad altri. È possibile scegliere un algoritmo specifico perché si ha un limite di tempo, soprattutto quando il set di dati è di grandi dimensioni.

Nella finestra di progettazione di Machine Learning, la creazione e l'uso di un modello di Apprendimento automatico è in genere un processo in tre passaggi:In Machine Learning designer, creating and using a machine learning model is typically a three-step process:

1.  Configurare un modello scegliendo un particolare tipo di algoritmo e quindi definendone i parametri o gli iperparametri. 

2.  Fornire un set di dati etichettato e con dati compatibili con l'algoritmo. Collegare sia i dati che il modello al [modulo Train Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Dopo il completamento del training, usare il modello sottoposto a training con uno dei moduli di [punteggio](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) per eseguire stime sui nuovi dati.

## <a name="linearity"></a>Linearità

La linearità nelle statistiche e nell'apprendimento automatico significa che esiste una relazione lineare tra una variabile e una costante nel set di dati. Ad esempio, gli algoritmi di classificazione lineare presuppongono che le classi possano essere separate da una linea retta (o dal suo analogo di dimensioni superiori).

Un numero elevato di algoritmi di Machine Learning utilizza la linearità. Nella finestra di progettazione di Azure Machine Learning includono:In Azure Machine Learning designer, they include: 

- [regressione logistica multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Regressione logistica a due classi](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Macchine vettoriali di supporto](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Gli algoritmi di regressione lineare ipotizzano che le tendenze dei dati seguano una linea retta. Questo presupposto non è male per alcuni problemi, ma per altri riduce la precisione. Nonostante i loro inconvenienti, gli algoritmi lineari sono popolari come prima strategia. Tendono ad essere algoritmicamente semplici e rapidi da addestrare.

![Limite di classe non lineare](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Limite di classe non lineare***: Affidarsi a un algoritmo di *classificazione lineare comporterebbe una bassa precisione.*

![Dati con una tendenza non lineare](./media/how-to-select-algorithms/nonlinear-trend.png)

***Dati con una tendenza non lineare***: *l'utilizzo di un metodo di regressione lineare genererebbe errori molto più grandi del necessario.*

## <a name="number-of-parameters"></a>Numero di parametri

I parametri sono i pulsanti che uno scienziato dei dati deve utilizzare per configurare un algoritmo. Sono numeri che influiscono sul comportamento dell'algoritmo, ad esempio la tolleranza di errore o il numero di iterazioni o le opzioni tra le varianti del comportamento dell'algoritmo. Il tempo di training e l'accuratezza dell'algoritmo a volte possono essere sensibili all'ottenimento delle impostazioni corrette. In genere, gli algoritmi con un numero elevato di parametri richiedono la maggior parte degli tentativi e degli errori per trovare una buona combinazione.

In alternativa, è disponibile il [modulo Sintonizzare gli iperparametri del modello](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) in Progettazione di Machine Learning: l'obiettivo di questo modulo è determinare gli iperparametri ottimali per un modello di apprendimento automatico. Il modulo compila e testa più modelli utilizzando diverse combinazioni di impostazioni. Confronta le metriche su tutti i modelli per ottenere le combinazioni di impostazioni. 

Anche se questo è un ottimo modo per assicurarsi di aver espandito lo spazio dei parametri, il tempo necessario per eseguire il training di un modello aumenta in modo esponenziale con il numero di parametri. Il vantaggio è un numero elevato di parametri in genere indica che un algoritmo ha maggiore flessibilità. Spesso può ottenere una precisione molto buona, a condizione di trovare la giusta combinazione di impostazioni dei parametri.

## <a name="number-of-features"></a>Numero di caratteristiche

Nell'apprendimento automatico, una funzionalità è una variabile quantificabile del fenomeno che si sta tentando di analizzare. Per alcuni tipi di dati, il numero di caratteristiche può essere molto grande rispetto al numero di punti dati. Questo accade spesso con i dati testuali o sulla genetica. 

Un gran numero di funzionalità può impantanare alcuni algoritmi di apprendimento, rendendo il tempo di allenamento incredibilmente lungo. [Le macchine vettoriali](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) di supporto sono particolarmente adatte a scenari con un numero elevato di funzionalità. Per questo motivo, sono stati utilizzati in molte applicazioni dal recupero delle informazioni alla classificazione di testo e immagini. Le macchine vettoriali di supporto possono essere utilizzate sia per le attività di classificazione che per le attività di regressione.

La selezione delle funzionalità si riferisce al processo di applicazione dei test statistici agli input, dato un output specificato. L'obiettivo è determinare quali colonne sono più predittive dell'output. Il [modulo Selezione funzionalità basata](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) su filtro in Progettazione Machine Learning offre più algoritmi di selezione delle funzionalità tra cui scegliere. Il modulo include metodi di correlazione come la correlazione di Pearson e i valori del chi quadrato.

È inoltre possibile utilizzare il [modulo Permutazione importanza funzionalità](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) per calcolare un set di punteggi di importanza delle funzionalità per il set di dati. È quindi possibile sfruttare questi punteggi per determinare le funzionalità migliori da utilizzare in un modello.


## <a name="next-steps"></a>Passaggi successivi

 - [Altre informazioni sulla finestra di progettazione di Azure Machine LearningLearn more about Azure Machine Learning designer](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Per le descrizioni di tutti gli algoritmi di apprendimento automatico disponibili nella finestra di progettazione di Azure Machine Learning, vedere Informazioni di riferimento sull'algoritmo e sui moduli della finestra di [progettazione di Machine LearningFor descriptions](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) of all the machine learning algorithms available in Azure Machine Learning designer, see Machine Learning designer algorithm and module reference
 - Per esplorare la relazione tra deep learning, apprendimento automatico e ima, vedere [Deep Learning vs.](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
