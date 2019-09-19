---
title: Apache Spark procedure consigliate in Azure HDInsight
description: Informazioni sulle procedure consigliate per l'uso di Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106125"
---
# <a name="apache-spark-best-practices"></a>Procedure consigliate Apache Spark

Questo articolo fornisce varie procedure consigliate per l'uso di Apache Spark in Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Ricerca per categorie eseguire o inviare processi Spark?

| Opzione | Documenti |
|---|---|
| VSCode | [Usare gli strumenti di Spark & hive per Visual Studio Code](../hdinsight-for-vscode.md) |
| Notebook di Jupyter | [Esercitazione: Caricare i dati ed eseguire query su un cluster Apache Spark in Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Esercitazione: Usare Azure Toolkit for IntelliJ per creare Apache Spark applicazioni per un cluster HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Esercitazione: Creare un'applicazione scala Maven per Apache Spark in HDInsight usando IntelliJ](./apache-spark-create-standalone-application.md) |
| Notebook Zeppelin | [Usare i notebook di Apache Zeppelin con cluster Apache Spark in Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Invio di processi remoti con Livio | [Usare l'API REST di Apache Spark per inviare i processi remoti a un cluster HDInsight Spark](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Ricerca per categorie monitorare ed eseguire il debug dei processi Spark?

| Opzione | Documenti |
|---|---|
| Toolkit di Azure per IntelliJ | [Errore durante il debug del processo Spark con Azure Toolkit for IntelliJ (anteprima)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ tramite SSH | [Eseguire il debug delle applicazioni Apache Spark in modalità remota o in locale su un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ tramite VPN | [Usare Azure Toolkit for IntelliJ per eseguire il debug di applicazioni Apache Spark in modalità remota in HDInsight tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Grafico del processo nel server di cronologia Apache Spark | [Usare il Server cronologia Apache Spark esteso per il debug e la diagnosi di applicazioni Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Ricerca per categorie far funzionare i processi Spark in modo più efficiente?

| Opzione | Documenti |
|---|---|
| Cache IO | [Migliorare le prestazioni dei carichi di lavoro Apache Spark usando la cache IO di Azure HDInsight (anteprima)](./apache-spark-improve-performance-iocache.md) |
| Opzioni di configurazione | [Ottimizzare i processi di Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Ricerca per categorie connettersi ad altri servizi di Azure?

| Opzione | Documenti |
|---|---|
| Apache Hive in HDInsight | [Integrare Apache Spark e Apache Hive con il connettore Hive Warehouse](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase in HDInsight | [Usare Apache Spark per leggere e scrivere dati Apache HBase](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka in HDInsight | [Esercitazione: Usare Apache Spark lo streaming strutturato con Apache Kafka su HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Implementare un'architettura lambda sulla piattaforma Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Quali sono le opzioni di archiviazione?

| Opzione | Documenti |
|---|---|
| Data Lake Storage Gen2 | [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Usare Data Lake Storage Gen1 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| Archivio BLOB di Azure | [Usare una risorsa di archiviazione di Azure con cluster Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le impostazioni di Apache Spark](apache-spark-settings.md)
* [Ottimizzare i processi di Apache Spark in HDInsight](apache-spark-perf.md)
