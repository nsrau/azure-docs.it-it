<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Azure HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Azure HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Introduzione ad Azure HDInsight
===============================

Panoramica
----------

Azure HDInsight è un servizio che consente di eseguire la distribuzione e il provisioning di cluster Apache™ Hadoop® nel cloud, mediante un framework software progettato per la gestione, l'analisi e la creazione di report di Big Data.

### Big Data

I dati sono definiti "Big Data" per indicare che vengono raccolti in volumi sempre più elevati, a velocità crescenti e per una gamma sempre più ampia di formati e contesti semantici variabili. La raccolta di Big Data non costituisce di per sé un valore aggiunto per un'azienda. Per fare in modo che i Big Data offrano valore aggiunto sotto forma di informazioni su cui è possibile intervenire o che consentono di approfondire determinati elementi, non è solo necessario porre le domande corrette e raccogliere i dati rilevanti per il problema da risolvere, ma è anche necessario che i dati siano accessibili, puliti, analizzati e quindi presentati in modo utile, spesso insieme a dati di origini diverse, al fine di offrire una prospettiva e un contesto sotto forma di mashup.

### Apache Hadoop

Apache Hadoop è un framework software che semplifica la gestione e l'analisi di Big Data. La funzionalità principale di Apache Hadoop offre un'archiviazione affidabile dei dati, mediante HDFS (Hadoop Distributed File System), e un semplice modello di programmazione MapReduce per l'elaborazione e l'analisi, in parallelo, dei dati archiviati in tale sistema distribuito. HDFS utilizza la replica dei dati per risolvere problemi relativi a errori hardware generati dalla distribuzione di sistemi altamente distribuiti.

### MapReduce

Per semplificare la complessità dell'analisi di dati non strutturati da origini diverse, il modello di programmazione MapReduce offre un'astrazione di base che sottoscrive la chiusura per operazioni di mapping e riduzione. Il modello di programmazione MapReduce visualizza tutti i propri processi come calcoli su set di dati costituiti da coppie chiave-valore. È pertanto necessario che i file di input e output includano set di dati costituiti solo da coppie chiave-valore. La conseguenza principale di questo vincolo è rappresentata dal fatto che i processi MapReduce sono componibili.

Altri progetti correlati a Hadoop, ad esempio Pig e Hive, vengono generati sopra HDFS e sul framework di MapReduce. Questi tipi di progetti vengono utilizzati per offrire un modo più semplice per la gestione di un cluster rispetto all'utilizzo diretto dei programmi MapReduce. Pig, ad esempio, consente di scrivere programmi mediante un linguaggio procedurale denominato Pig Latin. Tali programmi vengono compilati in programmi MapReduce nel cluster. Offre inoltre controlli Fluent per la gestione dei flussi di dati. Hive è un'infrastruttura di data warehouse che offre un'astrazione di tabella per i dati disponibili in file archiviati in un cluster, consentendo quindi di eseguire query su tali dati mediante istruzioni analoghe a SQL in un linguaggio dichiarativo denominato HiveQL.

### HDInsight

Azure HDInsight rende Apache Hadoop disponibile come servizio nel cloud, rendendo disponibile il framework software HDFS/MapReduce e i progetti correlati quali Pig e Hive in un ambiente più semplice, scalabile ed efficiente a livello di costi.

Uno dei vantaggi principali offerti da HDInsight consiste nella modalità di gestione e archiviazione dei dati. HDInsight utilizza l'archivio BLOB di Azure come file system predefinito. L'archiviazione BLOB e HDFS sono file system distinti, ottimizzati rispettivamente per l'archiviazione di dati e per l'esecuzione di calcoli su tali dati.

-   L'archiviazione BLOB di Azure offre un'opzione di archiviazione a scalabilità e disponibilità elevata, a basso costo e condivisibile per i dati che devono essere elaborati mediante HDInsight.
-   I cluster Hadoop distribuiti da HDInsight su HDFS sono ottimizzati per l'esecuzione di attività di calcolo di MapReduce sui dati.

