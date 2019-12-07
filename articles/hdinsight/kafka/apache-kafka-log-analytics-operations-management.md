---
title: Log di monitoraggio di Azure per Apache Kafka-Azure HDInsight
description: Informazioni su come usare i log di monitoraggio di Azure per analizzare i log da Apache Kafka cluster in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: d4263b8b338f057893c9dfcda1541fc338c2577f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894268"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analizzare i log per Apache Kafka in HDInsight

Informazioni su come usare i log di monitoraggio di Azure per analizzare i log generati da Apache Kafka in HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Abilitare i log di monitoraggio di Azure per Apache Kafka

I passaggi per abilitare i log di monitoraggio di Azure per HDInsight sono gli stessi per tutti i cluster HDInsight. Per informazioni su come creare e configurare i servizi richiesti, usare i collegamenti seguenti:

1. Creare un'area di lavoro Log Analytics. Per altre informazioni, vedere il documento [log in monitoraggio di Azure](../../azure-monitor/platform/data-platform-logs.md) .

2. Creare un cluster Kafka in HDInsight. Per altre informazioni, vedere il documento su come [iniziare a usare Apache Kafka in HDInsight](apache-kafka-get-started.md).

3. Configurare il cluster Kafka per l'uso dei log di monitoraggio di Azure. Per altre informazioni, vedere il documento [usare i log di monitoraggio di Azure per monitorare HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) .

> [!IMPORTANT]  
> Potrebbero essere necessari circa 20 minuti prima che i dati siano disponibili per i log di monitoraggio di Azure.

## <a name="query-logs"></a>Log di query

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'area di lavoro Log Analytics.

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

* Byte in ingresso al secondo: (sostituire `wn0-kafka` con un nome host del nodo di lavoro.)

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

    | Tipo di log | Description |
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
