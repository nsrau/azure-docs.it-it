---
title: Informazioni su Apache Spark in Azure HDInsight | Microsoft Docs
description: Questo articolo fornisce un'introduzione a Spark in HDInsight e presenta i diversi scenari in cui è possibile usare il cluster Spark in HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: overview
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 7e01915a63e0e260dc85893bdf65cf7b86000cef
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Informazioni su Apache Spark in Azure HDInsight

*Apache Spark* è un framework di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni di analisi dei dati di Big Data. Apache Spark in Azure HDInsight è l'implementazione di Microsoft di Apache Hadoop nel cloud. HDInsight rende più semplice creare e configurare un cluster Spark in Azure. I cluster Spark in HDInsight sono compatibili con Archiviazione di Azure e con Azure Data Lake Store. Pertanto, è possibile usare i cluster Spark HDInsight per elaborare i dati archiviati in Azure. Per i componenti e per le informazioni sulle versioni, vedere [Componenti e versioni Hadoop in Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: un framework unificato](./media/apache-spark-overview/hdinsight-spark-overview.png)


## <a name="what-is-spark"></a>Che cos'è Spark?

Spark fornisce le primitive per il cluster computing in memoria. Un processo Spark può caricare i dati e memorizzarli nella cache in memoria ed eseguire query su di essi ripetutamente. L'elaborazione in memoria è molto più veloce rispetto alle applicazioni basate su disco, ad esempio Hadoop, che condivide dati tramite Hadoop Distributed File System. Spark si integra anche nel linguaggio di programmazione Scala per consentire di modificare set di dati distribuiti come raccolte locali. Non è necessario strutturare tutti gli elementi come operazioni di mapping e riduzione.

