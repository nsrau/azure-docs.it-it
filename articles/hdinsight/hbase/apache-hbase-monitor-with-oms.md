---
title: Monitorare HBase con Operations Management Suite (OMS) - Azure HDInsight | Microsoft Docs
description: Usare OMS con Azure Log Analytics per monitorare i cluster HBase in HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: f78d570cfa8b040cd7673a5e14e6a992511f60bb
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="monitor-hbase-with-operations-management-suite-oms"></a>Monitorare HBase con Operations Management Suite (OMS)

La soluzione Monitoraggio HBase in HDInsight usa Azure Log Analytics per raccogliere le metriche delle prestazioni di HBase in HDInsight da dei nodi dei cluster HDInsight. Il monitoraggio fornisce visualizzazioni e dashboard specifici di HBase, strumenti per eseguire ricerche nelle metriche e la possibilità di creare regole e avvisi di monitoraggio personalizzati. È possibile monitorare le metriche per più cluster HBase di HDInsight in più sottoscrizioni di Azure.

Log Analytics è un servizio di [Operations Management Suite (OMS)](../../operations-management-suite/operations-management-suite-overview.md) che monitora gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni. Log Analytics raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio, per consentire l'analisi in più origini.

Le [soluzioni di gestione di Log Analytics](../../log-analytics/log-analytics-add-solutions.md) aggiungono funzionalità a OMS e forniscono strumenti di analisi e dati aggiuntivi. Le soluzioni di gestione di Log Analytics sono una raccolta di regole per la logica, la visualizzazione e l'acquisizione dei dati che forniscono le metriche per un'area specifica. Una soluzione può anche definire nuovi tipi di record da raccogliere, che possono essere analizzati con ricerche nei log o con le nuove funzionalità dell'interfaccia utente.