I cluster HDInsight vengono distribuiti in Azure su nodi di calcolo per eseguire attività di MapReduce e possono essere rilasciati dagli utenti al termine di tali attività. Mantenere i dati nei cluster HDFS dopo il completamento dei calcoli rappresenterebbe una soluzione di archiviazione dei dati molto costosa. L'archiviazione BLOB è una soluzione Azure per l'archiviazione affidabile e l'utilizzo generale. L'archiviazione dei dati nell'archiviazione BLOB consente pertanto l'eliminazione sicura dei cluster utilizzati per i calcoli, senza perdita di dati utente. L'archiviazione BLOB, tuttavia, non è solo una soluzione a basso costo, ma include un'interfaccia completa per il file system HDFS che offre un'esperienza lineare ai clienti tramite l'abilitazione dell'insieme completo di componenti nell'ecosistema Hadoop per il funzionamento diretto, per impostazione predefinita, sui dati gestiti.

HDInsight utilizza Azure PowerShell per configurare, eseguire e per effettuare azioni di post-elaborazione su processi Hadoop. In HDInsight è inoltre disponibile un connettore Sqoop, che può essere utilizzato per importare dati da un database SQL di Azure in HDFS oppure per esportare i dati in un database SQL di Azure da HDFS.

Per l'importazione di dati da Azure HDInsight o da qualsiasi HDFS in Excel è possibile utilizzare Microsoft Power Query per Excel. Tale componente aggiuntivo ottimizza l'esperienza BI in modalità self-service in Excel semplificando l'individuazione di dati e l'accesso a una vasta gamma di origini dati. Oltre a Power Query, è possibile utilizzare Microsoft Hive ODBC Driver per integrare gli strumenti di business intelligence (BI), ad esempio Excel, SQL Server Analysis Services e Reporting Services, in modo da semplificare l'analisi dei dati end-to-end.

### Descrizione generale

In questo argomento viene illustrato l'ecosistema di Hadoop supportato da HDInsight, vengono presentati gli scenari di utilizzo principali per HDInsight e vengono fornite indicazioni relative a ulteriori risorse. L'argomento include le sezioni seguenti:

