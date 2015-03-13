<properties 
	pageTitle="Novità nelle versioni cluster di Hadoop in HDInsight | Azure" 
	description="HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite in qualsiasi momento. Vedere le versioni di Hadoop e di HortonWorks Data Platform (HDP) supportate." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="bradsev"/>


#Novità delle versioni cluster di Hadoop incluse in HDInsight

##Versioni di HDInsight
HDInsight supporta più versioni cluster Hadoop che possono essere distribuite in qualsiasi momento. Ogni versione scelta effettua il provisioning di una versione specifica della distribuzione HDP (Hortonworks Data Platform) e di un set di componenti contenuti in tale distribuzione. Le versioni del componente associate a ciascuna versione del cluster HDInsight sono elencate nella tabella seguente. Notare che la versione cluster predefinita usata da Azure HDInsight è attualmente la 3.1 e, a partire dal 07/11/2014, è basata su HDP 2.1.7.


<table border="1">
<tr><th>Componente</th><th>HDInsight versione 3.1 (predefinita)</th><th>HDInsight versione 3.0</th><th>HDIinsight versione 2.1</th><th>HDInsight versione 1.6</th></tr>
<tr><td>Hortonworks Data Platform (HDP)</td><td>2.1.7</td><td>2.0</td><td>1.3</td><td>1.1</td></tr>
<tr><td>Apache Hadoop e YARN</td><td>2.4.0</td><td>2.2.0</td><td>1.2.0</td><td>1.0.3</td></tr>
<tr><td>Tez</td><td>0.4.0</td><td></td><td></td><td></td></tr>
<tr><td>Apache Pig</td><td>0.12.1</td><td>0.12.0</td><td>0.11.0</td><td>0.9.3</td></tr>
<tr><td>Apache Hive e HCatalog</td><td>0.13.1</td><td>0.12.0</td><td>0.11.0</td><td>0.9.0</td></tr>
<tr><td>HBase </td><td>0.98.0</td><td></td><td></td><td></td></tr>
<tr><td>Apache Sqoop</td><td>1.4.4</td><td>1.4.4</td><td>1.4.3</td><td>1.4.2</td></tr>
<tr><td>Apache Oozie</td><td>4.0.0</td><td>4.0.0</td><td>3.3.2</td><td>3.2.0</td></tr>
<tr><td>Apache HCatalog</td><td>Unito a Hive</td><td>Unito a Hive</td><td>Unito a Hive</td><td>0.4.1</td></tr>
<tr><td>Apache Templeton</td><td>Unito a Hive</td><td>Unito a Hive</td><td>Unito a Hive</td><td>0.1.4</td></tr>
<tr><td>Ambari</td><td>>=1.5.1</td><td>1.4.1</td><td>API v1.0</td><td></td></tr>
<tr><td>Zookeeper</td><td>3.4.5</td><td>3.4.5</td><td></td><td></td></tr>
<tr><td>Storm</td><td>0.9.1</td><td></td><td></td><td></td></tr>
<tr><td>Mahout</td><td>0.9.0</td><td></td><td></td><td></td></tr>
<tr><td>Phoenix</td><td>4.0.0.2.1.7.0-2162</td><td></td><td></td><td></td></tr>
</table>


**Ottenere le informazioni sulla versione del componente corrente**

Le versioni dei componenti associate alle versioni cluster HDInsight potrebbero subire modifiche nei futuri aggiornamenti a HDInsight. Per determinare i componenti disponibili e verificare le versioni in uso per un cluster, usare l'API REST Ambari. È possibile usare il comando **GetComponentInformation** per recuperare informazioni su un componente del servizio. Per informazioni dettagliate, vedere la [documentazione di Ambari][ambari-docs]. Per ottenere queste informazioni, è inoltre possibile accedere a un cluster usando Desktop remoto ed esaminare direttamente i contenuti della directory "C:\apps\dist".


**Note sulla versione**	

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-release-notes/).

### Selezionare una versione durante il provisioning di un cluster HDInsight

Quando si crea un cluster tramite i cmdlet di HDInsight PowerShell o .NET SDK per HDInsight, è possibile scegliere la versione per il cluster Hadoop di HDInsight usando il parametro "Version".

Se si usa l'opzione **Creazione rapida**, per impostazione predefinita si otterrà la versione 3.1 di HDInsight che crea il cluster Hadoop. Se si usa l'opzione **Creazione personalizzata** dal portale di Azure, è possibile scegliere la versione del cluster che si vuole distribuire dall'elenco a discesa **Versione HDInsight** nella pagina **Dettagli cluster**. 

##Funzionalità in primo piano
Per impostazione predefinita, Azure HDInsight ora supporta Hadoop 2.4 con cluster HDInsight versione 3.1 (con Hortonworks Data Platform 2.1.7), che sfrutta al meglio questa piattaforma fornendo ai clienti diversi vantaggi importanti, tra cui in particolare:

