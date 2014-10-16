<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in Hadoop cluster versions of HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Novità delle versioni cluster di Hadoop incluse in HDInsight

## Versioni di HDInsight

HDInsight supporta più versioni cluster Hadoop che possono essere distribuite in qualsiasi momento. Ogni versione scelta effettua il provisioning di una versione specifica della distribuzione HDP (Hortonworks Data Platform) e di un set di componenti contenuti in tale distribuzione. Le versioni del componente associate a ciascuna versione del cluster HDInsight sono elencate nella tabella seguente. Notare che la versione cluster predefinita usata da [Azure HDInsight][] è attualmente la 3.1 basata su HDP 2.1.

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Componente</th>
<th align="left">HDInsight versione 3.1 (predefinita)</th>
<th align="left">HDInsight versione 3.0</th>
<th align="left">HDIinsight versione 2.1</th>
<th align="left">HDInsight versione 1.6</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Hortonworks Data Platform (HDP)</td>
<td align="left">2.1</td>
<td align="left">2.0</td>
<td align="left">1.3</td>
<td align="left">1.1</td>
</tr>
<tr class="even">
<td align="left">Apache Hadoop e YARN</td>
<td align="left">2.4.0</td>
<td align="left">2.2.0</td>
<td align="left">1.2.0</td>
<td align="left">1.0.3</td>
</tr>
<tr class="odd">
<td align="left">Tez</td>
<td align="left">0.4.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Apache Pig</td>
<td align="left">0.12.1</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.3</td>
</tr>
<tr class="odd">
<td align="left">Apache Hive e HCatalog</td>
<td align="left">0.13.0</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.0</td>
</tr>
<tr class="even">
<td align="left">HBase</td>
<td align="left">0.98.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Apache Sqoop</td>
<td align="left">1.4.4</td>
<td align="left">1.4.4</td>
<td align="left">1.4.3</td>
<td align="left">1.4.2</td>
</tr>
<tr class="even">
<td align="left">Apache Oozie</td>
<td align="left">4.0.0</td>
<td align="left">4.0.0</td>
<td align="left">3.3.2</td>
<td align="left">3.2.0</td>
</tr>
<tr class="odd">
<td align="left">Apache HCatalog</td>
<td align="left">Unito a Hive</td>
<td align="left">Unito a Hive</td>
<td align="left">Unito a Hive</td>
<td align="left">0.4.1</td>
</tr>
<tr class="even">
<td align="left">Apache Templeton</td>
<td align="left">Unito a Hive</td>
<td align="left">Unito a Hive</td>
<td align="left">Unito a Hive</td>
<td align="left">0.1.4</td>
</tr>
<tr class="odd">
<td align="left">Ambari</td>
<td align="left">1.5.1</td>
<td align="left">1.4.1</td>
<td align="left">API v1.0</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Zookeeper</td>
<td align="left">3.4.5</td>
<td align="left">3.4.5</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Mahout</td>
<td align="left">0.9.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

**Ottenere le informazioni sulla versione del componente corrente**

Le versioni dei componenti associate alle versioni cluster HDInsight potrebbero subire modifiche nei futuri aggiornamenti a HDInsight. Per determinare i componenti disponibili e verificare le versioni in uso per un cluster, utilizzare l'API REST Ambari. È possibile usare il comando **GetComponentInformation** per recuperare informazioni su un componente del servizio. Per informazioni dettagliate, vedere la [documentazione di Ambari][]. Per ottenere queste informazioni, è inoltre possibile accedere a un cluster utilizzando Desktop remoto ed esaminare direttamente i contenuti della directory "C:\\apps\\dist".

**Note sulla versione**

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight][].

### Selezionare una versione durante il provisioning di un cluster HDInsight

Quando si crea un cluster tramite i cmdlet di HDInsight PowerShell o .NET SDK per HDInsight, è possibile scegliere la versione per il cluster HDInsight Hadoop utilizzando il parametro "Version".

Se si usa l'opzione **Creazione rapida**, per impostazione predefinita si otterrà la versione 3.1 di HDInsight che crea il cluster Hadoop. Se si utilizza l'opzione **Creazione personalizzata** dal portale Azure, è possibile scegliere la versione del cluster che si desidera distribuire dall'elenco a discesa **Versione HDInsight** nella pagina **Dettagli cluster**.

## Funzionalità in primo piano

Per impostazione predefinita, Azure HDInsight ora supporta Hadoop 2.4 con cluster HDInsight versione 3.1 (con Hortonworks Data Platform 2.1), che sfrutta al meglio questa piattaforma fornendo ai clienti diversi vantaggi importanti, tra cui in particolare:

