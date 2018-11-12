---
title: Eseguire query in Azure Log Analytics per monitorare i cluster Azure HDInsight
description: Informazioni su come eseguire query in Azure Log Analytics per monitorare i processi in esecuzione in un cluster HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: a4c4017d7fa798559817c281d159148ec675d158
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281354"
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Eseguire query in Azure Log Analytics per monitorare i cluster HDInsight

Informazioni sugli scenari relativi alle modalità d'uso di Azure Log Analytics per monitorare i cluster Azure HDInsight:

* [Analizzare le metriche dei cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Cercare messaggi di log specifici nei cluster](#search-for-specific-log-messages)
* [Creare avvisi di eventi](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Prerequisiti

* È necessario aver configurato un cluster HDInsight per l'uso di Azure Log Analytics e aver aggiunto soluzioni di gestione specifiche per i cluster HDInsight all'area di lavoro di Log Analytics. Per istruzioni, vedere [Usare Azure Log Analytics con i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizzare le metriche dei cluster HDInsight

Informazioni su come cercare metriche specifiche per il cluster HDInsight.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
2. Selezionare il riquadro **Ricerca log**.
3. Digitare la query seguente nella casella di testo per cercare tutte le metriche disponibili per tutti i cluster HDInsight configurati per l'uso di Azure Log Analytics e quindi selezionare **ESEGUI**.

        search *

    ![Cercare tutte le metriche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Cercare tutte le metriche")

    L'output dovrebbe essere simile al seguente:

    ![Output della ricerca di tutte le metriche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Output della ricerca di tutte le metriche")

5. Nel riquadro sinistro, in **Tipo**, selezionare una metrica da analizzare più a fondo e quindi selezionare **Applica**. Nella schermata riportata di seguito è illustrato il tipo `metrics_resourcemanager_queue_root_default_CL` selezionato.

    > [!NOTE]
    > Potrebbe essere necessario selezionare il pulsante **[+]Altro** per trovare la metrica che si sta cercando. Anche il pulsante **Applica** si trova nella parte inferiore dell'elenco, quindi è necessario scorrere verso il basso per visualizzarlo.

    Si noti che la query nella casella di testo cambia in quella visualizzata nella casella evidenziata nello screenshot seguente:

    ![Cercare metriche specifiche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Cercare metriche specifiche")

6. Per esaminare più a fondo la metrica specifica. È ad esempio possibile affinare l'output esistente in base alla media delle risorse usate in un intervallo di 10 minuti, classificate in base al nome del cluster usando la query seguente:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Invece di affinare i risultati in base alla media delle risorse usate, è possibile usare la query seguente per affinarli in base a quando sono state usate le risorse massime (nonché il 90° e il 95 ° percentile) in un intervallo di 10 minuti:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Cercare messaggi di log specifici nei cluster

Informazioni su come cercare i messaggi di errore in un intervallo di tempo specifico. Questi passaggi sono solo un esempio di come arrivare al messaggio di errore a cui si è interessati. È possibile usare qualsiasi proprietà disponibile per cercare gli errori che si vuole trovare.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
2. Selezionare il riquadro **Ricerca log**.
3. Digitare la query seguente per cercare tutti i messaggi di errore per tutti i cluster HDInsight configurati per l'uso di Azure Log Analytics e quindi selezionare **ESEGUI**. 

         search "Error"

    Verrà visualizzato un output simile al seguente:

    ![Output della ricerca di tutti gli errori](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Output della ricerca di tutti gli errori")

4. Nel riquadro sinistro, nella categoria **Tipo**, selezionare un tipo di errore da analizzare più a fondo e selezionare **Applica**.  Si noti che i risultati mostrano solo l'errore del tipo selezionato.
5. È possibile esaminare più a fondo l'errore specifico usando le opzioni disponibili nel riquadro sinistro. Ad esempio: 

    - Per cercare solo i messaggi di errore di un nodo del ruolo di lavoro specifico:

        ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Output della ricerca di errori specifici")

    - Per cercare un errore che si è verificato in un preciso momento:

        ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Output della ricerca di errori specifici")

6. Per cercare un errore specifico. È possibile selezionare **[+]mostra dettagli** per esaminare il messaggio di errore effettivo.

    ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Output della ricerca di errori specifici")

## <a name="create-alerts-for-tracking-events"></a>Creare avvisi per eventi di rilevamento

Il primo passaggio per creare un avviso consiste nel creare una query in base a cui l'avviso viene attivato. È possibile usare qualsiasi query per creare un avviso.

1. Aprire l'area di lavoro Log Analytics associata al cluster HDInsight dal portale di Azure.
2. Selezionare il riquadro **Ricerca log**.
3. Eseguire la query seguente per creare un avviso, quindi selezionare **ESEGUI**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    La query presenta un elenco delle applicazioni con errori in esecuzione nei cluster HDInsight.

4. Selezionare **Nuova regola di avviso** nella parte superiore della pagina.

    ![Immettere una query per creare un avviso](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Immettere una query per creare un avviso")

5. Nella finestra **Crea regola** immettere la query e altri dettagli per creare un avviso e selezionare **Crea regola di avviso**.

    ![Immettere una query per creare un avviso](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Immettere una query per creare un avviso")

Per modificare o eliminare un avviso esistente:

1. Aprire l'area di lavoro di Log Analytics dal portale di Azure.
2. Nel menu a sinistra selezionare **Avviso**.
3. Selezionare l'avviso da modificare o eliminare.
4. Sono disponibili le opzioni seguenti: **Salva**, **Ignora**, **Disabilita** ed **Elimina**.

    ![Modifica o eliminazione degli avvisi di Log Analytics in HDInsight](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Per altre informazioni, vedere [Utilizzo delle regole di avviso in Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Vedere anche 

* [Uso di Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Creare regole di avviso in Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
