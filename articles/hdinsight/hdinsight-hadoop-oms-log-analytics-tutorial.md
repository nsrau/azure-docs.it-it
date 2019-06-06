---
title: Usare monitoraggio di Azure Registra per monitorare i cluster HDInsight di Azure
description: Informazioni su come usare i log di monitoraggio di Azure per monitorare i processi in esecuzione in un cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 16659a335ef6126e75f5a9a99784e71afa056bef
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479257"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Usare monitoraggio di Azure Registra per monitorare i cluster HDInsight

Informazioni su come abilitare i log di monitoraggio di Azure monitorare le operazioni di cluster Hadoop in HDInsight e come aggiungere una soluzione di monitoraggio di HDInsight.

[Log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) è un servizio in Monitoraggio di Azure che consente di monitorare il cloud e in ambienti locali per mantenerne la disponibilità e prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* **Un'area di lavoro di Log Analytics**. È possibile considerare questa area di lavoro come un ambiente di log univoco di monitoraggio di Azure con il proprio repository dei dati, origini dati e soluzioni. Per istruzioni, vedere [Creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Un cluster HDInsight di Azure**. Attualmente, è possibile usare i log di monitoraggio di Azure con i tipi di cluster HDInsight seguenti:

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Per istruzioni su come creare un cluster HDInsight, vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Modulo Azure PowerShell Az**.  Visualizzare [introdurre il nuovo modulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> È consigliabile posizionare sia il cluster HDInsight che l'area di lavoro Log Analytics nella stessa area per ottenere prestazioni migliori. Log di monitoraggio di Azure non è disponibile in tutte le aree di Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Abilitare i log di monitoraggio di Azure usando il portale

In questa sezione si configura un cluster Hadoop HDInsight esistente per usare un'area di lavoro Azure Log Analytics per monitorare i processi, i log di debug e così via.

1. Dal [portale di Azure](https://portal.azure.com/), selezionare il cluster.  Per le istruzioni vedere la sezione su come [elencare e visualizzare i cluster](./hdinsight-administer-use-portal-linux.md#showClusters). Il cluster viene aperto in una nuova pagina del portale.

1. A sinistra, sotto **Monitoraggio**, selezionare **Operations Management Suite**.

1. Dalla vista principale, sotto **Monitoraggio di OMS**, selezionare **Abilita**.

1. Nell'elenco a discesa **Selezionare un'area di lavoro**, selezionare un'area di lavoro Log Analytics esistente.

1. Selezionare **Salva**.  Sono necessari alcuni minuti per salvare l'impostazione.

    ![Abilitare il monitoraggio per i cluster HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Abilitare il monitoraggio per i cluster HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Abilitare i log di monitoraggio di Azure usando Azure PowerShell

È possibile abilitare i log di monitoraggio di Azure usando il modulo Azure PowerShell Az [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) cmdlet.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Per disabilitare l'uso di [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) cmdlet:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installare soluzioni di gestione di cluster HDInsight

HDInsight offre soluzioni di gestione specifiche per i cluster che è possibile aggiungere per i log di monitoraggio di Azure. [Soluzioni di gestione](../log-analytics/log-analytics-add-solutions.md) aggiungono funzionalità a log di monitoraggio di Azure, che fornisce gli strumenti di analisi e dati aggiuntivi. Queste soluzioni raccolgono metriche importanti sulle prestazioni dai cluster HDInsight e forniscono gli strumenti per cercare le metriche. Le soluzioni forniscono anche visualizzazioni e dashboard per la maggior parte dei tipi di cluster supportati in HDInsight. Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati.

Queste sono le soluzioni HDInsight disponibili:

* Monitoraggio di Hadoop HDInsight
* Monitoraggio per HDInsight HBase
* Monitoraggio per HDInsight InteractiveQuery
* Monitoraggio Kafka in HDInsight
* Monitoraggio Spark in HDInsight
* Monitoraggio Storm in HDInsight

Per installare una soluzione di gestione, vedere [Soluzioni di gestione in Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Per provare, installare una soluzione di monitoraggio di Hadoop di HDInsight. Al termine verrà visualizzato il riquadro **HDInsightHadoop** elencato sotto **Riepilogo**. Selezionare il riquadro **HDInsightHadoop**. La soluzione HDInsightHadoop appare come segue:

![Visualizzazione della soluzione di monitoraggio di HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Poiché il cluster è nuovo, il report non visualizza tutte le attività.

## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio di Azure di query di registri per monitorare i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