-   **HBase**: HBase è un database NoSQL a bassa latenza che permette di eseguire l'elaborazione transazionale online dei Big Data. HBase è offerto come cluster gestito integrato nell'ambiente di Azure. I cluster sono configurati per l'archiviazione dei dati direttamente nell'archivio BLOB di Azure, che offre bassa latenza e maggiore flessibilità nelle opzioni relative a prestazioni e costi. Ciò permette ai clienti di creare siti Web interattivi da usare con grandi set di dati, per creare servizi che archiviano dati di sensori e telemetria da milioni di endpoint e per analizzare questi dati tramite processi Hadoop.

-   **Dashboard cluster**: una nuova applicazione Web distribuita nel cluster HDInsight. È possibile usarlo per eseguire query Hive, verificare i log dei processi ed esplorare l'archivio BLOB di Azure. L'URL usato per accedere all'applicazione Web è \<*NomeCluster*\>.azurehdinsight.net.

-   **Libreria Microsoft Avro**: questa libreria implementa il sistema di serializzazione dei dati Apache Avro per l'ambiente Microsoft .NET. Apache Avro offre un formato compatto di interscambio dei dati binari per la serializzazione e usa JSON per definire lo schema indipendente dal linguaggio che sottoscrive l'interoperabilità del linguaggio. I dati serializzati in un unico linguaggio possono essere letti in un altro linguaggio. I formati attualmente supportati sono C, C++, C#, Java, PHP, Python e Ruby. Il formato di serializzazione di Apache Avro è ampiamente usato in Azure HDInsight per rappresentare strutture dei dati complesse in un processo MapReduce di Hadoop.

-   **YARN**: nuovo framework di gestione delle applicazioni distribuito di utilizzo generale che ha sostituito il classico framework Apache Hadoop MapReduce per l'elaborazione dei dati nei cluster Hadoop. Funge di fatto da sistema operativo di Hadoop, trasformandolo da piattaforma dati monouso per l'elaborazione batch in una piattaforma multiuso che consente l'elaborazione batch, interattiva, online e di flussi. Questo nuovo framework di gestione migliora la scalabilità e l'utilizzo dei cluster in base a criteri quali garanzie di capacità, priorità e contratti di servizio.

-   **Tez (solo HDInsight 3.1)**: Un framework generico e personalizzabile che crea attività di elaborazione dati semplificate in carichi di lavoro in scala ridotta e su larga scala in Hadoop. Consente di eseguire un grafo aciclico diretto (DAG) delle attività relative a un singolo processo, in modo che i progetti presenti nell'ecosistema di Apache Hadoop, ad esempio Apache Hive e Apache Pig, possano soddisfare i requisiti riguardanti i tempi di risposta con interazione umana e l'estrema velocità effettiva a livello di petabyte. Notare che Hive 0.13 consente di eseguire le query Hive su Tez, piuttosto che su MapReduce.

-   **Disponibilità elevata**: è stato aggiunto un secondo nodo head ai cluster Hadoop distribuiti da HDInsight al fine di aumentare la disponibilità del servizio. Le implementazioni standard dei cluster Hadoop hanno in genere un singolo nodo head. HDInsight elimina questo singolo punto di errore con l'aggiunta di un nodo head secondario. Il passaggio alla nuova configurazione cluster HA non cambia il prezzo del cluster, a meno che i clienti effettuino il provisioning dei cluster con un nodo head molto grande invece che con il nodo grande predefinito.

