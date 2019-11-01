---
title: Panoramica dell'apprendimento automatico - Azure HDInsight
description: Panoramica delle opzioni di Big Data Machine Learning per i cluster in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: a770fca4f14e5441e5ef4f7bc99d9c0abd9a59d3
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241641"
---
# <a name="machine-learning-on-hdinsight"></a>Machine Learning in HDInsight

HDInsight permette di usare l'apprendimento automatico con Big Data, per ottenere informazioni approfondite di grande utilità da ingenti quantità (fino a diversi petabyte o addirittura exabyte) di dati strutturati, non strutturati e che cambiano rapidamente. Sono disponibili diverse opzioni di Machine Learning in HDInsight: SparkML e Apache Spark MLlib, R, Apache Hive e il Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>Spark e MLlib

[HDInsight Spark](spark/apache-spark-overview.md) è una soluzione ospitata in Azure di [Apache Spark](https://spark.apache.org/), un framework di elaborazione parallela dei dati unificato e open source che supporta l'elaborazione in memoria per ottimizzare l'analisi dei Big Data. Il motore di elaborazione Spark è costruito per la velocità, la semplicità d'uso e le analisi sofisticate. Le funzionalità di calcolo distribuite in memoria rendono Spark uno strumento valido per l'esecuzione di algoritmi iterativi utilizzati in calcoli grafici e di Machine Learning. Sono disponibili due librerie di apprendimento automatico scalabili che integrano funzionalità di modellazione algoritmica nell'ambiente distribuito: MLlib e SparkML. MLlib contiene l'API originale basata su RDD. SparkML è un pacchetto più recente che contiene un'API di livello superiore basata su Spark DataFrames per la costruzione di pipeline di apprendimento automatico. SparkML non supporta ancora tutte le funzionalità di MLlib, ma sta sostituendo MLlib come libreria di apprendimento automatico standard di Spark.

La libreria di apprendimento automatico Microsoft per Apache Spark è [MMLSpark](https://github.com/Azure/mmlspark). Questa libreria è stata progettata per incrementare la produttività dei data scientist in Spark, aumentare la velocità di sperimentazione e sfruttare le tecniche di apprendimento automatico più innovative, tra cui l'apprendimento avanzato, su set di dati di dimensioni molto grandi. MMLSpark offre un livello superiore rispetto alle API di basso livello di SparkML per la creazione di modelli di apprendimento automatico scalabili, tra cui l'indicizzazione di stringhe, l'applicazione forzata ai dati di un layout previsto dagli algoritmi di apprendimento automatico e l'assemblaggio di vettori di funzionalità. La libreria MMLSpark semplifica queste e altre attività comuni per la creazione di modelli in PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) è attualmente il linguaggio di programmazione per l'analisi statistica più diffuso al mondo. Si tratta di uno strumento open source di visualizzazione dei dati che vanta una community di oltre 2,5 milioni di utenti, ancora in aumento. Con la sua dinamica base di utenti e oltre 8.000 pacchetti creati dalla community, R è la scelta più probabile per molte società che hanno bisogno di funzionalità di apprendimento automatico. È possibile creare un cluster HDInsight con ML Services pronto per l'uso con set di dati e modelli di dimensioni molto grandi. Questa funzionalità offre a data scientist ed esperti di statistica un'interfaccia R familiare e scalabile on demand tramite HDInsight, senza dover eseguire attività aggiuntive di configurazione e manutenzione del cluster.

![Training per la stima con R Server](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

Il nodo perimetrale di un cluster offre una posizione pratica per connettersi al cluster ed eseguire gli script R.  È anche possibile scegliere di eseguire script R tra i nodi del cluster usando contesti di calcolo Hadoop MapReduce o Spark di ScaleR.

Con ML Services su HDInsight con Spark, è possibile parallelizzare il training tra i nodi di un cluster usando un contesto di calcolo Spark. È possibile eseguire script R direttamente nel nodo perimetrale usando tutti i core disponibili in parallelo, in base alle esigenze. In alternativa, è possibile eseguire il codice dal nodo perimetrale per avviare l'elaborazione distribuita tra tutti i nodi nel cluster. ML Services su HDInsight con Spark permette anche di usare funzioni di parallelizzazione da pacchetti R open source, se lo si desidera.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e Apache Hive

Azure Machine Learning offre strumenti per modellare l'analisi predittiva, insieme a un servizio completamente gestito che può essere usato per distribuire i modelli predittivi come servizi Web pronti per l'uso. Azure Machine Learning è una soluzione di analisi predittiva completa nel cloud che è possibile usare per creare, testare, rendere operativi e gestire i modelli predittivi. È possibile selezionare gli algoritmi da una libreria di grandi dimensioni, usare un ambiente basato sul Web per creare modelli e distribuire con facilità il modello come servizio Web.

![Panoramica di Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Creare funzionalità per i dati in un cluster Hadoop di HDInsight usando [query Hive](../machine-learning/team-data-science-process/create-features-hive.md). La *progettazione di funzionalità* tenta di aumentare la capacità predittiva degli algoritmi di apprendimento tramite la creazione di funzionalità da dati non elaborati per semplificare il processo di apprendimento. È possibile eseguire query HiveQL da Azure Machine Learning Studio (classico) e accedere ai dati elaborati in hive e archiviati nell'archiviazione BLOB usando il [modulo Import Data](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

L'[apprendimento avanzato](https://www.microsoft.com/en-us/research/group/dltc/) è un ambito dell'apprendimento automatico che usa reti neurali, ispirate ai processi biologici del cervello umano. Molti ricercatori considerano l'approccio adottato per l'apprendimento avanzato molto promettente per l'intelligenza artificiale. Alcuni esempi di apprendimento avanzato sono i sistemi di traduzione del linguaggio parlato, i sistemi di riconoscimento delle immagini e i sistemi di ragionamento automatico.

A sostegno del proprio impegno nel campo dell'apprendimento avanzato, Microsoft ha sviluppato [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), uno strumento open source gratuito e facile da usare. Il toolkit è ampiamente usato da numerosi prodotti Microsoft, da aziende di tutto il mondo che hanno la necessità di distribuire l'apprendimento avanzato su vasta scala e da studenti interessati agli algoritmi e alle tecniche più recenti.

## <a name="see-also"></a>Vedi anche

### <a name="scenarios"></a>Scenari

* [Apache Spark with Machine Learning: Use Spark in HDInsight for analyzing building temperature using HVAC data](spark/apache-spark-ipython-notebook-machine-learning.md) (Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio usando dati HVAC)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generare raccomandazioni di film con Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive e Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive e Azure Machine Learning end-to-end](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Apprendimento automatico con Apache Spark in HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Risorse sull'apprendimento avanzato

* [Usare Microsoft Cognitive Toolkit modello di apprendimento avanzato con Azure HDInsight Spark cluster](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Usare Caffe in Azure HDInsight Spark per l'apprendimento avanzato distribuito](spark/apache-spark-deep-learning-caffe.md)
* [Apprendimento avanzato e Framework di intelligenza artificiale nella Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-deep-learning-ai-frameworks.md)
