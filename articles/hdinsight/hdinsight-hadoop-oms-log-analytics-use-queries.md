---
title: Eseguire query sui log di monitoraggio di Azure per monitorare i cluster di Azure HDInsightQuery Azure Monitor logs to monitor Azure HDInsight clusters
description: Informazioni su come eseguire query nei log di Monitoraggio di Azure per monitorare i processi in esecuzione in un cluster HDInsight.Learn how to run queries on Azure Monitor logs to monitor jobs running in an HDInsight cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803816"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Eseguire query sui log di monitoraggio di Azure per monitorare i cluster HDInsightQuery Azure Monitor logs to monitor HDInsight clusters

Informazioni su alcuni scenari di base su come usare i log di Monitoraggio di Azure per monitorare i cluster di Azure HDInsight:Learn some basic scenarios on how to use Azure Monitor logs to monitor Azure HDInsight clusters:

* [Analizzare le metriche dei cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Creare avvisi di eventi](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario aver configurato un cluster HDInsight per l'uso dei log di Monitoraggio di Azure e aver aggiunto le soluzioni di monitoraggio di Monitoraggio di Azure specifiche del cluster HDInsight all'area di lavoro. Per istruzioni, vedere [Usare i log di Monitoraggio di Azure con i cluster HDInsight.For instructions, see Use Azure Monitor logs with HDInsight clusters.](hdinsight-hadoop-oms-log-analytics-tutorial.md)

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizzare le metriche dei cluster HDInsight

Informazioni su come cercare metriche specifiche per il cluster HDInsight.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
1. In **Generale**selezionare **Registri**.
1. Digitare la query seguente nella casella di ricerca per cercare tutte le metriche per tutte le metriche disponibili per tutti i cluster HDInsight configurati per l'uso dei log di Monitoraggio di Azure e quindi selezionare **Esegui.** Esaminare i risultati.

    ```kusto
    search *
    ```

    ![L'analisi Apache Ambari cerca tutte le metriche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Cerca tutte le metriche")

1. Nel menu a sinistra, selezionare la scheda **Filtro.**

1. In **Tipo**selezionare **Heartbeat**. Selezionare quindi **Applica & esecuzione**.

    ![metriche specifiche per la ricerca di log analytics](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Cercare metriche specifiche")

1. Si noti che la query nella casella di testo diventa:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. È possibile approfondire utilizzando le opzioni disponibili nel menu a sinistra. Ad esempio:

    - Per visualizzare i log da un nodo specifico:To see logs from a specific node:

        ![Ricerca di errori specifici output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Ricerca di errori specifici output1")

    - Per visualizzare i registri in determinati momenti:

        ![Ricerca di errori specifici output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Ricerca di errori specifici output2")

1. Selezionare **Applica & Esegui** ed esaminare i risultati. Si noti inoltre che la query è stata aggiornata a:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Query di esempio aggiuntive

Query di esempio basata sulla media delle risorse utilizzate in un intervallo di 10 minuti, suddivise in categorie in base al nome del cluster:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Invece di affinare i risultati in base alla media delle risorse usate, è possibile usare la query seguente per affinarli in base a quando sono state usate le risorse massime (nonché il 90° e il 95 ° percentile) in un intervallo di 10 minuti:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Creare avvisi per eventi di rilevamento

Il primo passaggio per creare un avviso consiste nel creare una query in base a cui l'avviso viene attivato. È possibile usare qualsiasi query per creare un avviso.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
1. In **Generale**selezionare **Registri**.
1. Eseguire la query seguente in cui si desidera creare un avviso e quindi selezionare **Esegui**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    La query presenta un elenco delle applicazioni con errori in esecuzione nei cluster HDInsight.

1. Selezionare **Nuova regola** di avviso nella parte superiore della pagina.

    ![Immettere la query per creare un avviso1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Immettere la query per creare un avviso1")

1. Nella finestra **Crea regola** immettere la query e altri dettagli per creare un avviso e selezionare **Crea regola di avviso**.

    ![Immettere una query per creare un avviso2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Immettere una query per creare un avviso2")

### <a name="edit-or-delete-an-existing-alert"></a>Modificare o eliminare un avviso esistente

1. Aprire l'area di lavoro Log Analytics dal portale di Azure.

1. Nel menu a sinistra, in **Monitoraggio**, selezionare **Avvisi**.

1. Verso la parte superiore selezionare **Gestisci regole**di avviso .

1. Selezionare l'avviso da modificare o eliminare.

1. Sono disponibili le opzioni seguenti: **Salva**, **Ignora**, **Disabilita** ed **Elimina**.

    ![HdInsight Azure Monitor registra avvisi eliminazione modifica](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Vedere anche

* [Introduzione alle query su log in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)
* [Creare visualizzazioni personalizzate usando Progettazione viste in Monitoraggio di AzureCreate custom views by using View Designer in Azure Monitor](../azure-monitor/platform/view-designer.md)
