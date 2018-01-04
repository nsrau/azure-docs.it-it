---
title: Prevedere Twitter sentiment con incorporamenti di word mediante il processo di analisi scientifica dei dati di Team - Azure | Documenti Microsoft
description: Procedura necessaria per eseguire i progetti di data science
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
ms.openlocfilehash: fe1c87df40102a62e1e0c8873b25fa3df7d743bc
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="predict-twitter-sentiment-with-word-embeddings-using-the-team-data-science-process"></a>Prevedere Twitter sentiment con gli incorporamenti di word con il processo di analisi scientifica dei dati di Team

In questo articolo viene illustrato come collaborare in modo efficace quando si utilizza il **Word2Vec** word incorporando algoritmo e **algoritmo Sentiment specifico Word incorporamento (SSWE)** per stimare Twitter sentiment con il [Azure Machine Learning](../preview/index.yml). Per ulteriori informazioni sulle attività di stima di twitter polarità di valutazione, vedere [repository](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction). La chiave per agevolare la collaborazione efficace su progetti di data science è per standardizzare la struttura e la documentazione dei progetti con un ciclo di vita di analisi scientifica dei dati stabiliti. Il [Team Data Science processo (TDSP)](overview.md) fornisce una tale un strutturati [ciclo di vita](lifecycle.md). 

