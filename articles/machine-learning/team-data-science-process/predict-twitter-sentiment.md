---
title: Stimare Twitter sentiment con word incorporamenti tramite il processo di analisi scientifica dei dati di Team in Azure | Documenti Microsoft
description: I passaggi necessari per eseguono i progetti della scienza dei dati.
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev;
ms.openlocfilehash: 20bc3f31897cec4a3cec9ca409062229133102f5
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Stimare Twitter sentiment con word incorporamenti tramite il processo di analisi scientifica dei dati di Team

In questo articolo viene illustrato come collaborare in modo efficace tramite il _Word2Vec_ word incorporando algoritmo e _incorporamento Word Sentiment specifiche (SSWE)_ algoritmo per la stima Twitter sentiment con [Azure Machine Learning](../preview/index.yml). Per ulteriori informazioni sulla stima polarità di Twitter sentiment, vedere il [MachineLearningSamples TwitterSentimentPrediction repository](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) su GitHub. La chiave per agevolare la collaborazione efficace su progetti scienza dei dati è quella di definire la struttura e la documentazione dei progetti con un ciclo di vita di analisi scientifica dei dati stabiliti. Il [Team Data Science processo (TDSP)](overview.md) fornisce questo tipo di strutturati [ciclo di vita](lifecycle.md). 

