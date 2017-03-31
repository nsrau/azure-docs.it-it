---
title: "Che cos&quot;è Hadoop? Introduzione ad Azure HDInsight | Microsoft Docs"
description: Introduzione all&quot;ecosistema Hadoop e ai componenti in HDInsight. HDInsight include Hadoop, Spark, HBase e altri strumenti per l&quot;analisi e l&quot;elaborazione dei Big Data.
keywords: "analisi di big data,introduzione a hadoop,che cos&quot;è hadoop,stack di tecnologie hadoop,ecosistema hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2016
ms.author: cgronlun
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: e0ebb2224c1f3290df2a89cfaeb9797a7dbcc051
ms.lasthandoff: 03/11/2017


---
# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Introduzione all'ecosistema Hadoop in Azure HDInsight
 In questo articolo viene fornita una presentazione di Hadoop in Azure HDInsight, del suo ecosistema e dei Big Data. Informazioni sui componenti di Hadoop, sulla terminologia comune e sugli scenari per l'analisi dei Big Data.

## <a name="what-is-hadoop-on-hdinsight"></a>Che cos'è Hadoop in HDInsight?
Hadoop fa riferimento a un ecosistema di software open source che funziona come framework per l'elaborazione distribuita, l'archiviazione e l'analisi dei set di Big Data su cluster di computer. Azure HDInsight rende disponibili nel cloud i componenti della distribuzione di **Hortonworks Data Platform (HDP)**, consente di distribuire i cluster gestiti con elevata affidabilità e disponibilità e offre sicurezza e governance di livello enterprise con Active Directory.  

