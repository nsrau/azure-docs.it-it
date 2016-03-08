<properties
	pageTitle="Cos’è Hadoop nel cloud? Introduzione ad HDInsight | Microsoft Azure"
	description="Che cos'è Hadoop nel cloud e come viene gestito in HDInsight? Un'introduzione ai componenti di Hadoop all’analisi di Big Data."
	keywords="analisi di big data, introduzione a hadoop, che cos'è hadoop, hadoop nel cloud"
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/27/2016"
   ms.author="cgronlun"/>


# Cos’è Hadoop nel cloud? Un'introduzione ai componenti di Hadoop in HDInsight l’analisi di Big Data.

In questo articolo viene fornita un'introduzione a Hadoop, al suo ecosistema e ai Big Data in Azure HDInsight; vengono fornite informazioni su Hadoop in HDInsight e sui i componenti di Hadoop, sulla terminologia comune e sugli scenari per l'analisi dei Big Data. Vengono inoltre fornite informazioni sulle esercitazioni di Hadoop, sulla documentazione e sulle risorse per l'uso del cloud Hadoop in HDInsight.

## Cos’è Hadoop nel cloud in HDInsight?

Azure HDInsight distribuisce ed esegue il provisioning dei cluster Apache Hadoop gestiti nel cloud, offrendo un framework software progettato per elaborare, analizzare e creare report sui Big Data con elevata affidabilità e disponibilità. HDInsight usa la distribuzione Hadoop **Hortonworks Data Platform (HDP)**. Hadoop fa spesso riferimento all'intero ecosistema Hadoop di componenti, che include Apache HBase, Apache Spark, e Apache Storm, nonché altre tecnologie comprese in Hadoop. Per informazioni dettagliate, vedere [Panoramica dell'ecosistema Hadoop in HDInsight](#overview) di seguito.


## Informazioni sui Big Data
Con Big Data si intendono i dati raccolti in volumi sempre più elevati, a velocità crescenti e per una gamma crescente di formati non strutturati e contesti semantici variabili.

I Big Data descrivono un qualsiasi corpo di grandi dimensioni di informazioni digitali, dal testo in un feed Twitter alle informazioni del sensore di un apparecchiatura industriale fino alle informazioni sull'esplorazione e gli acquisti dei clienti in un catalogo online. I Big Data possono essere cronologici (dati archiviati) oppure in tempo reale (trasmessi direttamente dall'origine).

Per ottenere informazioni con i Big Data su cui sia possibile intervenire o che consentano un'analisi più approfondita, non si devono solo raccogliere i dati rilevanti e porre le domande giuste, ma è anche necessario che i dati siano accessibili, puliti, analizzati e quindi presentati in modo utile. Per queste operazioni può essere utile l'analisi dei Big Data di Hadoop in HDInsight.


## <a name="overview"></a>Panoramica dell'ecosistema Hadoop in HDInsight

HDInsight è un'implementazione cloud su Microsoft Azure dello stack di tecnologie Apache Hadoop in rapida espansione che rappresenta la soluzione più adatta per l'analisi dei Big Data. Fornisce le implementazioni di Apache Spark, HBase, Storm, Pig, Hive, Sqoop, Oozie, Ambari, e così via. HDInsight si integra anche con gli strumenti di business intelligence (BI) come Power BI, Excel, SQL Server Analysis Services e SQL Server Reporting Services.

### Cluster in Linux

Azure HDInsight distribuisce ed esegue il provisioning di cluster Hadoop nel cloud in **Linux**. Per informazioni dettagliate, vedere la tabella seguente.

Categoria | Hadoop su Linux
---------| -------------------
**Sistema operativo del cluster** | Supporto a lungo termine (LST) Ubuntu 12.04
**Tipo di cluster** | Hadoop, Spark, HBase, Storm
**Distribuzione** | Portale di Azure, interfaccia della riga di comando di Azure, Azure PowerShell
**Interfaccia utente del cluster** | Ambari
**Accesso remoto** | Secure Shell (SSH), REST API, ODBC, JDBC



### Hadoop, HBase, Spark, Storm e cluster personalizzati