- **Azione di script (anteprima)**: Anteprima della funzionalità di personalizzazione dei cluster, che permette la modifica di cluster Hadoop in modo arbitrario tramite script personalizzati. Grazie a questa nuova funzionalità, gli utenti possono provare a usare e distribuire nei cluster HDInsight in Azure i progetti disponibili nell'ecosistema Apache Hadoop. Questa funzionalità di personalizzazione è disponibile in tutti i tipi di cluster HDInsight, inclusi Hadoop, HBase e Storm.

- **HBase**: HBase è un database NoSQL a bassa latenza che permette di eseguire l'elaborazione transazionale online dei Big Data. HBase è offerto come cluster gestito integrato nell'ambiente di Azure. I cluster sono configurati per l'archiviazione dei dati direttamente nell'archiviazione BLOB di Azure, che offre bassa latenza e maggiore flessibilità nelle opzioni relative a prestazioni e costi. Ciò permette ai clienti di creare siti Web interattivi da usare con grandi set di dati, per creare servizi che archiviano dati di sensori e telemetria da milioni di endpoint e per analizzare questi dati tramite processi Hadoop.

- **Apache Phoenix**: Apache Phoenix è un livello query SQL su HBase. Supporta un subset limitato della specifica del linguaggio di query SQL che include supporto per gli indici secondari. Viene fornito come driver JDBC integrato nel client che ha come destinazione le query a bassa latenza su dati HBase. Apache Phoenix accetta la query SQL, la compila in una serie di acquisizioni HBase e chiamate del coprocessore, quindi produce normali set di risultati JDBC. Apache Phoenix è un livello di database relazionale su HBase. Viene fornito come driver JDBC integrato nel client che ha come destinazione le query a bassa latenza su dati HBase. Apache Phoenix accetta la query SQL, la compila in una serie di acquisizioni HBase, quindi orchestra l'esecuzione di tali acquisizioni per produrre normali set di risultati JDBC.

- **Dashboard cluster**: una nuova applicazione Web distribuita nel cluster HDInsight.  È possibile usarlo per eseguire query Hive, verificare i log dei processi ed esplorare l'archiviazione BLOB di Azure. L'URL usato per accedere all'applicazione Web è <*NomeCluster*>.azurehdinsight.net.

- **Libreria Microsoft Avro**: questa libreria implementa il sistema di serializzazione dei dati Apache Avro per l'ambiente Microsoft .NET. Apache Avro offre un formato compatto di interscambio dei dati binari per la serializzazione e usa JSON per definire lo schema indipendente dal linguaggio che sottoscrive l'interoperabilità del linguaggio. I dati serializzati in un unico linguaggio possono essere letti in un altro linguaggio. I formati attualmente supportati sono C, C++, C#, Java, PHP, Python e Ruby. Il formato di serializzazione di Apache Avro è ampiamente usato in Azure HDInsight per rappresentare strutture dei dati complesse in un processo MapReduce di Hadoop.

- **YARN**: nuovo framework di gestione delle applicazioni distribuito di utilizzo generale che ha sostituito il classico framework Apache Hadoop MapReduce per l'elaborazione dei dati nei cluster Hadoop. Funge di fatto da sistema operativo di Hadoop, trasformandolo da piattaforma dati monouso per l'elaborazione batch in una piattaforma multiuso che consente l'elaborazione batch, interattiva, online e di flussi. Questo nuovo framework di gestione migliora la scalabilità e l'utilizzo dei cluster in base a criteri quali garanzie di capacità, priorità e contratti di servizio.

- **Tez (solo HDInsight 3.1)**: Un framework generico e personalizzabile che crea attività di elaborazione dati semplificate in carichi di lavoro in scala ridotta e su larga scala in Hadoop. Consente di eseguire un grafo aciclico diretto (DAG) delle attività relative a un singolo processo, in modo che i progetti presenti nell'ecosistema di Apache Hadoop, ad esempio Apache Hive e Apache Pig, possano soddisfare i requisiti riguardanti i tempi di risposta con interazione umana e l'estrema velocità effettiva a livello di petabyte. Notare che Hive 0.13 consente di eseguire le query Hive su Tez, piuttosto che su MapReduce.

- **Disponibilità elevata**: È stato aggiunto un secondo nodo head ai cluster Hadoop distribuiti da HDInsight al fine di aumentare la disponibilità del servizio. Le implementazioni standard dei cluster Hadoop hanno in genere un singolo nodo head. HDInsight elimina questo singolo punto di errore con l'aggiunta di un nodo head secondario. Il passaggio alla nuova configurazione del cluster HA non modifica il prezzo del cluster, a meno che i clienti non eseguano il provisioning dei cluster con un nodo head molto grande al posto del nodo di dimensioni predefinite.

