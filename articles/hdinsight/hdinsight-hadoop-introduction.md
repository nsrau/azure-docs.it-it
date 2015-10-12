<properties
	pageTitle="Informazioni su Hadoop in HDInsight: analisi dei Big Data nel cloud | Microsoft Azure"
	description="Un'introduzione ai componenti di Hadoop nel cloud in HDInsight. Informazioni su come HDInsight usa i cluster Hadoop per gestire, analizzare e creare report sui Big Data."
	keywords="big data,big data analysis,hadoop,introduction to hadoop,what is hadoop"
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/03/2015"
   ms.author="cgronlun"/>


# Introduzione a Hadoop in HDInsight: elaborazione e analisi dei Big Data nel cloud

In questo articolo viene fornita un'introduzione a Hadoop, al suo ecosistema e ai Big Data in Azure HDInsight; vengono fornite informazioni su Hadoop in HDInsight e sui i componenti di Hadoop, sulla terminologia comune e sugli scenari per l'analisi dei Big Data. Vengono inoltre fornite informazioni sulle esercitazioni di Hadoop, sulla documentazione e sulle risorse per l'uso di Hadoop in HDInsight.

## Che cos'è Hadoop in HDInsight?

Azure HDInsight distribuisce ed esegue il provisioning dei cluster Apache Hadoop nel cloud, offrendo un framework software progettato per gestire, analizzare e creare report sui Big Data con elevata affidabilità e disponibilità. HDInsight usa la distribuzione Hadoop **Hortonworks Data Platform (HDP)**. Hadoop fa spesso riferimento all'intero ecosistema Hadoop di componenti, che include i cluster Storm e HBase, nonché altre tecnologie comprese in Hadoop. Per informazioni dettagliate, vedere [Panoramica dell'ecosistema Hadoop in HDInsight](#overview) di seguito.


## Informazioni sui Big Data
Con Big Data si intendono i dati raccolti in volumi sempre più elevati, a velocità crescenti e per una gamma sempre più ampia di formati non strutturati e contesti semantici variabili.

I Big Data descrivono un qualsiasi corpo di grandi dimensioni di informazioni digitali, dal testo in un feed Twitter alle informazioni del sensore di un apparecchiatura industriale fino alle informazioni sull'esplorazione e gli acquisti dei clienti in un catalogo online. I Big Data possono essere cronologici (dati archiviati) oppure in tempo reale (trasmessi direttamente dall'origine).

Affinché i Big Data forniscano informazioni fruibili o consentano un'analisi più approfondita, non è sufficiente porre le domande corrette e raccogliere i dati rilevanti per i problemi da risolvere, ma è anche necessario che i dati siano accessibili, puliti, analizzati e quindi presentati in modo utile. Per queste operazioni può essere utile l'analisi dei Big Data di Hadoop in HDInsight.


## <a name="overview"></a>Panoramica dell'ecosistema Hadoop in HDInsight

HDInsight è un'implementazione cloud su Microsoft Azure dello stack di tecnologie Apache Hadoop in rapida espansione che rappresenta la soluzione più adatta per l'analisi dei Big Data. Fornisce le implementazioni di Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari e così via. HDInsight si integra anche con gli strumenti di business intelligence (BI) come Excel, SQL Server Analysis Services e SQL Server Reporting Services.

### Cluster Linux e Windows

Azure HDInsight effettua la distribuzione e il provisioning di cluster Hadoop nel cloud, usando **Linux** o **Windows** come sistema operativo sottostante.

* **HDInsight on Linux**: un cluster Hadoop su Ubuntu. Usare questa opzione se si ha familiarità con Linux o Unix, si esegue la migrazione da una soluzione Hadoop basata su Linux esistente o si desidera un metodo facile per l'integrazione con i componenti dell'ecosistema Hadoop sviluppati per Linux.

* **HDInsight on Windows**: un cluster Hadoop in Windows Server. Usare questa opzione se si ha familiarità con Windows, se si esegue la migrazione da una soluzione Hadoop basata su Windows Hadoop o se si vuole utilizzare .NET o altre tecnologie solo per Windows nel cluster.

Nella tabella seguente vengono confrontate le due opzioni:

Category | Hadoop su Linux | Hadoop su Windows
---------| -------------------| --------------------
**Sistema operativo del cluster** | Supporto a lungo termine (LST) Ubuntu 12.04 | Windows Server 2012 R2
**Tipo di cluster** | Hadoop, Hbase, Storm | Hadoop, Hbase, Storm
**Distribuzione** | Portale di anteprima di Azure, interfaccia della riga di comando di Azure, Azure PowerShell | Portale di Azure, portale di anteprima di Azure, interfaccia della riga di comando di Azure, Azure PowerShell, HDInsight .NET SDK
**Interfaccia utente del cluster** | Ambari | Dashboard del cluster
**Accesso remoto** | Secure Shell (SSH), REST API, ODBC, JDBC | Remote Desktop Protocol (RDP), REST API, ODBC, JDBC



### Hadoop, HBase, Storm, Spark e cluster personalizzati

HDInsight offre configurazioni cluster per carichi di lavoro specifici: Hadoop, HBase o Storm. In alternativa, è possibile [personalizzare i cluster con azioni di script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: (il carico di lavoro "Query"): fornisce un'archiviazione dei dati affidabile con [HDFS](#HDFS), nonché un semplice modello di programmazione [MapReduce](#mapreduce) per elaborare e analizzare i dati in parallelo.

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (il carico di lavoro "NoSQL"): un database NoSQL basato su Hadoop che fornisce accesso rapido e coerenza assoluta per quantità elevate di dati non strutturati e semistrutturati. Può gestire potenzialmente milioni di righe e colonne. Vedere la [panoramica di HBase in HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>**: (il carico di lavoro "Stream"): un sistema di calcolo distribuito in tempo reale per l'elaborazione rapida di grandi flussi di dati. Storm viene offerto come cluster gestito in HDInsight. Vedere [Analizzare i dati del sensore in tempo reale con Storm e Hadoop](hdinsight-storm-sensor-data-analysis.md).

#### Script di personalizzazione di esempio

Le azioni script sono script che vengono eseguiti durante il provisioning del cluster e che possono essere usati per installare componenti aggiuntivi nel cluster. Per i cluster HDInsight basati su Windows, questi sono gli script di PowerShell. Per cluster basati su Linux, questi sono gli script Bash.

Di seguito sono gli script di esempio forniti dal team di HDInsight:

* [Hue](hdinsight-hadoop-hue-linux.md)

	> [AZURE.NOTE]Lo script Hue è disponibile solo per i cluster basati su Linux.
	
* [Giraph,](hdinsight-hadoop-giraph-install-linux.md)

* [R](hdinsight-hadoop-r-scripts-linux.md)

* [Solr](hdinsight-hadoop-solr-install-linux.md)

* [Spark](hdinsight-hadoop-spark-install-linux.md)

Per informazioni su come sviluppare azioni script personalizzate, vedere [Sviluppo di azioni script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

## Quali sono i componenti di Hadoop?

Oltre alle configurazioni complessive precedenti, nei cluster HDInsight sono inoltre inclusi i singoli componenti seguenti.

* **[Ambari](#ambari)**: provisioning, gestione e monitoraggio dei cluster.

	> [AZURE.NOTE]Viene fornito solo un subset dell'API REST Ambari per i cluster HDInsight basati su Windows.

* **[Avro](#avro)** (la libreria Microsoft .NET per Avro): serializzazione dei dati per l'ambiente Microsoft .NET.

* **[Hive e HCatalog](#hive)**: Structured Query Language (SQL), ad esempio l'esecuzione di query e un livello di gestione tabella e archiviazione.

* **[Mahout](#mahout)**: Machine Learning.

* **[MapReduce e YARN](#mapreduce)**: elaborazione distribuita e gestione delle risorse.

* **[Oozie](#oozie)**: gestione dei flussi di lavoro.

* **[Phoenix](#phoenix)**: un livello di database relazionale su HBase.

* **[Pig](#pig)**: script semplice per le trasformazioni MapReduce.

* **[Sqoop](#sqoop)**: importazione ed esportazione dei dati.

* **[Tez](#tez)**: consente di eseguire in modo efficiente a livello di scalabilità processi a elevato utilizzo di dati.

* **[ZooKeeper](#zookeeper)**: coordinamento di processi in sistemi distribuiti.

> [AZURE.NOTE]Per informazioni sui componenti specifici e sulle versioni, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][component-versioning].

###<a name="ambari"></a>Ambari

Apache Ambari viene usato per il provisioning, la gestione e il monitoraggio di cluster Apache Hadoop. Comprende una raccolta di strumenti operatore intuitivi e un set affidabile di API che nascondono la complessità di Hadoop, semplificando le operazioni sui cluster. I cluster HDInsight basati su Linux forniscono sia l'interfaccia utente Web Ambari che l'API REST Ambari, mentre i cluster basati su Windows forniscono un subset dell'API REST.

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

### <a name="mapreduce"></a>MapReduce e YARN
Hadoop MapReduce è un framework software per la scrittura di applicazioni per l'elaborazione di set di Big Data in parallelo. Un processo MapReduce suddivide i set di dati di grandi dimensioni e organizza i dati in coppie chiave-valore per l'elaborazione.

Apache YARN rappresenta MapReduce di nuova generazione (MapReduce 2.0 o MRv2) che suddivide le due attività principali di JobTracker, la gestione delle risorse e la pianificazione/monitoraggio dei processi, in entità separate.

Per altre informazioni su MapReduce, vedere <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> in Hadoop Wiki. Per informazioni su YARN, vedere <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

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


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordina i processi in sistemi distribuiti di grandi dimensioni mediante uno spazio dei nomi gerarchico condiviso di registri di dati (znode). Gli Znode contengono piccole quantità di metainformazioni necessarie per il coordinamento dei processi: stato, posizione, configurazione e così via.

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

Integrare questa introduzione a Hadoop su HDInsight e all'analisi dei Big Data con le risorse seguenti.


### HDInsight in Linux

* [Documentazione HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/): pagina relativa alla documentazione per Azure HDInsight con collegamenti ad articoli, video e altre risorse.

* [Introduzione a HDInsight su Linux](hdinsight-hadoop-linux-tutorial-get-started.md): un'esercitazione introduttiva per il provisioning di cluster HDInsight Hadoop in Linux e l'esecuzione di query Hive di esempio.

* [Introduzione a Storm basato su Linux in HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md): un'esercitazione introduttiva per il provisioning di uno Storm nel cluster HDInsight e l’esecuzione di topologie di esempio Storm.

* [Provisioning di HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md): informazioni su come effettuare il provisioning di un cluster HDInsight Hadoop in Linux tramite il portale di Azure, l’interfaccia della riga di comando di Azure o Azure PowerShell.

* [Uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md): alcuni suggerimenti sull'uso di cluster Hadoop Linux di cui è stato eseguito il provisioning in Azure.

* [Gestire cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md): informazioni su come monitorare e gestire il cluster Hadoop basato su Linux in HDInsight tramite il sito Web Ambari o l'API REST Ambari.


### HDInsight in Windows

* [Documentazione HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/): pagina relativa alla documentazione per Azure HDInsight con collegamenti ad articoli, video e altre risorse.

* [Mappa di apprendimento per HDInsight](hdinsight-learn-map.md): presentazione guidata della documentazione di Hadoop per HDInsight.

* [Introduzione ad Azure HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md): esercitazione introduttiva sull'uso di Hadoop in HDInsight.

* [Eseguire gli esempi in HDInsight](hdinsight-run-samples.md): esercitazione relativa alle procedure per l'esecuzione degli esempi inclusi in HDInsight.

* [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx): documentazione di riferimento per HDinsight SDK.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: altre informazioni sulla raccolta software di Apache Hadoop, un framework che consente l'elaborazione distribuita di set di dati di grandi dimensioni in cluster di computer.

* <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a>: altre informazioni sull'architettura e la struttura di Hadoop Distributed File System, il sistema di archiviazione principale usato dalle applicazioni Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a>: altre informazioni sul framework di programmazione per la scrittura di applicazioni Hadoop in grado di elaborare rapidamente quantità elevate di dati in parallelo su cluster di grandi dimensioni di nodi di calcolo.

### Database SQL in Azure

* [Database SQL di Azure](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx): documentazione MSDN relativa a database SQL.

* [Portale di gestione per database SQL](https://msdn.microsoft.com/library/azure/dn771027.aspx): uno strumento semplificato e di facile utilizzo per la gestione di database SQL nel cloud.

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
 

<!---HONumber=Oct15_HO1-->