![Confronto tra il framework MapReduce tradizionale e Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

I cluster Spark in HDInsight offrono un servizio Spark completamente gestito. I vantaggi della creazione di un cluster Spark in HDInsight sono elencati qui.

| Funzionalità | DESCRIZIONE |
| --- | --- |
| Facilità di creazione |È possibile creare un nuovo cluster Spark in HDInsight in pochi minuti usando il portale di Azure, Azure PowerShell o HDInsight .NET SDK. Vedere [Introduzione ai cluster Spark in HDInsight](apache-spark-jupyter-spark-sql.md) |
| Semplicità d'uso |Il cluster Spark in HDInsight include notebook di Jupyter e Zeppelin. È possibile usare questi notebook per la visualizzazione e l'elaborazione interattiva di dati.|
| API REST |I cluster Spark in HDInsight includono [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), un server dei processi Spark basato sull'API REST per l'invi e il monitoraggio remoto dei processi. |
| Supporto per Archivio Azure Data Lake | I cluster Spark in HDInsight possono usare Azure Data Lake Store come risorsa di archiviazione primaria o come spazio di archiviazione aggiuntivo. Per altre informazioni su Archivio Data Lake, vedere [Panoramica di Archivio Azure Data Lake](../../data-lake-store/data-lake-store-overview.md). |
| Integrazione con servizi di Azure |Il cluster Spark in HDInsight viene fornito con un connettore per Hub eventi di Azure. È possibile creare applicazioni di streaming con Hub eventi, oltre a [Kafka](http://kafka.apache.org/)che è già disponibile come parte di Spark. |
| Supporto per R Server | È possibile impostare un R Server in un cluster HDInsight Spark per eseguire calcoli R distribuiti con la velocità garantita da un cluster Spark. Per altre informazioni, vedere [Introduzione all'uso di R Server in HDInsight](../r-server/r-server-get-started.md). |
| Integrazione con IDE di terze parti | HDInsight fornisce numerosi plugin IDE utili per creare e inviare applicazioni a un cluster HDInsight Spark. Per altre informazioni, vedere [Usare Azure Toolkit for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md) e [Usare Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Query simultanee |I cluster Spark in HDInsight supportano le query simultanee. Questa funzionalità consente a più query da un utente o da vari utenti e applicazioni di condividere le stesse risorse di cluster. |
| La memorizzazione nella cache nelle unità SSD |È possibile scegliere di memorizzare i dati in memoria o nelle SSD associate ai nodi del cluster. La memorizzazione nella cache in memoria offre le migliori prestazioni di query, ma può essere dispendiosa. La memorizzazione nella cache in unità SSD fornisce un'ottima opzione per migliorare le prestazioni delle query senza la necessità di creare un cluster con le dimensioni necessarie a contenere l'intero set di dati in memoria. |
| Integrazione con strumenti di Business Intelligence |I cluster Spark in HDInsight offrono connettori per strumenti di Business Intelligence, come [Power BI](http://www.powerbi.com/) per l'analisi dei dati. |
| Librerie Anaconda precaricate |I cluster Spark in HDInsight sono dotati di librerie Anaconda preinstallate. [Anaconda](http://docs.continuum.io/anaconda/) offre quasi 200 librerie per Machine Learning, l'analisi dei dati, la visualizzazione e così via. |
| Scalabilità | HDInsight consente di modificare il numero di nodi del cluster. È anche possibile eliminare i cluster Spark senza alcuna perdita di dati perché tutti i dati sono archiviati in Archiviazione di Azure o Data Lake Store. |
| Contratto di servizio |I cluster Spark in HDInsight includono il supporto continuo 24 ore su 24, 7 giorni su 7 e un Contratto di servizio che garantisce tempi di attività pari al 99,9%. |

I cluster Spark in HDInsight includono i componenti seguenti che sono disponibili nei cluster per impostazione predefinita.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Viene fornito con Spark Core, Spark SQL, streaming API Spark, GraphX e MLlib Spark.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Notebook Zeppelin](http://zeppelin-project.org/)

I cluster Spark in HDInsight forniscono inoltre un [driver ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) per la connettività ai cluster Spark in HDInsight da strumenti di Business Intelligence, quali Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Architettura del cluster Spark

![L'architettura HDInsight Spark](./media/apache-spark-overview/spark-architecture.png)

È facile comprendere i componenti di Spark se si comprende come Spark viene eseguito nei cluster HDInsight.

Le applicazioni Spark vengono eseguite in un cluster come un set di processi indipendenti, coordinati dall'oggetto SparkContext nel programma principale (chiamato il programma driver).

SparkContext può connettersi a diversi tipi di gestione cluster, che allocano risorse tra le applicazioni. Queste gestioni cluster includono Mesos Apache, YARN Apache o Gestione cluster Spark. Spark in HDInsight viene eseguito usando lo strumento di gestione cluster YARN. Una volta connesso, Spark acquisisce gli executor nei nodi di lavoro nel cluster, che sono i processi che eseguono calcoli e archiviano i dati per l'applicazione. Successivamente, invia il codice dell'applicazione (definito dai file JAR o Python passati a SparkContext) agli executor. Infine, SparkContext invia le attività agli executor per l'esecuzione.

SparkContext esegue la funzione principale dell'utente e le diverse operazioni parallele nei nodi di lavoro. In seguito, SparkContext raccoglie i risultati delle operazioni. I nodi di lavoro leggono e scrivono i dati da e in Hadoop Distributed File System (HDFS). I nodi di lavoro memorizzano nella cache i dati trasformati in memoria come oggetti RDD (Resilient Distributed Dataset).

SparkContext si connette al master Spark ed è responsabile della conversione di un'applicazione in un grafo orientato (DAG) di singole attività che vengono eseguite all'interno di un processo executor nei nodi di lavoro. Ogni applicazione ottiene i propri processi executor, che rimangono attivi per la durata dell'intera applicazione ed eseguono attività in più thread.

## <a name="spark-in-hdinsight-use-cases"></a>Casi d'uso di Spark in HDInsight

I cluster Spark in HDInsight consentono gli scenari principali seguenti:

- Analisi dei dati interattivi e Business Intelligence

    Apache Spark in HDInsight archivia i dati nell'Archiviazione di Azure o in Azure Data Lake Store. Gli esperti aziendali e i responsabili decisionali possono analizzare e generare rapporti su dati e usare Microsoft Power BI per creare rapporti dai dati analizzati. Gli analisti possono iniziare a usare dati non strutturati/parzialmente strutturati in una risorsa di archiviazione cluster, definire uno schema per i dati usando notebook e quindi creare modelli di dati usando Microsoft Power BI. I cluster Spark in HDInsight supportano anche alcuni strumenti di business intelligence di terze parti, come Tableau, semplificando il lavoro di analisti di dati, esperti aziendali e decision maker strategici.

    [Esercitazione: Visualizzare i dati di Spark usando Power BI](apache-spark-use-bi-tools.md)
- Machine Learning in Spark

    Apache Spark include [MLlib](http://spark.apache.org/mllib/), una libreria di Machine Learning basata su Spark, che è possibile usare da un cluster Spark in HDInsight. Il cluster Spark in HDInsight include inoltre Anaconda, una distribuzione di Python con un'ampia gamma di pacchetti per Machine Learning. Aggiungendo il supporto incorporato per notebook Jupyter e Zeppelin si otterrà un ambiente per la creazione di applicazioni di Machine Learning.

    [Esercitazione: Prevedere la temperatura dell'edificio con dati HVAC](apache-spark-ipython-notebook-machine-learning.md) [Esercitazione: Prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)    
- Analisi dei dati in tempo reale e streaming in Spark

    I cluster Spark in HDInsight offrono un supporto completo per la creazione di soluzioni di analisi in tempo reale. Mentre Spark ha già connettori per acquisire i dati da molte origini, quali socket Kafka, Flume, Twitter, ZeroMQ o TCP, Spark in HDInsight aggiunge un eccellente supporto per l'inserimento di dati da Hub eventi di Azure. Hub eventi è il servizio di accodamento più usato in Azure. La disponibilità di un supporto per Hub eventi rende i cluster Spark in HDInsight la piattaforma ideale per la compilazione della pipeline di analisi in tempo reale.
    
## <a name="where-do-i-start"></a>Dove iniziare?

Per altre informazioni su Spark in HDInsight, è possibile usare gli articoli seguenti:

- [Guida introduttiva: Creare un cluster di Spark in HDInsight ed eseguire query interattive usando Jupyter](./apache-spark-jupyter-spark-sql.md)
- [Esercitazione: Eseguire un processo Spark usando Jupyter](./apache-spark-load-data-run-query.md)
- [Esercitazione: Analizzare i dati usando gli strumenti di business intelligence](./apache-spark-use-bi-tools.md)
- [Esercitazione: Machine Learning usando Spark](./apache-spark-ipython-notebook-machine-learning.md)
- [Esercitazione: Creare un'applicazione di Scala Maven usando IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Passaggi successivi

In questa panoramica si ottiene una conoscenza di base di Apache Spark in Azure HDInsight. Passare all'articolo successivo per informazioni su come creare un cluster HDInsight Spark ed eseguire alcune query SQL Spark:

- [Creare un cluster Spark in HDInsight](./apache-spark-jupyter-spark-sql.md)