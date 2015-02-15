<properties 
	pageTitle="Introduzione ad Hadoop in HDInsight: Analisi dei Big Data nel cloud | Azure" 
	description="Informazioni su come Azure HDInsight usa i cluster Apache Hadoop nel cloud per fornire un framework software per gestire, analizzare e creare report sui Big Data." 
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="cgronlun"/>



# Introduzione ad Hadoop in HDInsight: elaborazione e analisi dei Big Data nel cloud

Viene offerta una panoramica dei componenti HDInsight, della terminologia comune e degli scenari; inoltre, vengono elencate le risorse per l'uso di Hadoop in HDInsight.

Azure HDInsight distribuisce ed eseguire il provisioning dei cluster Apache Hadoop nel cloud, offrendo un framework software progettato per gestire, analizzare e creare report sui Big Data. La memoria centrale di Hadoop fornisce un'archiviazione dei dati affidabile grazie ad Hadoop Distributed File System (HDFS), nonché un modello di programmazione semplice, MapReduce, per elaborare e analizzare in parallelo i dati archiviati nel sistema distribuito. 

### Informazioni sui Big Data
Con Big Data si intendono i dati raccolti in volumi sempre più elevati, a velocità crescenti e per una gamma sempre più ampia di formati non strutturati e contesti semantici variabili. 

I Big Data descrivono un qualsiasi corpo di grandi dimensioni di informazioni digitali, dal testo in un feed Twitter alle informazioni del sensore di un apparecchiatura industriale fino alle informazioni sull'esplorazione e gli acquisti dei clienti in un catalogo online. I Big Data possono essere cronologici (dati archiviati) oppure in tempo reale (trasmessi direttamente dall'origine). 

Per ottenere informazioni con i Big Data su cui sia possibile intervenire o che consentano un'analisi più approfondita, non si devono solo porre le domande corrette e raccogliere i dati rilevanti per il problema da risolvere, ma è anche necessario che i dati siano accessibili, puliti, analizzati e quindi presentati in modo utile. Per queste operazioni può essere utile Hadoop in HDInsight.


## Contenuto dell'articolo

Questo articolo fornisce una panoramica di Hadoop in HDInsight e comprende:

