---
title: Documentazione di Azure HDInsight - Guida per la risoluzione dei problemi | Microsoft Docs
description: Eseguire la risoluzione dei problemi relativi ai carichi di lavoro Hadoop in HDInsight. La documentazione dettagliata illustra come risolvere problemi comuni con Hive, Spark, HBase, Storm, Kafka in HDInsight.
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 29aa2510fc04f24c437d6bb6142eebb8859569c0
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---


# <a name="azure-hdinsight-troubleshooting"></a>Risoluzione dei problemi di Azure HDInsight

| Carico di lavoro Apache | Domande principali |
|---|---|
|![HBASE](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Risoluzione dei problemi di HBASE](hdinsight-troubleshoot-hbase.md)|<br>[Come si eseguono i report del comando hbck con più aree non assegnate?](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Come si risolvono i problemi di timeout con i comandi hbck per le assegnazioni di aree?](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Come si forza la disabilitazione della modalità sicura di HDFS in un cluster?](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-an-cluster)<br><br>[Come si risolvono i problemi di connettività relativi a JDBC o sqlline con Apache Phoenix?](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Qual è la causa dell'errore di avvio del server master?](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Cosa genera un errore di riavvio in un server di area?](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Risoluzione dei problemi relativi a Hadoop Distributed File System](hdinsight-troubleshoot-hdfs.md)|<br>[Come si accede al sistema HDFS locale dall'interno di un cluster?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Come si forza la disabilitazione della modalità sicura di HDFS in un cluster?](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![HIVE](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Risoluzione dei problemi di HIVE](hdinsight-troubleshoot-hive.md)|<br>[Come si esporta un metastore Hive e lo si importa in un altro cluster?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Come si individuano i log di Hive in un cluster?](hdinsight-troubleshoot-hive.md#how-do-locate-hive-logs-on-a-cluster)<br><br>[Come si avvia la shell Hive con configurazioni specifiche in un cluster?](hdinsight-troubleshoot-hive.md#how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Come si analizzano i dati Tez DAG in un percorso critico del cluster?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Come si scaricano i dati Tez DAG da un cluster?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![SPARK](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Risoluzione dei problemi di Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Come si configura un'applicazione Spark tramite Ambari nei cluster?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-ambari-on-clusters)<br><br>[Come si configura un'applicazione Spark tramite un notebook di Jupyter nei cluster?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters)<br><br>[Come si configura un'applicazione Spark nei cluster tramite LIVY?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-livy-on-clusters)<br><br>[Come si configura un'applicazione Spark tramite spark-submit nei cluster?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-spark-submit-on-clusters)<br><br>[Che cosa causa l'eccezione OutOfMemoryError in un'applicazione Spark?](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![STORM](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Risoluzione dei problemi di STORM](hdinsight-troubleshoot-STORM.md)|<br>[Come si accede all'interfaccia utente di Storm in un cluster?](hdinsight-troubleshoot-storm.md#how-do-i-access-storm-ui-on-a-cluster)<br><br>[Come trasferire informazioni di checkpoint dello spout di Hub eventi di Storm da una topologia all'altra](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Come si individuano i binari Storm in un cluster?](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Come si determina la topologia di distribuzione di un cluster Storm?](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Come si individuano i binari Storm-EventHub-Spout per lo sviluppo?](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-eventhub-spout-binaries-for-development)<br><br>[Come si individuano i file di configurazione di Storm Log4J nei cluster?](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Risoluzione dei problemi di YARN](hdinsight-troubleshoot-YARN.md)|<br>[Come si crea una nuova coda Yarn in un cluster?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Come si scaricano i log di Yarn da un cluster?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Risorse per la risoluzione dei problemi di HDInsight
Per altre informazioni di risoluzione dei problemi, vedere gli articoli seguenti.

| Per informazioni su | Vedere quanto segue |
| --- | --- |
| HDInsight in Linux e ottimizzazione | [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md)<br>[Memoria e prestazioni di Hadoop](hdinsight-hadoop-stack-trace-error-messages.md)<br>[Prestazioni delle query Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Log e dump | [Accedere ai log applicazioni di Hadoop YARN in Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>[Abilitare i dump dell'heap per i servizi di Hadoop](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>[Analizzare i log di HDInsight](hdinsight-debug-jobs.md)|
| Errori | [Comprendere e risolvere gli errori di WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>[Errore di memoria insufficiente corretto dalle impostazioni Hive](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Strumenti | [Usare le visualizzazioni di Ambari per il debug di processi Tez](hdinsight-debug-ambari-tez-view.md)<br>[Ottimizzare le query Hive](hdinsight-hadoop-optimize-hive-query.md) |



