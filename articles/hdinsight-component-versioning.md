<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in the cluster versions provided by HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

Novità delle versioni cluster incluse in HDInsight
==================================================

Azure HDInsight supporta Hadoop 2.2 con la versione cluster HDinsight 3.0 e sfrutta al meglio questa piattaforma fornendo ai clienti diversi vantaggi importanti, tra cui in particolare:

-   Hive: considerevoli miglioramenti ai tempi di risposta alle query Hive (fino a 40 volte) e alla compressione dei dati (fino all'80%) mediante il formato ORC (Optimized Row Columnar).

-   YARN: nuovo framework di gestione delle applicazioni distribuito di utilizzo generale che ha sostituito il classico framework Apache Hadoop MapReduce per l'elaborazione dei dati nei cluster Hadoop. Funge di fatto da sistema operativo di Hadoop, trasformandolo da piattaforma dati monouso per l'elaborazione batch in una piattaforma multiuso che consente l'elaborazione batch, interattiva, online e di flussi. Questo nuovo framework di gestione migliora la scalabilità e l'utilizzo dei cluster in base a criteri quali garanzie di capacità, priorità e contratti di servizio.

-   Pig, Sqoop, Qozie, Ambari: aggiornamenti di versione dei componenti per il cluster HDinsight versione 3.0 (HDP 2.0), che garantiscono parità con il cluster HDinsight versione 2.1 (HDP 1.3). Per le specifiche, vedere più avanti le tabelle relative alle versioni. Si noti che Hbase, Mahout, Flume non sono inclusi.

**Distribuzione**
La creazione di cluster HDInsight 3.0 in Hadoop 2.2 è supportata dal portale Azure, dall'SDK di HDinsight e da Azure PowerShell.

**Disponibilità globale**
Con il rilascio di Azure HDInsight in Hadoop 2.2, Microsoft ha reso HDInsight disponibile in tutte le principali aree geografiche di Azure, esclusa la Cina continentale. In particolare, sono stati collegati i data center dell'Europa occidentale e dell'Asia sudorientale. Ciò consente ai clienti di individuare i cluster in un data center chiuso e potenzialmente situato in una zona con analoghi requisiti di conformità.

**Modifiche di interruzione**
Nei cluster HDInsight 3.0 è supportata solo la sintassi "wasb://". La sintassi "asv://", meno recente, è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive. Ciò vuol dire che i processi inviati a un cluster HDInsight 3.0 che utilizzano esplicitamente la sintassi "asv://" avranno esito negativo. Sarà pertanto necessario utilizzare la sintassi wasb://. Avrà inoltre esito negativo qualsiasi processo inviato a un cluster HDInsight 3.0 e creato con un metastore esistente che contiene riferimenti espliciti a risorse che utilizzano la sintassi asv://. Sarà necessario ricreare i metastore in modo da fare riferimento alle risorse utilizzando la sintassi wasb://.

Versioni di HDInsight
---------------------

HDInsight supporta più versioni cluster Hadoop che possono essere distribuite in qualsiasi momento. Ogni versione scelta effettua il provisioning di una versione specifica della distribuzione HDP (HortonWorks Data Platform) e di un set di componenti contenuti in tale distribuzione.

### Versione cluster 3.0

Azure HDInsight supporta Hadoop 2.2. Si basa su Hortonworks Data Platform versione 2.0 e offre i servizi Hadoop con le versioni dei componenti elencate nella tabella seguente:

<table>
<tr><th>Componente</th><th>Versione</th></tr>
<tr><td>Apache Hadoop</td><td>2.2.0</td></tr>
<tr><td>Apache Hive</td><td>0.12.0</td></tr>
</table>

### Versione cluster 2.1

La versione cluster predefinita utilizzata da [Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) è la 2.1. Si basa su Hortonworks Data Platform versione 1.3.0 e offre i servizi Hadoop con le versioni dei componenti elencate nella tabella seguente:

<table>
<tr><th>Componente</th><th>Versione</th></tr>
<tr><td>Apache Hadoop</td><td>1.2.0</td></tr>
<tr><td>Apache Hive</td><td>0.11.0</td></tr>
</table>

### Versione cluster 1.6

È disponibile anche il cluster [Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) versione 1.6. Si basa su Hortonworks Data Platform versione 1.1.0 e offre i servizi Hadoop con le versioni dei componenti elencate nella tabella seguente:

<table>
<tr><th>Componente</th><th>Versione</th></tr>
<tr><td>Apache Hadoop</td><td>1.0.3</td>
<tr><td>Apache Hive</td><td>0.9.0</td></tr>
</table>

### Selezionare una versione durante il provisioning di un cluster HDInsight

Quando si crea un cluster tramite i cmdlet di HDInsight PowerShell o .NET SDK per HDInsight, è possibile scegliere la versione per il cluster HDInsight Hadoop utilizzando il parametro "Version".

Se si utilizza l'opzione **Creazione rapida**, per impostazione predefinita si otterrà la versione 2.1 del cluster HDInsight Hadoop. Se si utilizza l'opzione **Creazione personalizzata** dal portale Azure, è possibile scegliere la versione del cluster che si desidera distribuire dall'elenco a discesa **Versione HDInsight** nella pagina **Dettagli cluster**. La versione 3.0 del cluster HDInsight Hadoop è disponibile come opzione solo nella procedura guidata **Creazione personalizzata**.

![HDI.Versioning.VersionScreen](./media/hdinsight-component-versioning/hdi-versioning-version-screen.png)

Versioni supportate
-------------------

Nella tabella seguente sono elencate le versioni di HDInsight attualmente disponibili, le corrispondenti versioni HDP (Hortonworks Data Platform) utilizzate e le date di rilascio. Se note, verranno indicate anche le date in cui le versioni verranno dichiarate obsolete.

<table>
<tr><th>Versione di HDInsight</th><th>Versione HDP</th><th>Data di rilascio</th></tr>
<tr><td>HDI 3.0</td><td>HDP 2.0</td><td>11/02/2014</td></tr>
<tr><td>HDI 2.1</td><td>HDP 1.3</td><td>28/10/2013</td></tr>
<tr><td>HDI 1.6</td><td>HDP 1.1</td><td>28/10/2013</td></tr>
</table>

### Contratto di servizio per le versioni cluster HDInsight

Il Contratto di servizio viene definito come "finestra di supporto". Il termine finestra di supporto indica il periodo di tempo in cui una versione cluster HDInsight è supportata dal Servizio Supporto Tecnico Clienti Microsoft. Un cluster HDInsight non è compreso nella finestra di supporto se la data di scadenza del supporto della versione ha superato la data corrente. Nella tabella precedente è disponibile un elenco di versioni cluster HDInsight supportate. La data di scadenza del supporto per una determinata versione di HDInsight (indicata come versione X) viene calcolata come l'ultima di:

-   Formula 1: aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X
-   Formula 2: aggiungere 90 giorni alla data in cui il cluster HDInsight versione X+1 (la versione successiva a X) diventa disponibile nel portale di gestione di Azure.

> [WACOM.NOTE] HDInsight 2.1 e 3.0 utilizzano entrambi la versione a 64 bit di Windows 2008 R2 SP1 con .NET Framework 4.0.

**Note aggiuntive e informazioni sul numero di versione**

-   Il driver JDBC per SQL Server viene utilizzato internamente da HDInsight e non viene utilizzato per operazioni esterne. Se si desidera connettersi a HDInsight mediante ODBC, utilizzare Microsoft Hive ODBC Driver. Per informazioni sull'utilizzo di Hive ODBC, vedere [Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver](/it-it/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver).

-   Il cluster HDInsight versione 3.0 utilizza una distribuzione Hadoop basata su [Hortonworks Data Platform 2.0](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html).

-   Il cluster HDInsight versione 2.1 utilizza una distribuzione Hadoop basata su [Hortonworks Data Platform 1.3](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html). Si tratta del cluster HDInsight predefinito creato quando si utilizza il portale di Azure HDInsight.

-   Il cluster HDInsight versione 1.6 utilizza una distribuzione Hadoop basata su [Hortonworks Data Platform 1.1](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html).

-   Le versioni dei componenti associate alle versioni cluster HDInsight potrebbero subire modifiche nei futuri aggiornamenti a HDInsight. Per determinare i componenti disponibili e verificare le versioni in uso per un cluster, utilizzare l'API REST Ambari. È possibile utilizzare il comando GetComponentInformation per recuperare informazioni su un componente del servizio. Per informazioni dettagliate, vedere la [documentazione di Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md). Per ottenere queste informazioni, è inoltre possibile accedere a un cluster utilizzando Desktop remoto ed esaminare direttamente i contenuti della directory "C:\\apps\\dist".


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://www.windowsazure.com/it-it/support/forums/

[connect-excel-with-hive-ODBC]: /it-it/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