[Informazioni dettagliate e analisi](https://azure.microsoft.com/pricing/details/insight-analytics/) si basa sulla piattaforma di Log Analytics. È possibile scegliere di usare le funzionalità di Log Analytics e pagare in base ai GB inseriti nel servizio oppure passare al livello Informazioni dettagliate e analisi dell'area di lavoro e pagare in base ai nodi gestiti dal servizio. Informazioni dettagliate e analisi offre un soprainsieme delle funzionalità offerte da Log Analytics. La soluzione di monitoraggio HBase è disponibile sia con Log Analytics che con Informazioni dettagliate e analisi.

Quando si effettua il provisioning di una soluzione Monitoraggio HBase in HDInsight, si crea un'area di lavoro di OMS. Ogni area di lavoro può essere considerata come un ambiente di Log Analytics univoco con un archivio dati, origini dati e soluzioni personalizzati. È possibile creare più aree di lavoro nella sottoscrizione per supportare più ambienti, ad esempio di produzione e test.

## <a name="provision-hdinsight-hbase-monitoring"></a>Effettuare il provisioning di Monitoraggio HBase in HDInsight

1. Accedere al [portale di Azure](https://portal.azure.com) usando la sottoscrizione di Azure.
2. Nel riquadro **Nuovo** in **Marketplace**, selezionare **Monitoraggio e gestione**.
3. Nel riquadro **Monitoraggio e gestione** selezionare **Visualizza tutto**.

    ![Riquadro Monitoraggio e gestione](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. Nell'elenco cercare **Soluzioni di gestione**. A destra di **Soluzioni di gestione** selezionare **Altro**.

    ![Riquadro Monitoraggio e gestione](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. Nel riquadro **Soluzioni di gestione** selezionare la soluzione di gestione Monitoraggio HBase in HDInsight da aggiungere a un'area di lavoro.

    ![Riquadro Soluzioni di gestione](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. Nel riquadro della soluzione di gestione esaminare le informazioni sulla soluzione e quindi selezionare **Crea**. 
7. Nel riquadro relativo al *nome della soluzione di gestione* selezionare un'area di lavoro esistente da associare alla soluzione di gestione oppure creare una nuova area di lavoro di OMS e quindi selezionarla.
8. Modificare come appropriato le impostazioni dell'area di lavoro per la sottoscrizione di Azure, il gruppo di risorse e la posizione. 
    ![area di lavoro della soluzione](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. Selezionare **Create**.  
10. Per usare questa nuova soluzione di gestione nella relativa area di lavoro, passare a **Log Analytics** > ***nome area di lavoro*** > **Soluzioni**. Nell'elenco verrà visualizzata una voce per la soluzione di gestione. Selezionare la voce per passare alla soluzione.

    ![Soluzioni di Log Analytics](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. Verrà visualizzato il riquadro per la soluzione Monitoraggio HBase in HDInsight.

    ![Riquadro per le soluzioni HBase in HDInsight](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. I riquadri di riepilogo non contengono dati perché non è stato ancora configurato il cluster HBase di HDInsight per l'invio dei dati a Log Analytics.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Connettere il cluster HBase di HDInsight a Log Analytics

Per usare gli strumenti forniti da Monitoraggio HBase in HDInsight, è necessario configurare il cluster in modo che trasmetta le metriche dal relativo server di area, dai nodi head e dai nodi ZooKeeper a Log Analytics. Questa configurazione viene ottenuta eseguendo un'azione script nel cluster HBase di HDInsight.

### <a name="get-oms-workspace-id-and-workspace-key"></a>Ottenere l'ID dell'area di lavoro OMS e la chiave dell'area di lavoro

Per consentire ai nodi del cluster di eseguire l'autenticazione con Log Analytics, sono necessari l'ID dell'area di lavoro OMS e la chiave dell'area di lavoro. Per ottenere questi valori:

1. Nel riquadro della soluzione di monitoraggio HBase nel portale di Azure selezionare Panoramica.

    ![Riquadro della soluzione di monitoraggio HBase](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. Selezionare il portale di OMS per aprirlo in una nuova scheda o finestra del browser.

    ![Selezionare il portale di OMS](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. Nella home page del portale di OMS selezionare Impostazioni.

    ![Portale di OMS](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. Selezionare Origini connesse e quindi Server Linux.

    ![Selezionare Origini connesse e quindi Server Linux](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. Si noti che vengono visualizzati i valori relativi a ID dell'area di lavoro e chiave primaria, che sono i valori necessari per l'azione script.

### <a name="run-the-script-action"></a>Eseguire l'azione script

Per consentire la raccolta dati dal cluster HBase di HDInsight, eseguire un'azione script in tutti i nodi del cluster:

1. Passare al riquadro per il cluster HBase di HDInsight nel portale di Azure.
2. Selezionare **Azioni script**.

    ![Azioni script](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. Selezionare **Invia nuova**.

    ![Invia nuova](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. Nell'azione **Invia script** impostare il tipo di script su `"- Custom"`.
5. Specificare un nome per lo script.
6. Per **URI script Bash** incollare l'URI seguente:

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. Per **Tipi di nodo** selezionare tutti e tre i tipi (**Head**, **Area** e **ZooKeeper**).
8. Nella casella di testo **Parametri** immettere l'ID dell'area di lavoro e la chiave dell'area di lavoro, racchiudendo ogni valore tra virgolette e separandoli con uno spazio.

        "<Workspace ID>" "<Workspace Key>"

9. Selezionare **Salvare questa azione script in modo permanente per eseguirla di nuovo quando vengono aggiunti nuovi nodi al cluster**.

    ![Impostazioni dell'azione script](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. Selezionare **Create**.
11. Per l'esecuzione dell'azione script sono necessari alcuni minuti. È possibile monitorare lo stato nel riquadro Azioni script.

    ![Azione script in esecuzione](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. Al termine dell'esecuzione dell'azione script, verrà visualizzato un segno di spunta verde accanto al nome dello script nell'elenco.

    ![Azione script completata](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>Visualizzare la soluzione Monitoraggio HBase in HDInsight

Al termine dell'esecuzione dell'azione script, verranno visualizzati i dati nella soluzione di monitoraggio entro pochi minuti.

1. Nel portale di Azure passare al riquadro della soluzione HBase in HDInsight.
2. Selezionare la scheda **Panoramica**.
3. In **Riepilogo** viene visualizzato un riquadro che indica il numero di server di area monitorati.

    ![Riquadro di riepilogo del monitoraggio](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. Selezionare il riquadro con il numero di server di area. Verrà visualizzato il dashboard principale.
5. Questo dashboard fornisce l'accesso alle statistiche sulle aree, il numero di log write-ahead in uso, una raccolta di ricerche di Log Analytics (ad esempio per i log dei server di area, i log Phoenix e le eccezioni) e una raccolta dei grafici più comuni per visualizzare le metriche rilevanti. 

    ![Dashboard principale](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. Selezionando una delle voci, verrà eseguito il drill-down nella visualizzazione della ricerca log, dove è possibile affinare la query ed esplorare i dati in modo più dettagliato.

## <a name="next-steps"></a>Passaggi successivi

* [Creazione di avvisi in OMS Log Analytics](../../log-analytics/log-analytics-alerts-creating.md)
* [Trovare dati con ricerche log in Azure Log Analytics](../../log-analytics/log-analytics-log-searches.md).
