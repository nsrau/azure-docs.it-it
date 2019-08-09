---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottieni suggerimenti per lo sviluppo e dettagli per Hadoop, Spark, R Server, hive e altro ancora.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: b6e9a340a1fdcbe3ee24b8c81d171ade04c63139
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880030"
---
# <a name="release-notes-for-azure-hdinsight"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Riepilogo

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi Apache Hadoop e Apache Spark open source in Azure.

## <a name="new-features"></a>Nuove funzionalità

Per ulteriori informazioni sulle modifiche importanti con HDInsight 4,0, vedere Novità [di HDI 4,0](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Versioni dei componenti

Di seguito sono elencate le versioni ufficiali di Apache di tutti i componenti di HDInsight 4,0. I componenti elencati sono versioni delle versioni stabili più recenti disponibili.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1, 2.4.0
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Le versioni più recenti dei componenti Apache sono talvolta aggregate nella distribuzione di HDP, oltre alle versioni elencate in precedenza. In questo caso, queste versioni successive sono elencate nella tabella Anteprime tecniche e non dovrebbero sostituire le versioni del componente Apache dell'elenco precedente in un ambiente di produzione.

## <a name="apache-patch-information"></a>Informazioni sulle patch di Apache

Per altre informazioni sulle patch disponibili in HDInsight 4,0, vedere l'elenco delle patch per ogni prodotto nella tabella seguente.

| Nome prodotto | Informazioni patch |
|---|---|
| Ambari | [Informazioni patch Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informazioni patch Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| hbase | [Informazioni patch HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Questa versione fornisce hive 3.1.0 senza patch Apache aggiuntive.  |
| Kafka | Questa versione fornisce Kafka 1.1.1 senza patch Apache aggiuntive. |
| Oozie | [Informazioni patch OOZIE](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informazioni sulla patch Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informazioni sulla patch Pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informazioni patch Ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Informazioni patch Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Questa versione fornisce Sqoop 1.4.7 senza patch Apache aggiuntive. |
| Tez | Questa versione fornisce Tez 0.9.1 senza patch Apache aggiuntive. |
| Zeppelin | Questa versione fornisce Zeppelin 0.8.0 senza patch Apache aggiuntive. |
| Zookeeper | [Informazioni patch Zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Vulnerabilità comuni ed esposizioni risolte

Per ulteriori informazioni sui problemi di sicurezza risolti in questa versione, vedere la pagina relativa alle [vulnerabilità e alle esposizioni comuni fisse di Hortonworks per HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Problemi noti

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Replica interruppe per HBase protetti con installazione predefinita

Per HDInsight 4,0, seguire questa procedura:

1. Abilitare la comunicazione tra cluster.
1. Accedere al nodo head attivo.
1. Scaricare uno script per abilitare la replica con il comando seguente:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Digitare il comando `sudo kinit <domainuser>`.
1. Digitare il comando seguente per eseguire lo script:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
Per HDInsight 3,6, eseguire le operazioni seguenti:

1. Accedere ad Active HMaster ZK.
1. Scaricare uno script per abilitare la replica con il comando seguente:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Digitare il comando `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Digitare il comando seguente:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix sqlline smette di funzionare dopo la migrazione del cluster HBase a HDInsight 4,0

Seguire anche questa procedura:

1. Eliminare le tabelle Phoenix seguenti:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Se non è possibile eliminare alcuna tabella, riavviare HBase per cancellare le connessioni alle tabelle.
1. Eseguire di nuovo `sqlline.py`. Phoenix creerà di nuovo tutte le tabelle eliminate nel passaggio 1.
1. Rigenerare le tabelle e le visualizzazioni Phoenix per i dati HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix sqlline smette di funzionare dopo la replica dei metadati di HBase Phoenix da HDInsight 3,6 a 4,0

Seguire anche questa procedura:

1. Prima di eseguire la replica, passare al cluster di destinazione 4,0 ed `sqlline.py`eseguire. Questo comando genererà tabelle Phoenix come `SYSTEM.MUTEX` e `SYSTEM.LOG` che esistano solo in 4,0.
1. Eliminare le tabelle seguenti:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Avviare la replica di HBase

## <a name="deprecation"></a>Deprecazione

I servizi Apache Storm e ML non sono disponibili in HDInsight 4,0.
