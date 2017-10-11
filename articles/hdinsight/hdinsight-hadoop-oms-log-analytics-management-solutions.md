---
title: Aggiungere soluzioni di gestione di cluster HDInsight in Azure Log Analytics | Microsoft Docs
description: Informazioni su come usare Azure Log Analytics per creare visualizzazioni personalizzate per i cluster HDInsight.
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
ms.date: 09/12/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 21b474e37ef0a6037e05ee1fe8e5088cb3e3601d
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics-preview"></a>Aggiungere soluzioni di gestione di cluster HDInsight in Log Analytics (Anteprima)

HDInsight offre soluzioni di gestione specifiche per i cluster che è possibile aggiungere per Azure Log Analytics. Le [soluzioni di gestione](../log-analytics/log-analytics-add-solutions.md) aggiungono funzionalità a OMS e ulteriori dati e strumenti di analisi a Log Analytics. Queste soluzioni raccolgono metriche importanti sulle prestazioni dai cluster HDInsight e forniscono gli strumenti per cercare le metriche. Le soluzioni forniscono anche visualizzazioni e dashboard per la maggior parte dei tipi di cluster supportati in HDInsight. Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati. 

Questo articolo descrive come aggiungere soluzioni di gestione specifiche per i cluster in un'area di lavoro OMS.

## <a name="prerequisites"></a>Prerequisiti

* È necessario aver configurato un cluster HDInsight per l'uso di Azure Log Analytics. Per istruzioni, vedere [Usare Azure Log Analytics con i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Aggiungere soluzioni di gestione specifiche per i cluster

In questa sezione si aggiunge una soluzione di gestione di cluster HBase in un'area di lavoro OMS esistente. A breve saranno disponibili soluzioni simili per altri tipi di cluster HDInsight.

1. Aprire il dashboard OMS. Nel portale di Azure aprire il pannello del cluster HDInsight associato ad Azure Log Analytics, fare clic sulla scheda Monitoraggio e quindi fare clic su **Aprire il dashboard OMS**.

    ![Aprire il dashboard OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Aprire il dashboard OMS")

1. Nel dashboard OMS fare clic su **Raccolta soluzioni** o sull'icona Visualizza finestra di progettazione nel riquadro sinistro.

    ![Aggiungere una soluzione di gestione in OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Aggiungere una soluzione di gestione in OMS")

2. In Raccolta soluzioni trovare **Monitoraggio Hbase in HDInsight** e quindi fare clic sul riquadro.

    ![Trovare una soluzione di gestione HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/find-hbase-management-solution.png "Trovare una soluzione di gestione HBase")

3. Nella schermata successiva fare clic su **Aggiungi**.

     ![Aggiungere una soluzione di gestione HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Aggiungere una soluzione di gestione HBase")

4. A questo punto, dovrebbe venire visualizzato un riquadro nel dashboard OMS per la soluzione di gestione HBase. Se il cluster associato a OMS (come parte dei prerequisiti per questo articolo) è un cluster HBase, il riquadro mostra il nome del cluster e il numero di nodi nel cluster.

    ![Soluzione di gestione HBase aggiunta](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "Soluzione di gestione HBase aggiunta")

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query in Azure Log Analytics per monitorare i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Vedere anche

* [Uso di OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Creare regole di avviso in Log Analytics](../log-analytics/log-analytics-alerts-creating.md)

