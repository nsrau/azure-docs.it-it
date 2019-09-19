---
title: Eseguire query nei log di monitoraggio di Azure per monitorare i cluster HDInsight di Azure
description: Informazioni su come eseguire query nei log di monitoraggio di Azure per monitorare i processi in esecuzione in un cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 51344ff7381b6392870b1fd0e331eed38a33915d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103512"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Eseguire query nei log di monitoraggio di Azure per monitorare i cluster HDInsight

Informazioni su alcuni scenari di base su come usare i log di monitoraggio di Azure per monitorare i cluster HDInsight di Azure:

* [Analizzare le metriche dei cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Cercare messaggi di log specifici nei cluster](#search-for-specific-log-messages)
* [Creare avvisi di eventi](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario aver configurato un cluster HDInsight per l'uso dei log di monitoraggio di Azure e aver aggiunto i registri di monitoraggio di Azure specifici del cluster HDInsight per l'area di lavoro. Per istruzioni, vedere [usare i log di monitoraggio di Azure con i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizzare le metriche dei cluster HDInsight

Informazioni su come cercare metriche specifiche per il cluster HDInsight.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
1. Selezionare il riquadro **Ricerca log**.
1. Digitare la query seguente nella casella di ricerca per cercare tutte le metriche per tutte le metriche disponibili per tutti i cluster HDInsight configurati per l'uso dei log di monitoraggio di Azure e quindi selezionare **Esegui**.

        search *

    ![Ricerca di tutte le metriche in Apache Ambari Analytics](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Cerca in tutte le metriche")

    L'output dovrebbe essere simile al seguente:

    ![ricerca di log Analytics per tutte le metriche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Cerca nell'output di tutte le metriche")

1. Nel riquadro sinistro, in **Tipo**, selezionare una metrica da analizzare più a fondo e quindi selezionare **Applica**. Nella schermata riportata di seguito è illustrato il tipo `metrics_resourcemanager_queue_root_default_CL` selezionato.

    > [!NOTE]  
    > Potrebbe essere necessario selezionare il pulsante **[+]Altro** per trovare la metrica che si sta cercando. Anche il pulsante **Applica** si trova nella parte inferiore dell'elenco, quindi è necessario scorrere verso il basso per visualizzarlo.

    Si noti che la query nella casella di testo cambia in quella visualizzata nella casella evidenziata nello screenshot seguente:

    ![metriche specifiche per la ricerca in log Analytics](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Cercare metriche specifiche")

1. Per esaminare più a fondo la metrica specifica. È ad esempio possibile affinare l'output esistente in base alla media delle risorse usate in un intervallo di 10 minuti, classificate in base al nome del cluster usando la query seguente:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

1. Invece di affinare i risultati in base alla media delle risorse usate, è possibile usare la query seguente per affinarli in base a quando sono state usate le risorse massime (nonché il 90° e il 95 ° percentile) in un intervallo di 10 minuti:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Cercare messaggi di log specifici nei cluster

Informazioni su come cercare i messaggi di errore in un intervallo di tempo specifico. Questi passaggi sono solo un esempio di come arrivare al messaggio di errore a cui si è interessati. È possibile usare qualsiasi proprietà disponibile per cercare gli errori che si vuole trovare.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
2. Selezionare il riquadro **Ricerca log**.
3. Digitare la query seguente per cercare tutti i messaggi di errore per tutti i cluster HDInsight configurati per l'uso dei log di monitoraggio di Azure e quindi selezionare **Esegui**.

         search "Error"

    Verrà visualizzato un output simile al seguente:

    ![Errori di ricerca log portale di Azure](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Cerca nell'output di tutti gli errori")

4. Nel riquadro sinistro, nella categoria **Tipo**, selezionare un tipo di errore da analizzare più a fondo e selezionare **Applica**.  Si noti che i risultati mostrano solo l'errore del tipo selezionato.

5. È possibile esaminare più a fondo l'errore specifico usando le opzioni disponibili nel riquadro sinistro. Ad esempio:

    - Per cercare solo i messaggi di errore di un nodo del ruolo di lavoro specifico:

        ![Cerca errori specifici output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Cerca errori specifici output1")

    - Per cercare un errore che si è verificato in un preciso momento:

        ![Cerca errori specifici Output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Cerca errori specifici Output2")

6. Per cercare un errore specifico. È possibile selezionare **[+]mostra dettagli** per esaminare il messaggio di errore effettivo.

    ![Cerca errori specifici output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Cerca errori specifici output3")

## <a name="create-alerts-for-tracking-events"></a>Creare avvisi per eventi di rilevamento

Il primo passaggio per creare un avviso consiste nel creare una query in base a cui l'avviso viene attivato. È possibile usare qualsiasi query per creare un avviso.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
2. Selezionare il riquadro **Ricerca log**.
3. Eseguire la query seguente per creare un avviso, quindi selezionare **ESEGUI**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    La query presenta un elenco delle applicazioni con errori in esecuzione nei cluster HDInsight.

4. Selezionare **Nuova regola di avviso** nella parte superiore della pagina.

    ![Immettere una query per creare un Alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Immettere una query per creare un Alert1")

5. Nella finestra **Crea regola** immettere la query e altri dettagli per creare un avviso e selezionare **Crea regola di avviso**.

    ![Immettere una query per creare un alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Immettere una query per creare un alert2")

Per modificare o eliminare un avviso esistente:

1. Aprire l'area di lavoro Log Analytics dal portale di Azure.
2. Nel menu a sinistra selezionare **Avviso**.
3. Selezionare l'avviso da modificare o eliminare.
4. L'utente ha a disposizione le seguenti opzioni: **Salva**, **Disabilita**, **Disattiva**, e **Elimina**.

    ![HDInsight log di monitoraggio di Azure-modifica avviso eliminazione](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Vedere anche

* [Creare visualizzazioni personalizzate usando Progettazione viste in monitoraggio di Azure](../azure-monitor/platform/view-designer.md)
* [Creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../azure-monitor/platform/alerts-metric.md)
