---
title: Usare i log di monitoraggio di Azure per monitorare i cluster HDInsight di Azure
description: Informazioni su come usare i log di monitoraggio di Azure per monitorare i processi in esecuzione in un cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7d015f485a51ae1f929e2ecaf1a05811d21594a2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816022"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Usare i log di monitoraggio di Azure per monitorare i cluster HDInsight

Informazioni su come abilitare i log di monitoraggio di Azure per monitorare le operazioni del cluster Hadoop in HDInsight e come aggiungere una soluzione di monitoraggio HDInsight.

[Log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) è un servizio di monitoraggio di Azure che monitora gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* **Un'area di lavoro di Log Analytics**. Questa area di lavoro può essere considerata come un ambiente di log di monitoraggio di Azure univoco con il proprio archivio dati, le origini dati e le soluzioni. Per istruzioni, vedere [Creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Un cluster HDInsight di Azure**. Attualmente, è possibile usare i log di monitoraggio di Azure con i tipi di cluster HDInsight seguenti:

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Per istruzioni su come creare un cluster HDInsight, vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Azure PowerShell AZ Module**.  Vedere [Introduzione al nuovo Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> È consigliabile posizionare sia il cluster HDInsight che l'area di lavoro Log Analytics nella stessa area per ottenere prestazioni migliori. I log di monitoraggio di Azure non sono disponibili in tutte le aree di Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Abilitare i log di monitoraggio di Azure tramite il portale

In questa sezione si configura un cluster Hadoop HDInsight esistente per usare un'area di lavoro Azure Log Analytics per monitorare i processi, i log di debug e così via.

1. Dal [portale di Azure](https://portal.azure.com/)selezionare il cluster.  Per le istruzioni vedere la sezione su come [elencare e visualizzare i cluster](./hdinsight-administer-use-portal-linux.md#showClusters). Il cluster viene aperto in una nuova pagina del portale.

1. A sinistra, sotto **Monitoraggio**, selezionare **Operations Management Suite**.

1. Dalla vista principale, sotto **Monitoraggio di OMS**, selezionare **Abilita**.

1. Nell'elenco a discesa **Selezionare un'area di lavoro**, selezionare un'area di lavoro Log Analytics esistente.

1. Selezionare **Salva**.  Sono necessari alcuni minuti per salvare l'impostazione.

    ![Abilitare il monitoraggio per i cluster HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Abilitare il monitoraggio per i cluster HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Abilitare i log di monitoraggio di Azure usando Azure PowerShell

È possibile abilitare i log di monitoraggio di Azure usando il cmdlet Azure PowerShell AZ module [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) .

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

Per disabilitare, usare il cmdlet [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) :

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installare soluzioni di gestione di cluster HDInsight

HDInsight offre soluzioni di gestione specifiche per i cluster che è possibile aggiungere per i log di monitoraggio di Azure. Le [soluzioni di gestione](../log-analytics/log-analytics-add-solutions.md) aggiungono funzionalità ai log di monitoraggio di Azure, offrendo strumenti di analisi e dati aggiuntivi. Queste soluzioni raccolgono metriche importanti sulle prestazioni dai cluster HDInsight e forniscono gli strumenti per cercare le metriche. Le soluzioni forniscono anche visualizzazioni e dashboard per la maggior parte dei tipi di cluster supportati in HDInsight. Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati.

Queste sono le soluzioni HDInsight disponibili:

* Monitoraggio di Hadoop HDInsight
* Monitoraggio per HDInsight HBase
* Monitoraggio per HDInsight InteractiveQuery
* Monitoraggio Kafka in HDInsight
* Monitoraggio Spark in HDInsight
* Monitoraggio Storm in HDInsight

Per installare una soluzione di gestione, vedere [Soluzioni di gestione in Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Per sperimentare, installare una soluzione di monitoraggio Hadoop di HDInsight. Al termine verrà visualizzato il riquadro **HDInsightHadoop** elencato sotto **Riepilogo**. Selezionare il riquadro **HDInsightHadoop**. La soluzione HDInsightHadoop appare come segue:

![Visualizzazione della soluzione di monitoraggio di HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Poiché il cluster è nuovo, il report non visualizza tutte le attività.

## <a name="configuring-performance-counters"></a>Configurazione dei contatori delle prestazioni

Monitoraggio di Azure supporta anche la raccolta e l'analisi delle metriche delle prestazioni per i nodi del cluster. Per altre informazioni sull'abilitazione e la configurazione di questa funzionalità, vedere [origini dati per le prestazioni Linux in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query nei log di monitoraggio di Azure per monitorare i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