Creazione di progetti scienza dei dati con il _modello TDSP_ fornisce il framework standardizzato per i progetti di Azure Machine Learning. In precedenza, il team TDSP rilasciato un [repository GitHub per la struttura del progetto TDSP e i modelli](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Ora i progetti di Machine Learning che vengono creata un'istanza con [modelli TDSP per Azure Machine Learning](https://github.com/amlsamples/tdsp) sono abilitati. Per istruzioni, vedere come utilizzare [progetti struttura TDSP con il modello TDSP](../preview/how-to-use-tdsp-in-azure-ml.md) in Azure Machine Learning. 


## <a name="twitter-sentiment-polarity-sample"></a>Esempio di Twitter sentiment polarità

In questo articolo usa un esempio è illustrare come creare un'istanza ed eseguire un progetto di Machine Learning. L'esempio Usa la struttura TDSP e i modelli in Azure Machine Learning Workbench. L'esempio completo viene fornito [in questa procedura dettagliata](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). L'attività di modellazione stima polarità sentiment (positivo o negativo) utilizzando il testo da TWEET. In questo articolo vengono descritte le attività di modellazione dei dati che sono descritte nella procedura dettagliata. La procedura dettagliata vengono illustrate le attività seguenti:

- L'esplorazione dei dati, training e la distribuzione di un modello di machine learning che risolvere il problema di stima descritto nella panoramica di case di utilizzo. [Dati relativi al sentiment Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) viene usato per queste attività.
- Esecuzione del progetto usando il modello TDSP da Azure Machine Learning per questo progetto. Per l'esecuzione del progetto e creazione di report, viene utilizzato il ciclo di vita TDSP.
- Rendere operativo il la soluzione direttamente da Azure Machine Learning nel servizio contenitore di Azure.

Il progetto vengono evidenziate le seguenti funzionalità di Azure Machine Learning:

- La creazione di istanze e l'utilizzo della struttura TDSP.
- Esecuzione del codice nell'area di lavoro di Azure Machine Learning.
- Agevolato nel servizio contenitore tramite Docker e Kubernetes.

## <a name="team-data-science-process"></a>Processo di analisi scientifica dei dati per i team
Per eseguire questo esempio, utilizzare i modelli TDSP struttura e la documentazione del progetto in Azure Machine Learning Workbench. L'esempio implementa il [ciclo di vita TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), come illustrato nella figura riportata di seguito:

![Ciclo di vita del processo TDSP](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

In Azure Machine Learning Workbench in base a cui viene creato il progetto TDSP [queste istruzioni](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), come illustrato nella figura riportata di seguito:

![Creare TDSP nell'area di lavoro di Azure Machine Learning](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Preparazione e l'acquisizione dei dati](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Il primo passaggio in questo esempio è per scaricare il set di dati sentiment140 e dividere i dati in set di training e set di dati di testing. Il set di dati sentiment140 include il contenuto effettivo del tweet (con emoticon rimossa). Il set di dati contiene inoltre la polarità di ogni tweet (negativo = 0, positivo = 4) con il TWEET neutro rimosso. Dopo che i dati vengono suddivisi, i dati di training 1.3 milioni di righe, che i dati di testing hanno 320,000 righe.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Sviluppo del modello](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Il passaggio successivo nell'esempio è di sviluppare un modello per i dati. L'attività di modellazione è suddivisa in tre parti:

- Funzionalità di progettazione: generare funzioni per il modello utilizzando word diversi algoritmi di incorporamento. 
- Creazione del modello: eseguire il training di modelli diversi per stimare la valutazione del testo di input. Esempi di questi modelli includono _Logistic Regression_ e _Boosting dei gradienti_.
- Valutazione del modello: valutare i modelli con training su dati di test.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Progettazione di funzionalità](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Gli algoritmi Word2Vec e SSWE vengono utilizzati per generare incorporamenti di word. 


#### <a name="word2vec-algorithm"></a>Algoritmo Word2Vec

L'algoritmo Word2Vec viene utilizzato nel modello Skip-gramma. Questo modello è illustrato nel documento da Tomas Mikolov, et al. "[Distributed rappresentazioni di parole e frasi e i relativi composizionalità. Progressi nelle informazioni neurale sistemi di elaborazione. ](https://arxiv.org/abs/1310.4546)" 2013.

Il modello di Skip-gramma è una rete neurale superficiale. L'input è la parola che viene codificata come un vettore a caldo uno, viene usato per stimare nelle vicinanze di parole. Se **V** è la dimensione del vocabolario, quindi la dimensione del livello di output è __C * V__ dove C è la dimensione della finestra di contesto. Nella figura seguente è illustrata un'architettura che è basata sul modello di Skip-gramma:

![Modello Skip-gramma](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Il meccanismo dettagliato dell'algoritmo Word2Vec e modello Skip-gramma non rientrano nell'ambito di questo esempio. Per ulteriori informazioni, vedere i seguenti riferimenti:

- [Codice 02_A_Word2Vec.py con cui viene fatto riferimento TensorFlow esempi](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec) (Rappresentazioni vettoriali di parole)
- [Esattamente come funziona word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Note sulla stima Contrastive rumore e campionamento negativo](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Algoritmo di incorporamento Word sentiment specifiche
L'algoritmo SSWE tenta di superare un punto debole dell'algoritmo Word2Vec in cui le parole con contesti simili e opposto polarità possono avere vettori word simili. Questi aspetti in comune possono causare l'algoritmo Word2Vec per non eseguire in modo accurato per attività, ad esempio sentiment analysis. L'algoritmo SSWE tenta di gestire questa vulnerabilità incorporando sia la polarità della frase e il contesto di word nella propria funzione di perdita.

L'esempio Usa una variante dell'algoritmo SSWE come indicato di seguito:

- Entrambi originale _ngram_ e il danneggiato _ngram_ utilizzate come input.
- Oggetto stile cerniera perdita funzione di rango viene utilizzato per la perdita di sintassi e la perdita di semantica.
- La funzione di perdita finale è la combinazione di Media ponderata di sia la perdita di sintassi e la perdita di semantica.
- Per semplicità, solo la semantica tra entropia viene utilizzata come funzione di perdita.

L'esempio mostra che, anche con la funzione di perdita più semplice, le prestazioni dell'incorporamento SSWE sono migliore l'incorporamento Word2Vec. Nella figura seguente viene illustrato il modello convolutional utilizzato per generare l'incorporamento sentiment specifiche:

![Modello di rete neurale ispirata SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

Al termine il processo di training, vengono generati due file incorporamento nel formato di valori delimitati da tabulazioni (TSV) per la fase di modellazione.

Per ulteriori informazioni sugli algoritmi SSWE, vedere l'articolo Duyu Tang, et al. "[Sentiment specifiche di apprendimento Word incorporamento per Twitter Sentiment Classification](http://www.aclweb.org/anthology/P14-1146)." Associazione per calcolo Linguistics (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Creazione del modello](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Dopo avere generati i vettori di word usando l'algoritmo SSWE o Word2Vec, vengono eseguito il training dei modelli di classificazione per stimare la polarità sentiment effettivo. Due tipi di funzionalità: Word2Vec e SSWE, vengono applicate a due modelli: il modello di Boosting dei gradienti e il modello di regressione logistica. Di conseguenza, vengono eseguito il training quattro modelli diversi.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Valutazione del modello](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Dopo aver eseguito il training dei modelli, i modelli vengono utilizzati per dati di testo di Twitter di test e valutare le prestazioni di ciascun modello. L'esempio restituisce i seguenti quattro modelli:

- Sfumatura Boosting su SSWE incorporamento.
- Regressione logistica su SSWE incorporamento.
- Sfumatura Boosting su Word2Vec incorporamento.
- Regressione logistica su Word2Vec incorporamento.

Un confronto delle prestazioni di quattro modelli è illustrato nella figura riportata di seguito:

![Ricevitore operativo caratteristico confronto di modelli (ROC)](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

Il modello di Boosting dei gradienti con la funzionalità SSWE offre le migliori prestazioni quando si confrontano i modelli utilizzando l'area sotto la metrica della curva (AUC).


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Distribuzione](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Il passaggio finale è la distribuzione del modello di stima sentiment con Training da un servizio web in un cluster nel servizio contenitore di Azure. L'esempio Usa il modello di Boosting dei gradienti con l'algoritmo di incorporamento SSWE come il modello con training. L'ambiente di rendere operativo esegue il provisioning di Docker e Kubernetes del cluster per gestire la distribuzione del servizio web, come illustrato nella figura riportata di seguito: 

![Dashboard Kubernetes](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Per ulteriori informazioni sul processo di rendere operativo, vedere [la distribuzione di un modello di Azure Machine Learning come un servizio web](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Conclusioni

In questo articolo è stato descritto per il training di un modello di incorporamento di word utilizzando gli algoritmi Word2Vec e incorporamento Word Sentiment specifiche. L'estratti incorporamenti utilizzati come funzionalità per eseguire il training più modelli per stimare i punteggi di valutazione per i dati di testo di Twitter. La funzionalità SSWE con il modello di Boosting dei gradienti ha restituito prestazioni ottimali. Quindi, il modello è stato distribuito come servizio web in tempo reale nel servizio contenitore utilizzando l'area di lavoro di Azure Machine Learning.


## <a name="references"></a>Riferimenti

* [Processo di analisi scientifica dei dati di team](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [How to use Team Data Science Process (TDSP) in Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml) (Come usare Team Data Science Process, TDSP, in Azure Machine Learning)
* [Modelli di progetto TDSP per Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [Reddito di Stati Uniti set di dati dal repository UCI ML](https://archive.ics.uci.edu/ml/datasets/adult)
* [Riconoscimento delle entità biomedica utilizzando modelli TDSP](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov, Tomas, et al. "Le rappresentazioni di parole e frasi e i relativi composizionalità distribuito. Sposta in neurale informazioni nei sistemi di elaborazione." 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu, et al. "Apprendimento incorporamento Sentiment specifiche per la classificazione di Twitter Sentiment". ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