Creazione di progetti di analisi scientifica dei dati con il **modello TDSP** fornisce questo framework standardizzato per i progetti di Azure Machine Learning. In precedenza, il team TDSP ha rilasciato un [repository GitHub per la struttura del progetto TDSP e i modelli](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Ora di creazione di progetti di Azure Machine Learning che viene creata un'istanza con [modelli TDSP di struttura e la documentazione per Azure Machine Learning](https://github.com/amlsamples/tdsp) è stata abilitata. Per istruzioni su come utilizzare la struttura TDSP e modelli in Azure Machine Learning, vedere [struttura progetti con il modello di processo di analisi scientifica dei dati di Team](../preview/how-to-use-tdsp-in-azure-ml.md). 


## <a name="the-sentiment-polarity-sample"></a>L'esempio polarità sentiment

Un esempio che illustra come creare un'istanza ed eseguire un machine learning usando la struttura del processo di analisi scientifica dei dati del Team di progetto e modelli in Banco di Azure Machine Learning lavoro è stato fornito in questo [procedura dettagliata](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). L'attività di modellazione consiste nello stimare polarità sentiment (positivo o negativo) utilizzando il testo da TWEET. In questo articolo descrive le attività descritte nella procedura dettagliata di modellazione dei dati. La procedura dettagliata vengono illustrate le attività seguenti:

- Esplorazione, training e distribuzione dei dati di un modello di apprendimento automatico che risolve il problema di stima descritto nella panoramica del caso d'uso. A tale scopo, [dati Twitter Sentiment](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) viene utilizzato.
- Esecuzione del progetto in Azure Machine Learning usando il modello di Team Data Science Process (TDSP) per questo progetto. Per l'esecuzione del progetto e creazione di report, viene utilizzato il ciclo di vita TDSP.
- Operazionalizzazione della soluzione direttamente da Azure Machine Learning in servizi contenitore di Azure.

Il progetto evidenzia diverse funzionalità di Azure Machine Learning, tali la creazione di istanze di TDSP struttura e utilizzo, l'esecuzione di codice in Banco di lavoro di Azure Machine Learning e agevolato in servizi di Azure contenitore tramite Docker e Kubernetes.

## <a name="team-data-science-process"></a>Processo di analisi scientifica dei dati per i team
Utilizzare i modelli TDSP struttura e la documentazione di progetto per eseguire questo esempio. Ne consegue il [ciclo di vita TDSP]((https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)). Il progetto viene creato in base alle istruzioni fornite [qui](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).

![Ciclo di vita del processo TDSP](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

![Creare un'istanza di TDSP](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Acquisizione e comprensione dei dati](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Il primo passaggio in questo esempio è per scaricare il set di dati sentiment140 e di suddividerlo in set di training e set di dati di testing. Il set di dati sentiment140 contiene il contenuto effettivo del tweet (con emoticon rimossa) con la polarità di ogni il tweet (negativo = 0, positivo = 4), con TWEET neutro rimosso. Quando diviso, i dati di training risultanti 1.3 milioni di righe, che i dati di testing ha 320 righe k.


## <a name="modelinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Modellazione](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Questa parte dell'esempio viene ulteriormente divisa in tre parti:
 
- **Funzionalità di progettazione** corrispondente per la generazione delle funzionalità di word diversi algoritmi di incorporamento. 
- **Creazione del modello** riguarda il training di modelli diversi come _regressione logistica_ e _boosting dei gradienti_ per stimare una valutazione del testo di input. 
- **Valutazione del modello** si applica il modello con training su dati di test.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Progettazione di funzionalità](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Word2Vec e SSWE sono la parola di incorporamento di algoritmi usati per generare incorporamenti di word. 


#### <a name="word2vec"></a>Word2Vec

Nella modalità Skipgram viene utilizzato l'algoritmo di Word2Vec. In questo modo, la generazione di word incorporamenti è descritto nel documento da Tomas Mikolov et al.: [Distributed rappresentazioni di parole e frasi e i relativi composizionalità. Progressi nelle informazioni neurale sistemi di elaborazione. 2013.](https://arxiv.org/abs/1310.4546).

Ignora-gramma è una rete neurale superficiale. L'input è codificata come un un vettore di frequente, è utilizzato per stimare nelle vicinanze parole la parola di destinazione. Se **V** è la dimensione del vocabolario, quindi la dimensione del livello di output sarebbe __C * V__ dove C è la dimensione della finestra di contesto. L'architettura di base skip-gramma è illustrata nella figura seguente:

![Modello Skip-gramma](./media/predict-twitter-sentiment/skip-gram-model.PNG)

***Modello Skip-gramma***

Il meccanismo dettagliato del modello di algoritmo e skip-gramma word2vec non rientrano nell'ambito di questo esempio. I lettori sono interessati più dettagli sul relativo funzionamento è possono consultare i riferimenti seguenti:

- [02_A_Word2Vec.py il codice a cui fa riferimento TensorFlow esempi](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)
- [Rappresentazione di vettore di parole](https://www.tensorflow.org/tutorials/word2vec)
- [Esattamente come funziona word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Note sulla stima Contrastive rumore e campionamento negativo](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)


#### <a name="sswe"></a>SSWE
Il **Sentiment specifico Word incorporamento (SSWE) algoritmo** tenta di risolvere un punto debole dell'algoritmo Word2vec che le parole con contesti simili e opposto polarità possono avere i vettori di word simili. Questa somiglianza significa che Word2vec potrebbero non funzionare in modo accurato per l'attività, ad esempio sentiment analysis. L'algoritmo SSWE tenta di gestire questa vulnerabilità incorporando sia la polarità della frase e il contesto di word nella propria funzione di perdita.

In questo esempio Usa una variante di SSWE. Il SSWE utilizza sia il ngram originale e danneggiato ngram come input e utilizza uno stile di rango fare la funzione di perdita per la perdita di sintassi e la perdita di semantica. Funzione di perdita Ultimate è la combinazione del perdita sintattica e semantica perdita media ponderata. Ai fini della semplicità, solo la semantica tra entropia viene usata come funzione di perdita. Come viene visualizzato in un secondo momento, anche con questa funzione di perdita più semplice le prestazioni dell'incorporamento SSWE sono migliore l'incorporamento Word2Vec.

Il modello di rete neurale ispirata SSWE utilizzati in questo esempio è illustrato nella figura riportata di seguito:

![Confronto tra i modelli ROC](./media/predict-twitter-sentiment/embedding-model-2.PNG)

***Convolutional modello per generare l'incorporamento sentiment specifiche.***


Al termine il processo di training, vengono generati due file di incorporamento in formato TSV per la fase di modellazione.

Per ulteriori informazioni sugli algoritmi SSWE, vedere il documento da Duyu Tang et al.: [Learning Sentiment specifiche Word incorporamento per Twitter Sentiment Classification. ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146) 


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Creazione del modello](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Dopo avere generati i vettori di word utilizzando uno degli algoritmi SSWE o Word2vec, il passaggio successivo consiste nella Training dei modelli di classificazione per stimare la polarità sentiment effettivo. Due tipi di funzionalità, Word2Vec e SSWE, sono viene applicati a due modelli, il modello GBM e modello di regressione logistica. Pertanto quattro modelli differenti viene eseguito il training.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Valutazione del modello](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Ora è utilizzare quattro modelli di training nel passaggio precedente in dati di testing per valutare le prestazioni del modello. 

1. Sfumatura Boosting su SSWE incorporamento
2. Regressione logistica su SSWE incorporamento
3. Sfumatura Boosting su Word2Vec incorporamento
4. Regressione logistica su Word2Vec incorporamento

![Confronto tra i modelli ROC](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

Il modello GBM con funzionalità SSWE è quello più adatto tramite la metrica AUC.


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Distribuzione](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Questa parte consente di distribuire il modello di stima con training sentiment (incorporamento SSWE + modello GBM) a un servizio web in un cluster in servizio il contenitore di Azure (ACS). L'ambiente di operazionalizzazione effettua il provisioning di Docker e Kubernetes nel cluster per gestire la distribuzione del servizio Web. Altre informazioni sul processo di operazionalizzazione sono disponibili [qui](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

![kubenetes_dashboard](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)


## <a name="conclusion"></a>Conclusioni

Sono stati illustrati i dettagli su come eseguire il training di un modello di incorporamento di word utilizzando gli algoritmi Word2Vec e SSWE e gli incorporamenti estratti sono stati utilizzati come funzionalità per eseguire il training più modelli per stimare i punteggi di valutazione per i dati di testo Twitter. La funzionalità di valutazione specifico formulazione Embeddings(SSWE) con il modello dell'albero con Boosting dei gradienti ha restituito prestazioni ottimali. Questo modello è stato quindi distribuito come servizio web in tempo reale in servizi di contenitore di Azure utilizzando banco di lavoro di Azure Machine Learning.


## <a name="references"></a>Riferimenti

* [Processo di analisi scientifica dei dati di team](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [How to use Team Data Science Process (TDSP) in Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml) (Come usare Team Data Science Process, TDSP, in Azure Machine Learning)
* [TDSP project template for Azure Machine Learning](https://aka.ms/tdspamlgithubrepo) (Modello di progetto di TDSP per Azure Machine Learning)
* [Banco di lavoro di Azure ML](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [Set di dati sul reddito degli Stati Uniti del repository UCI per l'apprendimento automatico](https://archive.ics.uci.edu/ml/datasets/adult)
* [Utilizzando il modello TDSP biomedica riconoscimento delle entità](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov, Tomas, et al. Distributed representations of words and phrases and their compositionality. Progressi nelle informazioni neurale sistemi di elaborazione. 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu, et al. "Apprendimento incorporamento Sentiment specifiche per la classificazione di Twitter Sentiment". ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)