---
title: Prevedere il sentiment in Twitter con rappresentazioni distribuite delle parole usando Team Data Science Process in Azure | Microsoft Docs
description: Questi passaggi sono necessari per eseguire i progetti di data science.
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
ms.openlocfilehash: df1124ddb436f0cfeec8f4ed9728fa15278b9325
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Prevedere il sentiment in Twitter con rappresentazioni distribuite delle parole usando Team Data Science Process

Questo articolo mostra come collaborare in modo efficace usando l'algoritmo di rappresentazione distribuita delle parole _Word2Vec_ e l'algoritmo _Sentiment-Specific Word Embedding (SSWE)_ per prevedere il sentiment in Twitter con [Azure Machine Learning](../preview/index.yml). Per altre informazioni sulla previsione della polarità del sentiment in Twitter, vedere il [repository MachineLearningSamples-TwitterSentimentPrediction](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) in GitHub. L'aspetto chiave per favorire una collaborazione efficace del team in progetti di data science consiste nello standardizzare la struttura e la documentazione per i progetti con un ciclo di vita di data science stabilito. [Team Data Science Process (TDSP)](overview.md) offre questo tipo di [ciclo di vita](lifecycle.md) strutturato. 

La creazione di progetti di data science con _Team Data Science Process (TDSP)_ offre il framework standardizzato per progetti di Azure Machine Learning. In passato il team di TDSP ha reso disponibile un [repository GitHub per la struttura e i modelli di progetto di TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Oggi sono disponibili progetti di Machine Learning di cui vengono create istanze con [modelli di TDSP per Azure Machine Learning](https://github.com/amlsamples/tdsp). Per istruzioni, vedere come usare [progetti di struttura di TDSP con un modello di TDSP](../preview/how-to-use-tdsp-in-azure-ml.md) in Azure Machine Learning. 


## <a name="twitter-sentiment-polarity-sample"></a>Esempio di polarità del sentiment in Twitter

Questo articolo usa un esempio per mostrare come creare un'istanza di un progetto di Machine Learning ed eseguire il progetto. L'esempio usa la struttura e i modelli di TDSP in Azure Machine Learning Workbench. L'esempio completo è disponibile in [questa procedura dettagliata](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). L'attività di modellazione prevede la polarità del sentiment (positivo o negativo) usando il testo dei Tweet. Questo articolo presenta le attività di modellazione dei dati descritte nella procedura dettagliata. La procedura dettagliata include le attività seguenti:

- Esplorazione, training e distribuzione dei dati di un modello di apprendimento automatico che risolve il problema di previsione descritto nella panoramica del caso d'uso. Per queste attività vengono usati i [dati del sentiment in Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip).
- Esecuzione del progetto tramite il modello di TDSP da Azure Machine Learning per questo progetto. Per l'esecuzione del progetto e la creazione di report, si userà il ciclo di vita di TDSP.
- Operazionalizzazione della soluzione direttamente da Azure Machine Learning nel servizio contenitore di Azure.

Il progetto evidenzia le funzionalità seguenti di Azure Machine Learning:

- Creazione di un'istanza e uso della struttura di TDSP.
- Esecuzione di codice in Azure Machine Learning Workbench.
- Semplice operazionalizzazione nel servizio contenitore tramite Docker e Kubernetes.

## <a name="team-data-science-process"></a>Processo di analisi scientifica dei dati per i team
Per eseguire questo esempio, è necessario usare la struttura di progetto e i modelli di documentazione di TDSP in Azure Machine Learning Workbench. L'esempio implementa il [ciclo di vita di TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), mostrato nella figura seguente:

![Ciclo di vita del processo TDSP](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

Il progetto di TDSP viene creato in Azure Machine Learning Workbench in base a [queste istruzioni](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), come mostrato nella figura seguente:

![Creare il progetto di TDSP in Azure Machine Learning Workbench](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Acquisizione e preparazione dei dati](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Il primo passaggio in questo esempio consiste nello scaricare il set di dati sentiment140 e dividere i dati in set di dati di training e test. Il set di dati sentiment140 contiene l'effettivo contenuto dei Tweet (dopo aver rimosso le emoticon). Il set di dati contiene anche la polarità di ogni Tweet (negativo=0, positivo=4), dopo aver rimosso i Tweet neutri. Dopo aver diviso i dati, i dati di training hanno 1,3 milioni di righe, mentre i dati di test ne hanno 320.000.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Sviluppo del modello](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Il passaggio successivo nell'esempio consiste nello sviluppare un modello per i dati. L'attività di modellazione è divisa in tre parti:

- Progettazione delle funzionalità: generazione delle funzionalità per il modello usando diversi algoritmi di rappresentazione distribuita delle parole. 
- Creazione del modello: training di modelli diversi per prevedere il sentiment del testo di input. Tra i possibili esempi, sono disponibili i modelli di _regressione logistica_ e di _boosting a gradienti_.
- Valutazione dei modelli: valutazione dei modelli con training con i dati di test.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Progettazione delle funzionalità](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Gli algoritmi Word2Vec e SSWE vengono usati per generare rappresentazioni distribuite delle parole. 


#### <a name="word2vec-algorithm"></a>Algoritmo Word2Vec

L'algoritmo Word2Vec viene usato nel modello skip-gram. Questo modello viene descritto nella pubblicazione di Tomas Mikolov, et al. "[Distributed Representations of Words and Phrases and their Compositionality. Advances in neural information processing systems](https://arxiv.org/abs/1310.4546)" (Rappresentazioni distribuite di parole e frasi e composizionalità. Progressi nei sistemi neurali di elaborazione delle informazioni). 2013.

Il modello skip-gram è una rete neurale superficiale. L'input è la parola di destinazione codificata come vettore one-hot, usato per prevedere le parole vicine. Se **V** è la dimensione del vocabolario, la dimensione del livello di output è __C*V__, dove C è la dimensione della finestra di contesto. La figura seguente mostra un'architettura basata sul modello skip-gram:

![Modello skip-gram](./media/predict-twitter-sentiment/skip-gram-model.PNG)

I meccanismi dettagliati dell'algoritmo Word2Vec e del modello skip-gram non rientrano nell'ambito di questo esempio. Per altre informazioni, vedere i riferimenti seguenti:

- [02_A_Word2Vec.py code with referenced TensorFlow examples](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) (Codice 02_A_Word2Vec.py con esempi TensorFlow di riferimento) 
- [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec) (Rappresentazioni vettoriali di parole)
- [How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf) (Come funziona esattamente Word2Vec?)
- [Notes on Noise Contrastive Estimation and Negative Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf) (Note sulla stima contrastiva del rumore e sul campionamento negativo)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Algoritmo Sentiment-Specific Word Embedding (SSWE)
L'algoritmo SSWE prova a superare i punti deboli dell'algoritmo Word2Vec, in cui le parole con contesti simili e polarità opposta possono avere vettori di parola simili. Le similarità possono far sì che l'algoritmo Word2Vec non venga eseguito accuratamente per attività come l'analisi del sentiment. L'algoritmo SSWE prova a gestire questi punti deboli incorporando sia la polarità della frase sia il contesto della parola nella funzione di perdita.

L'esempio usa una variante dell'algoritmo SSWE nel modo seguente:

- Vengono usati come input sia l'_n-gram_ originale sia l'_n-gram_ danneggiato.
- Viene usata una funzione di perdita cardine in stile classificazione per la perdita sintattica e la perdita semantica.
- La funzione di perdita finale è la combinazione pesata della perdita sintattica e della perdita semantica.
- Per motivi di semplicità, come funzione di perdita viene usata solo l'entropia incrociata semantica.

L'esempio mostra che anche con una funzione di perdita più semplice, le prestazioni di rappresentazione distribuita di SSWE sono migliori di quelle di Word2Vec. La figura seguente mostra il modello convoluzionale usato per generare la rappresentazione distribuita delle parole specifica del sentiment:

![Modello di rete neurale basato su SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

Al termine del processo di training, vengono generati due file di rappresentazione dinamica in formato TSV (Tab-Separated Values, valori separati da tabulazioni) per la fase di modellazione.

Per altre informazioni sugli algoritmi SSWE, vedere la pubblicazione di Duyu Tang, et al. "[Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification](http://www.aclweb.org/anthology/P14-1146)" (Apprendimento della rappresentazione distribuita delle parole specifica del sentiment per la classificazione del sentiment in Twitter). Association for Computational Linguistics (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Creazione del modello](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Dopo la generazione dei vettori delle parole tramite l'algoritmo SSWE o Word2Vec, viene eseguito il training dei modelli di classificazione per prevedere la polarità effettiva del sentiment. Due tipi di funzionalità, Word2Vec e SSWE, vengono applicati a due modelli: il modello di boosting a gradienti e il modello di regressione logistica. Di conseguenza, viene eseguito il training di quattro modelli diversi.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Valutazione del modello](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Al termine del training, i modelli vengono usati per testare i dati di testo di Twitter e valutare le prestazioni di ciascuno. L'esempio valuta i quattro modelli seguenti:

- Boosting a gradienti con rappresentazione dinamica SSWE.
- Regressione logistica con rappresentazione dinamica SSWE.
- Boosting a gradienti con rappresentazione dinamica Word2Vec.
- Regressione logistica con rappresentazione dinamica Word2Vec.

Nella figura seguente viene mostrato un confronto delle prestazioni dei quattro modelli:

![Confronto dei modelli ROC (Receiver Operating Characteristic)](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

Il modello di boosting a gradienti con la funzionalità SSWE offre le prestazioni migliori quando si confrontano i modelli usando la metrica dell'area sottesa dalla curva (AUC, Area Under Curve).


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Distribuzione](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Il passaggio finale consiste nella distribuzione del modello di previsione del sentiment con training come servizio Web in un cluster nel servizio contenitore di Azure. L'esempio usa il modello di boosting a gradienti con l'algoritmo di rappresentazione distribuita SSWE come modello con training. L'ambiente di operazionalizzazione effettua il provisioning di Docker e Kubernetes nel cluster per gestire la distribuzione del servizio Web, come mostrato nella figura seguente: 

![Dashboard Kubernetes](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Per altre informazioni sul processo di operazionalizzazione, vedere [Distribuzione di un modello di Machine Learning come servizio Web](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Conclusioni

In questo articolo si è appreso come eseguire il training di un modello di rappresentazione distribuita delle parole usando gli algoritmi Word2Vec e Sentiment-Specific Word Embedding (SSWE). Le rappresentazioni distribuite estratte sono state usate come funzionalità per il training di diversi modelli in modo da prevedere i punteggi del sentiment per i dati di testo di Twitter. La funzionalità SSWE usata con il modello di boosting a gradienti ha avuto le prestazioni migliori. Il modello è stato quindi distribuito come servizio Web in tempo reale nel servizio contenitore usando Azure Machine Learning Workbench.


## <a name="references"></a>Riferimenti

* [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [How to use Team Data Science Process (TDSP) in Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml) (Come usare Team Data Science Process, TDSP, in Azure Machine Learning)
* [TDSP project template for Azure Machine Learning](https://aka.ms/tdspamlgithubrepo) (Modello di progetto di TDSP per Azure Machine Learning)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Set di dati sul reddito degli Stati Uniti del repository UCI per l'apprendimento automatico](https://archive.ics.uci.edu/ml/datasets/adult)
* [Riconoscimento di entità biomediche con un modello di Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov, Tomas, et al. "Distributed Representations of Words and Phrases and their Compositionality. Advances in neural information processing systems" (Rappresentazioni distribuite di parole e frasi e composizionalità. Progressi nei sistemi neurali di elaborazione delle informazioni) 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu, et al. "Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification" (Apprendimento della rappresentazione distribuita delle parole specifica del sentiment per la classificazione del sentiment in Twitter) ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
