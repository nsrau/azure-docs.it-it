---
title: Informazioni su HDInsight, lo stack di tecnologie Hadoop e Spark - Azure | Microsoft Docs
description: Introduzione a HDInsight, lo stack di tecnologie Hadoop e Spark con i relativi componenti, tra cui Kafka, Hive, Storm e HBase, per l'analisi dei Big Data.
keywords: azure hadoop, hadoop azure, introduzione hadoop, intro hadoop, stack di tecnologie hadoop, intro a hadoop, introduzione a hadoop, informazioni sul cluster hadoop, info sul cluster hadoop, uso di hadoop
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/13/2017
ms.author: cgronlun
ms.openlocfilehash: ba2a14e3f327fd8ab4f1ebfe329a64b4aa1a25a1
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="introduction-to-azure-hdinsight-the-hadoop-and-spark-technology-stack"></a>Introduzione ad Azure HDInsight, lo stack di tecnologie Hadoop e Spark
Questo articolo è un'introduzione ad Azure HDInsight, un servizio di analisi open source completamente gestito e ad ampio spettro per le aziende. È possibile usare framework open source, ad esempio Hadoop, Spark, Hive, LLAP, Kafka, Storm, R e altri. 

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

[Apache Hadoop](http://hadoop.apache.org/) era il framework open source originale per l'elaborazione distribuita e l'analisi di set di Big Data in cluster. Lo stack di tecnologie Hadoop include software e utilità correlate, tra cui Apache Hive, HBase, Spark, Kafka e molti altri. Per i componenti dello stack di tecnologie Hadoop disponibili in HDInsight, vedere [Componenti e versioni disponibili con HDInsight][component-versioning]. Per altre informazioni su Hadoop in HDInsight, vedere la pagina delle [funzionalità di Azure per HDInsight](https://azure.microsoft.com/services/hdinsight/).

[Apache Spark](http://spark.apache.org) è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data. Per altre informazioni su Spark in HDInsight,vedere [Introduzione a Spark in HDInsight](../spark/apache-spark-overview.md). 

<a href="https://ms.portal.azure.com/#create/Microsoft.HDInsightCluster" target="_blank"><img src="./media/apache-hadoop-introduction/deploy-to-azure.png" alt="Deploy an Azure HDInsight cluster"></a>

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Informazioni su Azure HDInsight e sullo stack di tecnologie Hadoop 
Azure HDInsight è una distribuzione cloud dei componenti Hadoop di [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/).  Azure HDInsight rende semplice, rapida ed economicamente conveniente l'elaborazione di grandi quantità di dati. È possibile usare i framework open source più diffusi, ad esempio Hadoop, Spark, Hive, LLAP, Kafka, Storm, R e altri, per abilitare un'ampia gamma di scenari, ad esempio estrazione, trasformazione e caricamento (ETL), data warehousing, apprendimento automatico e IoT.

## <a name="what-is-big-data"></a>Informazioni sui Big Data

I Big Data vengono raccolti in volumi sempre più elevati, a velocità crescenti e in una gamma crescente di formati. I Big Data possono essere cronologici (dati archiviati) oppure in tempo reale (trasmessi dall'origine). Per altre informazioni sui caso d'uso più comuni per i Big Data, vedere [Scenari per l'uso di HDInsight](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-hdinsight"></a>Perché usare HDInsight

Questa sezione elenca le funzionalità di Azure HDInsight.


|Funzionalità  |Descrizione  |
|---------|---------|
|Cloud nativo     |     Azure HDInsight consente di creare cluster ottimizzati per [Hadoop](apache-hadoop-linux-tutorial-get-started.md), [Spark](../spark/apache-spark-jupyter-spark-sql.md), [Interactive Query (LLAP)](../interactive-query/apache-interactive-query-get-started.md), [Kafka](../kafka/apache-kafka-get-started.md), [Storm](../storm/apache-storm-tutorial-get-started-linux.md), [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md) e  [R Server](../r-server/r-server-get-started.md) in Azure. HDInsight fornisce anche un contratto di servizio end-to-end per tutti i carichi di lavoro di produzione.  |
|Costi contenuti e scalabilità     | HDInsight consente di [ridimensionare](../hdinsight-administer-use-portal-linux.md) i carichi di lavoro aumentando o riducendo le prestazioni. È possibile ridurre il costo  [creando cluster su richiesta](../hdinsight-hadoop-create-linux-clusters-adf.md) e pagando solo per le risorse usate. È anche possibile compilare pipeline di dati per rendere operativi i processi. Calcolo e archiviazione disaccoppiati offrono migliori prestazioni e flessibilità. |
|Sicurezza e conformità    | HDInsight consente di proteggere i dati aziendali usando [Rete virtuale di Azure](../hdinsight-extend-hadoop-virtual-network.md), la [crittografia](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) e l'integrazione con [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md). HDInsight soddisfa anche i più diffusi [standard di conformità](https://azure.microsoft.com/overview/trusted-cloud) del settore e governativi.        |
|Monitoraggio    | Azure HDInsight si integra con [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) per fornire una singola interfaccia per il monitoraggio di tutti i cluster.        |
|Disponibilità globale | HDInsight è disponibile in più  [aree](https://azure.microsoft.com/regions/services/)  di qualsiasi altra offerta di analisi di Big Data. Azure HDInsight è anche disponibile in Azure per enti pubblici, Cina e Germania per soddisfare le esigenze aziendali nelle principali aree sovrane. |  
|Produttività     |  Azure HDInsight consente di usare strumenti produttivi avanzati per Hadoop e Spark con l'ambiente di sviluppo preferito, ad esempio [Visual Studio](apache-hadoop-visual-studio-tools-get-started.md), [Eclipse](../spark/apache-spark-eclipse-tool-plugin.md) e [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md) per supportare Scala, Python, R, Java e .NET. I data scientist possono anche collaborare usando notebook diffusi, ad esempio [Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) e [Zeppelin](../spark/apache-spark-zeppelin-notebook.md).    |
|Estensibilità     |  È possibile estendere i cluster HDInsight tramite l'installazione di componenti (Hue, Presto e così via) con [azioni script](../hdinsight-hadoop-customize-cluster-linux.md), l'[aggiunta di nodi perimetrali](../hdinsight-apps-use-edge-node.md) o l'[integrazione con altre applicazioni Big Data certificate](../hdinsight-apps-install-applications.md). HDInsight consente un'integrazione senza problemi con le più diffuse soluzioni Big Data tramite una distribuzione [con un clic](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Scenari per l'uso di HDInsight

Azure HDInsight è utilizzabile per un'ampia gamma di casi d'uso per l'elaborazione di Big Data. I Big Data vengono raccolti in volumi sempre più elevati, a velocità crescenti e in una gamma crescente di formati. Possono essere dati cronologici (dati già raccolti e archiviati) o dati in tempo reale (dati trasmessi direttamente dall'origine). I casi d'uso per l'elaborazione di tali dati possono essere riepilogati nelle categorie seguenti: 

### <a name="batch-processing-etl"></a>Elaborazione batch (ETL)

L'estrazione, trasformazione e caricamento (ETL) è un processo in cui dati strutturati o non strutturati vengono estratti da origini dati eterogenei, trasformati in un formato strutturato e caricati in un'origine dati. I dati trasformati possono essere usati per la data science o il data warehousing.

### <a name="internet-of-things-iot"></a>Internet delle cose

È possibile usare HDInsight per elaborare dati di streaming ricevuti in tempo reale da svariati dispositivi. Per altre informazioni, vedere [questo blog](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![Architettura di HDInsight: Internet delle cose](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png) 

### <a name="data-science"></a>Data science

HDInsight consente di compilare applicazioni che estraggono informazioni critiche dai dati. È anche possibile usare Azure Machine Learning per prevedere le tendenze future per l'azienda. Per altre informazioni, [vedere la storia di questo cliente](https://customers.microsoft.com/story/pros).

![Architettura di HDInsight: data science](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png)

### <a name="data-warehousing"></a>Data warehousing

È possibile usare HDInsight per eseguire query interattive nell'ordine di grandezza di petabyte su dati strutturati o non strutturati in qualsiasi formato. È anche possibile compilare modelli che le connettono a strumenti di business intelligence. Per altre informazioni, [vedere la storia di questo cliente](https://customers.microsoft.com/story/milliman). 

![Architettura di HDInsight: data warehouse](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png)

### <a name="hybrid"></a>Ibrido

È possibile usare HDInsight per estendere l'infrastruttura per Big Data locale esistente ad Azure per sfruttare le funzionalità di analisi avanzata del cloud.

![Architettura di HDInsight: ibrida](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png)

## <a name="overview"></a>Tipi di cluster in HDInsight
HDInsight include tipi di cluster specifici e funzionalità di personalizzazione dei cluster, ad esempio l'aggiunta di componenti, utilità e linguaggi.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Tipi di cluster Spark, Kafka, Interactive Query, HBase, personalizzati e di altro tipo
HDInsight offre i seguenti tipi di cluster:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: usa [HDFS](#hdfs), la gestione risorse [YARN](#yarn) e un semplice modello di programmazione [MapReduce](#mapreduce) per elaborare e analizzare i dati batch in parallelo.
* **[Apache Spark](http://spark.apache.org/)**: è un framework di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data; è compatibile con SQL, dati di streaming e Machine Learning. Vedere [Informazioni su Apache Spark in HDInsight](../spark/apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: un database NoSQL basato su Hadoop che fornisce accesso casuale e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati. Può gestire potenzialmente milioni di righe e colonne. Vedere [Informazioni su HBase in HDInsight](../hbase/apache-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: un server che ospita e gestisce processi R paralleli e distribuiti. Offre ai data scientist, agli statistici e ai programmatori R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight. Vedere [Panoramica di R Server su HDInsight](../r-server/r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)**: un sistema di calcolo distribuito e in tempo reale per l'elaborazione rapida di grandi flussi di dati. Storm viene offerto come cluster gestito in HDInsight. Vedere [Analizzare i dati del sensore in tempo reale con Storm e Hadoop](../storm/apache-storm-sensor-data-analysis.md).
* **[Anteprima di Apache Interactive Query (lunga durata e funzionamento)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: la memorizzazione nella cache integrata per query Hive interattive e più veloci. Vedere [Usare Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md).
* **[Apache Kafka](https://kafka.apache.org/)**: una piattaforma open source usata per creare applicazioni e pipeline di dati di streaming. Kafka fornisce inoltre funzionalità di code di messaggi che consentono di pubblicare e sottoscrivere i flussi di dati. Vedere [Introduction to Apache Kafka on HDInsight](../kafka/apache-kafka-introduction.md) (Introduzione ad Apache Kafka in HDInsight).

## <a name="open-source-components-in-hdinsight"></a>Componenti open source in HDInsight

Azure HDInsight consente di creare cluster con framework open source, ad esempio Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase e R. Questi cluster, per impostazione predefinita, vengono forniti con altri componenti open source inclusi nel cluster, ad esempio [Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](http://avro.apache.org/docs/current/spec.html), [Hive](http://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Mahout](https://mahout.apache.org/), [MapReduce](http://wiki.apache.org/hadoop/MapReduce), [YARN](http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Phoenix](http://phoenix.apache.org/), [Pig](http://pig.apache.org/), [Sqoop](http://sqoop.apache.org/), [Tez](http://tez.apache.org/), [Oozie](http://oozie.apache.org/), [ZooKeeper](http://zookeeper.apache.org/).  


## <a name="programming-languages-on-hdinsight"></a>Linguaggi di programmazione in HDInsight
I cluster di HDInsight, ovvero cluster Spark, HBase, Kafka, Hadoop e altri, supportano molti linguaggi di programmazione, ma alcuni di questi non sono installati per impostazione predefinita. Per le librerie, i moduli o i pacchetti non installati per impostazione predefinita, [usare un'azione script per installare il componente](../hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Supporto per i linguaggi di programmazione predefiniti
Per impostazione predefinita, i cluster HDInsight supportano:

* Java
* Python

È possibile installare linguaggi aggiuntivi usando [azioni script](../hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Linguaggi per macchine virtuali Java
È possibile eseguire molti linguaggi diversi da Java in Java Virtual Machine. L'esecuzione di alcuni di questi linguaggi può tuttavia richiedere l'installazione di componenti aggiuntivi nel cluster.

I linguaggi basati su JVM sono supportati nei cluster HDInsight:

* Clojure
* Jython (Python per Java)
* Scala

### <a name="hadoop-specific-languages"></a>Linguaggi specifici di Hadoop
I cluster HDInsight supportano i linguaggi seguenti, specifici dello stack di tecnologie Hadoop:

* Pig Latin per processi Pig
* HiveQL per processi Hive e SparkSQL

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence in HDInsight
Gli strumenti di business intelligence (BI) noti consentono di recuperare, analizzare e creare report di dati integrati con HDInsight usando il componente aggiuntivo Power Query o Microsoft Hive ODBC Driver:

* [Apache Spark BI usando gli strumenti di visualizzazione di dati con Azure HDInsight](../spark/apache-spark-use-bi-tools.md)
* [Visualizzare i dati Hive con Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md) 
* [Connettere Excel a Hadoop con Power Query](apache-hadoop-connect-excel-power-query.md): informazioni sulle procedure di connessione di Excel all'account di archiviazione di Azure in cui vengono archiviati i dati del cluster HDInsight tramite Microsoft Power Query per Excel. Workstation Windows necessaria. 
* [Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md): informazioni su come importare i dati da HDInsight con Microsoft Hive ODBC Driver. Workstation Windows necessaria. 
* [Microsoft Cloud Platform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): informazioni su Power BI per Office 365, download della versione di valutazione di SQL Server e configurazione di SharePoint Server 2013 e SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Passaggi successivi

* [Introduzione a Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Introduzione a Spark in HDInsight](../spark/apache-spark-jupyter-spark-sql.md)
* [Get started with Kafka on HDInsight](../kafka/apache-kafka-get-started.md) (Introduzione a Kafka in HDInsight)
* [Introduzione a Storm in HDInsight](../storm/apache-storm-tutorial-get-started-linux.md)
* [Introduzione a HBase in HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
* [Introduzione a Interactive Query (LLAP) in HDInsight](../interactive-query/apache-interactive-query-get-started.md)
* [Introduzione a R Server in HDInsight](../r-server/r-server-get-started.md)
* [Gestire cluster HDInsight](../hdinsight-administer-use-portal-linux.md)
* [Proteggere i cluster HDInsight](../domain-joined/apache-domain-joined-introduction.md)
* [Monitorare cluster HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md)


[component-versioning]: ../hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/