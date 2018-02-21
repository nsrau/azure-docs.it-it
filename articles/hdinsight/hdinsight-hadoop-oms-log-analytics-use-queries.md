---
title: Eseguire query in Azure Log Analytics per monitorare i cluster Azure HDInsight | Microsoft Docs
description: Informazioni su come eseguire query in Azure Log Analytics per monitorare i processi in esecuzione in un cluster HDInsight.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: da7b83846418bfe5f95b126d4f5f7b34d3a7b35d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Eseguire query in Azure Log Analytics per monitorare i cluster HDInsight

Informazioni sugli scenari relativi alle modalità d'uso di Azure Log Analytics per monitorare i cluster Azure HDInsight:

* [Analizzare le metriche dei cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Cercare messaggi di log specifici nei cluster](#search-for-specific-log-messages)
* [Creare avvisi di eventi](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>prerequisiti

* È necessario aver configurato un cluster HDInsight per l'uso di Azure Log Analytics. Per istruzioni, vedere [Usare Azure Log Analytics con i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* È necessario aver aggiunto soluzioni di gestione specifiche per i cluster HDInsight all'area di lavoro [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), come descritto in [Aggiungere soluzioni di gestione di cluster HDInsight in Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analizzare le metriche dei cluster HDInsight

Informazioni su come cercare metriche specifiche per il cluster HDInsight.

1. Aprire un cluster HDInsight associato ad Azure Log Analytics nel portale di Azure.
2. Fare clic su **Monitoraggio**, quindi fare clic su **Aprire il dashboard OMS**.

    ![Aprire il dashboard OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Aprire il dashboard OMS")

2. Fare clic su **Ricerca log** nel menu a sinistra.

    ![Aprire la ricerca log](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Aprire la ricerca log")

3. Digitare la query seguente nella casella di testo per cercare tutte le metriche disponibili per tutti i cluster HDInsight configurati per l'uso di Azure Log Analytics e quindi premere **INVIO**.

        `search *` 

    ![Cercare tutte le metriche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Cercare tutte le metriche")

    L'output dovrebbe essere simile al seguente:

    ![Output della ricerca di tutte le metriche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Output della ricerca di tutte le metriche")

5. Nel riquadro sinistro, in **Tipo**, selezionare una metrica da analizzare più a fondo e quindi fare clic su **Applica**. Nella schermata riportata di seguito è illustrato il tipo `metrics_resourcemanager_queue_root_default_CL` selezionato. 

    > [!NOTE]
    > Potrebbe essere necessario fare clic sul pulsante **[+]Altro** per trovare la metrica che si sta cercando. Anche il pulsante **Applica** si trova nella parte inferiore dell'elenco, quindi è necessario scorrere verso il basso per visualizzarlo.
    > 
    >    

    Si noti che la query nella casella di testo cambia in quella visualizzata nella casella evidenziata nello screenshot seguente:

    ![Cercare metriche specifiche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Cercare metriche specifiche")

6. Per esaminare più a fondo la metrica specifica. È ad esempio possibile affinare l'output esistente in base alla media delle risorse usate in un intervallo di 10 minuti, classificate in base al nome del cluster usando la query seguente:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Invece di affinare i risultati in base alla media delle risorse usate, è possibile usare la query seguente per affinarli in base a quando sono state usate le risorse massime (nonché il 90° e il 95 ° percentile) in un intervallo di 10 minuti:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Cercare messaggi di log specifici nei cluster

Informazioni su come cercare i messaggi di errore in un intervallo di tempo specifico. Questi passaggi sono solo un esempio di come arrivare al messaggio di errore a cui si è interessati. È possibile usare qualsiasi proprietà disponibile per cercare gli errori che si vuole trovare.

1. Aprire un cluster HDInsight associato ad Azure Log Analytics nel portale di Azure.
2. Fare clic su **Monitoraggio**, quindi fare clic su **Aprire il dashboard OMS**.

    ![Aprire il dashboard OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Aprire il dashboard OMS")

2. Nella schermata iniziale del dashboard OMS fare clic su **Ricerca log**.

    ![Aprire la ricerca log](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Aprire la ricerca log")

3. Digitare la query seguente per cercare tutti i messaggi di errore per tutti i cluster HDInsight configurati per l'uso di Azure Log Analytics e quindi premere **INVIO**. 

         search "Error"

    Verrà visualizzato un output simile al seguente:

    ![Output della ricerca di tutti gli errori](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Output della ricerca di tutti gli errori")

5. Nel riquadro sinistro, nella categoria **Tipo**, selezionare un tipo di errore da analizzare più a fondo e fare clic su **Applica**.  Si noti che i risultati mostrano solo l'errore del tipo selezionato.
7. È possibile esaminare più a fondo l'errore specifico usando le opzioni disponibili nel riquadro sinistro. Ad esempio, 

    - Per cercare solo i messaggi di errore di un nodo del ruolo di lavoro specifico:

        ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Output della ricerca di errori specifici")

    - Per cercare un errore che si è verificato in un preciso momento:

        ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Output della ricerca di errori specifici")

9. Per cercare un errore specifico. È possibile fare clic su **[+]mostra dettagli** per esaminare il messaggio di errore effettivo.

    ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Output della ricerca di errori specifici")

## <a name="create-alerts-for-tracking-events"></a>Creare avvisi per eventi di rilevamento

Il primo passaggio per creare un avviso consiste nel creare una query in base a cui l'avviso viene attivato. Per semplicità, si userà la query seguente che fornisce l'elenco delle applicazioni in esecuzione nei cluster HDInsight in cui si è verificato un errore.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

È possibile usare qualsiasi query per creare un avviso.

1. Aprire un cluster HDInsight associato ad Azure Log Analytics nel portale di Azure.
2. Fare clic su **Monitoraggio**, quindi fare clic su **Aprire il dashboard OMS**.

    ![Aprire il dashboard OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Aprire il dashboard OMS")

2. Nella schermata iniziale del dashboard OMS fare clic su **Ricerca log**.

    ![Aprire la ricerca log](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Aprire la ricerca log")

3. Eseguire la query seguente per creare un avviso, quindi premere **INVIO**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. Fare clic su **Avviso** nella parte superiore della pagina.

    ![Immettere una query per creare un avviso](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Immettere una query per creare un avviso")

4. Nella finestra **Aggiungi regola di avviso** immettere la query e altri dettagli per creare un avviso e fare clic su **Salva**.

    ![Immettere una query per creare un avviso](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Immettere una query per creare un avviso")

    In questo screenshot viene illustrata la configurazione per inviare una notifica tramite posta elettronica quando la query di avviso restituisce un output.

5. È anche possibile modificare o eliminare un avviso esistente. A tale scopo, da qualsiasi pagina nel portale OMS fare clic sull'icona **Impostazioni**.

    ![Immettere una query per creare un avviso](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Immettere una query per creare un avviso")

6. Nella pagina **Impostazioni** fare clic su **Avvisi** per vedere gli avvisi creati. È anche possibile abilitare o disabilitare un avviso, modificarlo o eliminarlo. Per altre informazioni, vedere [Utilizzo delle regole di avviso in Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Vedere anche 

* [Uso di Log Analytics di Operations Management Suite](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Creare regole di avviso in Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
