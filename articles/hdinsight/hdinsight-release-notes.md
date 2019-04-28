---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti per lo sviluppo e i dettagli per Hadoop, Spark, R Server, Hive e altro ancora.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 0beac64ceb0bbf729d2f6d0f6a0ca8d5af499f42
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760121"
---
# <a name="release-notes-for-azure-hdinsight"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Riepilogo

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi Apache Hadoop e Apache Spark open source in Azure.

## <a name="new-features"></a>Nuove funzionalità

Per altre informazioni sulle modifiche importanti con HDInsight 4.0., vedere [quali sono le novità in HDI 4.0?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Versioni dei componenti

Le versioni di Apache ufficiale di tutti i componenti di HDInsight 4.0 sono indicate di seguito. I componenti elencati sono versioni delle versioni stabili più recenti disponibili.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Nelle versioni successive di componenti di Apache in alcuni casi vengono aggregate della distribuzione HDP oltre alle versioni elencate in precedenza. In questo caso, queste versioni successive sono elencate nella tabella Anteprime tecniche e non dovrebbero sostituire le versioni del componente Apache dell'elenco precedente in un ambiente di produzione.

## <a name="apache-patch-information"></a>Informazioni sulle patch di Apache

Per altre informazioni sulle patch disponibili in HDInsight 4.0, vedere la patch di elenco per ogni prodotto nella tabella seguente.

| Nome prodotto | Informazioni sulle patch |
|---|---|
| Ambari | [Informazioni sulle patch di Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informazioni sulle patch di Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| hbase | [Informazioni su patch HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Questa versione offre Hive 3.1.0 con nessuna patch Apache aggiuntiva.  |
| Kafka | Questa versione offre Kafka 1.1.1 con nessuna patch Apache aggiuntiva. |
| Oozie | [Informazioni sulle patch di Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informazioni sulle patch di Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informazioni sulle patch di Pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informazioni sulle patch di Ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Informazioni sulle patch di Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Questa versione offre 1.4.7 con nessuna aggiuntiva patch Apache Sqoop. |
| Tez | Questa versione offre 0.9.1 con nessuna aggiuntiva patch Apache Tez. |
| Zeppelin | Questa versione 0.8.0 con nessuna aggiuntiva patch Apache Zeppelin sono disponibili. |
| Zookeeper | [Informazioni sulle patch di zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Vulnerabilità comuni ed esposizioni risolte

Per altre informazioni sulla sicurezza problemi risolti in questa versione, vedere 'Hortonworks [fisso Common Vulnerabilities and Exposures per HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Problemi noti

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>La replica viene interrotta per proteggere HBase con installazione predefinita

Per HDInsight 4.0, procedere come segue:

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
Per HDInsight 3.6, eseguire le operazioni seguenti:

1. Accedere a active HMaster ZK.
1. Scaricare uno script per abilitare la replica con il comando seguente:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Digitare il comando `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Digitare il comando seguente:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline smette di funzionare dopo la migrazione di cluster HBase di HDInsight 4.0

Seguire anche questa procedura:

1. Eliminare le seguenti tabelle di Phoenix:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Se non è possibile eliminare qualsiasi delle tabelle, riavviare HBase per cancellare tutte le connessioni alle tabelle.
1. Eseguire di nuovo `sqlline.py`. Phoenix crea nuovamente tutte le tabelle che sono state eliminate nel passaggio 1.
1. Rigenera le tabelle di Phoenix e le viste per i dati di HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline smette di funzionare dopo la replica dei metadati di HBase Phoenix da HDInsight 3.6 a 4.0

Seguire anche questa procedura:

1. Prima di eseguire la replica, passare al cluster di destinazione 4.0 ed eseguire `sqlline.py`. Questo comando genera, ad esempio le tabelle di Phoenix `SYSTEM.MUTEX` e `SYSTEM.LOG` che esiste solo in 4.0.
1. Eliminare le tabelle seguenti:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Avviare la replica di HBase

## <a name="deprecation"></a>Deprecazione

Apache Storm e Machine Learning services non sono disponibili in HDInsight 4.0.
