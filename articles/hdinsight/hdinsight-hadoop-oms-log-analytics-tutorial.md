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
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: dbd3d0ed4337d4fe86465c5c59bf20c0a50a87b4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Usare Azure Log Analytics per monitorare i cluster HDInsight (Anteprima)

Questo articolo descrive come usare Azure Log Analytics per monitorare le operazioni sui cluster Hadoop HDInsight.

Log Analytics è un servizio di [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) che monitora gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini. 

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento ad Azure. Vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/free).

* **Un cluster HDInsight di Azure**. Attualmente, è possibile usare Azure OMS con i tipi di cluster HDInsight seguenti:
    * Hadoop
    * Spark
    * HBase
    * Storm
    * Kafka
    * Interactive Hive

    Per istruzioni su come creare un cluster HDInsight, vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).


* **Un'area di lavoro di Log Analytics**. Un'area di lavoro è un ambiente di Log Analytics univoco con un archivio dati, origini dati e soluzioni. È necessario avere un'area di lavoro di questo tipo già creata, da poter associare ai cluster Azure HDInsight. Per istruzioni, vedere [Creare un'area di lavoro di Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>Configurare il cluster HDInsight per l'uso di Azure Log Analytics

In questa sezione si configura un cluster Hadoop HDInsight esistente per usare un'area di lavoro di Azure Log Analytics per monitorare i processi, i log di debug e così via.

1. Nel riquadro sinistro del portale di Azure fare clic su **Cluster HDInsight** e quindi sul nome del cluster da configurare con Azure Log Analytics.

2. Nel pannello del cluster fare clic su **Monitoraggio** nel riquadro sinistro.

3. Nel riquadro destro fare clic su **Abilita** e selezionare un'area di lavoro di Log Analytics esistente. Fare clic su **Salva**.

    ![Abilitare il monitoraggio per i cluster HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Abilitare il monitoraggio per i cluster HDInsight")

4. Dopo aver configurato il cluster per l'uso di Log Analytics per il monitoraggio, viene visualizzata l'opzione **Aprire il dashboard OMS** nella parte superiore della scheda. Fare clic sul pulsante.

    ![Aprire il dashboard OMS](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Aprire il dashboard OMS")

5. Immettere le credenziali di Azure, se richiesto. Si verrà reindirizzati al dashboard di Microsoft OMS.

    ![Portale di Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Portale di Operations Management Suite")

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere soluzioni di gestione di cluster HDInsight in Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
