---
title: Componenti e versioni di Apache Hadoop - Azure HDInsight
description: Informazioni sui componenti e le versioni di Apache Hadoop in Azure HDInsight.
keywords: versioni hadoop, componenti ecosistema hadoop, componenti hadoop, come controllare versione hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 598f423bdb15b1d4975633db19c28b399fde484b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166796"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Componenti e versioni di Apache Hadoop disponibili in HDInsight.

Informazioni su componenti e versioni dell'ecosistema [Apache Hadoop](https://hadoop.apache.org/) in Microsoft Azure HDInsight e su Enterprise Security Package. Informazioni su come controllare le versioni di componenti Hadoop in HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Componenti di Apache Hadoop disponibili con diverse versioni di HDInsight

Azure HDInsight supporta più versioni cluster di Hadoop che possono essere distribuite in qualsiasi momento. A partire dal 4 aprile 2017, la versione predefinita del cluster usata da Azure HDInsight è 3,6.

Le versioni del componente associate alle versioni del cluster HDInsight sono elencate nella tabella seguente: 

> [!NOTE]  
> La versione predefinita per il servizio HDInsight può essere modificata senza preavviso. Se si dispone di una dipendenza della versione, specificare la versione HDInsight quando si crea il cluster tramite l'SDK .NET con Azure PowerShell e l'interfaccia della riga di comando classica di Azure.

| Componente              | HDInsight 4.0 | HDInsight 3.6 (predefinito)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop e YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0, 1.2.1                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.1         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (vedere la nota seguente) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.0                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> A causa di considerazioni sulle prestazioni di sistema, il supporto per Kafka versione 0.10 è scaduto nel mese di marzo 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Controllare le informazioni sulle versioni correnti dei componenti Hadoop

