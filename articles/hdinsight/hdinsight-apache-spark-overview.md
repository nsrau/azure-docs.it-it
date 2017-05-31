---
title: Introduzione a Spark in HDInsight | Microsoft Docs
description: "Questo articolo fornisce un&quot;introduzione a Spark in HDInsight e presenta i diversi scenari in cui è possibile usare il cluster Spark in HDInsight."
keywords: informazioni su apache spark,cluster spark,introduzione a spark,spark in hdinsight
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: 4dc9729f036c0c497dfd53342aa92c983126e7d1
ms.contentlocale: it-it
ms.lasthandoff: 05/20/2017


---
# <a name="introduction-to-spark-on-hdinsight"></a>Introduzione a Spark in HDInsight

Questo articolo fornisce un'introduzione a Spark in HDInsight. <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data. Il cluster Spark in HDInsight è compatibile con Archiviazione di Azure (WASB) e con Azure Data Lake Store. È quindi possibile elaborare con facilità tramite un cluster Spark i dati esistenti archiviati in Azure.

Quando si crea un cluster Spark in HDInsight, si creano risorse di calcolo di Azure con Spark installato e configurato. Bastano circa dieci minuti per creare un cluster di Spark in HDInsight. I dati da elaborare vengono archiviati in Archiviazione di Azure o Azure Data Lake Store. Vedere [Usare l'Archiviazione di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

**Per creare un cluster Spark in HDInsight**, vedere [Guida introduttiva: Creare un cluster Spark in HDInsight ed eseguire query interattive usando Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="what-is-apache-spark-on-azure-hdinsight"></a>Informazioni su Apache Spark in Azure HDInsight
I cluster Spark in HDInsight offrono un servizio Spark completamente gestito. I vantaggi della creazione di un cluster Spark in HDInsight sono elencati qui.

| Funzionalità | Descrizione |
| --- | --- |
| Facilità di creazione dei cluster Spark |È possibile creare un nuovo cluster Spark in HDInsight in pochi minuti mediante il portale di Azure, Azure PowerShell o HDInsight .NET SDK. Vedere [Introduzione ai cluster Spark in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Semplicità d'uso |Il cluster Spark in HDInsight include notebook di Jupyter e Zeppelin. È possibile usarli per la visualizzazione e l'elaborazione interattiva di dati.|
| API REST |I cluster Spark in HDInsight includono [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), un server dei processi Spark basato sull'API REST per l'invi e il monitoraggio remoto dei processi. |
| Supporto per Archivio Azure Data Lake | È possibile configurare un cluster Spark in HDInsight per l'uso di Azure Data Lake Store come spazio di archiviazione aggiuntivo o come risorsa di archiviazione primaria (solo con cluster HDInsight 3.5). Per altre informazioni su Archivio Data Lake, vedere [Panoramica di Archivio Azure Data Lake](../data-lake-store/data-lake-store-overview.md). |
| Integrazione con servizi di Azure |Il cluster Spark in HDInsight viene fornito con un connettore per Hub eventi di Azure. Gli utenti possono creare applicazioni di streaming mediante Hub eventi, oltre a [Kafka](http://kafka.apache.org/)che è già disponibile come parte di Spark. |
| Supporto per R Server | È possibile impostare R Server in un cluster HDInsight Spark per eseguire calcoli R distribuiti con la velocità garantita da un cluster Spark. Per altre informazioni, vedere [Introduzione all'uso di R Server in HDInsight](hdinsight-hadoop-r-server-get-started.md). |
| Integrazione con IDE di terze parti | HDInsight fornisce i plug-in per gli IDE, ad esempio IntelliJ IDEA ed Eclipse, che possono essere usati per creare e inviare applicazioni in un cluster Spark in HDInsight. Per altre informazioni, vedere [Usare Azure Toolkit per IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin.md) e [Usare Azure Toolkit per Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md).|
| Query simultanee |I cluster Spark in HDInsight supportano le query simultanee. In questo modo più query da un utente o più query da vari utenti e applicazioni possono condividere le stesse risorse di cluster. |
| La memorizzazione nella cache nelle unità SSD |È possibile scegliere di memorizzare i dati in memoria o nelle SSD associate ai nodi del cluster. La memorizzazione nella cache in memoria offre le migliori prestazioni di query ma può essere costosa; la memorizzazione nella cache in SSDs fornisce un'ottima opzione per migliorare le prestazioni delle query senza la necessità di creare un cluster di dimensioni necessario ad adattare l'intero set di dati in memoria. |
| Integrazione con strumenti di Business Intelligence |I cluster Spark in HDInsight offrono connettori per strumenti di Business Intelligence, come [Power BI](http://www.powerbi.com/) e [Tableau](http://www.tableau.com/products/desktop), per l'analisi dei dati. |
| Librerie Anaconda precaricate |I cluster Spark in HDInsight sono dotati di librerie Anaconda preinstallate [Anaconda](http://docs.continuum.io/anaconda/) offre quasi 200 librerie per Machine Learning, l'analisi dei dati, la visualizzazione e così via. |
| Scalabilità |Anche se è possibile specificare il numero di nodi del cluster durante la fase di creazione, in seguito può essere necessario aumentare o ridurre il cluster sulla base del carico di lavoro. Tutti i cluster HDInsight consentono di modificare il numero di nodi del cluster. È anche possibile eliminare i cluster Spark senza alcuna perdita di dati perché tutti i dati sono archiviati in Archiviazione di Azure o Data Lake Store. |
| Supporto 24/7 |I cluster Spark in HDInsight includono il supporto continuo a livello aziendale e un Contratto di servizio che garantisce tempi di attività pari al 99,9%. |

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Quali sono i casi d'uso per Spark in HDInsight?
I cluster Spark in HDInsight consentono gli scenari principali seguenti.

### <a name="interactive-data-analysis-and-bi"></a>Analisi dei dati interattivi e Business Intelligence
[Esaminare un'esercitazione](hdinsight-apache-spark-use-bi-tools.md)

Apache Spark in HDInsight archivia i dati nell'Archiviazione di Azure o in Azure Data Lake Store. Gli esperti aziendali e i responsabili decisionali possono analizzare e generare rapporti su dati e usare Microsoft Power BI per creare rapporti dai dati analizzati. Gli analisti possono iniziare a usare dati non strutturati/parzialmente strutturati in una risorsa di archiviazione cluster, definire uno schema per i dati usando notebook e quindi creare modelli di dati usando Microsoft Power BI. I cluster Spark in HDInsight supportano anche alcuni strumenti di BI di terze parti, ad esempio Tableau, e sono quindi una piattaforma ottimale per gli analisti di dati, gli esperti aziendali e i decision maker principali.

### <a name="spark-machine-learning"></a>Machine Learning in Spark
[Esaminare un'esercitazione: stima delle temperature di compilazione mediante i dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Esaminare un'esercitazione: stima dei risultati di ispezione del cibo](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark include [MLlib](http://spark.apache.org/mllib/), una libreria di Machine Learning basata su Spark, che è possibile usare da un cluster Spark in HDInsight. Il cluster Spark in HDInsight include inoltre Anaconda, una distribuzione di Python con un'ampia gamma di pacchetti per l'apprendimento automatico. Aggiungendo il supporto incorporato per notebook Jupyter e Zeppelin si otterrà un ambiente di qualità elevata per la creazione di applicazioni di Machine Learning.

### <a name="spark-streaming-and-real-time-data-analysis"></a>Analisi dei dati in tempo reale e streaming in Spark
[Esaminare un'esercitazione](hdinsight-apache-spark-eventhub-streaming.md)

I cluster Spark in HDInsight offrono un supporto completo per la creazione di soluzioni di analisi in tempo reale. Mentre Spark ha già connettori per acquisire i dati da molte origini, quali socket Kafka, Flume, Twitter, ZeroMQ o TCP, Spark in HDInsight aggiunge un eccellente supporto per l'inserimento di dati da Hub eventi di Azure. Hub eventi è il servizio di Accodamento messaggi maggiormente usato in Azure. La disponibilità di un supporto per Hub eventi rende i cluster Spark in HDInsight la piattaforma ideale per la compilazione della pipeline di analisi in tempo reale.

## <a name="next-steps"></a>Quali componenti sono inclusi come parte di un cluster di Spark?
I cluster Spark in HDInsight includono i componenti seguenti che sono disponibili nei cluster per impostazione predefinita.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Viene fornito con Spark Core, Spark SQL, streaming API Spark, GraphX e MLlib Spark.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Notebook Zeppelin](http://zeppelin-project.org/)

I cluster Spark in HDInsight forniscono inoltre un [driver ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) per la connettività ai cluster Spark in HDInsight da strumenti di Business Intelligence, quali Microsoft Power BI e Tableau.

## <a name="where-do-i-start"></a>Dove iniziare?
Iniziare con la creazione di un cluster Spark in HDInsight. Vedere [Guida introduttiva: creare un cluster di Spark in HDInsight ed eseguire query interattive usando Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Passaggi successivi
### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)
* [Problemi noti di Apache Spark in Azure HDInsight](hdinsight-apache-spark-known-issues.md).

