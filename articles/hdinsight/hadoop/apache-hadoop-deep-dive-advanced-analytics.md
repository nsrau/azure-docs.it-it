---
title: Approfondimento - Analisi avanzata - Azure HDInsight | Microsoft Docs
description: Informazioni sull'uso degli algoritmi da parte dell'analisi avanzata per elaborare i Big Data.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 61a00c902be5cd3e37dabba09c15f9226e5e88b4
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="deep-dive---advanced-analytics"></a>Approfondimento - Analisi avanzata

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Cos'è l'analisi avanzata per HDInsight?

HDInsight offre la possibilità di ottenere informazioni dettagliate importanti da grandi quantità di dati strutturati, non strutturati e in rapido movimento. L'analisi avanzata prevede l'uso di architetture altamente scalabili, modelli di apprendimento statistici e di Machine Learning e dashboard intelligenti per offrire informazioni dettagliate significative. Il Machine Learning, o *analisi predittiva*, si serve di algoritmi che identificano le relazioni tra i dati e apprendono da tali relazioni per generare previsioni e aiutare nei processi decisionali.

## <a name="advanced-analytics-process"></a>Processo di analisi avanzata

![Processo](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

Dopo aver identificato il problema aziendale e aver avviato la raccolta e l'elaborazione dei dati, è necessario creare un modello che rappresenti la domanda per cui generare una previsione. Il modello usa uno o più algoritmi di Machine Learning per creare il tipo di previsione più adatto alle esigenze aziendali specifiche.  La maggior parte dei dati deve essere usata per eseguire il training del modello, mentre i dati rimanenti vengono usati per testare o valutare il modello. 

Dopo aver creato, caricato, testato e valutato il modello, il passaggio successivo è costituito dalla distribuzione del modello per iniziare a rispondere alle domande. L'ultimo passaggio consiste nel monitorare le prestazioni del modello e procedere all'ottimizzazione in base alle esigenze. 

## <a name="common-types-of-algorithms"></a>Tipi di algoritmi comuni

Le soluzioni di analisi avanzata offrono un set di algoritmi di Machine Learning. Di seguito è riportato un riepilogo delle categorie di algoritmi e dei casi d'uso aziendali comuni associati.

![Casi d'uso di Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Insieme alla selezione degli algoritmi più adatti, è necessario considerare se devono essere forniti dati per il training. Gli algoritmi di Machine Learning sono suddivisi in categorie come indicato di seguito:

* Supervisionati: per poter ottenere risultati, è necessario eseguire il training dell'algoritmo su un set di dati etichettati
* Semi-supervisionati: l'algoritmo può essere integrato, tramite query interattive eseguite da un istruttore, con destinazioni aggiuntive che non erano disponibili nella fase iniziale del training
* Non supervisionati: l'algoritmo non richiede dati di training 
* Rinforzo: l'algoritmo usa agenti software per determinare il comportamento ideale in un contesto specifico (tecnica spesso usata in robotica)


| Categoria algoritmo| Uso | Tipo di apprendimento | Algoritmi |
| --- | --- | --- | -- |
| Classificazione | Classificare oggetti o persone in gruppi | Supervisionato | Alberi delle decisioni, regressione logistica, reti neurali |
| Clustering | Dividere un set di esempi in gruppi omogenei | Non supervisionato | Clustering K-Means |
| Rilevamento schemi | Identificare associazioni frequenti nei dati | Non supervisionato | Regole di associazione |
| Regressione | Prevedere risultati numerici | Supervisionato | Regressione lineare, reti neurali |
| Rinforzo | Determinare il comportamento ottimale dei robot | Rinforzo | Simulazioni Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Machine Learning in HDInsight

HDInsight offre diverse opzioni di Machine Learning per un flusso di lavoro di analisi avanzata:

* [Machine Learning e Spark](#machine-learning-and-spark)
* [R e R Server](#r-and-r-server)
* [Azure Machine Learning e Hive](#azure-machine-learning-and-hive)
* [Spark e apprendimento avanzato](#spark-and-deep-learning)

### <a name="machine-learning-and-spark"></a>Machine Learning e Spark

[HDInsight Spark](../spark/apache-spark-overview.md) è un'offerta ospitata in Azure di [Spark](http://spark.apache.org/), un framework di elaborazione parallela dei dati unificato e open source che usa l'elaborazione in memoria per ottimizzare l'analisi dei Big Data. Il motore di elaborazione Spark è costruito per la velocità, la semplicità d'uso e le analisi sofisticate. Le funzionalità di calcolo distribuite in memoria rendono Spark uno strumento valido per l'esecuzione di algoritmi iterativi utilizzati in calcoli grafici e di Machine Learning. 

Tre librerie di Machine Learning scalabili introducono funzionalità di modellazione algoritmica nell'ambiente distribuito:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html): MLlib contiene l'API originale basata su RDD di Spark.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html): SparkML è un pacchetto più recente che contiene un'API di livello superiore basata su Spark DataFrames per la costruzione di pipeline ML.
* [**MMLSpark**](https://github.com/Azure/mmlspark): la libreria di Microsoft Machine Learning per Apache Spark (MMLSpark) è stata progettata per incrementare la produttività dei data scientist in Spark, aumentare la velocità di sperimentazione e sfruttare le più innovative tecniche di Machine Learning, tra cui l'apprendimento avanzato, su set di dati molto ampi. La libreria MMLSpark semplifica le attività di modellazione comuni per la creazione di modelli in PySpark. 

### <a name="r-and-r-server"></a>R e R Server

Nell'ambito di HDInsight è possibile creare un cluster HDInsight con [R Server](../r-server/r-server-overview.md) pronto per essere usato con set di dati e modelli di grandi dimensioni. Questa nuova funzionalità offre a data scientist ed esperti di statistica un'interfaccia R familiare e scalabile su richiesta tramite HDInsight, senza dover eseguire attività aggiuntive di configurazione e manutenzione del cluster.

### <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning e Hive

[Azure Machine Learning Studio](https://studio.azureml.net/) offre strumenti per modellare l'analisi predittiva e un servizio completamente gestito che può essere usato per distribuire i modelli predittivi come servizi Web pronti all'uso. Grazie agli strumenti per la creazione di soluzioni di analisi predittiva complete nel cloud, Azure Machine Learning consente di creare, testare, usare e gestire modelli predittivi in modo rapido. È possibile selezionare gli algoritmi da una libreria di grandi dimensioni, usare un ambiente basato sul Web per creare modelli e distribuire con facilità il modello come servizio Web.

### <a name="spark-and-deep-learning"></a>Spark e apprendimento avanzato

L'[apprendimento avanzato](https://www.microsoft.com/research/group/dltc/) è un ambito del Machine Learning che usa *reti neurali profonde* (DNN) ispirate dai processi biologici del cervello umano. Molti ricercatori considerano l'approccio adottato per l'apprendimento avanzato come promettente per l'intelligenza artificiale. Alcuni esempi di apprendimento avanzato sono i sistemi di traduzione del parlato, i sistemi di riconoscimento delle immagini e i sistemi di ragionamento automatici. A sostegno del proprio impegno nel campo dell'apprendimento avanzato, Microsoft ha sviluppato [Microsoft Cognitive Toolkit](https://www.microsoft.com/cognitive-toolkit/), uno strumento open source gratuito e facile da usare. Il toolkit è ampiamente usato da numerosi prodotti Microsoft, da aziende di tutto il mondo che hanno la necessità di distribuire l'apprendimento avanzato su vasta scala e da studenti interessati agli algoritmi e alle tecniche più recenti. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenario - Riconoscere le immagini per identificare gli schemi di sviluppo urbano

Di seguito è riportato un esempio di pipeline di Machine Learning di analisi avanzata che usa HDInsight.

In questo scenario si vedrà come usare le reti DNN generate in un framework di apprendimento avanzato, Cognitive Toolkit (CNTK) di Microsoft, per riconoscere grandi raccolte di immagini archiviate in un account di Archiviazione BLOB di Azure usando PySpark in un cluster Spark HDInsight. Questo approccio viene applicato a un caso d'uso DNN comune, alla classificazione di immagini aeree e può essere usato per identificare modelli recenti di sviluppo urbano.  Si userà un modello di classificazione delle immagini su cui è stato eseguito un training preliminare. Il training preliminare è stato eseguito in base al [set di dati CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) e il modello è stato applicato a 10.000 immagini trattenute.

Questo scenario di analisi avanzata prevede tre principali attività:

1. Creare un cluster Hadoop di Azure HDInsight con una distribuzione di Apache Spark 2.1.0. 
2. Eseguire uno script personalizzato per installare Microsoft Cognitive Toolkit in un cluster Spark di Azure HDInsight. 
3. Caricare un notebook di Jupyter pre-compilato nel cluster Spark HDInsight per applicare un modello con training di apprendimento avanzato di Microsoft Cognitive Toolkit ai file di un account di Archiviazione BLOB di Azure tramite l'API Python Spark (PySpark). 

Questo esempio è basato sul set di immagini CIFAR-10 compilato e distribuito da Alex Krizhevsky, Vinod Nair e Geoffrey Hinton. Il set di dati 10 CIFAR contiene 60.000 immagini a colori 32x32 appartenenti a 10 classi che si escludono a vicenda:

![Immagini](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Per altri dettagli sul set di dati, vedere [Learning Multiple Layers of Features from Tiny Images](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf) (Apprendimento di più livelli di caratteristiche da immagini di piccole dimensioni) di Alex Krizhevsky.

Il set di dati è stato suddiviso in un set di training costituito da 50.000 immagini e un set di test costituito da 10.000 immagini. Il primo set è stato usato per il training di un modello di rete residua (ResNet) convoluzionale costituito da venti livelli tramite Microsoft Cognitive Toolkit seguendo [questa esercitazione](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) disponibile nel repository GitHub di Cognitive Toolkit. Le altre 10.000 immagini sono state usate per testare l'accuratezza del modello. A questo punto entra in gioco l'elaborazione distribuita. L'attività di pre-elaborazione e riconoscimento delle immagini è altamente parallelizzabile. Con il modello sottoposto a training salvato, si è usato:

* PySpark per distribuire le immagini e il modello sottoposto a training nei nodi di lavoro del cluster.
* Python per elaborare in via preliminare le immagini in ogni nodo del cluster Spark HDInsight.
* Cognitive Toolkit per caricare il modello e riconoscere le immagini pre-elaborate in ogni nodo.
* Notebook di Jupyter per eseguire lo script PySpark, aggregare i risultati e usare [Matplotlib](https://matplotlib.org/) per visualizzare le prestazioni del modello.

L'intero processo di pre-elaborazione/riconoscimento delle 10.000 immagini dura meno di un minuto in un cluster con quattro nodi di lavoro. Il modello prevede con accuratezza le etichette di circa 9.100 immagini (91%). Una matrice di confusione illustra gli errori di classificazione più comuni, ad esempio l'inversione di etichettatura di cani e gatti che si verifica più spesso rispetto ad altre coppie di etichette.

![Risultati](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Prova

Seguire [questa esercitazione](../spark/apache-spark-microsoft-cognitive-toolkit.md) per implementare questa soluzione end-to-end. Configurare un cluster Spark HDInsight, installare Cognitive Toolkit ed eseguire il notebook di Jupyter che riconosce 10.000 immagini CIFAR.

## <a name="next-steps"></a>Passaggi successivi

Hive e Azure Machine Learning

* [Hive and Azure Machine Learning end-to-end](../../machine-learning/team-data-science-process/hive-walkthrough.md) (Hive e Azure Machine Learning end-to-end)
* [Uso di un cluster Hadoop di Azure HDInsight su un set di dati da 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Spark e MLLib

* [Machine Learning con Spark in HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](../spark/apache-spark-machine-learning-mllib-ipython.md)

Apprendimento avanzato, Cognitive Toolkit e altri strumenti

* [Embarrassingly parallel image classification, using Cognitive Toolkit and TensorFlow on Azure HDInsight Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/) (Classificazione di immagini imbarazzantemente parallela tramite Cognitive Toolkit e TensorFlow in Spark di Azure HDInsight)
* [Macchina virtuale data science di Azure](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introducing H2O.ai on Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/) (Introduzione a H2O.ai in Azure HDInsight)
