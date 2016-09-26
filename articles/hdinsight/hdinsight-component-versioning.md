<properties
	pageTitle="Quali sono i diversi componenti disponibili con un cluster HDInsight? | Microsoft Azure"
	description="HDInsight supporta più componenti e versioni del cluster Hadoop che possono essere distribuite. Vedere le versioni di Hadoop e di HortonWorks Data Platform (HDP) supportate."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="jgao"/>


# Quali sono i diversi componenti di Hadoop disponibili in HDInsight?

Scoprire i diversi livelli di servizio offerti da HDInsight, nonché le versioni dei diversi componenti hadoop inclusi in HDInsight.

## HDInsight Standard e HDInsight Premium

Azure HDInsight presenta le offerte cloud per i Big Data in due categorie: **Standard** e **Premium**. Nella tabella seguente sono elencate le funzionalità disponibili **solo** come parte della categoria Premium. Le funzionalità non indicate esplicitamente nella tabella di seguito sono disponibili come parte della categoria Standard.

>[AZURE.NOTE] Attualmente l’offerta HDInsight Premium è disponibile in anteprima e solo per i cluster Linux.

| Funzionalità HDInsight Premium | Descrizione |
|--------------|---------------|
| Microsoft R Server (Anteprima) | Microsoft R Server è la piattaforma di analisi di livello aziendale più ampiamente distribuita per R scalabile. Il linguaggio R supporta un'ampia gamma di statistiche di Big Data, la modellazione predittiva e funzionalità di Machine Learning. Come parte di HDInsight Premium, è ora possibile creare un cluster di HDInsight con R Server pronto per essere utilizzato con i modelli e set di dati di grandi dimensioni. Questa nuova funzionalità fornisce a data scientist ed esperti in statistica un'interfaccia R familiare scalabile su richiesta tramite HDInsight, senza il sovraccarico di manutenzione e configurazione del cluster. <br> <br>Per altre informazioni, vedere [Introduzione a R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md).

### Tipi di cluster supportati per Premium

Nella tabella seguente sono elencati il tipo di cluster HDInsight e la matrice di supporto Premium.

| Tipo di cluster | Standard | Premium |
|--------------|---------------|--------------|
| Hadoop | Sì | Sì |
| Spark | Sì | Sì |
| HBase | Sì | No |
| Storm | Sì | No |

Questa tabella verrà aggiornata man mano che vengono inclusi altri cluster in HDInsight Premium.

### Prezzi e contratto di servizio

Per informazioni su prezzi e contratto di servizio per HDInsight Premium, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Componenti di Hadoop disponibili con diverse versioni di HDInsight

Azure HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite in qualsiasi momento. Ogni versione scelta crea una versione specifica della distribuzione HDP (Hortonworks Data Platform) e un set di componenti contenuti in tale distribuzione. Le versioni del componente associate alle versioni del cluster HDInsight sono elencate nella tabella seguente. Notare che la versione cluster predefinita usata da Azure HDInsight è attualmente la 3.4 e, a partire dal 14/09/2016, è basata su HDP 2.4.

> [AZURE.NOTE] La versione predefinita del servizio può cambiare senza preavviso. Se si dispone di una dipendenza della versione, è consigliabile specificare la versione quando si crea il cluster tramite l'SDK .NET, Azure PowerShell e l'interfaccia della riga di comando di Azure.

Componente|HDInsight versione 3.4 (predefinita) | HDInsight versione 3.3 | HDInsight versione 3.2 |HDInsight versione 3.1 |HDInsight versione 3.0|
---|---|---|---|---|---
Hortonworks Data Platform|2\.4|2\.3|2\.2|2\.1.7|2\.0|
Apache Hadoop e YARN|2\.7.1|2\.7.1|2\.6.0|2\.4.0|2\.2.0|
Apache Tez|0\.7.0|0\.7.0 | 0\.5.2|0\.4.0||
Apache Pig|0\.15.0|0\.15.0|0\.14.0|0\.12.1|0\.12.0|
Apache Hive e HCatalog|1\.2.1|1\.2.1|0\.14.0|0\.13.1|0\.12.0|
Apache HBase |1\.1.2|1\.1.1|0\.98.4|0\.98.0||
Apache Sqoop|1\.4.6|1\.4.6|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.2.0|4\.2.0|4\.1.0|4\.0.0|4\.0.0|
Apache Zookeeper|3\.4.6|3\.4.6|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.10.0|0\.10.0|0\.9.3|0\.9.1||
Apache Mahout|0\.9.0+|0\.9.0+|0\.9.0|0\.9.0||
Apache Phoenix|4\.4.0|4\.4.0|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.6.0 (solo Linux)|1\.5.2 (solo Linux/build sperimentale)|1\.3.1 (solo Windows)|||


**Ottenere le informazioni sulla versione del componente corrente**

