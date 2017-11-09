---
title: 'Log Analytics per Apache Kafka: Azure HDInsight| Microsoft Docs'
description: Informazioni su come usare Operations Management Suite per analizzare i log del cluster Apache Kafka in Azure HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/11/2017
ms.author: larryfr
ms.openlocfilehash: 856314cafde6059c35963b067287b66c566364a2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-logs-for-apache-kafka-preview-on-hdinsight"></a>Analizzare i log per Apache Kafka (anteprima) in HDInsight

Informazioni su come usare Microsoft Operations Management Suite per analizzare i log generati da Apache Kafka in HDInsight.

## <a name="enable-oms-for-kafka"></a>Abilitare OMS per Kafka

I passaggi per abilitare Log Analytics per HDInsight sono uguali per tutti i cluster HDInsight. Per informazioni su come creare e configurare i servizi richiesti, usare i collegamenti seguenti:

1. Creare un'area di lavoro di Log Analytics. Per altre informazioni, vedere il documento [Introduzione a un'area di lavoro di Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Creare un cluster Kafka in HDInsight. Per altre informazioni, vedere il documento su come [iniziare a usare Apache Kafka in HDInsight](apache-kafka-get-started.md).

3. Configurare il cluster Kafka per l'uso di Log Analytics. Per altre informazioni, vedere il documento su come [usare Log Analytics per monitorare HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md).

    > [!NOTE]
    > È anche possibile configurare il cluster per l'uso di Log Analytics con il cmdlet `Enable-AzureRmHDInsightOperationsManagementSuite`. Questo cmdlet richiede le informazioni seguenti:
    >
    > * Il nome del cluster HDInsight.
    > * L'ID dell'area di lavoro per Log Analytics. È possibile trovare l'ID dell'area di lavoro nell'area di lavoro di OMS della propria area di lavoro di Log Analytics.
    > * La chiave primaria per la connessione OMS. Per trovare la chiave primaria, selezionare l'istanza di Log Analytics e quindi __Portale di OMS__. Nel portale di OMS selezionare __Impostazioni__, __Origini connesse__ e quindi __Server Linux__.


> [!IMPORTANT]
> Potrebbero essere necessari circa 20 minuti affinché i dati siano disponibili per Log Analytics.

## <a name="query-logs"></a>Log di query

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'area di lavoro di Log Analytics.

2. Selezionare __Ricerca log__. Qui è possibile cercare i dati raccolti da Kafka. Di seguito alcuni esempi di ricerche:

    * Utilizzo disco: `Type=Perf ObjectName="Logical Disk" (CounterName="Free Megabytes")  InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by   Computer interval 1HOUR`
    * Utilizzo di CPU: `Type:Perf CounterName="% Processor Time" InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by Computer interval 1HOUR`
    * Messaggi in ingresso al secondo: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s interval 1HOUR`
    * Byte in ingresso al secondo: `Type=metrics_kafka_CL HostName_s="wn0-kafka" InstanceName_s="kafka-BrokerTopicMetrics-BytesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) interval 1HOUR`
    * Byte in uscita al secondo: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-BytesOutPerSec-Count" |  measure avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) interval 1HOUR`

    > [!IMPORTANT]
    > Sostituire i valori della query con le informazioni specifiche del cluster. Ad esempio, `ClusterName_s` deve essere impostato sul nome del cluster. `HostName_s` deve essere impostato sul nome di dominio di un nodo del ruolo di lavoro nel cluster.

    È anche possibile immettere `*` per cercare tutti i tipi registrati. Questi log sono attualmente disponibili per le query:

    | Tipo di log | Descrizione |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Metriche JMX Kafka |

    ![Immagine della ricerca per utilizzo di CPU](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Log Analytics, vedere il documento [Introduzione a un'area di lavoro di Log Analytics](../../log-analytics/log-analytics-get-started.md).

Per altre informazioni sull'uso di Kafka, vedere i documenti seguenti:

 * [Eseguire il mirroring di Kafka tra cluster HDInsight](apache-kafka-mirroring.md)
 * [Aumentare la scalabilità di Kafka in HDInsight](apache-kafka-scalability.md)
 * [Usare lo streaming Spark (DStream) con Kafka](../hdinsight-apache-spark-with-kafka.md)
 * [Usare lo streaming strutturato Spark con Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)