Le versioni dei componenti dell'ecosistema Hadoop associate alle versioni cluster HDInsight potrebbero subire modifiche con gli aggiornamenti a HDInsight. Per controllare i componenti Hadoop e verificare le versioni in uso per un cluster, usare l'API REST Ambari. Il comando **GetComponentInformation** recupera informazioni sui componenti del servizio. Per informazioni dettagliate, vedere la [documentazione di Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Note sulla versione

Per altre note sulla versione relative alle versioni più recenti di HDInsight, vedere [Note sulla versione di HDInsight](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>Versioni supportate di HDInsight

Le tabelle seguenti elencano le versioni di HDInsight. Se note, vengono indicate anche la data di scadenza del supporto e la data di ritiro.

### <a name="available-versions"></a>Versioni disponibili

La tabella seguente elenca le versioni di HDInsight disponibili nel portale di Azure nonché altri metodi di distribuzione come PowerShell e .NET SDK.

| Versione HDInsight | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | disponibilità elevata |  Disponibilità nel portale di Azure | 
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 settembre 2018 | | |Sì |Sì |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 aprile 2017 | 30 giugno 2020 |31 dicembre 2020 |Sì |Sì |


> [!NOTE]  
> Se il supporto per una versione è scaduto, potrebbe non essere disponibile tramite il portale di Microsoft Azure. Tuttavia, le versioni dei cluster continueranno a essere disponibili usando il parametro `Version` nel comando [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) di Windows PowerShell e .NET SDK fino alla data di ritiro della versione.
>

### <a name="retired-versions"></a>Versioni ritirate

La tabella seguente elenca le versioni di HDInsight attualmente **non** disponibili nel portale di Azure.

| Versione HDInsight | Versione HDP | Sistema operativo della macchina virtuale | Data di rilascio | Data di scadenza del supporto | Data di ritiro | disponibilità elevata |  Disponibilità nel portale di Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 settembre 2016 |5 settembre 2017 |28 giugno 2018 |Sì |No |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 marzo 2016 |29 dicembre 2016 |9 gennaio 2018 |Sì |No |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 dicembre 2015 |27 giugno 2016 |31 luglio 2018 |Sì |No |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 dicembre 2015 |27 giugno 2016 |31 luglio 2017 |Sì |No |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS o Windows Server 2012 R2 |18 febbraio 2015 |1° marzo 2016 |1° aprile 2017 |Sì |No |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 giugno 2014 |18 maggio 2015 |30 giugno 2016 |Sì |No |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 febbraio 2014 |17 settembre 2014 |30 giugno 2015 |Sì |No |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 ottobre 2013 |12 maggio 2014 |31 maggio 2015 |Sì |No |
| HDInsight 1.6 |HDP 1.1 | |28 ottobre 2013 |26 aprile 2014 |31 maggio 2015 |No |No |

> [!NOTE]  
> I cluster ad alta disponibilità con due nodi head vengono distribuiti per impostazione predefinita per HDInsight versione 2.1 e successive. Non sono disponibili per i cluster HDInsight versione 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Pacchetto di sicurezza aziendale per HDInsight

Si tratta di un pacchetto opzionale che è possibile aggiungere al cluster HDInsight come parte del flusso di lavoro di creazione del cluster. Il Pacchetto di sicurezza aziendale supporta:

- Integrazione con Active Directory per l'autenticazione.

    In passato era possibile creare cluster HDInsight solo con un utente amministratore locale e un utente SSH locale. L'utente amministratore locale poteva accedere a tutti i file, cartelle, tabelle e colonne.  Con il Pacchetto di sicurezza aziendale è possibile abilitare il controllo degli accessi in base al ruolo integrando i cluster HDInsight con il proprio Active Directory, inclusi Active Directory locale, Azure AD Domain Services o Active Directory su macchina virtuale IaaS. L'amministratore del dominio nel cluster può concedere agli utenti di usare il proprio nome utente aziendale (del dominio) e relativa password per accedere al cluster. 

    Per altre informazioni, vedere:

    - [Introduzione alla sicurezza Apache Hadoop con i cluster HDInsight aggiunti al dominio](./domain-joined/hdinsight-security-overview.md)
    - [Pianificare cluster Apache Hadoop aggiunti a un dominio di Azure in HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configurare l'ambiente sandbox aggiunto al dominio](./domain-joined/apache-domain-joined-configure.md)
    - [Configurare cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorizzazione per i dati

  - Integrazione con Apache Ranger per l'autorizzazione per code Yarn, Hive e Spark SQL.
  - È possibile impostare il controllo di accesso per file e cartelle.

    Per altre informazioni, vedere:

  - [Configurare criteri Apache Hive in HDInsight aggiunto al dominio](./domain-joined/apache-domain-joined-run-hive.md)

- Visualizzare i log di controllo per monitorare gli accessi e i criteri configurati. 

### <a name="supported-cluster-types"></a>Tipi di cluster supportati

Attualmente solo i tipi di cluster seguenti supportano il Pacchetto di sicurezza aziendale:

- Hadoop (solo HDInsight 3.6)
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-storage"></a>Supporto per Azure Data Lake Storage

Enterprise Security Package supporta l'uso di Azure Data Lake Storage come risorsa di archiviazione sia primaria che aggiuntiva.

### <a name="pricing-and-service-level-agreement"></a>Prezzi e contratto di servizio

Per informazioni su prezzi e contratto di servizio per il Pacchetto di sicurezza aziendale, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contratto di servizio per le versioni dei cluster HDInsight

Il Contratto di servizio (SLA) viene definito come _finestra di supporto_. Il termine finestra di supporto indica il periodo di tempo in cui una versione cluster HDInsight è supportata dal Supporto tecnico Microsoft. Un cluster HDInsight non è compreso nella finestra di supporto se la _data di scadenza del supporto_ della versione ha superato la data corrente. La data di scadenza del supporto per una specifica versione di HDInsight X (dopo che sarà disponibile una versione X+1 più recente) viene calcolata come l'ultima di:  

* Formula 1: aggiungere 180 giorni alla data di rilascio del cluster HDInsight versione X.
* Formula 2: aggiungere 90 giorni alla data di rilascio del cluster HDInsight versione X+1 nel portale di Azure.

La _data di ritiro_ è la data dopo la quale non è possibile creare la versione del cluster su HDInsight. A partire dal 31 luglio 2017 non è possibile ridimensionare un cluster HDInsight dopo la data di ritiro. 

> [!NOTE]  
> I cluster HDInsight su Windows (incluse le versioni 2.1, 3.0, 3.1, 3.2 e 3.3) eseguono il sistema operativo guest di Azure Family versione 4, che usa la versione a 64 bit di Windows Server 2012 R2. Il sistema operativo guest di Azure Family versione 4 supporta le versioni di .NET Framework 4.0, 4.5, 4.5.1 e 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Note sulla versione di Hortonworks associate alle versioni di HDInsight

La sezione fornisce collegamenti alle note sulla versione di Hortonworks Data Platform e i componenti di Apache utilizzati con HDInsight.
* Il cluster HDInsight versione 4.0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html).
* Il cluster HDInsight versione 3.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Il cluster HDInsight versione 3.5 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). La versione del cluster HDInsight 3.5 è il cluster Hadoop _predefinito_ che viene creato nel portale di Azure.
* Il cluster HDInsight versione 3.4 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Il cluster HDInsight versione 3.3 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * Le [note sulla versione di Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) sono disponibili nel sito Web di Apache.
  * Le [note sulla versione di Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) sono disponibili nel sito Web di Apache.
* Il cluster HDInsight versione 3,2 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2,2][hdp-2-2].

  * Note sulla versione per componenti specifici di Apache sono disponibili come segue: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) e [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Il cluster HDInsight versione 3,1 USA una distribuzione Hadoop basata su [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. I cluster HDInsight 3,1 creati prima del 7 novembre 2014 sono basati su [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Il cluster HDInsight versione 3,0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2,0][hdp-2-0-8].
* Il cluster HDInsight versione 2,1 USA una distribuzione Hadoop basata su [Hortonworks Data Platform 1,3][hdp-1-3-0].
* Il cluster HDInsight versione 1,6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1,1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster

Per altre informazioni sugli SKU di macchine virtuali da selezionare per il cluster, vedere [i dettagli di configurazione del cluster HDInsight di Azure](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Passaggi successivi
- [Configurazione del cluster per Apache Hadoop, Spark e altre informazioni su HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Lavorare da un computer Windows in Apache Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