-   [Ecosistema di Hadoop su HDInsight](#Ecosystem): HDInsight offre implementazioni di Pig, Hive e Sqoop e supporta altri strumenti di business intelligence, quali Excel, SQL Server Analysis Services e Reporting Services, integrati con l'archiviazione BLOB o HDFS e il framework di MapReduce mediante Power Query o Microsoft Hive ODBC Driver. In questa sezione vengono illustrati i processi che verranno gestiti da tali programmi nell'ecosistema di Hadoop.

-   [Scenari Big Data per HDInsight](#Scenarios): In questa sezione vengono esaminati i tipi di processo per cui è consigliabile utilizzare la tecnologia HDInsight.

-   [Risorse per HDInsight](#Resources): In questa sezione viene indicato dove trovare risorse rilevanti per ottenere ulteriori informazioni.

Ecosistema di Hadoop su Azure
-----------------------------

### Introduzione

HDInsight offre un framework che implementa la soluzione Microsoft basata sul cloud per la gestione di Big Data. Questo ecosistema federato è in grado di gestire e analizzare quantità elevate di dati, avvalendosi delle capacità di elaborazione parallela offerte dal modello di programmazione di MapReduce. Le tecnologie Hadoop compatibili con Apache e utilizzabili con HDInsight vengono elencate e descritte brevemente in questa sezione.

In HDInsight sono disponibili implementazioni di Hive e Pig per l'integrazione di capacità di elaborazione di dati e di data warehouse. La soluzione Microsoft per Big Data interagisce con strumenti di business intelligence Microsoft, quali SQL Server Analysis Services, Reporting Services, PowerPivot ed Excel. È pertanto possibile eseguire operazioni dirette di business intelligence su dati archiviati e gestiti da HDInsight nell'archiviazione BLOB.

Altre tecnologie compatibili con Apache e tecnologie simili incluse nell'ecosistema di Hadoop e progettate per l'esecuzione su cluster Hadoop sono inoltre disponibili per il download e per l'utilizzo con HDInsight. Tali tecnologie includono le tecnologie open source, ad esempio Sqoop, che consentono l'integrazione di HDFS con gli archivi di dati relazionali.

### Pig

Pig è una piattaforma di livello elevato per l'elaborazione di Big Data su cluster Hadoop ed è costituito da un linguaggio per flusso di dati, denominato Pig Latin, che supporta la scrittura di query su set di dati di grandi dimensioni, e da un ambiente di esecuzione che consente di eseguire programmi da una console. I programmi in Pig Latin sono costituiti da una serie di trasformazioni di set di dati convertita in modo non visibile all'utente in una serie di programmi MapReduce. Le astrazioni di Pig Latin offrono strutture di dati più avanzate rispetto a MapReduce, e svolgono per Hadoop la stessa funzione svolta da SQL per i sistemi RDBMS (Relational Database Management Systems). Pig Latin è completamente estensibile. È possibile chiamare funzioni definite dall'utente (UDF, User Defined Function), scritte in Java, Python, Ruby, C\# o JavaScript, per personalizzare ogni fase del percorso di elaborazione durante la composizione dell'analisi. Per ulteriori informazioni, vedere la [pagina introduttiva su Apache Pig](http://pig.apache.org/).

### Hive

Hive è un data warehouse distribuito che consente la gestione di dati archiviati in HDFS. È il motore di query di Hadoop. Hive è stato progettato per business analyst con competenze avanzate in ambito SQL per offrire un'interfaccia analoga a quella di SQL e un modello di dati relazionale. Hive utilizza un linguaggio denominato HiveQL, un dialetto di SQL. Analogamente a Pig, Hive è un'astrazione basata su MapReduce e, se eseguito, consente di convertire le query in una serie di processi MapReduce. Gli scenari relativi a Hive sono concettualmente simili a quelli per RDBMS e sono pertanto appropriati per l'utilizzo con dati più strutturati. Pig è invece ottimale per dati non strutturati. Per ulteriori informazioni, vedere la [pagina introduttiva su Apache Hive](http://hive.apache.org/).

### Sqoop

Sqoop è uno strumento che consente di trasferire nel modo più efficiente possibile i dati per operazioni bulk tra Hadoop e database relazionali quali i database SQL o altri archivi di dati strutturati. È possibile utilizzare Sqoop per importare dati da archivi esterni di dati strutturati in HDFS o in sistemi correlati, ad esempio Hive. Sqoop consente inoltre di estrarre dati da Hadoop ed esportare i dati estratti in database relazionali esterni, data warehouse aziendali o qualsiasi tipo di archivio di dati strutturati. Per ulteriori informazioni, vedere il sito Web relativo ad [Apache Sqoop](http://sqoop.apache.org/).

### Strumenti e connettori di business intelligence

Gli strumenti di business intelligence (BI) noti, ad esempio Excel, PowerPivot, SQL Server Analysis Services e Reporting Services, consentono di recuperare, analizzare e creare report di dati integrati con HDInsight utilizzando il componente aggiuntivo Power Query o Microsoft Hive ODBC Driver.

-   È possibile scaricare Microsoft Power Query per Excel dall'[Area download Microsoft](http://go.microsoft.com/fwlink/?LinkID=286689).

-   Microsoft Hive ODBC Driver è disponibile per il download da questo [sito per il download](http://go.microsoft.com/fwlink/?LinkID=286698).

-   Per informazioni su Analysis Services, vedere [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx).

-   Per informazioni su Reporting Services, vedere [SQL Server 2012 Reporting](http://www.microsoft.com/en-us/sqlserver/solutions-technologies/business-intelligence/reporting.aspx).

Scenari Big Data per HDInsight
------------------------------

Uno scenario di esempio che presenta un caso di utilizzo di HDInsight è costituito da un'analisi ad hoc, in modalità batch, su un intero data set non strutturato archiviato in nodi di Azure che non necessita di aggiornamenti frequenti.

Tali condizioni sono applicabili a svariate attività in ambito aziendale, scientifico e governativo, inclusi ad esempio il monitoraggio della supply chain per la vendita al dettaglio, dei modelli di compravendita sospetti in ambito finanziario, dei modelli di domanda per le imprese di pubblici servizi, della qualità di aria e acqua rilevata da svariati sensori ambientali oppure di tendenze in ambito criminale nelle aree metropolitane.

HDInsight e le tecnologie Hadoop in generale risultano ottimali per la gestione di una quantità elevata di dati registrati o archiviati che non richiedono aggiornamenti frequenti dopo la scrittura e vengono letti spesso, in genere ai fini di un'analisi completa. Tale scenario è complementare a dati che vengono gestiti in modo migliore da RDBMS, che necessita di quantità minori di dati (gigabyte invece di petabyte) e che necessita in modo continuativo di aggiornamento o di esecuzione di query per punti dati specifici all'interno del set di dati completo. RDBMS è la soluzione ideale per dati strutturati organizzati e archiviati in base a uno schema fisso. MapReduce risulta ottimale per dati non strutturati senza schema predefinito, poiché è in grado di interpretare i dati durante l'elaborazione.

Passaggi successivi: Risorse per HDInsight
------------------------------------------

**Microsoft: HDInsight**

-   [Documentazione relativa a HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601): pagina relativa alla documentazione per Azure HDInsight. Include collegamenti ad articoli, video e altre risorse.

-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/): esercitazione che fornisce indicazioni rapide per iniziare a utilizzare HDInsight.

-   [Esecuzione degli esempi relativi a HDInsight](/en-us/manage/services/hdinsight/howto-run-samples/): esercitazione relativa alle procedure per l'esecuzione degli esempi inclusi in HDInsight.

-   [Big Data e Azure](http://www.windowsazure.com/it-it/home/scenarios/big-data/): scenari relativi a Big Data, che illustrano i risultati che è possibile ottenere con Azure.

-   [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx): documentazione di riferimento per HDinsight SDK.

**Microsoft: Windows e database SQL**

-   [Home page di Azure](https://www.windowsazure.com/en-us/): scenari, iscrizione alla versione di valutazione gratuita, strumenti per lo sviluppo e documentazione necessaria per iniziare a creare applicazioni.

-   [Database SQL di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/ee336279.aspx): documentazione MSDN relativa a database SQL.

-   [Portale di gestione per database SQL](http://msdn.microsoft.com/it-it/library/windowsazure/gg442309.aspx): uno strumento semplificato e di facile utilizzo per la gestione di database, che consente di gestire database SQL nel cloud.

-   [Adventure Works per database SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304): pagina per il download del database di esempio per database SQL.

**Microsoft: business intelligence**

-   [Connessione di Excel a HDInsight mediante Power Query](/en-us/manage/services/hdinsight/connect-excel-with-power-query/): informazioni sulle procedure di connessione di Excel all'account di archiviazione di Azure in cui vengono archiviati i dati associati al cluster HDInsight mediante Microsoft Power Query per Excel.

-   [Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver](/en-us/manage/services/hdinsight/connect-excel-with-hive-ODBC/): informazioni sulle procedure di importazione di dati da Azure HDInsight mediante Microsoft Hive ODBC Driver.

-   [PowerPivot per Microsoft BI](http://www.microsoft.com/en-us/bi/PowerPivot.aspx): informazioni sul download e informazioni generali sullo strumento avanzato per il mashup e l'esplorazione dei dati.

-   [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx): informazioni sul download di una copia di valutazione di SQL Server 2012 e informazioni sulle procedure per la creazione di soluzioni analitiche complete e di livello aziendale che consentono di ottenere osservazioni su cui è possibile intervenire.

-   [SQL Server 2012 Reporting](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx): informazioni sul download di una copia di valutazione di SQL Server 2012 e informazioni sulle procedure per la creazione di soluzioni complete e a scalabilità elevata che supportano il processo decisionale in tempo reale nell'intera azienda.

**Apache Hadoop**:

-   [Apache Hadoop](http://hadoop.apache.org/): ulteriori informazioni sulla raccolta software di Apache Hadoop, un framework che consente l'elaborazione distribuita di set di dati di grandi dimensioni in cluster di computer.

-   [HDFS](http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html): ulteriori informazioni sull'architettura e la struttura di Hadoop Distributed File System (HDFS), il sistema di archiviazione principale utilizzato da applicazioni Hadoop.

-   [MapReduce](http://mapreduce.org/): ulteriori informazioni sul framework di programmazione per la scrittura di applicazioni Hadoop in grado di elaborare rapidamente quantità elevate di dati in parallelo su cluster di grandi dimensioni di nodi di calcolo.


