---
title: Eseguire la migrazione di carichi di lavoro Apache Spark 2.1 o 2.2 a 2.3 o 2.4 - Azure HDInsight
description: Informazioni su come eseguire la migrazione di Apache Spark 2.1 e 2.2 a 2.3 o 2.4.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 1ce9b0faa6636d1318871cc9ef66cfbe47908265
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504979"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Eseguire la migrazione di carichi di lavoro Apache Spark 2.1 e 2.2 a 2.3 e 2.4

Questo documento illustra come eseguire la migrazione di carichi di lavoro Apache Spark in Spark 2.1 e 2.2 a 2.3 o 2.4.

Come illustrato nelle [Note sulla versione](../hdinsight-release-notes.md#upcoming-changes), a partire dal 1° luglio 2020 le seguenti configurazioni di cluster non saranno supportate e i clienti non potranno creare nuovi cluster con queste configurazioni:
 - Spark 2.1 e 2.2 in un cluster HDInsight 3.6 Spark
 - Spark 2.3 in un cluster HDInsight 4.0 Spark

I cluster esistenti in queste configurazioni vengono eseguiti così come sono senza il supporto di Microsoft. Se si utilizza Spark 2.1 o 2.2 in HDInsight 3.6, passare a Spark 2.3 in HDInsight 3.6 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto. Se si utilizza Spark 2.3 in un cluster HDInsight 4.0, passare a Spark 2.4 in HDInsight 4.0 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.

Per informazioni generali sulla migrazione di un cluster HDInsight da 3.6 a 4.0, vedere [Eseguire la migrazione del cluster HDInsight a una versione più recente](../hdinsight-upgrade-cluster.md). Per informazioni generali sulla migrazione a una versione più recente di Apache Spark, vedere [Apache Spark: criteri di versione](https://spark.apache.org/versioning-policy.html).

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>Istruzioni per gli aggiornamenti di versione di Spark in HDInsight

| Scenario di aggiornamento | Mechanism | Aspetti da considerare | Integrazione Spark/hive |
|------------------|-----------|--------------------|------------------------|
|Da HDInsight 3.6 Spark 2.1 a HDInsight 3.6 Spark 2.3| Ricreare i cluster con HDInsight Spark 2.3 | Vedere gli articoli seguenti: <br> [Apache Spark: Aggiornamento da Spark SQL 2.2 a 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Aggiornamento da Spark SQL 2.1 a 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Nessuna modifica |
|Da HDInsight 3.6 Spark 2.2 a HDInsight 3.6 Spark 2.3 | Ricreare i cluster con HDInsight Spark 2.3 | Vedere gli articoli seguenti: <br> [Apache Spark: Aggiornamento da Spark SQL 2.2 a 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Nessuna modifica |
| Da HDInsight 3.6 Spark 2.1 a HDInsight 4.0 Spark 2.4 | Ricreare i cluster con HDInsight 4.0 Spark 2.4 | Vedere gli articoli seguenti: <br> [Apache Spark: Aggiornamento da Spark SQL 2.3 a 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Aggiornamento da Spark SQL 2.2 a 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Aggiornamento da Spark SQL 2.1 a 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | L'integrazione di Spark e hive è cambiata in HDInsight 4,0. <br><br> In HDInsight 4.0, Spark e Hive usano cataloghi indipendenti per accedere alle tabelle SparkSQL o Hive. Una tabella creata da Spark si trova nel catalogo Spark. Una tabella creata da Hive si trova nel catalogo Hive. Questo comportamento è diverso da quello di HDInsight 3.6 in cui Hive e Spark condividono un catalogo comune. L'integrazione di Hive e Spark in HDInsight 4.0 si basa su Hive Warehouse Connector (HWC). HWC funziona come bridge tra Spark e Hive. Informazioni su Hive Warehouse Connector. <br> In HDInsight 4.0 se si vuole condividere il metastore tra Hive e Spark, è possibile modificare la proprietà metastore.catalog.default in hive nel cluster Spark. Questa proprietà è disponibile in Ambari Advanced spark2-hive-site-override. È importante comprendere che la condivisione del Metastore funziona solo per le tabelle hive esterne. questa operazione non funzionerà se sono presenti tabelle hive interne/gestite o tabelle ACID. <br><br>Per altre informazioni, vedere [Eseguire la migrazione dei carichi di lavoro Azure HDInsight 3.6 Hive a HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md).<br><br> |
| Da HDInsight 3.6 Spark 2.2 a HDInsight 4.0 Spark 2.4 | Ricreare i cluster con HDInsight 4.0 Spark 2.4 | Vedere gli articoli seguenti: <br> [Apache Spark: Aggiornamento da Spark SQL 2.3 a 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Aggiornamento da Spark SQL 2.2 a 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | L'integrazione di Spark e hive è cambiata in HDInsight 4,0. <br><br> In HDInsight 4.0, Spark e Hive usano cataloghi indipendenti per accedere alle tabelle SparkSQL o Hive. Una tabella creata da Spark si trova nel catalogo Spark. Una tabella creata da Hive si trova nel catalogo Hive. Questo comportamento è diverso da quello di HDInsight 3.6 in cui Hive e Spark condividono un catalogo comune. L'integrazione di Hive e Spark in HDInsight 4.0 si basa su Hive Warehouse Connector (HWC). HWC funziona come bridge tra Spark e Hive. Informazioni su Hive Warehouse Connector. <br> In HDInsight 4.0 se si vuole condividere il metastore tra Hive e Spark, è possibile modificare la proprietà metastore.catalog.default in hive nel cluster Spark. Questa proprietà è disponibile in Ambari Advanced spark2-hive-site-override. È importante comprendere che la condivisione del Metastore funziona solo per le tabelle hive esterne. questa operazione non funzionerà se sono presenti tabelle hive interne/gestite o tabelle ACID. <br><br>Per altre informazioni, vedere [Eseguire la migrazione dei carichi di lavoro Azure HDInsight 3.6 Hive a HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md).|

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire la migrazione di un cluster HDInsight a una versione più recente](../hdinsight-upgrade-cluster.md)
* [Eseguire la migrazione di carichi di lavoro Azure HDInsight 3.6 Hive a HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)