Le versioni dei componenti associate alle versioni cluster HDInsight potrebbero subire modifiche nei futuri aggiornamenti a HDInsight. Per determinare i componenti disponibili e verificare le versioni in uso per un cluster, usare l'API REST Ambari. È possibile usare il comando **GetComponentInformation** per recuperare informazioni su un componente del servizio. Per informazioni dettagliate, vedere la [documentazione di Ambari][ambari-docs]. Per ottenere queste informazioni, è anche possibile accedere a un cluster usando Desktop remoto ed esaminare direttamente il contenuto della directory "C:\\apps\\dist".


**Note sulla versione**

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md).


## Versioni supportate di HDInsight
La tabella seguente include l'elenco delle versioni di HDInsight attualmente disponibili, le corrispondenti versioni HDP (Hortonworks Data Platform) usate e le date di rilascio. Se note, vengono indicate anche la data di scadenza del supporto e la data in cui le versioni sono deprecate. Tenere presente quanto segue:

* i cluster ad alta disponibilità con due nodi head vengono distribuiti per impostazione predefinita per HDInsight 2.1 e versioni successive. Non sono disponibili per i cluster HDInsight 1.6.
* Dopo che il supporto per una particolare versione è scaduto, potrebbe non essere disponibile tramite il portale di Azure. Nella tabella seguente sono indicate le versioni disponibili sul portale di Azure classico. Le versioni cluster continueranno a essere disponibili usando il parametro `Version` nel comando [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) di Windows PowerShell e .NET SDK fino alla data di dichiarazione di obsolescenza.

HDInsight Version|Versione HDP|Sistema operativo della macchina virtuale|Disponibilità elevata|Data di rilascio|Disponibile nel portale di Azure|Data di scadenza del supporto|Data di dichiarazione obsolescenza
---|---|---|---|---|---|---|---
HDI 3.4|HDP 2.4|Ubuntu 14.0.4 LTS|Sì|29/03/2016|Sì||
HDI 3.3|HDP 2.3|Ubuntu 14.0.4 LTS o Windows Server 2012R2|Sì|02/12/2015|Sì||
HDI 3.2|HDP 2.2|Ubuntu 12.04 LTS o Windows Server 2012R2|Sì|2/18/2015|Sì||
HDI 3.1|HDP 2.1|Windows Server 2012 R2|Sì|6/24/2014|Sì||
HDI 3.0|HDP 2.0|Windows Server 2012 R2|Sì|11/02/2014|Sì|17/09/2014|30/06/2015
HDI 2.1|HDP 1.3|Windows Server 2012 R2|Sì|28/10/2013|Sì|12/05/2014|31/05/2015
HDI 1.6|HDP 1.1||No|28/10/2013|Sì|26/04/2014|31/05/2015

**Distribuzione di cluster non predefiniti**

### Contratto di servizio per le versioni dei cluster HDInsight

Il Contratto di servizio viene definito come "finestra di supporto". Il termine finestra di supporto indica il periodo di tempo in cui una versione cluster HDInsight è supportata dal Supporto tecnico Microsoft. Un cluster HDInsight non è compreso nella finestra di supporto se la **data di scadenza del supporto** della versione ha superato la data corrente. Nella tabella precedente è disponibile un elenco di versioni di cluster HDInsight supportate. La data di scadenza del supporto per una determinata versione di HDInsight X (una volta che sarà disponibile una versione X+1 più recente) viene calcolata come l'ultima di:

- Formula 1: aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X.
- Formula 2: aggiungere 90 giorni alla data in cui il cluster HDInsight versione X+1 (la versione successiva a X) diventa disponibile nel portale.

**Data in cui è deprecata** è la data dopo la quale non è possibile creare la versione del cluster su HDInsight.

> [AZURE.NOTE] Entrambi i cluster HDInsight 2.1 e 3.0 eseguono il sistema operativo guest Azure, [famiglia 4](../cloud-services/cloud-services-guestos-update-matrix.md), che usa la versione a 64 bit di Windows Server 2012 R2 e supporta .NET Framework 4.0, 4.5 e 4.5.1.

## Note sulla versione di Hortonworks associate alle versioni di HDInsight##

* Il cluster HDInsight versione 3.4 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). Si tratta del cluster Hadoop **predefinito** creato mediante il portale.



* Il cluster HDInsight versione 3.3 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).
	* Le note sulla versione di Apache Storm sono disponibili [qui](https://storm.apache.org/2015/11/05/storm0100-released.html).
	* Le note sulla versione di Apache Hive sono disponibili [qui](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).

* Il cluster HDInsight versione 3.2 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.2][hdp-2-2].

	* Note sulla versione per componenti specifici di Apache - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* Il cluster HDInsight versione 3.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. I cluster HDInsight versione 3.1 creati prima del 7/11/2014 sono basati su [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* Il cluster HDInsight versione 3.0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.0][hdp-2-0-8].

* Il cluster HDInsight versione 2.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.3][hdp-1-3-0].

* Il cluster HDInsight versione 1.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.1][hdp-1-1-0].


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0914_2016-->