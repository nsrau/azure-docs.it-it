---
title: Classificazione del sentiment di Twitter con il pacchetto di Azure Machine Learning (AML) per l'analisi del testo (AMLPTA) e il processo di data science per i team (TDSP) | Microsoft Docs
description: Viene descritto l'uso del processo di data science per i team (TDSP) e del pacchetto di Azure Machine Learning (AML) per l'analisi del testo (AMLPTA) per la classificazione del sentiment
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: acad053712d237fdb86934faa7c5c4546c630387
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394562"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Classificazione del sentiment di Twitter con il pacchetto di Azure Machine Learning (AML) per l'analisi del testo (AMLPTA) e il processo di data science per i team (TDSP)

## <a name="introduction"></a>Introduzione
La standardizzazione della struttura e della documentazione dei progetti di data science, che è associata a un [ciclo di vita di data science](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) prestabilito, è fondamentale per favorire una collaborazione efficace nei team di data science.

In passato è stato reso disponibile un [repository GitHub per la struttura e i modelli del progetto di TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Oggi è possibile creare progetti di Azure Machine Learning di cui creare istanze con la [struttura e i modelli di documentazione di TDSP per Azure Machine Learning](https://github.com/amlsamples/tdsp). Le istruzioni su come usare la struttura e i modelli di TDSP in Azure Machine Learning sono disponibili [qui](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

In questo esempio viene illustrato l'uso del pacchetto di Azure Machine Learning per l'analisi del testo e del processo di data science per i team per sviluppare e distribuire modelli predittivi per la classificazione del sentiment di Twitter.


## <a name="use-case"></a>Caso d'uso
### <a name="twitter-sentiment-polarity-sample"></a>Esempio di polarità del sentiment in Twitter
Questo articolo usa un esempio per mostrare come creare un'istanza di un progetto di Machine Learning ed eseguire il progetto. L'esempio usa la struttura e i modelli di TDSP in Azure Machine Learning Workbench. L'esempio completo è disponibile in questa procedura dettagliata. L'attività di modellazione prevede la polarità del sentiment (positivo o negativo) usando il testo dei Tweet. Questo articolo presenta le attività di modellazione dei dati descritte nella procedura dettagliata. La procedura dettagliata include le attività seguenti:

1. Esplorazione, training e distribuzione dei dati di un modello di apprendimento automatico che risolve il problema di previsione descritto nella panoramica del caso d'uso. Per queste attività vengono usati i dati del sentiment di Twitter.
2. Esecuzione del progetto tramite il modello di TDSP da Azure Machine Learning per questo progetto. Per l'esecuzione del progetto e la creazione di report, si userà il ciclo di vita di TDSP.
3. Operazionalizzazione della soluzione direttamente da Azure Machine Learning nel servizio contenitore di Azure.

Il progetto evidenzia l'uso del pacchetto di Azure Machine Learning per l'analisi del testo.


## <a name="link-to-github-repository"></a>Collegamento al repository GitHub
Il collegamento al repository GitHub è disponibile [qui](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Scopo
Lo scopo principale di questo esempio è mostrare come creare un'istanza di un progetto di apprendimento automatico ed eseguire il progetto usando la struttura e i modelli del processo di data science per i team (TDSP) in Azure Machine Learning Workbench. A tale scopo, vengono usati i [dati del sentiment di Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). L'attività di modellazione consiste nel prevedere la polarità del sentiment (positivo o negativo) usando il testo dei tweet.

### <a name="scope"></a>Scope
- Esplorazione, training e distribuzione dei dati di un modello di apprendimento automatico che risolve il problema di stima descritto nella panoramica del caso d'uso.
- Esecuzione del progetto in Azure Machine Learning usando il modello di Team Data Science Process (TDSP) per questo progetto. Per l'esecuzione del progetto e la creazione del report, si userà il ciclo di vita di TDSP.
- Operazionalizzazione della soluzione direttamente da Azure Machine Learning in servizi contenitore di Azure.

Il progetto evidenzia diverse funzionalità di Azure Machine Learning, tra cui la creazione di istanze e l'uso della struttura del processo di data science per i team (TDSP), l'esecuzione di codice in Azure Machine Learning Workbench e la semplice operazionalizzazione in servizi contenitore di Azure con Docker e Kubernetes.

## <a name="team-data-science-process-tds"></a>Processo di data science per i team (TDSP)
Per eseguire questo esempio, vengono usati la struttura di progetto e i modelli di documentazione del processo di data science per i team (TDSP). Viene seguito il [ciclo di vita del processo di data science per i team](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle). Il progetto viene creato in base alle istruzioni fornite [qui](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Panoramica del caso d'uso
L'attività consiste nel prevedere la polarità binaria del sentiment di ogni tweet usando funzionalità di rappresentazione distribuita delle parole estratte dal testo di Twitter. Per informazioni dettagliate, vedere questo [repository](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Acquisizione e comprensione dei dati](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Il primo passaggio dell'esempio consiste nello scaricare il set di dati sentiment140 e dividerlo in set di dati di training e di test. Il set di dati sentiment140 include il contenuto effettivo del tweet (con le emoticon rimosse) insieme alla polarità di ogni tweet (negativo = 0, positivo = 4), con i tweet neutri rimossi. I dati di training risultanti contengono 1,3 milioni di righe e i dati di test 320.000 righe.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modellazione](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Questa parte dell'esempio viene ulteriormente divisa in tre parti secondarie: 
- **Progettazione delle funzionalità**: corrisponde alla generazione delle funzionalità usando un algoritmo di rappresentazione distribuita delle parole diverso, ovvero Word2Vec. 
- **Creazione del modello**: riguarda il training di diversi modelli, come quelli di _regressione logistica_ e _aumento priorità a gradienti_ per prevedere il sentiment del testo di input. 
- **Valutazione del modello**: si applica al modello sottoposto a training con i dati di test.

#### <a name="feature-engineering"></a>Progettazione delle funzioni
Viene usato <b>Word2Vec</b> per generare rappresentazioni distribuite delle parole. Prima di tutto si usa l'algoritmo Word2Vec in modalità Skip-gram, come illustrato nel documento[Mikolov, Tomas, et al. Distributed representations of words and phrases and their compositionality. Advances in neural information processing systems. 2013.](https://arxiv.org/abs/1310.4546) per generare rappresentazioni distribuite delle parole.

Skip-gram è una rete neurale superficiale che accetta come input la parola di destinazione codificata come vettore one-hot e la usa per prevedere le parole vicine. Se V è la dimensione del vocabolario, la dimensione del livello di output è __C*V__, dove C è la dimensione della finestra di contesto. L'architettura basata su Skip-gram è illustrata nella figura seguente.
 
<table class="image" align="center">
<caption align="bottom">Modello Skip-gram</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

I dettagli dell'algoritmo Word2Vec e del modello Skip-gram esulano dall'ambito di questo esempio e i lettori interessati possono vedere i collegamenti seguenti per altri dettagli. [Esempi tensorflow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) di riferimento con codice 02_A_Word2Vec.py

* [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec) (Rappresentazioni vettoriali di parole)
* [How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf) (Come funziona esattamente Word2Vec?)
* [Notes on Noise Contrastive Estimation and Negative Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf) (Note sulla stima contrastiva del rumore e sul campionamento negativo)

Al termine del processo di training, vengono generati due file di rappresentazione distribuita in formato TSV per la fase di modellazione.

#### <a name="model-training"></a>Training dei modelli
Dopo la generazione dei vettori delle parole tramite l'algoritmo Word2vec o SSWE, il passaggio successivo consiste nell'eseguire il training dei modelli di classificazione per prevedere la polarità effettiva del sentiment. Vengono applicati i due tipi di funzionalità, Word2Vec e SSWE, in due modelli, ovvero regressione logistica e reti neurali convoluzionali (CNN). 

#### <a name="model-evaluation"></a>Valutazione del modello.
Viene fornito il codice per caricare e valutare più modelli sottoposti a training con un set di dati di test.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Distribuzione](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
In questa parte vengono forniti collegamenti alle istruzioni per l'operazionalizzazione di un modello di stima del sentiment prima del training in un servizio Web in un cluster nel servizio contenitore di Azure (AKS). L'ambiente di operazionalizzazione effettua il provisioning di Docker e Kubernetes nel cluster per gestire la distribuzione del servizio Web. Altre informazioni sul processo di operazionalizzazione sono disponibili [qui](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Conclusioni
Sono state presentate informazioni dettagliate su come eseguire il training di un modello di rappresentazione distribuita delle parole usando l'algoritmo Word2Vec e quindi usare le rappresentazioni distribuite estratte come caratteristiche per il training di due modelli diversi per prevedere il punteggio del sentiment dei dati di testo di Twitter. Uno di questi modelli viene distribuito nel servizio contenitore di Azure (AKS). 

## <a name="next-steps"></a>Passaggi successivi
Per iniziare, leggere altra documentazione sul [pacchetto di Azure Machine Learning per l'analisi del testo (AMLPTA)](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) e sul [processo di data science per i team (TDSP)](https://aka.ms/tdsp).

## <a name="references"></a>Riferimenti
* [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [How to use Team Data Science Process (TDSP) in Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml) (Come usare Team Data Science Process, TDSP, in Azure Machine Learning)
* [TDSP project template for Azure Machine Learning](https://aka.ms/tdspamlgithubrepo) (Modello di progetto di TDSP per Azure Machine Learning)
* [Azure ML Workbench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Mikolov, Tomas, et al. Distributed representations of words and phrases and their compositionality. Advances in neural information processing systems. 2013.](https://arxiv.org/abs/1310.4546)