* **[Panoramica dell'ecosistema Hadoop in HDInsight:](#overview)**  HDInsight è la soluzione Hadoop per Azure e fornisce le implementazioni di Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari e così via. HDInsight si integra anche con gli strumenti di business intelligence (BI) come Excel, SQL Server Analysis Services e SQL Server Reporting Services.
* **[Vantaggi di Hadoop nel cloud:](#advantage)** motivi per prendere in considerazione l'implementazione cloud di HDInsight di Hadoop.
* **[Soluzioni HDInsight per l'analisi dei Big Data:](#solutions)** alcuni metodi pratici per far sì che HDInsight risponda alle domande relative all'organizzazione, dall'analisi dei sentimenti Twitter all'analisi dell'efficacia dei sistemi HVAC.
* **[Risorse per ottenere altre informazioni sull'analisi dei Big Data, Hadoop e HDInsight:](#resources)** collegamenti a informazioni aggiuntive.


## <a name="overview"></a>Panoramica dell'ecosistema Hadoop in HDInsight

Hadoop è lo stack di tecnologie in rapida espansione che rappresenta la soluzione più adatta per l'analisi dei Big Data. HDInsight è il framework per l'implementazione cloud Microsoft Azure di Hadoop.
 
I cluster Hadoop in HDInsight usano le versioni della distribuzione Hortonworks Data Platform (HDP) e il set di componenti Hadoop all'interno della distribuzione. Per informazioni sui componenti e sulle versioni nei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][component-versioning]

Apache Hadoop è un framework software per la gestione e l'analisi di Big Data. La funzionalità principale di Apache Hadoop offre un'archiviazione affidabile dei dati, mediante HDFS (Hadoop Distributed File System), e un semplice modello di programmazione MapReduce per l'elaborazione e l'analisi, in parallelo, dei dati archiviati in tale sistema distribuito. HDFS usa la replica dei dati per risolvere problemi relativi a errori hardware generati dalla distribuzione di sistemi altamente distribuiti.

Di seguito sono elencate le tecnologie Hadoop in HDInsight:

* **[Ambari](#ambari):** provisioning dei cluster, gestione e monitoraggio 
* **[Avro](#avro)** (Libreria Microsoft .NET per Avro): serializzazione dei dati per l'ambiente Microsoft .NET 
* **[HBase](#hbase):** database non relazionale per tabelle di dimensioni molto grandi
* **[HDFS](#hdfs):** file system distribuito Hadoop
* **[Hive](#hive):** query simili a SQL
* **[Mahout](#mahout):** Machine Learning
* **[MapReduce e YARN](#mapreduce):** elaborazione distribuita e gestione delle risorse
* **[Oozie](#oozie):** gestione dei flussi di lavoro
* **[Pig](#pig):** script semplice per le trasformazioni MapReduce
* **[Sqoop](#sqoop):** importazione ed esportazione dei dati 
* **[Storm](#storm):** elaborazione in tempo reale per flussi di dati rapidi e di grandi dimensioni
* **[Zookeeper](#zookeeper):** processi coordinati in sistemi distribuiti
 
Altre informazioni sugli **[strumenti di business intelligence](#bi)** che è possibile usare con HDInsight.

###<a name="ambari"></a>Ambari

Apache Ambari viene usato per il provisioning, la gestione e il monitoraggio di cluster Apache Hadoop. Comprende una raccolta di strumenti operatore intuitivi e un set affidabile di API che nascondono la complessità di Hadoop, semplificando le operazioni sui cluster. Vedere  [Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari](../hdinsight-monitor-use-ambari-api/) e <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">i riferimenti all'API Apache Ambari</a>.

### <a name="avro"></a>Avro (Libreria Microsoft .NET per Avro)

La libreria Microsoft .NET per Avro implementa il formato compatto di interscambio dei dati binari Apache Avro per la serializzazione nell'ambiente Microsoft .NET e usa <a target="_blank" href="http://www.json.org/">JSON</a> per definire lo schema indipendente dal linguaggio che sottoscrive l'interoperabilità del linguaggio, ossia che consente di leggere in un linguaggio i dati serializzati in un altro linguaggio. Informazioni dettagliate sul formato sono disponibili nelle <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">specifiche di Apache Avro</a>. 
Il formato dei file Avro supporta il modello di programmazione MapReduce distribuito. I file possono essere suddivisi, ossia è possibile cercare un qualsiasi punto del file e iniziare a leggere da uno specifico blocco. Per informazioni sulle procedure, vedere [Serializzazione dei dati con la libreria Microsoft .NET per Avro](../hdinsight-dotnet-avro-serialization/).

### <a name="hbase"></a>HBase

<a target="_blank" href="http://hbase.apache.org/">Apache HBase</a> è un database non relazionale basato su Hadoop e progettato per grandi quantità di dati non strutturati o semistrutturati. Può gestire potenzialmente milioni di righe e colonne. I cluster HBase in HDInsight sono configurati per archiviare i dati direttamente nell'archiviazione BLOB di Azure con una bassa latenza e un'elasticità ottimizzata. Vedere la [panoramica di HBase in HDInsight](../hdinsight-hbase-overview/).

### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) è un file system distribuito che, insieme a MapReduce e YARN, costituisce il nucleo dell'ecosistema Hadoop. HDFS è il file system standard per i cluster Hadoop in HDInsight.

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> è un software di data warehouse basato su Hadoop che consente di eseguire query e gestire grandi set di dati nella risorsa di archiviazione distribuita con un linguaggio simile a SQL chiamato HiveQL. Analogamente a Pig, Hive è un'astrazione basata su MapReduce e, se eseguito, consente di convertire le query in una serie di processi MapReduce. Hive è concettualmente più simile a un sistema di gestione di database relazionali che a Pig ed è quindi più adatto a essere usato con dati più strutturati. Pig è invece ottimale per dati non strutturati. Vedere [Usare Hive con Hadoop in HDInsight](../hdinsight-use-hive/).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> è una libreria scalabile di algoritmi di Machine Learning eseguita in Hadoop. Usando i principi della statistica, le applicazioni di Machine Learning istruiscono i sistemi in modo che apprendano dai dati e usino i risultati precedenti per determinare i comportamenti futuri. Vedere [Generare consigli sui film usando Mahout in Hadoop](../hdinsight-mahout/).

### <a name="mapreduce"></a>MapReduce e YARN
Hadoop MapReduce è un framework software per la scrittura di applicazioni per l'elaborazione di set di Big Data in parallelo. Un processo MapReduce suddivide i set di dati di grandi dimensioni e organizza i dati in coppie chiave-valore per l'elaborazione. 

Apache YARN rappresenta MapReduce di nuova generazione (MapReduce 2.0 o MRv2) che suddivide le due attività principali di JobTracker, la gestione delle risorse e la pianificazione/monitoraggio dei processi, in entità separate.

Per altre informazioni su MapReduce, vedere <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> in Hadoop Wiki. Per informazioni su YARN, vedere <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> è un sistema di coordinamento dei flussi di lavoro per la gestione dei processi Hadoop. È integrato con lo stack Hadoop e supporta i processi Hadoop per MapReduce, Pig, Hive e Sqoop. Può anche essere usato per pianificare processi specifici di un sistema, come i programmi Java o gli script della shell. Vedere [Uso del coordinatore Oozie basato sul tempo con HDInsight](../hdinsight-use-oozie-coordinator-time/).

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> è una piattaforma di alto livello che consente di eseguire trasformazioni MapReduce complesse in set di dati molto grandi usando un linguaggio di scripting semplice chiamato Pig Latin. Pig converte gli script Pig Latin in modo che possano essere eseguiti in Hadoop. È possibile creare funzioni definite dall'utente (UDFs) per estendere Pig Latin. Vedere [Usare Pig con Hadoop per analizzare un file di log di Apache](../hdinsight-use-pig/).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> è uno strumento che consente di trasferire nel modo più efficiente possibile i dati per operazioni bulk tra Hadoop e database relazionali quali i database SQL o altri archivi di dati strutturati. Vedere [Uso di Sqoop con HDInsight.](../hdinsight-use-sqoop/).

### <a name="storm"></a>Storm
<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a> è un sistema di calcolo distribuito in tempo reale per l'elaborazione rapida di grandi flussi di dati. Storm viene offerto come cluster gestito in HDInsight. Vedere [Analizzare i dati del sensore in tempo reale con Storm e Hadoop](../hdinsight-storm-sensor-data-analysis/).

### <a name="zookeeper"></a>Zookeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a> coordina i processi in sistemi distribuiti di grandi dimensioni mediante uno spazio dei nomi gerarchico condiviso di registri di dati (znode). Gli Znode contengono piccole quantità di metainformazioni necessarie per il coordinamento dei processi: stato, posizione, configurazione e così via. 

### <a name="bi"></a>Strumenti di business intelligence 
Gli strumenti di business intelligence (BI) noti, ad esempio Excel, PowerPivot, SQL Server Analysis Services e Reporting Services, consentono di recuperare, analizzare e creare report di dati integrati con HDInsight usando il componente aggiuntivo Power Query o Microsoft Hive ODBC Driver. 

Questi strumenti BI sono utili per l'analisi dei Big Data:

* <a target="_blank" href="http://www.microsoft.com/it-it/download/details.aspx?id=39379">Scaricare Microsoft Power Query per Excel</a> 
* <a target="_blank" href="http://www.microsoft.com/it-it/download/details.aspx?id=40886">Scaricare Microsoft Hive ODBC Driver</a>
* <a target="_blank" href="http://www.microsoft.com/it-it/server-cloud/solutions/business-intelligence/analysis.aspx">Altre informazioni su SQL Server Analysis Services</a>
* <a target="_blank" href="http://msdn.microsoft.com/it-it/library/ms159106.aspx">Informazioni su SQL Server Reporting Services</a>

## <a name="advantage"></a>Vantaggi di Hadoop nel cloud

All'interno dell'ecosistema cloud Azure, Hadoop in HDInsight offre numerosi vantaggi, tra cui:

* Componenti Hadoop aggiornati. Per dettagli, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][component-versioning]
* Disponibilità e affidabilità elevate dei cluster. Per dettagli, vedere [Disponibilità e affidabilità dei cluster Hadoop in HDInsight](../hdinsight-high-availability/).
* Archiviazione dei dati efficace e conveniente con l'archiviazione BLOB di Azure, un'opzione compatibile con Hadoop. Per dettagli, vedere [Uso dell'archiviazione BLOB di Azure con Hadoop in HDInsight](../hdinsight-use-blob-storage/).
* Integrazione con altri servizi di Azure, inclusi [Siti Web](../documentation/services/websites/) e [Database SQL](../documentation/services/sql-database/).
* Costo iniziale ridotto. Scaricare una [versione di valutazione gratuita](/pricing/free-trial/) o consultare [Dettagli prezzi di HDInsight](../pricing/details/hdinsight/).

Per altre informazioni sui vantaggi di Hadoop in HDInsight, vedere la [pagina delle funzionalità di Azure per HDInsight][marketing-page].

## <a name="solutions"></a>Provare le HDInsight per l'analisi dei Big Data

Analizzare i dati dell'organizzazione per ottenere informazioni dettagliate sulle attività aziendali. Di seguito sono riportati alcuni esempi: 

* [Analizzare i dati del sensore HVAC](../hdinsight-hive-analyze-sensor-data/): informazioni su come analizzare i dati del sensore usando Hive con HDInsight (Hadoop) e quindi visualizzare i dati in Microsoft Excel. In questo esempio Hive viene usato per elaborare i dati cronologici prodotti da sistemi HVAC per individuare quali sistemi non sono in grado di mantenere una temperatura impostata in modo affidabile.
* [Usare Hive con HDInsight per analizzare i log dei siti Web](../hdinsight-hive-analyze-website-log/): informazioni su come usare HiveQL in HDInsight per analizzare i log dei siti Web per ottenere informazioni dettagliate sulla frequenza delle visite di siti Web esterni nell'arco di un giorno e per visualizzare un riepilogo degli errori relativi ai siti Web incontrati dagli utenti.
* [Analizzare i dati del sensore in tempo reale con Storm e HBase in HDInsight (Hadoop)](../hdinsight-storm-sensor-data-analysis/): Informazioni su come compilare una soluzione che usi un cluster Storm in HDInsight per elaborare i dati del sensore dagli hub eventi di Azure e quindi visualizzi i dati del sensore elaborati come informazioni quasi in tempo reale in un dashboard basato su Web.

Per provare Hadoop in HDInsight, vedere gli articoli in "Informazioni di base" nella sezione [Esplorazione della pagina della documentazione di HDInsight](../documentation/services/hdinsight/). Per esempi avanzati, scorrere fino alla sezione Analisi.



## <a name="resources"></a>Risorse per ottenere altre informazioni sull'analisi dei Big Data, Hadoop e HDInsight 

### Hadoop in HDInsight	

* [Documentazione relativa a HDInsight](../documentation/services/hdinsight/): pagina relativa alla documentazione per Azure HDInsight. Include collegamenti ad articoli, video e altre risorse.

* [Mappa di apprendimento per Hadoop in HDInsight](../hdinsight-learn-map): presentazione guidata della documentazione di Hadoop per HDInsight.

* [Introduzione all'uso di Azure HDInsight](../hdinsight-get-started/): esercitazione introduttiva sull'uso di Hadoop in HDInsight.

* [Eseguire gli esempi relativi a HDInsight](../hdinsight-run-samples/): esercitazione relativa alle procedure per l'esecuzione degli esempi inclusi in HDInsight.
	
* [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/it-it/library/dn479185.aspx): documentazione di riferimento per HDinsight SDK.


### Database SQL in Azure	
		
* [Database SQL di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/ee336279.aspx): documentazione MSDN relativa a database SQL.
	
* [Portale di gestione per database SQL](http://msdn.microsoft.com/it-it/library/windowsazure/gg442309.aspx): uno strumento semplificato e di facile utilizzo per la gestione di database, che consente di gestire database SQL nel cloud.

* [Adventure Works per database SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304): pagina per il download del database di esempio per database SQL.	

### Microsoft business intelligence		

* [Connettere Excel a Hadoop mediante Power Query](../hdinsight-connect-excel-power-query/): informazioni sulle procedure di connessione di Excel all'account di archiviazione di Azure in cui vengono archiviati i dati associati al cluster HDInsight mediante Microsoft Power Query per Excel. 

* [Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver](../hdinsight-connect-excel-hive-ODBC-driver/): informazioni su come importare i dati da HDInsight con Microsoft Hive ODBC Driver.

* [Microsoft Business Intelligence (BI)](http://www.microsoft.com/it-it/server-cloud/solutions/business-intelligence/default.aspx): informazioni su Power BI per Office 365, download della versione di valutazione di SQL Server e configurazione di SharePoint Server 2013 e SQL Server BI.
			

### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: ulteriori informazioni sulla raccolta software di Apache Hadoop, un framework che consente l'elaborazione distribuita di set di dati di grandi dimensioni in cluster di computer.	

*  <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html">HDFS</a>: ulteriori informazioni sull'architettura e la struttura di Hadoop Distributed File System (HDFS), il sistema di archiviazione principale usato da applicazioni Hadoop.		

* <a target="_blank" href="http://mapreduce.org/">MapReduce</a>: altre informazioni sul framework di programmazione per la scrittura di applicazioni Hadoop in grado di elaborare rapidamente quantità elevate di dati in parallelo su cluster di grandi dimensioni di nodi di calcolo.	


[marketing-page]: ../services/hdinsight/
[component-versioning]: ../hdinsight-component-versioning/

[zookeeper]: http://zookeeper.apache.org/ 

<!--HONumber=42-->