HDInsight offre configurazioni cluster per carichi di lavoro specifici: Apache Hadoop, Spark, HBase, o Storm. In alternativa, è possibile [personalizzare i cluster con azioni di script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: (il carico di lavoro "Query"): fornisce un'archiviazione dei dati affidabile con [HDFS](#HDFS), nonché un semplice modello di programmazione [MapReduce](#mapreduce) per elaborare e analizzare i dati in parallelo.

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**: è un framework di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data, Spark funziona con SQL, dati di streaming e machine learning. Vedere [Panoramica: cos’è Apache Spark in HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (il carico di lavoro "NoSQL"): un database NoSQL basato su Hadoop che fornisce accesso rapido e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati. Può gestire potenzialmente milioni di righe e colonne. Vedere la [panoramica di HBase in HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>**: (il carico di lavoro "Stream"): un sistema di calcolo distribuito in tempo reale per l'elaborazione rapida di grandi flussi di dati. Storm viene offerto come cluster gestito in HDInsight. Vedere [Analizzare i dati del sensore in tempo reale con Storm e Hadoop](hdinsight-storm-sensor-data-analysis.md).

#### Script di personalizzazione di esempio

Le azioni script sono script che vengono eseguiti durante il provisioning del cluster e che possono essere usati per installare componenti aggiuntivi nel cluster. Per cluster basati su Linux, questi sono gli script Bash.

I seguenti gli script di esempio vengono forniti dal team di HDInsight:

* [Hue](hdinsight-hadoop-hue-linux.md): un insieme di applicazioni Web che consente di interagire con un cluster. Solamente cluster Linux

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): elaborazione Graph per modellare le relazioni tra persone o elementi.

* [R](hdinsight-hadoop-r-scripts-linux.md): un linguaggio e un ambiente open source per l'elaborazione statistica usata in machine learning.

* [Solr](hdinsight-hadoop-solr-install-linux.md): una piattaforma di ricerca a scalabilità aziendale che permette di eseguire ricerche full-text sui dati.

Per informazioni su come sviluppare azioni script personalizzate, vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

## Quali sono i componenti e le utilità di Hadoop?

I componenti e le utilità seguenti sono inclusi nei cluster HDInsight.

* **[Ambari](#ambari)**: provisioning, gestione, monitoraggio e utilità del cluster.

* **[Avro](#avro)** (la libreria Microsoft .NET per Avro): serializzazione dei dati per l'ambiente Microsoft .NET.

* **[Hive e HCatalog](#hive)**: Structured Query Language (SQL), ad esempio l'esecuzione di query e un livello di gestione tabella e archiviazione.

* **[Mahout](#mahout)**: Machine Learning.

* **[MapReduce](#mapreduce)**: framework legacy per l'elaborazione distribuita e la gestione delle risorse Hadoop. Vedere [YARN](#yarn), il framework risorse di nuova generazione.

* **[Oozie](#oozie)**: gestione dei flussi di lavoro.

* **[Phoenix](#phoenix)**: un livello di database relazionale su HBase.

* **[Pig](#pig)**: script semplice per le trasformazioni MapReduce.

* **[Sqoop](#sqoop)**: importazione ed esportazione dei dati.

* **[Tez](#tez)**: consente di eseguire in modo efficiente a livello di scalabilità processi a elevato utilizzo di dati.

* **[YARN](#yarn)**: parte della libreria centrale Hadoop e della prossima generazione di framework software MapReduce.

* **[ZooKeeper](#zookeeper)**: coordinamento di processi in sistemi distribuiti.

> [AZURE.NOTE] Per informazioni sui componenti specifici e sulle versioni, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][component-versioning].

### <a name="ambari"></a>Ambari

Apache Ambari viene usato per il provisioning, la gestione e il monitoraggio di cluster Apache Hadoop. Comprende una raccolta di strumenti operatore intuitivi e un set affidabile di API che nascondono la complessità di Hadoop, semplificando le operazioni sui cluster. I cluster HDInsight basati su Linux forniscono sia l'interfaccia utente Web Ambari che l'API REST Ambari, mentre i cluster basati su Windows forniscono un subset dell'API REST. Le viste di Ambari nei cluster HDInsight consentono funzionalità di plug-in dell'interfaccia utente.

Vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md) (solo Linux), [Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari](hdinsight-monitor-use-ambari-api.md) e i <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md"> riferimenti all'API Apache Ambari</a>.

### <a name="avro"></a>Avro (Libreria Microsoft .NET per Avro)

La libreria Microsoft .NET per Avro implementa il formato compatto di interscambio dei dati binari Apache Avro per la serializzazione nell'ambiente Microsoft .NET. Usa <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> per definire lo schema indipendente dal linguaggio che sottoscrive l'interoperabilità del linguaggio, ossia che consente di leggere in un linguaggio i dati serializzati in un altro linguaggio. Informazioni dettagliate sul formato sono disponibili nelle <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">specifiche di Apache Avro</a>. Il formato dei file Avro supporta il modello di programmazione MapReduce distribuito. I file possono essere suddivisi, ossia è possibile cercare un qualsiasi punto del file e iniziare a leggere da uno specifico blocco. Per informazioni sulle procedure, vedere [Serializzazione dei dati con la libreria Microsoft .NET per Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) è un file system distribuito che, insieme a MapReduce e YARN, costituisce il nucleo dell'ecosistema Hadoop. HDFS è il file system standard per i cluster Hadoop in HDInsight.

### <a name="hive"></a>Apache Hive e HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> è un software di data warehouse basato su Hadoop che consente di eseguire query e gestire grandi set di dati nella risorsa di archiviazione distribuita con un linguaggio simile a SQL chiamato HiveQL. Analogamente a Pig, Hive è un'astrazione basata su MapReduce e, se eseguito, consente di convertire le query in una serie di processi MapReduce. Hive è concettualmente più simile a un sistema di gestione di database relazionali che a Pig ed è quindi più adatto a essere usato con dati più strutturati. Pig è invece la scelta ottimale per dati non strutturati. Vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> è un livello di gestione tabella e archiviazione per Hadoop che fornisce agli utenti una visualizzazione relazionale dei dati. In HCatalog, è possibile leggere e scrivere file in qualsiasi formato per il quale può essere scritto un SerDe (serializzatore-deserializzatore) Hive.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> è una libreria scalabile di algoritmi di Machine Learning eseguita in Hadoop. Usando i principi della statistica, le applicazioni di Machine Learning istruiscono i sistemi in modo che apprendano dai dati e usino i risultati precedenti per determinare i comportamenti futuri. Vedere [Generare consigli sui film usando Mahout in Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce è un software legacy per Hadoop per la scrittura di applicazioni per l'elaborazione batch di set di Big Data in parallelo. Un processo MapReduce suddivide i set di dati di grandi dimensioni e organizza i dati in coppie chiave-valore per l'elaborazione.

[YARN](#yarn) è il framework applicazioni e lo strumento di gestione delle risorse di nuova generazione di Hadoop e viene denominato MapReduce 2.0. I processi MapReduce verranno eseguiti in YARN.

Per altre informazioni su MapReduce, vedere <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> in Hadoop Wiki.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop. È integrato con lo stack Hadoop e supporta i processi Hadoop per MapReduce, Pig, Hive e Sqoop. Può anche essere usato per pianificare processi specifici di un sistema, come i programmi Java o gli script della shell. Vedere [Uso del coordinatore Oozie basato sul tempo con HDInsight](hdinsight-use-oozie-coordinator-time.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> è un livello di database relazionale su HBase. Phoenix include un driver JDBC che consente agli utenti di eseguire query e gestire direttamente le tabelle SQL. Anziché utilizzare MapReduce, Phoenix converte query e altre istruzioni in chiamate API NoSQL native, consentendo in tal modo applicazioni più rapide in archivi NoSQL. Vedere [Usare Apache Phoenix e SQuirreL con i cluster HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> è una piattaforma di alto livello che consente di eseguire trasformazioni MapReduce complesse in set di dati molto grandi usando un linguaggio di scripting semplice chiamato Pig Latin. Pig converte gli script Pig Latin in modo che possano essere eseguiti in Hadoop. È possibile creare funzioni definite dall'utente (UDFs) per estendere Pig Latin. Vedere [Uso di Pig con HDInsight](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> è uno strumento che consente di trasferire dati in blocco tra Hadoop e i database relazionali, ad esempio SQL o altri archivi dati strutturati, nel modo più efficace possibile. Vedere [Uso di Sqoop con HDInsight](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> è un framework di applicazione basato su Hadoop YARN che esegue grafici complessi, aciclici di elaborazione dati generali. È il successore più flessibile e potente del framework MapReduce, che consente l’esecuzione di processi di uso intensivo di dati, ad esempio Hive, in modo più efficiente a livello di scalabilità. Vedere ["Usare Apache Tez per ottenere prestazioni migliorate" in Uso di Hive e HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN rappresenta la nuova generazione di MapReduce (MapReduce 2.0 o MRv2) e supporta scenari di elaborazione dati che superano l'elaborazione batch di MapReduce, offrendo maggiore scalabilità ed elaborazione in tempo reale. YARN offre un framework applicazioni distribuito e gestione delle risorse. I processi MapReduce verranno eseguiti in YARN.

Per informazioni su YARN, vedere <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordina i processi in sistemi distribuiti di grandi dimensioni mediante uno spazio dei nomi gerarchico condiviso di registri di dati (znode). Gli Znode contengono piccole quantità di metainformazioni necessarie per il coordinamento dei processi: stato, posizione, configurazione e così via.

## Linguaggi di programmazione in HDInsight

I cluster di HDInsight - cluster Hadoop, HBase, Storm e Spark - supportano diversi linguaggi di programmazione, ma per impostazione predefinita alcuni linguaggi non sono installati. Per le librerie, i moduli o i pacchetti non installati per impostazione predefinita, usare un'azione script per installare il componente. Vedere l'articolo relativo allo [sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Supporto per i linguaggi di programmazione predefiniti

Per impostazione predefinita, i cluster HDInsight supportano:

* Java

* Python

È possibile installare lingue aggiuntive usando azioni script, vedere l'articolo relativo allo [sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Linguaggi per macchine virtuali Java

È possibile eseguire molti linguaggi diversi da Java usando una macchina virtuale Java (JVM). L'esecuzione di alcuni di questi linguaggi può tuttavia richiedere l'installazione di componenti aggiuntivi nel cluster.

I linguaggi basati su JVM sono supportati nei cluster HDInsight:

* Clojure

* Jython (Python per Java)

* Scala

### Linguaggi specifici di Hadoop

I cluster HDInsight offrono supporto per i linguaggi seguenti, specifici dell'ecosistema Hadoop:

* Pig Latin per processi Pig

* HiveQL per processi Hive e SparkSQL


## <a name="advantage"></a>Vantaggi di Hadoop nel cloud

All'interno dell'ecosistema cloud Azure, Hadoop in HDInsight offre numerosi vantaggi, tra cui:

* Provisioning automatico dei cluster Hadoop. I cluster HDInsight sono molto più facile creare rispetto alla configurazione manuale dei cluster Hadoop. Per informazioni dettagliate, vedere [Provision di cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Componenti Hadoop aggiornati. Per dettagli, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][component-versioning].

* Disponibilità e affidabilità elevate dei cluster. Per dettagli, vedere [Disponibilità e affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md).

* Archiviazione dei dati efficace e conveniente con l'archiviazione BLOB di Azure, un'opzione compatibile con Hadoop. Per dettagli, vedere [Usare l'archivio BLOB di Azure con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md).

* Integrazione con altri servizi di Azure, inclusi [App Web](../documentation/services/app-service/web/) e [Database SQL](../documentation/services/sql-database/).

* Costo iniziale ridotto. Scaricare una [versione di valutazione gratuita](/pricing/free-trial/) o consultare [Dettagli prezzi di HDInsight](/pricing/details/hdinsight/).


Per altre informazioni sui vantaggi di Hadoop in HDInsight, vedere la pagina delle [funzionalità di Azure per HDInsight][marketing-page].



## <a id="resources"></a>Risorse per ottenere altre informazioni sull'analisi dei Big Data, Hadoop e HDInsight

Integrare questa introduzione a Hadoop nel cloud e nell'analisi dei Big Data con le risorse seguenti.


### Documentazione di Hadoop per HDInsight.

* [Documentazione HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): pagina relativa alla documentazione per Azure HDInsight con collegamenti ad articoli, video e altre risorse.

* [Introduzione a HDInsight su Linux](hdinsight-hadoop-linux-tutorial-get-started.md): un'esercitazione introduttiva per il provisioning di cluster HDInsight Hadoop in Linux e l'esecuzione di query Hive di esempio.

* [Introduzione a Storm basato su Linux in HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md): una rapida esercitazione introduttiva per il provisioning di uno Storm nel cluster HDInsight e l’esecuzione di topologie di esempio Storm.

* [Provisioning di HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md): informazioni su come effettuare il provisioning di un cluster HDInsight Hadoop in Linux tramite il portale di Azure, l’interfaccia della riga di comando di Azure o Azure PowerShell.

* [Uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md): alcuni suggerimenti sull'uso di cluster Hadoop Linux di cui è stato eseguito il provisioning in Azure.

* [Gestire cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md): informazioni su come monitorare e gestire il cluster Hadoop basato su Linux in HDInsight tramite il sito Web Ambari o l'API REST Ambari.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: altre informazioni sulla raccolta software di Apache Hadoop, un framework che consente l'elaborazione distribuita di set di dati di grandi dimensioni in cluster di computer.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: altre informazioni sull'architettura e la struttura di Hadoop Distributed File System, il sistema di archiviazione principale usato dalle applicazioni Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a>: altre informazioni sul framework di programmazione per la scrittura di applicazioni Hadoop in grado di elaborare rapidamente quantità elevate di dati in parallelo su cluster di grandi dimensioni di nodi di calcolo.

### Database SQL in Azure

* [Database SQL di azure](/documentation/services/sql-database/): documentazione, esercitazioni e video per il Database SQL.

* [Database SQL nel Portale di Azure](sql-database-manage-portal.md): uno strumento semplificato e di facile utilizzo per la gestione di database SQL nel cloud.

* [Adventure Works per database SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304): pagina di download del database di esempio di database SQL.

### Microsoft Business Intelligence (per HDInsight in Windows)

Gli strumenti di business intelligence (BI) noti, ad esempio Excel, PowerPivot, SQL Server Analysis Services e SQL Server Reporting Services, consentono di recuperare, analizzare e creare report di dati integrati con HDInsight usando il componente aggiuntivo Power Query o Microsoft Hive ODBC Driver.

Gli strumenti di BI seguenti sono utili per l'analisi dei Big Data:

* [Connettere Excel a Hadoop mediante Power Query](hdinsight-connect-excel-power-query.md): informazioni sulle procedure di connessione di Excel all'account di archiviazione di Azure in cui vengono archiviati i dati associati al cluster HDInsight mediante Microsoft Power Query per Excel.

* [Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-ODBC-driver.md): informazioni su come importare i dati da HDInsight con Microsoft Hive ODBC Driver.

* [Microsoft Cloud Platform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): informazioni su Power BI per Office 365, download della versione di valutazione di SQL Server e configurazione di SharePoint Server 2013 e SQL Server BI.

* <a target="_blank" href="http://msdn.microsoft.com/library/hh231701.aspx">Altre informazioni su SQL Server Analysis Services</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Informazioni su SQL Server Reporting Services</a>.


### Provare le soluzioni HDInsight per l'analisi dei Big Data (per HDInsight in Windows)

Usare l'analisi dei Big Data sui dati dell'organizzazione per ottenere informazioni dettagliate sulle attività aziendali. Di seguito sono riportati alcuni esempi:

* [Analizzare i dati del sensore HVAC:](hdinsight-hive-analyze-sensor-data.md): informazioni su come analizzare i dati del sensore usando Hive con HDInsight (Hadoop) e quindi visualizzare i dati in Microsoft Excel. In questo esempio Hive viene usato per elaborare i dati cronologici prodotti da sistemi HVAC per individuare quali sistemi non sono in grado di mantenere una temperatura impostata in modo affidabile.

* [Usare Hive con HDInsight per analizzare i log dei siti Web:](hdinsight-hive-analyze-website-log.md): informazioni su come usare HiveQL in HDInsight per analizzare i log dei siti Web per ottenere informazioni dettagliate sulla frequenza delle visite di siti Web esterni nell'arco di un giorno e per visualizzare un riepilogo degli errori relativi ai siti Web incontrati dagli utenti.

* [Analizzare i dati del sensore in tempo reale con Storm e HBase in HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): informazioni su come compilare una soluzione che usi un cluster Storm in HDInsight per elaborare i dati del sensore dagli hub eventi di Azure e quindi visualizzi i dati del sensore elaborati come informazioni quasi in tempo reale in un dashboard basato su Web.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0302_2016-->