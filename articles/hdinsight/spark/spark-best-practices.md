---
title: Apache Spark best practices on Azure HDInsight
description: Informazioni sulle procedure consigliate per l'uso di Apache Spark in Azure HDInsight.Learn best practices for using Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106125"
---
# <a name="apache-spark-best-practices"></a>Best practice per Apache Spark

Questo articolo offre varie procedure consigliate per l'uso di Apache Spark in Azure HDInsight.This article provides various best practices for using Apache Spark on Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Come si eseguono o si inviano i processi Spark?

| Opzione | Documenti |
|---|---|
| VSCode | [Usare gli strumenti Spark & Hive per il codice di Visual StudioUse Spark & Hive Tools for Visual Studio Code](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [Esercitazione: Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Esercitazione: Usare Azure Toolkit per IntelliJ per creare applicazioni Apache Spark per un cluster HDInsightTutorial: Use Azure Toolkit for IntelliJ to create Apache Spark applications for an HDInsight cluster](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Esercitazione: Creare un'applicazione di Scala Maven per Apache Spark in HDInsight usando IntelliJ](./apache-spark-create-standalone-application.md) |
| Notebook di Zeppelin | [Usare i notebook di Apache Zeppelin con cluster Apache Spark in Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Invio remoto di lavori con Livy | [Usare l'API REST di Apache Spark per inviare i processi remoti a un cluster HDInsight Spark](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Come si esegue il monitoraggio e il debug dei processi Spark?

| Opzione | Documenti |
|---|---|
| Toolkit di Azure per IntelliJ | [Debug del processo di spark non riuscite con Azure Toolkit per IntelliJ (anteprima)Failure spark job debugging with Azure Toolkit for IntelliJ (preview)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ through SSH | [Eseguire il debug delle applicazioni Apache Spark in modalità remota o in locale su un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ through VPN | [Usare Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni Apache Spark in HDInsight tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Grafico dei processi nel server Apache Spark History | [Usare il Server cronologia Apache Spark esteso per il debug e la diagnosi di applicazioni Apache Spark](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Come è possibile fare in modo che i processi Spark siano eseguiti in modo più efficiente?

| Opzione | Documenti |
|---|---|
| Cache I/O | [Migliorare le prestazioni dei carichi di lavoro di Apache Spark usando Azure HDInsight IO Cache (anteprima)](./apache-spark-improve-performance-iocache.md) |
| Opzioni di configurazione | [Ottimizzare i processi Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Come è possibile connettersi ad altri servizi di Azure?

| Opzione | Documenti |
|---|---|
| Apache Hive in HDInsight | [Integrare Apache Spark e Apache Hive con il connettore Hive Warehouse](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase in HDInsight | [Usare Apache Spark per leggere e scrivere dati Apache HBase](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka in HDInsight | [Esercitazione: Usare lo streaming strutturato di Apache Spark con Apache Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Implementare un'architettura lambda nella piattaforma Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Quali sono le opzioni di archiviazione?

| Opzione | Documenti |
|---|---|
| Data Lake Storage Gen2 | [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Usare Data Lake Storage Gen1 con i cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| Archiviazione BLOB di Azure | [Usare una risorsa di archiviazione di Azure con cluster Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le impostazioni di Apache Spark](apache-spark-settings.md)
* [Ottimizzare i processi di Apache Spark in HDInsightOptimize Apache Spark jobs in HDInsight](apache-spark-perf.md)
