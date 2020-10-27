---
title: Linee guida Apache Spark in Azure HDInsight
description: Informazioni sulle linee guida per l'uso di Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: a93b3e0da7b881b80f7613907e40af509bffadbd
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545566"
---
# <a name="apache-spark-guidelines"></a>Linee guida per Apache Spark

Questo articolo fornisce diverse linee guida per l'uso di Apache Spark in Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Ricerca per categorie eseguire o inviare processi Spark?

| Opzione | Documenti |
|---|---|
| VSCode | [Usare Spark & Hive Tools per Visual Studio Code](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [Esercitazione: Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Esercitazione: usare Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Esercitazione: Creare un'applicazione Scala Maven per Apache Spark in HDInsight usando IntelliJ](./apache-spark-create-standalone-application.md) |
| Notebook di Zeppelin | [Usare i notebook di Apache Zeppelin con cluster Apache Spark in Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Invio di processi remoti con Livio | [Usare l'API REST di Apache Spark per inviare i processi remoti a un cluster HDInsight Spark](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|È possibile usare Livy per l'esecuzione interattiva di shell Spark o per inviare processi batch da eseguire su Spark.|
|[Azure Data Factory per Apache Spark](../../data-factory/transform-data-using-spark.md)|L'attività Spark in una pipeline di Data Factory esegue un programma Spark nel proprio o [cluster HDInsight su richiesta.|
|[Azure Data Factory per Apache Hive](../../data-factory/transform-data-using-hadoop-hive.md)|L'attività hive di HDInsight in una pipeline Data Factory esegue query hive nel cluster HDInsight personalizzato o su richiesta.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Ricerca per categorie monitorare ed eseguire il debug dei processi Spark?

| Opzione | Documenti |
|---|---|
| Toolkit di Azure per IntelliJ | [Errore durante il debug del processo Spark con Azure Toolkit for IntelliJ (anteprima)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ tramite SSH | [Eseguire il debug delle applicazioni Apache Spark in modalità remota o in locale su un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ tramite VPN | [Usare Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni Apache Spark in HDInsight tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Grafico del processo nel server di cronologia Apache Spark | [Usare il Server cronologia Apache Spark esteso per il debug e la diagnosi di applicazioni Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Ricerca per categorie far funzionare i processi Spark in modo più efficiente?

| Opzione | Documenti |
|---|---|
| Cache IO | [Migliorare le prestazioni dei carichi di lavoro di Apache Spark usando Azure HDInsight IO Cache (anteprima)](./apache-spark-improve-performance-iocache.md) |
| Opzioni di configurazione | [Ottimizzare i processi Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Ricerca per categorie connettersi ad altri servizi di Azure?

| Opzione | Documenti |
|---|---|
| Apache Hive in HDInsight | [Integrare Apache Spark e Apache Hive con il connettore Hive Warehouse](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase in HDInsight | [Usare Apache Spark per leggere e scrivere dati Apache HBase](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka in HDInsight | [Esercitazione: usare lo streaming strutturato di Apache Spark con Apache Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Collegamento ad Azure Synapse per Azure Cosmos DB](../../cosmos-db/synapse-link.md) |

## <a name="what-are-my-storage-options"></a>Quali sono le opzioni di archiviazione?

| Opzione | Documenti |
|---|---|
| Azure Data Lake Storage Gen2 | [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Azure Data Lake Storage Gen1 | [Usare Azure Data Lake Storage Gen1 con i cluster HDInsight di Azure](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Archiviazione BLOB di Azure | [Usare una risorsa di archiviazione di Azure con cluster Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le impostazioni di Apache Spark](apache-spark-settings.md)
* [Ottimizzare i processi Apache Spark in HDInsight](apache-spark-perf.md)
