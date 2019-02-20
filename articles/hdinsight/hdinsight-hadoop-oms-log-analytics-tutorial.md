---
title: Usare Log Analytics per monitorare i cluster Azure HDInsight
description: Informazioni su come usare Azure Log Analytics per monitorare i processi in esecuzione in un cluster HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: cd129ea68315223516ac1cd3e7577b5ee4bf92e5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005115"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Usare Azure Log Analytics per monitorare i cluster HDInsight

Procedura di abilitazione di Azure Log Analytics per monitorare le operazioni del cluster Hadoop in HDInsight e aggiunta di una soluzione di monitoraggio di HDInsight.

[Log Analytics](../log-analytics/log-analytics-overview.md) è un servizio incluso in Monitoraggio di Azure che monitora gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* **Un'area di lavoro di Log Analytics**. Un'area di lavoro è un ambiente di Log Analytics univoco con un archivio dati, origini dati e soluzioni. Per istruzioni, vedere [Creare un'area di lavoro di Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Un cluster HDInsight di Azure**. Attualmente, è possibile usare Log Analytics con i tipi di cluster HDInsight seguenti:

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Per istruzioni su come creare un cluster HDInsight, vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

> [!NOTE]  
> È consigliabile posizionare sia il cluster HDInsight che l'area di lavoro di Log Analytics nella stessa area per ottenere prestazioni migliori. Azure Log Analytics non è disponibile in tutte le aree di Azure.

## <a name="enable-log-analytics-by-using-the-portal"></a>Abilitare Log Analytics tramite il portale

In questa sezione si configura un cluster Hadoop HDInsight esistente per usare un'area di lavoro di Azure Log Analytics per monitorare i processi, i log di debug e così via.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu a sinistra selezionare **Tutti i servizi**.

3. In **ANALISI** selezionare **Cluster HDInsight**.

4. A sinistra, sotto **Monitoraggio**, selezionare **Operations Management Suite**.

5. Dalla vista principale, sotto **Monitoraggio di OMS**, selezionare **Abilita**.

6. Nell'elenco a discesa **Selezionare un'area di lavoro**, selezionare un'area di lavoro di Log Analytics esistente.

7. Selezionare **Salva**.

    ![Abilitare il monitoraggio per i cluster HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Abilitare il monitoraggio per i cluster HDInsight")

    Sono necessari alcuni minuti per salvare l'impostazione.

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Abilitare Log Analytics tramite Azure PowerShell

È possibile abilitare Log Analytics tramite Azure PowerShell. Il cmdlet è:

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

Vedere [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Per disabilitare, il cmdlet è:

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

Vedere [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).

## <a name="install-hdinsight-cluster-management-solutions"></a>Installare soluzioni di gestione di cluster HDInsight

HDInsight offre soluzioni di gestione specifiche per i cluster che è possibile aggiungere per Azure Log Analytics. Le [soluzioni di gestione](../log-analytics/log-analytics-add-solutions.md) aggiungono funzionalità a Log Analytics e forniscono altri strumenti di analisi e dati. Queste soluzioni raccolgono metriche importanti sulle prestazioni dai cluster HDInsight e forniscono gli strumenti per cercare le metriche. Le soluzioni forniscono anche visualizzazioni e dashboard per la maggior parte dei tipi di cluster supportati in HDInsight. Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati.

Queste sono le soluzioni HDInsight disponibili:

* Monitoraggio di Hadoop HDInsight
* Monitoraggio per HDInsight HBase
* Monitoraggio per HDInsight InteractiveQuery
* Monitoraggio Kafka in HDInsight
* Monitoraggio Spark in HDInsight
* Monitoraggio Storm in HDInsight

Per installare una soluzione di gestione, vedere [Soluzioni di gestione in Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Per provare, installare una soluzione di monitoraggio Hadoop di HDInsight. Al termine verrà visualizzato il riquadro **HDInsightHadoop** elencato sotto **Riepilogo**. Selezionare il riquadro **HDInsightHadoop**. La soluzione HDInsightHadoop appare come segue:

![Visualizzazione della soluzione di monitoraggio di HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Poiché il cluster è nuovo, il report non visualizza tutte le attività.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query in Azure Log Analytics per monitorare i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