- **Prestazioni di Hive**: considerevoli miglioramenti ai tempi di risposta alle query Hive (fino a 40 volte) e alla compressione dei dati (fino all'80%) mediante il formato **ORC (Optimized Row Columnar)**.

- **Pig, Sqoop, Oozie, Ambari**: aggiornamenti di versione dei componenti per il cluster HDInsight versione 3.0 (HDP 2.0/Hadoop 2.2), che garantiscono parità con il cluster HDInsight versione 2.1 (HDP 1.3/Hadoop 1.2). Per le specifiche, vedere più avanti le tabelle relative alle versioni.

- **Mahout**: questa libreria scalabile di algoritmi Machine Learning viene preinstallata sui cluster Hadoop di HDInsight 3.1. In questo modo, è possibile eseguire processi Mahout senza dover configurare ulteriormente i cluster.

- **Supporto della rete virtuale**: I cluster HDInsight possono essere usati con la rete virtuale di Azure per supportare l'isolamento delle risorse cloud o gli scenari ibridi che collegano le risorse cloud con quelle del data center.


## Versioni supportate
Nella tabella seguente sono elencate le versioni di HDInsight attualmente disponibili, le corrispondenti versioni HDP (Hortonworks Data Platform) usate e le date di rilascio. Se note, verranno indicate anche la data di scadenza del supporto e la data in cui le versioni verranno dichiarate obsolete. Tenere presente quanto segue:

* i cluster ad alta disponibilità con due nodi head vengono distribuiti per impostazione predefinita per i cluster HDInsight 2.1, 3.0 e 3.1. Non sono disponibili per i cluster HDInsight 1.6.
* Quando il supporto per una particolare versione sarà scaduto, potrebbe non essere disponibile attraverso il portale di gestione di Azure. Nella tabella seguente sono indicate le versioni disponibili sul portale di gestione di Azure. Le versioni cluster continueranno a essere disponibili usando il parametro `Version` nel comando [New-AzureHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) di PowerShell e .NET SDK fino alla data in cui verrà dichiarata obsoleta. 

<table border="1">
<tr><th>Versione HDInsight</th><th>Versione HDP</a><th>Disponibilità elevata</th></th><th>Data di rilascio</th><th>Disponibile sul portale di gestione di Azure</th><th>Data di scadenza del supporto</th><th>Data di dichiarazione obsolescenza</th></tr>
<tr><td>HDI 3.1</td><td>HDP 2.1</td><td>Sì</td><td></td><td>Sì</td><td></td><td></td></tr>
<tr><td>HDI 3.0</td><td>HDP 2.0</td><td>Sì</td><td>11/02/2014</td><td>Sì</td><td>17/09/2014</td><td>30/06/2015</td></tr>
<tr><td>HDI 2.1</td><td>HDP 1.3</td><td>Sì</td><td>28/10/2013</td><td>No</td><td>12/05/2014</td><td>31/05/2015</td></tr>
<tr><td>HDI 1.6</td><td>HDP 1.1</td><td>No</td><td>28/10/2013</td><td>No</td><td>26/04/2014</td><td>31/05/2015</td></tr>
</table><br>

**Distribuzione di cluster non predefiniti**	

La creazione di cluster HDInsight 3.1 in Hadoop 2.4 è supportata dal portale di Azure, dall'SDK di HDinsight e da Azure PowerShell. Notare che i cluster HDInsight 3.1 sono stati creati per impostazione predefinita su Hadoop 2.4, quindi gli utenti devono usare l'opzione **Creazione personalizzata** nel portale per specificare altre versioni del cluster HDInsight, se è necessario crearle.

### Contratto di servizio per le versioni cluster HDInsight

Il Contratto di servizio viene definito come "finestra di supporto". Il termine finestra di supporto indica il periodo di tempo in cui una versione cluster HDInsight è supportata dal Servizio Supporto Tecnico Clienti Microsoft.  Un cluster HDInsight non è compreso nella finestra di supporto se la **data di scadenza del supporto** della versione ha superato la data corrente.  Nella tabella precedente è disponibile un elenco di versioni cluster HDInsight supportate.  La data di scadenza del supporto per una determinata versione di HDInsight X (una volta che sarà disponibile una versione X+1 più recente) viene calcolata come l'ultima di:  

- Formula 1:  aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X
- Formula 2: aggiungere 90 giorni alla data in cui il cluster HDInsight versione X+1 (la versione successiva a X) diventa disponibile nel portale di gestione di Azure.

La **data di dichiarazione obsolescenza** è la data dopo la quale non è possibile creare la versione del cluster su HDInsight.

> [AZURE.NOTE] Entrambi i cluster HDInsight 2.1 e 3.0 eseguono il sistema operativo guest di Azure [Family 4](http://msdn.microsoft.com/library/azure/ee924680.aspx#explanation) che usa la versione a 64 bit di Windows Server 2012 R2 e supporta .NET Framework 4.0, 4.5 e 4.5.1. 

## Note sulla versione di Hortonworks associate alle versioni di HDInsight##

* Il cluster HDInsight versione 3.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Si tratta del cluster Hadoop predefinito creato quando si usa il portale di Azure HDInsight dopo il 07/11/2014. I cluster HDInsight 3.1 creati prima del 07/11/2014 sono basati su [Hortonworks Data Platform 2.1.1][hdp-2-1-1]. 

* Il cluster HDInsight versione 3.0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.0][hdp-2-0-8].

* Il cluster HDInsight versione 2.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* Il cluster HDInsight versione 1.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.1][hdp-1-1-0]. 


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/ 
<!--HONumber=42-->
