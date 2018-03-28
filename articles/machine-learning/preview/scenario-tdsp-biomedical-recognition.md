---
title: Riconoscimento di entità biomediche - Team Data Science Process - Azure Machine Learning | Microsoft Docs
description: Guida introduttiva per un progetto di Team Data Science Process che usa l'apprendimento avanzato per il riconoscimento di entità biomediche in Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 4e8450cc20718185a3cea02bf8fbb6b97dd91ddb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Riconoscimento di entità biomediche con un modello di Team Data Science Process (TDSP)

L'estrazione di entità è una sottoattività dell'estrazione di informazioni (nota anche come [riconoscimento di entità denominate (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), suddivisione di entità in blocchi e identificazione di entità). L'obiettivo di questo scenario reale è mostrare come usare Azure Machine Learning Workbench per risolvere una complessa attività di elaborazione del linguaggio naturale, ad esempio l'estrazione di entità da testo non strutturato:

1. Come eseguire il training di un modello neurale di rappresentazione distribuita delle parole in un insieme di testi di circa 18 milioni di riassunti di PubMed con un'[implementazione di Spark Word2Vec](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Come creare un modello di rete neurale ricorrente LSTM (Long Short-Term Memory) per l'estrazione di entità in una macchina virtuale (VM) di data science di Azure abilitata per la GPU in Azure.
2. Viene dimostrato che il modello di rappresentazione distribuita delle parole specifico di dominio può avere prestazioni migliori rispetto ai modelli di rappresentazione distribuita delle parole generici nell'attività di riconoscimento delle entità. 
3. Viene descritto come eseguire il training e l'operazionalizzazione di modelli di apprendimento avanzato con Azure Machine Learning Workbench.

4. Vengono presentate le funzionalità seguenti di Azure Machine Learning Workbench:

    * Creazione di istanze di [struttura e modelli di Team Data Science Process (TDSP)](how-to-use-tdsp-in-azure-ml.md)
    * Gestione automatica delle dipendenze di progetto inclusi download e installazione
    * Esecuzione di script Python in ambienti di calcolo diversi
    * Monitoraggio della cronologia di esecuzione per script Python
    * Esecuzione di processi in un contesto di calcolo Spark remoto con cluster HDInsight Spark 2.1
    * Esecuzione di processi in macchine virtuali remote abilitate per la GPU in Azure
    * Facile operazionalizzazione dei modelli di apprendimento avanzato come servizi Web nei servizi contenitore di Azure (ACS)

## <a name="use-case-overview"></a>Panoramica del caso d'uso
Il riconoscimento di entità biomediche denominate è un passaggio cruciale per complesse attività di elaborazione del linguaggio naturale dei dati biomedici, tra cui: 
* Estrazione delle menzioni di entità denominate, quali malattie, farmaci, prodotti chimici e sintomi, da record elettronici di argomento medico o sanitario.
* Individuazione di farmaci
* Comprensione delle interazioni tra tipi di entità diversi, come l'interazione tra farmaci, la relazione tra farmaco e malattia e la relazione tra gene e proteina.

Questo scenario reale è incentrato su come analizzare un insieme di dati non strutturati di grandi dimensioni, ad esempio un serie di riassunti di Medline PubMed per il training di un modello di rappresentazione distribuita delle parole. Le rappresentazioni distribuite di output vengono quindi considerate caratteristiche generate automaticamente per il training di un estrattore di entità neurale.

I risultati mostrano che il training del modello di estrazione di entità biomediche nelle caratteristiche di rappresentazione distribuita delle parole specifiche di dominio ha prestazioni migliori rispetto al training del modello in base al tipo di caratteristiche generico. Il modello specifico di dominio è in grado di rilevare correttamente 7012 entità (su 9475) con punteggio F1 pari a 0,73 rispetto alle 5274 entità con punteggio F1 di 0,61 per il modello generico.

La figura seguente mostra l'architettura usata per elaborare i dati ed eseguire il training dei modelli.

![Architecture](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Descrizione dei dati

### <a name="1-word2vec-model-training-data"></a>1. Dati di training del modello Word2Vec
Prima di tutto sono stati scaricati i dati non elaborati dei riassunti di MEDLINE da [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). I dati sono disponibili pubblicamente sotto forma di file XML nel relativo [server FTP](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Nel server sono disponibili 892 file XLM, ognuno dei quali contiene le informazioni di 30.000 articoli. Altri dettagli riguardo al passaggio di raccolta dati vengono forniti nella sezione Struttura del progetto. I campi presenti in ogni file sono 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. Dati di training del modello LSTM

Il modello di estrazione di entità neurale è stato sottoposto a training e valutato in base a set di dati disponibili pubblicamente. Per ottenere una descrizione dettagliata di questi set di dati, è possibile fare riferimento alle origini seguenti:
 * [Attività di riconoscimento di entità in BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Insieme di attività BioCreative V CDR](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - Task 9.1 (riconoscimento di farmaci)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte di Azure
Di seguito viene fornito il collegamento al repository GitHub pubblico dello scenario reale, che contiene il codice e una descrizione più dettagliata: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>prerequisiti 

* Una [sottoscrizione](https://azure.microsoft.com/free/) di Azure.
* Azure Machine Learning Workbench. Vedere la [guida all'installazione](quickstart-installation.md). Attualmente Azure Machine Learning Workbench può essere installato solo nei sistemi operativi seguenti: 
    * Windows 10 o Windows Server 2016
    * macOS Sierra (o versioni successive)


### <a name="azure-services"></a>Servizi di Azure
* [Cluster HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) versione Spark 2.1 su Linux (HDI 3.6) per il calcolo della scalabilità orizzontale. Per elaborare la quantità totale di riassunti di MEDLINE indicata di seguito, è necessaria la configurazione minima degli elementi seguenti: 
    * Nodo head: dimensioni [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/)
    * Nodi di lavoro: almeno 4 di dimensioni [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). In questo esempio, sono stati usati 11 nodi di lavoro di dimensioni D12_V2.
* [Macchina virtuale di data science NC6](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) per il calcolo della scalabilità verticale.

### <a name="python-packages"></a>Pacchetti Python

Tutte le dipendenze necessarie sono definite nel file aml_config/conda_dependencies.yml nella cartella di progetto dello scenario. Viene effettuato il provisioning automatico delle dipendenze definite in questo file per l'esecuzione su destinazioni Docker, macchina virtuale e cluster HDI. Per informazioni dettagliate sul formato di file dell'ambiente Conda, vedere [qui](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Istruzioni di base per Azure Machine Learning Workbench
* [Overview](overview-what-is-azure-ml.md)
* [Installazione](quickstart-installation.md)
* [Uso di TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Come leggere e scrivere file](how-to-read-write-files.md)
* [Come usare i notebook di Jupyter](how-to-use-jupyter-notebooks.md)
* [Come usare la GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Struttura dello scenario
Per lo scenario, vengono usati la struttura del progetto e i modelli di documentazione di TDSP (figura 1), che seguono il [ciclo di vita di TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Il progetto viene creato in base alle istruzioni fornite [qui](./how-to-use-tdsp-in-azure-ml.md).


![Immettere le informazioni sul progetto](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

Il flusso di lavoro di data science dettagliato è il seguente:

### <a name="1-data-acquisition-and-understanding"></a>1. Acquisizione e comprensione dei dati

Vedere [Data Acquisition and Understanding](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) (Acquisizione e comprensione dei dati).

L'insieme MEDLINE non elaborato include un totale di 27 milioni di riassunti, in cui per circa 10 milioni di articoli il campo relativo al riassunto è vuoto. Viene usato Azure HDInsight Spark per elaborare Big Data che non possono essere caricati nella memoria di un singolo computer come [frame di dati Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Prima di tutto, i dati vengono scaricati nel cluster Spark. Vengono quindi eseguiti i passaggi seguenti nel [frame di dati Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* Vengono analizzati i file XML usando il parser XML di Medline
* Viene eseguita l'analisi preliminare del testo dei riassunti, includendo divisione delle frasi, tokenizzazione e normalizzazione delle maiuscole/minuscole.
* Vengono esclusi gli articoli il cui campo relativo al riassunto è vuoto o contiene testo breve. 
* Viene creato il vocabolario di parole dai riassunti di training.
* Viene eseguito il training del modello neurale di rappresentazione distribuita delle parole. Per altre informazioni, fare riferimento al [collegamento al codice di GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) per iniziare.


Dopo l'analisi dei file XML, i dati hanno il formato seguente: 

![Dati di esempio](./media/scenario-tdsp-biomedical-recognition/datasample.png)

Il modello di estrazione di entità neurale è stato sottoposto a training e valutato in base a set di dati disponibili pubblicamente. Per ottenere una descrizione dettagliata di questi set di dati, è possibile fare riferimento alle origini seguenti:
 * [Attività di riconoscimento di entità in BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Insieme di attività BioCreative V CDR](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - Task 9.1 (riconoscimento di farmaci)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modellazione

Vedere [Modeling](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling) (Modellazione).

La modellazione è la fase in cui viene mostrato come usare i dati scaricati nella sezione precedente per il training di un modello di rappresentazione distribuita delle parole personalizzato, da usare per altre attività a valle. Anche se si usano i dati di PubMed, la pipeline per generare le rappresentazioni distribuite è generica e può essere riutilizzata per eseguire il training delle rappresentazioni distribuite delle parole per qualsiasi altro dominio. Per ottenere rappresentazioni distribuite accurate dei dati, è essenziale che il training di Word2Vec venga eseguito su una quantità elevata di dati.
Quando le rappresentazioni distribuite delle parole sono pronte, è possibile eseguire il training di un modello di rete neurale che usa le rappresentazioni distribuite delle parole acquisite per inizializzare il livello di rappresentazione distribuita. Il livello di rappresentazione distribuita viene contrassegnato come non abilitato per il training, ma questa operazione non è obbligatoria. Poiché il training del modello di rappresentazione distribuita delle parole non è supervisionato, è possibile trarre vantaggio dai testi senza etichetta. Tuttavia, il training del modello di riconoscimento di entità è un'attività di apprendimento supervisionata, la cui precisione dipende dalla quantità e dalla qualità dei dati con annotazioni manuali. 


#### <a name="21-feature-generation"></a>2.1. Creazione di caratteristiche

Vedere [Feature generation](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering) (Creazione di caratteristiche).

Word2Vec è l'algoritmo di apprendimento automatico della rappresentazione distribuita delle parole che addestra un modello di rete neurale da un insieme di training senza etichetta. L'algoritmo genera un vettore continuo per ogni parola nell'insieme, che ne rappresenta le informazioni semantiche. Questi modelli sono reti neurali semplici con un solo livello nascosto. L'apprendimento dei vettori/rappresentazioni distribuite delle parole avviene tramite la propagazione e la discesa del gradiente stocastico. Esistono due tipi di modelli Word2Vec, ovvero i modelli skip-gram e i modelli continuous-bag-of-words. Per altre informazioni, vedere [qui](https://arxiv.org/pdf/1301.3781.pdf). Poiché si sta usando l'implementazione MLlib di Word2Vec, che supporta il modello skip-gram, questo modello viene descritto brevemente qui (immagine ottenuta da questo [collegamento](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Modello skip-gram](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

Il modello usa Hierarchical Softmax e il campionamento negativo per ottimizzare le prestazioni. Hierarchical SoftMax (H-SoftMax) è un'approssimazione ispirata agli alberi binari. H-SoftMax sostituisce essenzialmente il livello SoftMax flat con un livello gerarchico, in cui le parole sono in forma di foglie. In questo modo, è possibile scomporre il calcolo della probabilità di una parola in una sequenza di calcoli di probabilità, in modo da evitare di calcolare la dispendiosa normalizzazione su tutte le parole. Poiché un albero binario bilanciato ha una profondità uguale a log2(|V|) (V è il vocabolario), al massimo è necessario valutare solo i nodi log2(|V|) per ottenere la probabilità finale di una parola. Di conseguenza, la probabilità di una parola w dato il suo contesto c è semplicemente il prodotto delle probabilità di seguire rispettivamente i percorsi verso destra e verso sinistra al relativo nodo foglia. È possibile creare un albero di Huffman basato sulla frequenza delle parole nel set di dati per fare in modo che le parole più frequenti ottengano rappresentazioni più brevi. Per altre informazioni, vedere [questo collegamento](http://sebastianruder.com/word-embeddings-softmax/).
Immagine ottenuta da [questo sito](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Visualizzazione

Dopo aver ottenuto le rappresentazioni distribuite delle parole, è utile visualizzarle per osservare la relazione tra parole semanticamente simili. 

![Similarità in W2V](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Sono stati mostrati due metodi diversi per visualizzare le rappresentazioni distribuite. Il primo usa un algoritmo di analisi in componenti principali (PCA) per proiettare il vettore multidimensionale in uno spazio vettoriale 2D. Questo comporta una perdita significativa di informazioni e una visualizzazione poco accurata. Il secondo metodo consiste nell'usare un algoritmo PCA con [t-SNE](https://distill.pub/2016/misread-tsne/). t-SNE è una tecnica di riduzione della dimensionalità non lineare ideale per la rappresentazione distribuita di dati multidimensionali in uno spazio di due o tre dimensioni, che può quindi essere visualizzato in un grafico a dispersione.  Questo metodo modella ogni oggetto multidimensionale tramite un punto bidimensionale o tridimensionale in modo da modellare gli oggetti simili tramite punti vicini e gli oggetti dissimili tramite punti distanti. Il processo avviene in due parti. Prima di tutto, viene creata una distribuzione di probabilità sulle coppie nello spazio con più dimensioni, in modo che gli oggetti simili abbiano una maggiore probabilità di essere scelti e che gli oggetti dissimili vengano scelti in base a una probabilità minore. Viene quindi definita una distribuzione di probabilità simile sui punti in una mappa a dimensioni minori, riducendo al minimo la divergenza KL tra le due distribuzioni rispetto alla posizione dei punti sulla mappa. La posizione dei punti nello spazio a dimensioni minori viene ottenuta riducendo al minimo la divergenza KL tramite la discesa del gradiente. t-SNE non è tuttavia sempre affidabile. Per informazioni dettagliate, vedere [qui](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


Come mostrato nella figura seguente, la visualizzazione t-SNE offre una separazione maggiore tra i vettori di parole e i possibili modelli di clustering. 


* Visualizzazione con l'algoritmo PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Visualizzazione con t-SNE

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Punti più vicini alla parola "Cancer" (sono tutti sottotipi di Cancer)

![Punti più vicini a Cancer](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Training dell'estrattore di entità neurale

Vedere [Train the neural entity extractor](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md) (Eseguire il training dell'estrattore di entità neurale).

L'architettura di rete neurale di inoltro del feed è soggetta a un problema per cui ogni input o output viene considerato indipendente dagli altri input e output. Questa architettura non è in grado di modellare attività di aggiunta di etichette sequenza per sequenza come la traduzione automatica e l'estrazione di entità. I modelli di rete neurale ricorrente permettono di risolvere questo problema, perché possono passare al nodo successivo le informazioni calcolate fino al momento attuale. Questa caratteristica è definita come capacità di avere memoria nella rete, in quanto è in grado di usare le informazioni precedentemente calcolate, come mostrato nella figura seguente:

![Rete neurale ricorrente](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Le reti neurali ricorrenti di Vanilla sono soggette al [problema di scomparsa del gradiente](https://en.wikipedia.org/wiki/Vanishing_gradient_problem), a causa del quale non sono in grado di utilizzare tutte le informazioni visualizzate in precedenza. Il problema diventa evidente solo quando è necessaria una quantità elevata di contesto per eseguire una stima. Tuttavia, i modelli come LSTM non sono soggetti a questo problema perché in realtà sono progettati per memorizzare dipendenze a lungo termine. A differenza delle reti neurali ricorrenti di Vanilla, costituite da una singola rete neurale, i modelli LSTM includono le interazioni tra quattro reti neurali per ogni cella. Per una spiegazione dettagliata del funzionamento di LSTM, fare riferimento a [questo post](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![Cella LSTM](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Si proverà ora a organizzare la rete neurale ricorrente basata su LSTM e a estrarre tipi di entità, ad esempio le citazioni di farmaci, malattie e sintomi dai dati PubMed. Il primo passaggio consiste nell'ottenere una quantità elevata di dati con etichetta, che non è un'operazione facile. La maggior parte dei dati medici contiene molte informazioni riservate sul paziente, che di conseguenza non sono disponibili pubblicamente. A questo scopo, si usa una combinazione di set di dati diversi disponibili pubblicamente. Il primo set di dati è tratto da Semeval 2013 - Task 9.1 (riconoscimento di farmaci) e l'altro dall'attività BioCreative V CDR. Questi due set di dati verranno combinati e contrassegnati con etichette in modo da poter rilevare i farmaci e le malattie dai testi medici, per valutare le rappresentazioni distribuite delle parole. Per informazioni dettagliate sull'implementazione, fare riferimento al [collegamento al codice di GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

Di seguito viene mostrata l'architettura del modello usata in tutto il codice per il confronto. Il parametro che cambia in base ai diversi set di dati è la lunghezza massima della sequenza (qui 613).

![Modello LSTM](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Valutazione del modello.
Vedere [Valutazione del modello](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Viene usato lo script di valutazione dall'[attività condivisa di riconoscimento delle entità biomediche in NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) per valutare la precisione, il richiamo e il punteggio F1 del modello. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>Confronto tra modelli di rappresentazione distribuita delle parole generici e specifici di dominio

Di seguito viene presentato un confronto in termini di precisione tra due tipi di funzionalità: (1) rappresentazioni distribuite delle parole sottoposte a training su riassunti di PubMed e (2) rappresentazioni distribuite delle parole sottoposte a training su Google News. Come si può osservare, il modello specifico di dominio ha prestazioni migliori rispetto al modello generico. Di conseguenza, è molto più utile usare un modello di rappresentazione distribuita delle parole specifico invece di uno generico. 

* Attività 1: rilevamento di farmaci e malattie

![Confronto 1 tra modelli](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Viene eseguita la valutazione delle rappresentazioni distribuite delle parole su altri set di dati in modo simile, riscontrando che il modello specifico di dominio è sempre il migliore.

* Attività 2: rilevamento di proteine, serie cellulare, tipo di cellula, DNA e RNA

![Confronto 2 tra modelli](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Attività 3: rilevamento di sostanze chimiche e malattie

![Confronto 3 tra modelli](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Attività 4: rilevamento dei farmaci

![Confronto 4 tra modelli](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Attività 5: Rilevamento dei geni

![Confronto 5 tra modelli](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow e CNTK
Tutti i modelli presentati vengono sottoposti a training usando Keras con TensorFlow come back-end. Al momento della creazione di questo scenario, Keras con CNTK come back-end non supporta l'inversione. Di conseguenza, ai fini del confronto, è stato eseguito il training di un modello LSTM unidirezionale con CNTK come back-end, che è stato quindi confrontato con un modello LSTM unidirezionale con TensorFlow come back-end. Installare CNTK 2.0 per Keras da [qui](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Confronto 6 tra modelli](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Si è concluso che CNTK ha prestazioni altrettanto buone di TensorFlow in termini sia di durata del training per ogni periodo (60 secondi per CNTK e 75 secondi per Tensorflow) sia di numero di entità di test rilevate. Per la valutazione, vengono usati livelli unidirezionali.


### <a name="3-deployment"></a>3. Distribuzione

Vedere [Deployment](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment) (Distribuzione).

È stato distribuito un servizio Web in un cluster nel [servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/). L'ambiente di operazionalizzazione effettua il provisioning di Docker e Kubernetes nel cluster per gestire la distribuzione del servizio Web. Altre informazioni sul processo di operazionalizzazione sono disponibili [qui](model-management-service-deploy.md ).


## <a name="conclusion"></a>Conclusioni

Sono state presentate informazioni dettagliate su come eseguire il training di un modello di rappresentazione distribuita delle parole usando l'algoritmo Word2Vec in Spark e quindi usare le rappresentazioni distribuite estratte come caratteristiche per il training di una rete neurale avanzata per l'estrazione di entità. È stata applicata la pipeline di training nel dominio biomedico. Tuttavia, la pipeline è sufficientemente generica da poter essere applicata per rilevare tipi di entità personalizzati di qualsiasi altro dominio. Basta una quantità sufficiente di dati per poter adattare facilmente il flusso di lavoro presentato qui per un dominio diverso.

## <a name="references"></a>Riferimenti

* Tomas Mikolov, Kai Chen, Greg Corrado e Jeffrey Dean. 2013a. Efficient estimation of word representations in vector space. In Proceedings of ICLR.
* Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S Corrado e Jeff Dean. 2013b. Distributed representations of words and phrases and their compositionality. In Proceedings of NIPS, pagine 3111-3119.
* Billy Chiu, Gamal Crichton, Anna Korhonen e Sampo Pyysalo. 2016. [How to Train Good Word Embeddings for Biomedical NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), In Proceedings of the fifteenth Workshop on Biomedical Natural Language Processing, pagine 166-174.
* [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec) (Rappresentazioni vettoriali di parole)
* [Recurrent Neural Networks](https://www.tensorflow.org/tutorials/recurrent) (Reti neurali ricorrenti)
* [Problems encountered with Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/) (Problemi riscontrati con Spark ml Word2Vec)
* [Spark Word2Vec: lessons learned](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/) (Spark Word2Vec: lezioni apprese)