-   **Prestazioni di Hive**: considerevoli miglioramenti ai tempi di risposta alle query Hive (fino a 40 volte) e alla compressione dei dati (fino all'80%) con il formato **ORC (Optimized Row Columnar)**.

-   **Pig, Sqoop, Qozie, Ambari**: aggiornamenti di versione dei componenti per il cluster HDinsight versione 3.0 (HDP 2.0/Hadoop 2.2), che garantiscono parità con il cluster HDinsight versione 2.1 (HDP 1.3/Hadoop 1.2). Per le specifiche, vedere più avanti le tabelle relative alle versioni.

-   **Mahout**: questa libreria scalabile di algoritmi Machine Learning viene preinstallata sui cluster Hadoop di HDInsight 3.1. In questo modo, è possibile eseguire processi Mahout senza dover configurare ulteriormente i cluster.

## Versioni supportate

Nella tabella seguente sono elencate le versioni di HDInsight attualmente disponibili, le corrispondenti versioni HDP (Hortonworks Data Platform) utilizzate e le date di rilascio. Se note, verranno indicate anche la data di scadenza del supporto e la data in cui le versioni verranno dichiarate obsolete. Tenere presente quanto segue:

-   i cluster ad alta disponibilità con due nodi head vengono distribuiti per impostazione predefinita per i cluster HDInsight 2.1, 3.0 e 3.1. Non sono disponibili per i cluster HDInsight 1.6.
-   Quando il supporto per una particolare versione sarà scaduto, potrebbe non essere disponibile attraverso il portale di gestione di Azure. Nella tabella seguente sono indicate le versioni disponibili sul portale di gestione di Azure. Le versioni cluster continueranno a essere disponibili usando il parametro `Version` nel comando [New-AzureHDInsightCluster][] di Powershell e .NET SDK fino alla data in cui verrà dichiarata obsoleta.

<table border="1">
<tr>
<th>
HDInsight Version

</th>
<th>
Versione HDP</a>

<th>
Disponibilità elevata

</th>
</th>
<th>
Data di rilascio

</th>
<th>
Disponibile sul portale di gestione di Azure

</th>
<th>
Data di scadenza del supporto

</th>
<th>
Data di dichiarazione obsolescenza

</th>
</tr>
<tr>
<td>
HDI 3.1

</td>
<td>
HDP 2.1

</td>
<td>
Sì

</td>
<td>
</td>
<td>
Sì

</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td>
HDI 3.0

</td>
<td>
HDP 2.0

</td>
<td>
Sì

</td>
<td>
11/02/2014

</td>
<td>
Sì

</td>
<td>
17/09/2014

</td>
<td>
30/06/2015

</td>
</tr>
<tr>
<td>
HDI 2.1

</td>
<td>
HDP 1.3

</td>
<td>
Sì

</td>
<td>
28/10/2013

</td>
<td>
No

</td>
<td>
12/05/2014

</td>
<td>
31/05/2015

</td>
</tr>
<tr>
<td>
HDI 1.6

</td>
<td>
HDP 1.1

</td>
<td>
No

</td>
<td>
28/10/2013

</td>
<td>
No

</td>
<td>
26/04/2014

</td>
<td>
31/05/2015

</td>
</tr>
</table>
**Distribuzione di cluster non predefiniti**

La creazione di cluster HDInsight 3.1 in Hadoop 2.4 è supportata dal portale di Azure, dall'SDK di HDinsight e da Azure PowerShell. Notare che i cluster HDInsight 3.1 sono stati creati per impostazione predefinita su Hadoop 2.4, quindi gli utenti devono usare l'opzione **Creazione personalizzata** nel portale per specificare altre versioni del cluster HDInsight, se è necessario crearle.

### Contratto di servizio per le versioni cluster HDInsight

Il Contratto di servizio viene definito come "finestra di supporto". Il termine finestra di supporto indica il periodo di tempo in cui una versione cluster HDInsight è supportata dal Servizio Supporto Tecnico Clienti Microsoft. Un cluster HDInsight non è compreso nella finestra di supporto se la **data di scadenza del supporto** della versione ha superato la data corrente. Nella tabella precedente è disponibile un elenco di versioni cluster HDInsight supportate. La data di scadenza del supporto per una determinata versione di HDInsight X (una volta che sarà disponibile una versione X+1 più recente) viene calcolata come l'ultima di:

-   Formula 1: aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X
-   Formula 2: aggiungere 90 giorni alla data in cui il cluster HDInsight versione X+1 (la versione successiva a X) diventa disponibile nel portale di gestione di Azure.

La **data di dichiarazione obsolescenza** è la data dopo la quale non è possibile creare la versione del cluster su HDInsight.

> [WACOM.NOTE] Entrambi i cluster HDInsight 2.1 e 3.0 eseguono il sistema operativo guest di Azure [Family 4][] che usa la versione a 64 bit di Windows Server 2012 R2 e supporta .NET Framework 4.0, 4.5 e 4.5.1.

## Note sulla versione di Hortonworks associate alle versioni di HDInsight

-   Il cluster HDInsight versione 3.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.1][].Si tratta del cluster HDInsight predefinito creato quando si usa il portale di Azure HDInsight.

-   Il cluster HDInsight versione 3.0 utilizza una distribuzione Hadoop basata su [Hortonworks Data Platform 2.0][].

-   Il cluster HDInsight versione 2.1 utilizza una distribuzione Hadoop basata su [Hortonworks Data Platform 1.3][].

-   Il cluster HDInsight versione 1.6 utilizza una distribuzione Hadoop basata su [Hortonworks Data Platform 1.1][].

  [Azure HDInsight]: http://go.microsoft.com/fwlink/?LinkID=285601
  [documentazione di Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Note sulla versione di HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-release-notes/
  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/en-us/library/dn593744.aspx
  [Family 4]: http://msdn.microsoft.com/en-us/library/azure/ee924680.aspx#explanation
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
