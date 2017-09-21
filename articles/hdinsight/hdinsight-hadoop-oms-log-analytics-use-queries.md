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
ms.date: 09/11/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Eseguire query in Azure Log Analytics per monitorare i cluster HDInsight (Anteprima)

Questo articolo descrive alcuni scenari relativi alle modalità d'uso di Azure Log Analytics con i cluster Azure HDInsight. I tre scenari più comuni sono:

* Analizzare le metriche dei cluster HDInsight in OMS
* Cercare messaggi di log specifici per i cluster HDInsight
* Creare avvisi basati sugli eventi che si verificano nei cluster

## <a name="prerequisites"></a>Prerequisiti

* È necessario aver configurato un cluster HDInsight per l'uso di Azure Log Analytics. Per istruzioni, vedere [Usare Azure Log Analytics con i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* È necessario aver aggiunto soluzioni di gestione specifiche per i cluster HDInsight all'area di lavoro OMS, come descritto in [Aggiungere soluzioni di gestione di cluster HDInsight in Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>Analizzare le metriche dei cluster HDInsight in OMS

Questa sezione illustra i passaggi dettagliati per cercare metriche specifiche per il cluster HDInsight.

1. Aprire il dashboard OMS. Nel portale di Azure aprire il pannello del cluster HDInsight associato ad Azure Log Analytics, fare clic sulla scheda Monitoraggio e quindi fare clic su **Aprire il dashboard OMS**.

    ![Aprire il dashboard OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Aprire il dashboard OMS")

2. Nella schermata iniziale del dashboard OMS fare clic su **Ricerca log**.

    ![Aprire la ricerca log](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Aprire la ricerca log")

3. Nella casella di testo **Iniziare la ricerca qui** della finestra Ricerca log digitare `*` per cercare tutte le metriche disponibili per tutti i cluster HDInsight configurati per l'uso di Azure Log Analytics. Premere INVIO.

    ![Cercare tutte le metriche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Cercare tutte le metriche")

4. Dovrebbe venire visualizzato un output simile al seguente.

    ![Output della ricerca di tutte le metriche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Output della ricerca di tutte le metriche")

5. Nel riquadro sinistro, in **Tipo**, cercare una metrica da analizzare più a fondo. Per questa esercitazione, scegliere `metrics_resourcemanager_queue_root_default_CL`. Selezionare la casella di controllo corrispondente alla metrica e quindi fare clic su **Applica**.

    > [!NOTE]
    > Potrebbe essere necessario fare clic sul pulsante **[+]Altro** per trovare la metrica che si sta cercando. Anche il pulsante **Applica** si trova nella parte inferiore dell'elenco, quindi è necessario scorrere verso il basso per visualizzarlo.
    > 
    >    
    Si noti che la query nella casella di testo cambia ora in quella visualizzata nella casella evidenziata nello screenshot seguente:

    ![Cercare metriche specifiche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Cercare metriche specifiche")

6. È ora possibile esaminare più a fondo la metrica specifica. È ad esempio possibile affinare l'output esistente in base alla media delle risorse usate in un intervallo di 10 minuti, classificate in base al nome del cluster. Digitare la query seguente nella relativa casella di testo.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![Cercare metriche specifiche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "Cercare metriche specifiche")

7. Invece di affinare i risultati in base alla media delle risorse usate, è possibile usare la query seguente per affinarli in base a quando sono state usate le risorse massime (nonché il 90° e il 95 ° percentile) in un intervallo di 10 minuti.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![Cercare metriche specifiche](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "Cercare metriche specifiche")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>Cercare messaggi di log specifici nei cluster HDInsight

Questa sezione illustra i passaggi dettagliati per cercare i messaggi di errore in un intervallo di tempo specifico. Questi passaggi sono solo un esempio di come arrivare al messaggio di errore a cui si è interessati. È possibile usare qualsiasi proprietà disponibile per cercare gli errori che si vuole trovare.

1. Aprire il dashboard OMS. Nel portale di Azure aprire il pannello del cluster HDInsight associato ad Azure Log Analytics, fare clic sulla scheda Monitoraggio e quindi fare clic su **Aprire il dashboard OMS**.

    ![Aprire il dashboard OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Aprire il dashboard OMS")

2. Nella schermata iniziale del dashboard OMS fare clic su **Ricerca log**.

    ![Aprire la ricerca log](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Aprire la ricerca log")

3. Nella casella di testo **Iniziare la ricerca qui** della finestra Ricerca log digitare `"Error"` (con le virgolette) per cercare tutti i messaggi di errore per tutti i cluster HDInsight configurati per l'uso di Azure Log Analytics. Premere INVIO.

    ![Cercare tutti gli errori](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "Cercare tutti gli errori")

4. Dovrebbe venire visualizzato un output simile al seguente.

    ![Output della ricerca di tutti gli errori](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Output della ricerca di tutti gli errori")

5. Nel riquadro sinistro, in **Tipo**, cercare un tipo di errore da analizzare più a fondo. Per questa esercitazione, scegliere `log_sparkappsexecutors_CL`. Selezionare la casella di controllo corrispondente alla metrica e quindi fare clic su **Applica**.

    ![Cercare errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "Cercare errori specifici")

        
6. Si noti che la query nella casella di testo cambia ora in quella visualizzata nella casella evidenziata sotto e i risultati mostrano solo l'errore del tipo selezionato.

    ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "Output della ricerca di errori specifici")

7. È ora possibile esaminare più a fondo l'errore specifico usando le opzioni disponibili nel riquadro sinistro. È ad esempio possibile affinare la query per cercare solo i messaggi di errore di un nodo del ruolo di lavoro specifico.

    ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Output della ricerca di errori specifici")

8. È possibile restringere ulteriormente la ricerca in base all'ora in cui si ritiene che si sia verificato l'errore selezionando il valore temporale rilevante nel riquadro sinistro.

    ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Output della ricerca di errori specifici")

9. A questo punto, è stato raggiunto l'errore specifico che si sta cercando. È possibile fare clic su **[+]mostra dettagli** per esaminare il messaggio di errore effettivo.

    ![Output della ricerca di errori specifici](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Output della ricerca di errori specifici")

## <a name="create-alerts-to-track-events"></a>Creare avvisi per tenere traccia degli eventi

Il primo passaggio per creare un avviso consiste nel creare una query in base a cui l'avviso viene attivato. Per semplicità, si userà la query seguente che fornisce l'elenco delle applicazioni in esecuzione nei cluster HDInsight in cui si è verificato un errore.

    * (Type=metrics_resourcemanager_queue_root_default_CL) AppsFailed_d>0 

È possibile usare qualsiasi query per creare un avviso.

1. Aprire il dashboard OMS. Nel portale di Azure aprire il pannello del cluster HDInsight associato ad Azure Log Analytics, fare clic sulla scheda Monitoraggio e quindi fare clic su **Aprire il dashboard OMS**.

    ![Aprire il dashboard OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Aprire il dashboard OMS")

2. Nella schermata iniziale del dashboard OMS fare clic su **Ricerca log**.

    ![Aprire la ricerca log](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Aprire la ricerca log")

3. Nella casella di testo **Iniziare la ricerca qui** della finestra Ricerca log incollare la query in base a cui si vuole creare un avviso, premere INVIO e quindi fare clic sul pulsante **Avviso**.

    ![Immettere una query per creare un avviso](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Immettere una query per creare un avviso")

4. Nella finestra **Aggiungi regola di avviso** immettere la query e altri dettagli per creare un avviso e fare clic su **Salva**.

    ![Immettere una query per creare un avviso](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Immettere una query per creare un avviso")

    In questo screenshot viene inviata una notifica di posta elettronica solo se la query di avviso recupera un output.

5. È anche possibile modificare o eliminare un avviso esistente. A tale scopo, da qualsiasi pagina nel portale OMS fare clic sull'icona **Impostazioni**.

    ![Immettere una query per creare un avviso](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Immettere una query per creare un avviso")

6. Nella pagina **Impostazioni** fare clic su **Avvisi** per vedere gli avvisi creati. È anche possibile abilitare o disabilitare un avviso, modificarlo o eliminarlo. Per altre informazioni, vedere [Utilizzo delle regole di avviso in Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Vedere anche

* [Uso di OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Creare regole di avviso in Log Analytics](../log-analytics/log-analytics-alerts-creating.md)

