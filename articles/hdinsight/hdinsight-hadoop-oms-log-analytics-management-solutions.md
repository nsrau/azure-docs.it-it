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
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 9b2871a3dc7e8c3f36666d44e68c298f43fa6267
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Aggiungere soluzioni di gestione di cluster HDInsight in Log Analytics

HDInsight offre soluzioni di gestione specifiche per i cluster che è possibile aggiungere per Azure Log Analytics. Le [soluzioni di gestione](../log-analytics/log-analytics-add-solutions.md) aggiungono funzionalità a [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) e ulteriori dati e strumenti di analisi a Log Analytics. Queste soluzioni raccolgono metriche importanti sulle prestazioni dai cluster HDInsight e forniscono gli strumenti per cercare le metriche. Le soluzioni forniscono anche visualizzazioni e dashboard per la maggior parte dei tipi di cluster supportati in HDInsight. Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati. 

Questo articolo descrive come aggiungere soluzioni di gestione specifiche per i cluster in un'area di lavoro Operations Management Suite.

## <a name="prerequisites"></a>prerequisiti

* È necessario aver configurato un cluster HDInsight per l'uso di Azure Log Analytics. Per istruzioni, vedere [Usare Azure Log Analytics con i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="add-cluster-specific-management-solutions"></a>Aggiungere soluzioni di gestione specifiche per i cluster

In questa sezione si aggiunge una soluzione di gestione di cluster HBase in un'area di lavoro Operations Management Suite esistente.

1. Aprire un cluster HDInsigt nel portale di Azure, fare clic su **Monitoraggio**, quindi fare clic su **Aprire il dashboard OMS**.

    ![Aprire il dashboard Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Aprire il dashboard OMS")

1. Nel dashboard OMS fare clic su **Raccolta soluzioni** o sull'icona **Visualizza finestra di progettazione** nel riquadro sinistro.

    ![Aggiungere una soluzione di gestione in Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Aggiungere una soluzione di gestione in Operations Management Suite")

2. Nella Raccolta soluzioni, fare clic su uno dei seguenti riquadri:

    - Monitoraggio di Hadoop HDInsight
    - Monitoraggio Hbase in HDInsight (anteprima)
    - Monitoraggio Kafka in HDInsight
    - Monitoraggio Storm in HDInsight
    - Monitoraggio Spark in HDInsight

3. Nella schermata successiva fare clic su **Aggiungi**.  La schermata seguente mostra il pulsante Aggiungi per il monitoraggio di HBase.

     ![Aggiungere una soluzione di gestione HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Aggiungere una soluzione di gestione HBase")

4. Viene visualizzato un riquadro sul dashboard OMS per la soluzione di gestione HBase. Se il cluster associato a Operations Management Suite (come parte dei prerequisiti per questo articolo) è un cluster HBase, il riquadro mostra il nome del cluster e il numero di nodi nel cluster.

    ![Soluzione di gestione HBase aggiunta](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "Soluzione di gestione HBase aggiunta")

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query in Azure Log Analytics per monitorare i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Vedere anche 

* [Uso di Log Analytics di Operations Management Suite](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Creare regole di avviso in Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
