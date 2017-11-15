---
title: Guide alla risoluzione dei problemi di Azure HDInsight | Microsoft Docs
description: Risolvere i problemi dei carichi di lavoro di Hadoop tramite Azure HDInsight. La documentazione dettagliata illustra come usare HDInsight per risolvere i problemi comuni con Hive, Spark, YARN, HBase, HDFS e Storm.
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 785884b7fd03339d195fbd7853b17300effecc6a
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Risolvere i problemi usando Azure HDInsight

| Carico di lavoro Apache | Domande principali |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Risolvere i problemi di HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Come si eseguono i report del comando hbck con più aree non assegnate?](hbase/apache-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Come si risolvono i problemi di timeout con i comandi hbck per le assegnazioni di aree?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Come si forza la disabilitazione della modalità sicura di HDFS in un cluster?](hbase/apache-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Come si risolvono i problemi di connettività relativi a JDBC o SQLLine con Apache Phoenix?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Qual è la causa dell'errore di avvio del server master?](hbase/apache-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Cosa genera un errore di riavvio in un server di area?](hbase/apache-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Risolvere i problemi di HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Come si accede al sistema HDFS locale dall'interno di un cluster?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Come si forza la disabilitazione della modalità sicura di HDFS in un cluster?](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Risolvere i problemi di HBase](hdinsight-troubleshoot-hive.md)|<br>[Come si esporta un metastore Hive e lo si importa in un altro cluster?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Come si individuano i log Hive in un cluster?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Come si avvia la shell di Hive con configurazioni specifiche in un cluster?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Come si analizzano i dati di un grafo aciclico diretto di Tez in un percorso critico del cluster?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Come si scaricano i dati di un grafo aciclico diretto di Tez da un cluster?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Risolvere i problemi di Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Come si configura un'applicazione Spark tramite Ambari nei cluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Come si configura un'applicazione Spark tramite un notebook di Jupyter nei cluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Come si configura un'applicazione Spark tramite Livy nei cluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Come si configura un'applicazione Spark tramite lo script spark-submit nei cluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[Che cosa causa l'eccezione OutOfMemoryError in un'applicazione Spark?](spark/apache-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Risolvere i problemi di Storm](hdinsight-troubleshoot-STORM.md)|<br>[Come si accede all'interfaccia utente di Storm in un cluster?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Come si trasferiscono le informazioni di checkpoint dello spout dell'hub eventi di Storm da una topologia all'altra?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Come si individuano i binari Storm in un cluster?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Come si determina la topologia di distribuzione di un cluster Storm?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Come si individuano i file binari dello spout dell'hub eventi di Storm per lo sviluppo?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Come si individuano i file di configurazione di Storm Log4J nei cluster?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Risolvere i problemi di YARN](hdinsight-troubleshoot-YARN.md)|<br>[Come si crea una nuova coda YARN in un cluster?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Come si scaricano i log di YARN da un cluster?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Risorse per la risoluzione dei problemi di HDInsight

| Per informazioni su | Vedere gli articoli seguenti |
| --- | --- |
| HDInsight in Linux e ottimizzazione | - [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md)<br>- [Risoluzione dei problemi di memoria e prestazioni di Hadoop](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Hive query performance](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) (Prestazioni delle query Hive) |
| Log e dump | - [Accedere ai log applicazioni di Hadoop YARN in Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Abilitare i dump dell'heap per i servizi di Hadoop in Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analizzare i log di HDInsight](hdinsight-debug-jobs.md)|
| Errors | - [Comprendere e risolvere gli errori di WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Impostazioni di Hive per la risoluzione dell'errore OutofMemory](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Strumenti | - [Usare le visualizzazioni di Ambari per il debug di processi Tez](hdinsight-debug-ambari-tez-view.md)<br>- [Ottimizzare le query Hive](hdinsight-hadoop-optimize-hive-query.md) |