Il progetto open source originale per l'elaborazione dei Big Data era Apache Hadoop, a cui è succeduto lo sviluppo di software e utilità considerati parte dello stack di tecnologie Hadoop, tra cui Apache Hive, Apache HBase, Apache Spark, Apache Kafka e molti altri. Per informazioni dettagliate, vedere [Panoramica dell'ecosistema Hadoop in HDInsight](#overview) di seguito.

## <a name="what-is-big-data"></a>Informazioni sui Big Data
I Big Data descrivono qualsiasi corpo di grandi dimensioni di informazioni digitali, dal testo in un feed Twitter alle informazioni del sensore di un'apparecchiatura industriale fino alle informazioni sull'esplorazione e gli acquisti dei clienti in un sito Web. I Big Data possono essere cronologici (dati archiviati) oppure in tempo reale (trasmessi direttamente dall'origine). I Big Data vengono raccolti in volumi sempre più elevati, a velocità crescenti e in una gamma crescente di formati.

Per fare in modo che i Big Data forniscano informazioni dettagliate, è necessario raccogliere dati utili e porre le domande giuste. È inoltre necessario assicurarsi che i dati siano accessibili, puliti, analizzati e quindi presentati in modo utile. Per queste operazioni può essere utile l'analisi dei Big Data di Hadoop in HDInsight.

## <a name="overview"></a>Panoramica dell'ecosistema Hadoop in HDInsight
HDInsight è una distribuzione cloud su Microsoft Azure dello stack di tecnologie Apache Hadoop in rapida espansione per l'analisi dei Big Data. Fornisce le implementazioni di Apache Spark, HBase, Kafka, Storm, Pig, Hive, Interactive Hive, Sqoop, Oozie, Ambari e così via. HDInsight si integra anche con gli strumenti di business intelligence (BI) come Power BI, Excel, SQL Server Analysis Services e SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-kafka-interactive-hive-storm-customized-and-other-clusters"></a>Hadoop, HBase, Spark, Kafka, Interactive Hive, Storm, strumenti personalizzati e altri cluster
HDInsight offre i seguenti tipi di cluster:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: fornisce un'affidabile archiviazione dei dati con [HDFS](#hdfs), nonché un semplice modello di programmazione [MapReduce](#mapreduce) per elaborare e analizzare i dati in parallelo.
* **[Apache Spark](http://spark.apache.org/)**: è un framework di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data; è compatibile con SQL, dati di streaming e Machine Learning. Vedere [Panoramica: cos’è Apache Spark in HDInsight?](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: un database NoSQL basato su Hadoop che fornisce accesso casuale e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati. Può gestire potenzialmente milioni di righe e colonne. Vedere [Panoramica di HBase in HDInsight](hdinsight-hbase-overview.md).
* **[Microsoft R Server](https://msdn.microsoft.com/en-us/microsoft-r/rserver)**: un server di livello enterprise che ospita e gestisce processi R paralleli e distribuiti. Offre ai data scientist, agli statistici e ai programmatori R l'accesso su richiesta a metodi di analisi scalabili e distribuiti su HDInsight. Vedere [Panoramica di R Server su HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-overview).
* **[Apache Storm](https://storm.incubator.apache.org/)**: un sistema di calcolo distribuito e in tempo reale per l'elaborazione rapida di grandi flussi di dati. Storm viene offerto come cluster gestito in HDInsight. Vedere [Analizzare i dati del sensore in tempo reale con Storm e Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Anteprima di Apache Interactive Hive (cioè lunga durata e funzionamento)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: la memorizzazione nella cache integrata per query Hive interattive e più veloci. Vedere [Usare Interactive Hive in HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-interactive-hive).
* **[Anteprima di Apache Kafka](https://kafka.apache.org/)**: una piattaforma open source usata per generare applicazioni e pipeline di dati dei flussi. Kafka fornisce inoltre funzionalità di code di messaggi che consentono di pubblicare e sottoscrivere i flussi di dati. Vedere [Introduction to Apache Kafka on HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-kafka-introduction) (Introduzione ad Apache Kafka in HDInsight).
* **[Anteprima dei cluster aggiunti al dominio](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)**: un cluster aggiunto a un dominio di Active Directory per poterne controllare l'accesso e offrire la governance dei dati.
* **[Cluster personalizzati con le azioni script](hdinsight-hadoop-customize-cluster-linux.md)**: cluster con script eseguiti durante il provisioning e che possono installare componenti aggiuntivi.

### <a name="example-customization-scripts"></a>Script di personalizzazione di esempio
Le azioni script sono script Bash su Linux che vengono eseguiti durante il provisioning del cluster e che possono essere usati per installare componenti aggiuntivi nel cluster.

I seguenti gli script di esempio vengono forniti dal team di HDInsight:

* [Hue](hdinsight-hadoop-hue-linux.md): un insieme di applicazioni Web che consente di interagire con un cluster. Solamente cluster Linux
* [Giraph](hdinsight-hadoop-giraph-install-linux.md): elaborazione Graph per modellare le relazioni tra persone o elementi.
* [R](hdinsight-hadoop-r-scripts-linux.md): un linguaggio e un ambiente open source per l'elaborazione statistica usata in machine learning.
* [Solr](hdinsight-hadoop-solr-install-linux.md): una piattaforma di ricerca a scalabilità aziendale che permette di eseguire ricerche full-text sui dati.

Per informazioni su come sviluppare azioni script personalizzate, vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="what-are-the-hadoop-components-and-utilities"></a>Quali sono i componenti e le utilità di Hadoop?
I componenti e le utilità seguenti sono inclusi nei cluster HDInsight.

* **[Ambari](#ambari)**: provisioning, gestione, monitoraggio e utilità dei cluster.
* **[Avro](#avro)** (la libreria Microsoft .NET per Avro): serializzazione dei dati per l'ambiente Microsoft .NET.
* **[Hive e HCatalog](#hive)**: Structured Query Language (SQL), ad esempio l'esecuzione di query e un livello di gestione tabella e archiviazione.
* **[Mahout](#mahout)**: per applicazioni Machine Learning scalabili.
* **[MapReduce](#mapreduce)**: framework legacy per l'elaborazione distribuita e la gestione delle risorse Hadoop. Vedere [YARN](#yarn), il framework delle risorse di nuova generazione.
* **[Oozie](#oozie)**: gestione dei flussi di lavoro.
* **[Phoenix](#phoenix)**: un livello di database relazionale su HBase.
* **[Pig](#pig)**: script semplice per le trasformazioni MapReduce.
* **[Sqoop](#sqoop)**: importazione ed esportazione dei dati.
* **[Tez](#tez)**: consente di eseguire in modo efficiente a livello di scalabilità processi a elevato utilizzo di dati.
* **[YARN](#yarn)**: parte della libreria principale Hadoop e framework del software MapReduce di nuova generazione.
* **[ZooKeeper](#zookeeper)**: coordinamento di processi in sistemi distribuiti.

> [!NOTE]
> Per informazioni sui componenti specifici e sulle versioni, vedere [Hadoop components, versioning, and service offerings in HDInsight][component-versioning] (Componenti, versioni e offerte di servizio di Hadoop disponibili in HDInsight)
>
>

### <a name="ambari"></a>Ambari
Apache Ambari viene usato per il provisioning, la gestione e il monitoraggio di cluster Apache Hadoop. Comprende una raccolta di strumenti operatore intuitivi e un set affidabile di API che nascondono la complessità di Hadoop, semplificando le operazioni sui cluster. I cluster HDInsight basati su Linux forniscono sia l'interfaccia utente Web Ambari che l'API REST Ambari, mentre i cluster su Windows forniscono un subset dell'API REST. Le viste di Ambari nei cluster HDInsight consentono funzionalità di plug-in dell'interfaccia utente.

Vedere [Gestire i cluster HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md) (solo Linux), [Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari](hdinsight-monitor-use-ambari-api.md) e i <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">riferimenti all'API Apache Ambari</a>.

### <a name="avro"></a>Avro (Libreria Microsoft .NET per Avro)
La libreria Microsoft .NET per Avro implementa il formato compatto di interscambio dei dati binari Apache Avro per la serializzazione nell'ambiente Microsoft .NET. Usa <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> per definire lo schema indipendente dal linguaggio che sottoscrive l'interoperabilità del linguaggio, ossia che consente di leggere in un linguaggio i dati serializzati in un altro linguaggio. Informazioni dettagliate sul formato sono disponibili nelle <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">specifiche di Apache Avro</a>.
Il formato dei file Avro supporta il modello di programmazione MapReduce distribuito. I file possono essere suddivisi, ossia è possibile cercare un qualsiasi punto del file e iniziare a leggere da uno specifico blocco. Per informazioni sulle procedure, vedere [Serializzazione dei dati con la libreria Microsoft .NET per Avro](hdinsight-dotnet-avro-serialization.md).

### <a name="hdfs"></a>HDFS
Hadoop Distributed File System (HDFS) è un file system distribuito che, insieme a MapReduce e YARN, costituisce il nucleo dell'ecosistema Hadoop. HDFS è il file system standard per i cluster Hadoop in HDInsight.

### <a name="hive"></a>Apache Hive e HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> è un software di data warehouse basato su Hadoop che consente di eseguire query e gestire grandi set di dati nella risorsa di archiviazione distribuita con un linguaggio simile a SQL chiamato HiveQL. Analogamente a Pig, Hive è un'astrazione basata su MapReduce e, se eseguito, consente di convertire le query in una serie di processi MapReduce. Hive è concettualmente più simile a un sistema di gestione di database relazionali che a Pig ed è quindi più adatto a essere usato con dati più strutturati. Pig è invece la scelta ottimale per dati non strutturati. Vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> è un livello di gestione tabella e archiviazione per Hadoop che fornisce agli utenti una visualizzazione relazionale dei dati. In HCatalog, è possibile leggere e scrivere file in qualsiasi formato per il quale può essere scritto un SerDe (serializzatore-deserializzatore) Hive.

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> è una libreria scalabile di algoritmi di Machine Learning eseguita in Hadoop. Usando i principi della statistica, le applicazioni di Machine Learning istruiscono i sistemi in modo che apprendano dai dati e usino i risultati precedenti per determinare i comportamenti futuri. Vedere [Generare consigli sui film usando Mahout in Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce è un software legacy per Hadoop per la scrittura di applicazioni per l'elaborazione batch di set di Big Data in parallelo. Un processo MapReduce suddivide i set di dati di grandi dimensioni e organizza i dati in coppie chiave-valore per l'elaborazione.

[YARN](#yarn) è il framework applicazioni e lo strumento di gestione delle risorse di nuova generazione di Hadoop ed è denominato MapReduce 2.0. I processi MapReduce vengono eseguiti in YARN.

Per altre informazioni su MapReduce, vedere <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> in Hadoop Wiki.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop. È integrato con lo stack Hadoop e supporta i processi Hadoop per MapReduce, Pig, Hive e Sqoop. Può anche essere usato per pianificare processi specifici di un sistema, come i programmi Java o gli script della shell. Vedere [Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in HDInsight](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> è un livello di database relazionale su HBase. Phoenix include un driver JDBC che consente agli utenti di eseguire query e di gestire direttamente le tabelle SQL. Anziché utilizzare MapReduce, Phoenix converte query e altre istruzioni in chiamate API NoSQL native, consentendo in tal modo applicazioni più rapide in archivi NoSQL. Vedere [Usare Apache Phoenix e SQuirreL con i cluster HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> è una piattaforma di alto livello che consente di eseguire trasformazioni MapReduce complesse in set di dati molto grandi usando un linguaggio di scripting semplice chiamato Pig Latin. Pig converte gli script Pig Latin in modo che possano essere eseguiti in Hadoop. È possibile creare funzioni definite dall'utente (UDF) per estendere Pig Latin. Vedere [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> è uno strumento che consente di trasferire dati in blocco tra Hadoop e i database relazionali, ad esempio SQL o altri archivi dati strutturati, nel modo più efficace possibile. Vedere [Uso di Sqoop con HDInsight](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> è un framework di applicazione basato su Hadoop YARN che esegue grafici complessi, aciclici di elaborazione dati generali. È il successore più flessibile e potente del framework MapReduce, che consente l’esecuzione di processi di uso intensivo di dati, ad esempio Hive, in modo più efficiente a livello di scalabilità. Vedere ["Usare Apache Tez per ottenere prestazioni migliorate" in Uso di Hive e HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN rappresenta la nuova generazione di MapReduce (MapReduce 2.0 o MRv2) e supporta scenari di elaborazione dati che superano l'elaborazione batch di MapReduce, offrendo maggiore scalabilità ed elaborazione in tempo reale. YARN offre un framework applicazioni distribuito e gestione delle risorse. I processi MapReduce vengono eseguiti in YARN.

Per informazioni su YARN, vedere <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordina i processi in sistemi distribuiti di grandi dimensioni mediante uno spazio dei nomi gerarchico condiviso di registri di dati (znode). Gli Znode contengono piccole quantità di metainformazioni necessarie per il coordinamento dei processi: stato, posizione, configurazione e così via.

## <a name="programming-languages-on-hdinsight"></a>Linguaggi di programmazione in HDInsight
I cluster di HDInsight - cluster Hadoop, HBase, Storm e Spark - supportano diversi linguaggi di programmazione, ma per impostazione predefinita alcuni linguaggi non sono installati. Per le librerie, i moduli o i pacchetti non installati per impostazione predefinita, usare un'azione script per installare il componente. Vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Supporto per i linguaggi di programmazione predefiniti
Per impostazione predefinita, i cluster HDInsight supportano:

* Java
* Python

È possibile installare linguaggi aggiuntivi usando azioni script. Vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Linguaggi per macchine virtuali Java
È possibile eseguire molti linguaggi diversi da Java usando una macchina virtuale Java (JVM). L'esecuzione di alcuni di questi linguaggi può tuttavia richiedere l'installazione di componenti aggiuntivi nel cluster.

I linguaggi basati su JVM sono supportati nei cluster HDInsight:

* Clojure
* Jython (Python per Java)
* Scala

### <a name="hadoop-specific-languages"></a>Linguaggi specifici di Hadoop
I cluster HDInsight supportano i linguaggi seguenti, specifici dell'ecosistema Hadoop:

* Pig Latin per processi Pig
* HiveQL per processi Hive e SparkSQL

## <a name="advantage"></a>Vantaggi di Hadoop nel cloud
All'interno dell'ecosistema cloud Azure, Hadoop in HDInsight offre numerosi vantaggi, tra cui:

* Provisioning automatico dei cluster Hadoop. I cluster HDInsight sono molto più facile creare rispetto alla configurazione manuale dei cluster Hadoop. Per informazioni dettagliate, vedere [Provision di cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Componenti Hadoop aggiornati. Per informazioni dettagliate, vedere [Hadoop components, versioning, and service offerings in HDInsight][component-versioning] (Componenti, versioni e offerte di servizio di Hadoop disponibili in HDInsight).
* Disponibilità e affidabilità elevate dei cluster. Per dettagli, vedere [Disponibilità e affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md) .
* Archiviazione dei dati efficace ed economica con Archiviazione di Azure o Azure Data Lake Store, entrambe opzioni di archiviazione compatibili con Hadoop. Per altre informazioni, vedere [Usare Archiviazione di Azure con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md) o [Usare Data Lake Store con un cluster HDInsight](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).
* Integrazione con altri servizi di Azure, inclusi [App Web](https://azure.microsoft.com/documentation/services/app-service/web/) e [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/).
* Altri tipi e dimensioni delle VM per l'esecuzione di cluster HDInsight. Per informazioni dettagliate, vedere [Hadoop components, versioning, and service offerings in HDInsight][component-versioning] (Componenti, versioni e offerte di servizio di Hadoop disponibili in HDInsight).
* Ridimensionamento del cluster. Il ridimensionamento del cluster consente di modificare il numero di nodi di un cluster HDInsight in esecuzione senza doverlo eliminare o ricreare.
* Supporto della rete virtuale. I cluster HDInsight possono essere usati con la rete virtuale di Azure per supportare l'isolamento delle risorse cloud o gli scenari ibridi che collegano le risorse cloud con quelle del data center.
* Costo iniziale ridotto. Avviare una [versione di valutazione gratuita](https://azure.microsoft.com/free/) o consultare i [dettagli sui prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Per altre informazioni sui vantaggi di Hadoop in HDInsight, vedere la pagina delle [funzionalità di Azure per HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e HDInsight Premium
HDInsight fornisce offerte cloud per i Big Data in due categorie, Standard e Premium. HDInsight Standard offre un cluster su scala aziendale che le organizzazioni possono usare per eseguire i carichi di lavoro legati ai Big Data. HDInsight Premium offre in più funzionalità analitiche e di sicurezza avanzate per un cluster HDInsight. Per altre informazioni, vedere [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a id="resources"></a>Risorse per ottenere altre informazioni sull'analisi dei Big Data, Hadoop e HDInsight
Integrare questa introduzione a Hadoop nel cloud e nell'analisi dei Big Data con le risorse seguenti.

### <a name="hadoop-documentation-for-hdinsight"></a>Documentazione di Hadoop per HDInsight.
* [Documentazione su HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): pagina relativa alla documentazione per Hadoop in Azure HDInsight con collegamenti ad articoli, video e altre risorse.
* [Introduzione all'uso di Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): esercitazione introduttiva per il provisioning di cluster Hadoop di HDInsight e l'esecuzione di semplici query Hive.
* [Introduzione: creare cluster Apache Spark in HDInsight Linux ed eseguire query interattive usando Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md): esercitazione introduttiva per la creazione di un cluster Spark e l'esecuzione di query interattive Spark SQL.
* [Introduzione all'uso di R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md): introduzione all'uso di R Server in HDInsight Premium.
* [Effettuare il provisioning di cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md): informazioni su come effettuare il provisioning di un cluster Hadoop di HDInsight tramite il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell.

### <a name="apache-hadoop"></a>Apache Hadoop
* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: altre informazioni sulla raccolta software di Apache Hadoop, un framework che consente l'elaborazione distribuita di set di dati di grandi dimensioni in cluster di computer.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: altre informazioni sull'architettura e la struttura di Hadoop Distributed File System, il sistema di archiviazione principale usato dalle applicazioni Hadoop.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a>: altre informazioni sul framework di programmazione per la scrittura di applicazioni Hadoop in grado di elaborare rapidamente quantità elevate di dati in parallelo su cluster di grandi dimensioni di nodi di calcolo.

### <a name="microsoft-business-intelligence"></a>Microsoft business intelligence
Gli strumenti di business intelligence (BI) noti, ad esempio Excel, PowerPivot, SQL Server Analysis Services e  SQL Server Reporting Services, consentono di recuperare, analizzare e creare report di dati integrati con HDInsight usando il componente aggiuntivo Power Query o Microsoft Hive ODBC Driver.

Gli strumenti di BI seguenti sono utili per l'analisi dei Big Data:

* [Connettere Excel a Hadoop mediante Power Query](hdinsight-connect-excel-power-query.md): informazioni sulle procedure di connessione di Excel all'account di archiviazione di Azure in cui vengono archiviati i dati associati al cluster HDInsight mediante Microsoft Power Query per Excel. Workstation Windows necessaria. È compatibile con i cluster in Linux o Windows.
* [Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-odbc-driver.md): informazioni su come importare i dati da HDInsight con Microsoft Hive ODBC Driver. Workstation Windows necessaria. È compatibile con i cluster in Linux o Windows.
* [Microsoft Cloud Platform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): informazioni su Power BI per Office 365, download della versione di valutazione di SQL Server e configurazione di SharePoint Server 2013 e SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).

[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

