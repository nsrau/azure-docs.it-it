---
title: Informazioni su HDInsight, sullo stack di tecnologie Hadoop e sui cluster - Azure | Microsoft Docs
description: Introduzione a HDInsight e allo stack di tecnologie Hadoop con i relativi componenti, tra cui Spark, Kafka, Hive e HBase, per l'analisi dei Big Data.
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
ms.date: 07/20/2017
ms.author: cgronlun
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e5ed09ddb1556e6c76813e71bcb31cf4f792b616
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Introduzione ad Azure HDInsight, allo stack di tecnologie Hadoop e ai cluster Hadoop
 Questo articolo offre un'introduzione ad Azure HDInsight, una distribuzione cloud dello stack di tecnologie Hadoop. Illustra anche il cluster Hadoop e quando viene usato. 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Informazioni su Azure HDInsight e sullo stack di tecnologie Hadoop 
Azure HDInsight è una distribuzione cloud dei componenti Hadoop di [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). [Apache Hadoop](http://hadoop.apache.org/) era il framework open source originale per l'elaborazione distribuita e l'analisi di set di Big Data in cluster di computer. 


Lo stack di tecnologie Hadoop include software e utilità correlate, tra cui Apache Hive, HBase, Spark, Kafka e molti altri. Per i componenti dello stack di tecnologie Hadoop disponibili in HDInsight, vedere [Componenti e versioni disponibili con HDInsight][component-versioning]. Per altre informazioni su Hadoop in HDInsight, vedere la pagina delle [funzionalità di Azure per HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>Definizione di cluster Hadoop e casi d'uso
*Hadoop* è anche un tipo di cluster con:

* YARN per la pianificazione dei processi e la gestione delle risorse
* MapReduce per l'elaborazione parallela
* Hadoop Distributed File System (HDFS)
  
I cluster Hadoop vengono spesso usati per l'elaborazione batch di dati archiviati. Altri tipi di cluster in HDInsight hanno funzionalità aggiuntive: Spark è particolarmente diffuso in virtù dell'elaborazione in memoria più rapida. Vedere [Tipi di cluster in HDInsight](#overview) per informazioni dettagliate.

## <a name="what-is-big-data"></a>Informazioni sui Big Data
I Big Data descrivono qualsiasi corpo di informazioni digitali di grandi dimensioni, ad esempio:

* Dati di sensori di macchine industriali
* Attività dei clienti raccolta da un sito Web
* Un newsfeed Twitter

I Big Data vengono raccolti in volumi sempre più elevati, a velocità crescenti e in una gamma crescente di formati. I Big Data possono essere cronologici (dati archiviati) oppure in tempo reale (trasmessi dall'origine). 

## <a name="overview"></a>Tipi di cluster in HDInsight
HDInsight include tipi di cluster specifici e funzionalità di personalizzazione dei cluster, ad esempio l'aggiunta di componenti, utilità e linguaggi.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Tipi di cluster Spark, Kafka, Interactive Query, HBase, personalizzati e di altro tipo
HDInsight offre i seguenti tipi di cluster:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: usa [HDFS](#hdfs), la gestione risorse [YARN](#yarn) e un semplice modello di programmazione [MapReduce](#mapreduce) per elaborare e analizzare i dati batch in parallelo.
* **[Apache Spark](http://spark.apache.org/)**: è un framework di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data; è compatibile con SQL, dati di streaming e Machine Learning. Vedere [Informazioni su Apache Spark in HDInsight](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: un database NoSQL basato su Hadoop che fornisce accesso casuale e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati. Può gestire potenzialmente milioni di righe e colonne. Vedere [Informazioni su HBase in HDInsight](hdinsight-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: un server che ospita e gestisce processi R paralleli e distribuiti. Offre ai data scientist, agli statistici e ai programmatori R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight. Vedere [Panoramica di R Server su HDInsight](hdinsight-hadoop-r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)**: un sistema di calcolo distribuito e in tempo reale per l'elaborazione rapida di grandi flussi di dati. Storm viene offerto come cluster gestito in HDInsight. Vedere [Analizzare i dati del sensore in tempo reale con Storm e Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Anteprima di Apache Interactive Query (lunga durata e funzionamento)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: la memorizzazione nella cache integrata per query Hive interattive e più veloci. Vedere [Usare Interactive Query in HDInsight](hdinsight-hadoop-use-interactive-hive.md).
* **[Apache Kafka](https://kafka.apache.org/)**: una piattaforma open source usata per creare applicazioni e pipeline di dati di streaming. Kafka fornisce inoltre funzionalità di code di messaggi che consentono di pubblicare e sottoscrivere i flussi di dati. Vedere [Introduction to Apache Kafka on HDInsight](hdinsight-apache-kafka-introduction.md) (Introduzione ad Apache Kafka in HDInsight).

I cluster possono anche essere configurati nei modi seguenti:
* **[Anteprima dei cluster aggiunti al dominio](hdinsight-domain-joined-introduction.md)**: un cluster aggiunto a un dominio di Active Directory per poterne controllare l'accesso e offrire la governance dei dati.
* **[Cluster personalizzati con le azioni script](hdinsight-hadoop-customize-cluster-linux.md)**: cluster con script eseguiti durante il provisioning e che possono installare componenti aggiuntivi.

### <a name="example-cluster-customization-scripts"></a>Script di personalizzazione cluster di esempio
Le azioni script sono script Bash in Linux che vengono eseguiti durante il provisioning del cluster e che possono essere usati per installare componenti aggiuntivi nel cluster. 

I seguenti gli script di esempio vengono forniti dal team di HDInsight:

* **[Hue](hdinsight-hadoop-hue-linux.md)**: un insieme di applicazioni Web che consente di interagire con un cluster. Solamente cluster Linux
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)**: elaborazione Graph per modellare le relazioni tra persone o elementi.
* **[Solr](hdinsight-hadoop-solr-install-linux.md)**: una piattaforma di ricerca su scala aziendale che consente di eseguire ricerche full-text sui dati.

Per informazioni su come sviluppare azioni script personalizzate, vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="components-and-utilities-on-hdinsight-clusters"></a>Componenti e utilità nei cluster HDInsight
Nei cluster HDInsight sono inclusi i componenti e le utilità seguenti:

* **[Ambari](#ambari)**: provisioning, gestione, monitoraggio e utilità dei cluster.
* **[Avro](#avro)** (la libreria Microsoft .NET per Avro): serializzazione dei dati per l'ambiente Microsoft .NET. 
* **[Hive e HCatalog](#hive)**: query simili a SQL e un livello di gestione di tabelle e archiviazione.
* **[Mahout](#mahout)**: per applicazioni Machine Learning scalabili.
* **[MapReduce](#mapreduce)**: framework legacy per l'elaborazione distribuita e la gestione delle risorse Hadoop. Vedere [YARN](#yarn).
* **[Oozie](#oozie)**: gestione dei flussi di lavoro.
* **[Phoenix](#phoenix)**: un livello di database relazionale su HBase.
* **[Pig](#pig)**: script semplice per le trasformazioni MapReduce.
* **[Sqoop](#sqoop)**: importazione ed esportazione dei dati.
* **[Tez](#tez)**: consente di eseguire in modo efficiente a livello di scalabilità processi a elevato utilizzo di dati.
* **[YARN](#yarn)**: gestione delle risorse nell'ambito della libreria principale di Hadoop.
* **[ZooKeeper](#zookeeper)**: coordinamento di processi in sistemi distribuiti.

> [!NOTE]
> Per informazioni sui componenti specifici e sulle versioni, vedere [Componenti e versioni Hadoop in HDInsight][component-versioning]
>
>

### <a name="ambari"></a>Ambari
Apache Ambari viene usato per il provisioning, la gestione e il monitoraggio di cluster Apache Hadoop. Comprende una raccolta di strumenti operatore intuitivi e un set affidabile di API che nascondono la complessità di Hadoop, semplificando le operazioni sui cluster. I cluster HDInsight in Linux forniscono sia l'interfaccia utente Web Ambari che l'API REST Ambari. Le viste di Ambari nei cluster HDInsight consentono funzionalità di plug-in dell'interfaccia utente.
Vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md) e le <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">informazioni di riferimento sull'API Apache Ambari</a>.

### <a name="avro"></a>Avro (Libreria Microsoft .NET per Avro)
La libreria Microsoft .NET per Avro implementa il formato compatto di interscambio dei dati binari Apache Avro per la serializzazione nell'ambiente Microsoft .NET. Definisce uno schema indipendente dal linguaggio in modo che i dati serializzati in un linguaggio possano essere letti in un altro. Informazioni dettagliate sul formato sono disponibili nelle <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">specifiche di Apache Avro</a>. Il formato dei file Avro supporta il modello di programmazione MapReduce: i file possono essere suddivisi, ossia è possibile cercare un qualsiasi punto del file e iniziare a leggere da un blocco specifico. Per informazioni sulle procedure, vedere [Serializzazione dei dati con la libreria Microsoft .NET per Avro](hdinsight-dotnet-avro-serialization.md). Il supporto di cluster basati su Linux è in programma.

### <a name="hdfs"></a>HDFS
Hadoop Distributed File System (HDFS) è un file system che, insieme a MapReduce e YARN, costituisce il nucleo della tecnologia Hadoop. Si tratta del file system standard per i cluster Hadoop in HDInsight. Vedere [Eseguire query sui dati da un archivio compatibile con HDFS](hdinsight-hadoop-use-blob-storage.md).

### <a name="hive"></a>Apache Hive e HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> è un software di data warehouse basato su Hadoop che consente di eseguire query e gestire grandi set di dati nella risorsa di archiviazione distribuita con un linguaggio simile a SQL chiamato HiveQL. Hive, analogamente a Pig, è un'astrazione basata su MapReduce e converte le query in una serie di processi MapReduce. Hive è più simile a un sistema di gestione di database relazionali che a Pig e viene usato con dati più strutturati. Pig è invece la scelta ottimale per dati non strutturati. Vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> è un livello di gestione tabella e archiviazione per Hadoop che fornisce agli utenti una visualizzazione relazionale dei dati. In HCatalog, è possibile leggere e scrivere file in qualsiasi formato adatto per un SerDe (serializzatore-deserializzatore) Hive.

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> è una libreria di algoritmi di Machine Learning eseguita in Hadoop. Usando i principi della statistica, le applicazioni di Machine Learning istruiscono i sistemi in modo che apprendano dai dati e usino i risultati precedenti per determinare i comportamenti futuri. Vedere [Generare consigli sui film usando Mahout in Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce è un software legacy per Hadoop per la scrittura di applicazioni per l'elaborazione batch di set di Big Data in parallelo. Un processo MapReduce suddivide i set di dati di grandi dimensioni e organizza i dati in coppie chiave-valore per l'elaborazione. I processi MapReduce vengono eseguiti in [YARN](#yarn). Vedere <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> nel wiki di Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop. È integrato con lo stack Hadoop e supporta i processi Hadoop per MapReduce, Pig, Hive e Sqoop. Può anche essere usato per pianificare processi specifici di un sistema, come i programmi Java o gli script della shell. Vedere [Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in HDInsight](hdinsight-use-oozie-linux-mac.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> è un livello di database relazionale su HBase. Phoenix include un driver JDBC che consente agli utenti di eseguire query e di gestire direttamente le tabelle SQL. Anziché utilizzare MapReduce, Phoenix converte query e altre istruzioni in chiamate API NoSQL native, consentendo in tal modo applicazioni più rapide in archivi NoSQL. Vedere [Usare Apache Phoenix e SQuirreL con i cluster HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> è una piattaforma di alto livello che consente di eseguire trasformazioni MapReduce complesse in set di dati di grandi dimensioni usando un linguaggio di scripting semplice chiamato Pig Latin. Pig converte gli script Pig Latin in modo che possano essere eseguiti in Hadoop. È possibile creare funzioni definite dall'utente (UDF) per estendere Pig Latin. Vedere [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> è uno strumento che consente di trasferire dati in blocco tra Hadoop e i database relazionali, ad esempio SQL o altri archivi dati strutturati, nel modo più efficace possibile. Vedere [Uso di Sqoop con HDInsight](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> è un framework di applicazione basato su Hadoop YARN che esegue grafici complessi, aciclici di elaborazione dati generali. È il successore più flessibile e potente del framework MapReduce, che consente l’esecuzione di processi di uso intensivo di dati, ad esempio Hive, in modo più efficiente a livello di scalabilità. Vedere ["Usare Apache Tez per ottenere prestazioni migliorate" in Uso di Hive e HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN rappresenta la nuova generazione di MapReduce (MapReduce 2.0 o MRv2) e supporta scenari di elaborazione dati che superano l'elaborazione batch di MapReduce, offrendo maggiore scalabilità ed elaborazione in tempo reale. YARN offre un framework applicazioni distribuito e gestione delle risorse. I processi MapReduce vengono eseguiti in YARN. Vedere <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordina i processi in sistemi distribuiti di grandi dimensioni usando uno spazio dei nomi gerarchico condiviso di registri di dati (znode). Gli Znode contengono piccole quantità di metainformazioni necessarie per il coordinamento dei processi: stato, posizione, configurazione e così via. Vedere un esempio di [ZooKeeper con un cluster HBase e Apache Phoenix](hdinsight-hbase-phoenix-squirrel-linux.md). 

## <a name="programming-languages-on-hdinsight"></a>Linguaggi di programmazione in HDInsight
I cluster di HDInsight, ovvero cluster Spark, HBase, Kafka, Hadoop e altri, supportano molti linguaggi di programmazione, ma alcuni di questi non sono installati per impostazione predefinita. Per le librerie, i moduli o i pacchetti non installati per impostazione predefinita, [usare un'azione script per installare il componente](hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Supporto per i linguaggi di programmazione predefiniti
Per impostazione predefinita, i cluster HDInsight supportano:

* Java
* Python

È possibile installare linguaggi aggiuntivi usando [azioni script](hdinsight-hadoop-script-actions-linux.md).

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

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e HDInsight Premium
HDInsight fornisce offerte cloud per i Big Data in due categorie, Standard e Premium. HDInsight Standard offre un cluster su scala aziendale che le organizzazioni possono usare per eseguire i carichi di lavoro legati ai Big Data. HDInsight Premium si basa sulle funzionalità del piano Standard e offre funzionalità di analisi e sicurezza avanzate per un cluster HDInsight. Per altre informazioni, vedere [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>Microsoft business intelligence e HDInsight
Gli strumenti di business intelligence (BI) noti consentono di recuperare, analizzare e creare report di dati integrati con HDInsight usando il componente aggiuntivo Power Query o Microsoft Hive ODBC Driver:

* [Connettere Excel a Hadoop con Power Query](hdinsight-connect-excel-power-query.md): informazioni sulle procedure di connessione di Excel all'account di archiviazione di Azure in cui vengono archiviati i dati del cluster HDInsight tramite Microsoft Power Query per Excel. Workstation Windows necessaria. 
* [Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-odbc-driver.md): informazioni su come importare i dati da HDInsight con Microsoft Hive ODBC Driver. Workstation Windows necessaria. 
* [Microsoft Cloud Platform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): informazioni su Power BI per Office 365, download della versione di valutazione di SQL Server e configurazione di SharePoint Server 2013 e SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Passaggi successivi

* [Introduzione all'uso di Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): esercitazione introduttiva per il provisioning di cluster Hadoop di HDInsight e l'esecuzione di semplici query Hive.
* [Introduzione: creare cluster Apache Spark in HDInsight Linux ed eseguire query interattive usando Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md): esercitazione introduttiva per la creazione di un cluster Spark e l'esecuzione di query interattive Spark SQL.
* [Introduzione all'uso di R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md): introduzione all'uso di R Server in HDInsight Premium.
* [Effettuare il provisioning di cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md): informazioni su come effettuare il provisioning di un cluster Hadoop di HDInsight tramite il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell.


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
