---
title: Usare Log Analytics per monitorare i cluster Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Azure Log Analytics per monitorare i processi in esecuzione in un cluster HDInsight.
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
ms.date: 11/08/2017
ms.author: nitinme
ms.openlocfilehash: 73c472140861a0d0d270021ab268e8c1113c23b5
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Usare Azure Log Analytics per monitorare i cluster HDInsight

Informazioni su come usare Azure Log Analytics per monitorare le operazioni del cluster Hadoop in HDInsight.

[Log Analytics](../log-analytics/log-analytics-overview.md) è un servizio di [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) che consente di monitorare gli ambienti cloud e locali per garantirne la disponibilità e le prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini. 

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento ad Azure. Vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/free).

* **Un cluster HDInsight di Azure**. Attualmente, è possibile usare Azure OMS con i tipi di cluster HDInsight seguenti:

    * Hadoop
    * HBase
    * Interactive Query
    * Kafka
    * Spark
    * Storm

    Per istruzioni su come creare un cluster HDInsight, vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Un'area di lavoro di Log Analytics**. Un'area di lavoro è un ambiente di Log Analytics univoco con un archivio dati, origini dati e soluzioni. È necessario avere un'area di lavoro di questo tipo già creata, da poter associare ai cluster Azure HDInsight. Per istruzioni, vedere [Creare un'area di lavoro di Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-log-analytics"></a>Configurare il cluster HDInsight per l'uso di Log Analytics

In questa sezione si configura un cluster Hadoop HDInsight esistente per usare un'area di lavoro di Azure Log Analytics per monitorare i processi, i log di debug e così via.

1. Nel portale di Azure aprire un cluster HDInsight.
2. Nel riquadro sinistro fare clic su **Monitoraggio**.
3. Nel riquadro destro fare clic su **Abilita**, selezionare un'area di lavoro di Log Analytics esistente e fare clic su **Salva**.

    ![Abilitare il monitoraggio per i cluster HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Abilitare il monitoraggio per i cluster HDInsight")

    Sono necessari alcuni minuti per salvare l'impostazione.  Al termine, è possibile visualizzare un pulsante **Aprire il dashboard OMS** nella parte superiore. 

    ![Aprire il dashboard OMS](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Aprire il dashboard OMS")

5. Fare clic su **Aprire il dashboard OMS**.
6. Immettere le credenziali di Azure, se richiesto.

    ![Portale di Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Portale di Operations Management Suite")

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere soluzioni di gestione di cluster HDInsight in Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
