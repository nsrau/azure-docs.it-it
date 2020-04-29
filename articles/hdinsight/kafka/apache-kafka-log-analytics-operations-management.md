---
title: Log di monitoraggio di Azure per Apache Kafka-Azure HDInsight
description: Informazioni su come usare i log di monitoraggio di Azure per analizzare i log da Apache Kafka cluster in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77471181"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analizzare i log per Apache Kafka in HDInsight

Informazioni su come usare i log di monitoraggio di Azure per analizzare i log generati da Apache Kafka in HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Percorso dei log

I log Apache Kafka nel cluster si trovano in `/var/log/kafka`. I registri Kafka non vengono salvati né salvati in permanenza tra i cicli di vita del cluster, indipendentemente dal fatto che vengano usati i dischi gestiti. La tabella seguente illustra i log disponibili.

|File di log |Descrizione |
|---|---|
|Kafka. out|stdout e stderr del processo Kafka. In questo file sono disponibili i log di avvio e arresto di Kafka.|
|Server. log|Log del server Kafka principale. Tutti i log del broker Kafka si concludono qui.|
|controller. log|Log del controller se il broker funge da controller.|
|StateChange. log|Tutti gli eventi di modifica dello stato ai broker vengono registrati in questo file.|
|Kafka-GC. log|Statistiche di Garbage Collection Kafka.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Abilitare i log di monitoraggio di Azure per Apache Kafka

I passaggi per abilitare i log di monitoraggio di Azure per HDInsight sono gli stessi per tutti i cluster HDInsight. Per informazioni su come creare e configurare i servizi richiesti, usare i collegamenti seguenti:

1. Creare un'area di lavoro Log Analytics. Per altre informazioni, vedere il documento [log in monitoraggio di Azure](../../azure-monitor/platform/data-platform-logs.md) .

2. Creare un cluster Kafka in HDInsight. Per altre informazioni, vedere il documento su come [iniziare a usare Apache Kafka in HDInsight](apache-kafka-get-started.md).

3. Configurare il cluster Kafka per l'uso dei log di monitoraggio di Azure. Per altre informazioni, vedere il documento [usare i log di monitoraggio di Azure per monitorare HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) .

> [!IMPORTANT]  
> Potrebbero essere necessari circa 20 minuti prima che i dati siano disponibili per i log di monitoraggio di Azure.

## <a name="query-logs"></a>Log di query

1. Dal [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro log Analytics.

2. Nel menu a sinistra, in **generale**, selezionare **log**. Qui è possibile cercare i dati raccolti da Kafka. Immettere una query nella finestra query e quindi selezionare **Esegui**. Di seguito alcuni esempi di ricerche:

* Utilizzo disco: 

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Utilizzo CPU:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Messaggi in ingresso al secondo: (sostituire `your_kafka_cluster_name` con il nome del cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Byte in ingresso al secondo: (sostituire `wn0-kafka` con un nome host del nodo di lavoro).

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Byte in uscita al secondo: sostituire `your_kafka_cluster_name` con il nome del cluster.

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    È anche possibile immettere `*` per cercare tutti i tipi registrati. Questi log sono attualmente disponibili per le query:

    | Tipo di log | Descrizione |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Metriche JMX Kafka |

    ![Utilizzo CPU di Apache Kafka log Analytics](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su monitoraggio di Azure, vedere [Panoramica di monitoraggio di Azure](../../log-analytics/log-analytics-get-started.md)ed [eseguire query sui log di monitoraggio di Azure per monitorare i cluster HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Per altre informazioni sull'uso di Apache Kafka, vedere i documenti seguenti:

* [Eseguire il mirroring di Apache Kafka tra cluster HDInsight](apache-kafka-mirroring.md)
* [Aumentare la scalabilità di Apache Kafka in HDInsight](apache-kafka-scalability.md)
* [Usare il flusso Apache Spark (DStream) con Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Usare lo streaming strutturato di Apache Spark con Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
