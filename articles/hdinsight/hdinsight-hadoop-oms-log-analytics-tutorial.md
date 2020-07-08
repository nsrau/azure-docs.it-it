---
title: Usare i log di Monitoraggio di Azure per monitorare i cluster Azure HDInsight
description: Informazioni su come usare i log di Monitoraggio di Azure per monitorare i processi in esecuzione in un cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 05/13/2020
ms.openlocfilehash: e4096b00ca1bbe1ad7d88c37a846d3f56b2860b1
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085328"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Usare i log di Monitoraggio di Azure per monitorare i cluster HDInsight

Informazioni su come abilitare i log di Monitoraggio di Azure per monitorare le operazioni del cluster Hadoop in HDInsight e su come aggiungere una soluzione di monitoraggio di HDInsight.

I [log di Monitoraggio di Azure](../log-analytics/log-analytics-overview.md) sono un servizio di Monitoraggio di Azure che consente di monitorare gli ambienti cloud e locali per mantenerne la disponibilità e le prestazioni. Il monitoraggio raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio. I dati vengono usati per consentire l'analisi in più origini.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro Log Analytics. Un'area di lavoro è un ambiente dei log di Monitoraggio di Azure univoco con un proprio repository dei dati, origini dati e soluzioni proprie. Per istruzioni, vedere [Creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Disporre di un cluster HDInsight di Azure. Attualmente, è possibile usare i log di Monitoraggio di Azure con i tipi di cluster HDInsight seguenti:

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Per istruzioni su come creare un cluster HDInsight, vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Se si usa PowerShell, è necessario il [modulo Az](https://docs.microsoft.com/powershell/azure/overview). Verificare di avere l'ultima versione. Se necessario, eseguire `Update-Module -Name Az`.

* Se si vuole usare l'interfaccia della riga di comando di Azure e non è ancora stata installata, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!NOTE]  
> È consigliabile posizionare sia il cluster HDInsight che l'area di lavoro Log Analytics nella stessa area per ottenere prestazioni migliori. I log di Monitoraggio di Azure non sono disponibili in tutte le aree di Azure.

## <a name="enable-azure-monitor-using-the-portal"></a>Abilitare Monitoraggio di Azure tramite il portale

In questa sezione si configura un cluster Hadoop HDInsight esistente per usare un'area di lavoro di Azure Log Analytics per monitorare i processi, i log di debug e così via.

1. Selezionare il proprio cluster nel [portale di Azure](https://portal.azure.com/). Il cluster viene aperto in una nuova pagina del portale.

1. A sinistra, in **Monitoraggio** selezionare **Monitoraggio di Azure**.

1. Nella visualizzazione principale in **Integrazione di Monitoraggio di Azure** selezionare **Abilita**.

1. Nell'elenco a discesa **Selezionare un'area di lavoro**, selezionare un'area di lavoro Log Analytics esistente.

1. Selezionare **Salva**.  Sono necessari alcuni minuti per salvare l'impostazione.

    ![Abilitare il monitoraggio per i cluster HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Abilitare il monitoraggio per i cluster HDInsight")

## <a name="enable-azure-monitor-using-azure-powershell"></a>Abilitare Monitoraggio di Azure tramite Azure PowerShell

È possibile abilitare i log di Monitoraggio di Azure usando il cmdlet [Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) del modulo Az di Azure PowerShell.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Per disabilitare, usare il cmdlet [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring):

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Abilitare Monitoraggio di Azure tramite l'interfaccia della riga di comando di Azure

È possibile abilitare i log di Monitoraggio di Azure usando il comando `[az hdinsight monitor enable`] (https://docs.microsoft.com/cli/azure/hdinsight/monitor?view=azure-cli-latest#az-hdinsight-monitor-enable) dell'interfaccia della riga di comando di Azure.

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Per disabilitare, usare il comando [`az hdinsight monitor disable`](https://docs.microsoft.com/cli/azure/hdinsight/monitor?view=azure-cli-latest#az-hdinsight-monitor-disable).

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installare soluzioni di gestione di cluster HDInsight

HDInsight offre soluzioni di gestione specifiche per i cluster che è possibile aggiungere per i log di Monitoraggio di Azure. Le [soluzioni di gestione](../log-analytics/log-analytics-add-solutions.md) aggiungono funzionalità ai log di Monitoraggio di Azure e offrono altri strumenti di analisi e dati. Queste soluzioni raccolgono metriche importanti sulle prestazioni dai cluster HDInsight e includono gli strumenti per cercare le metriche. Le soluzioni forniscono anche visualizzazioni e dashboard per la maggior parte dei tipi di cluster supportati in HDInsight. Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati.

Soluzioni HDInsight disponibili:

* Monitoraggio di Hadoop HDInsight
* Monitoraggio per HDInsight HBase
* Monitoraggio per HDInsight InteractiveQuery
* Monitoraggio Kafka in HDInsight
* Monitoraggio Spark in HDInsight
* Monitoraggio Storm in HDInsight

Per le istruzioni sulle soluzioni di gestione, vedere [Soluzioni di gestione in Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Per provare, installare una soluzione di monitoraggio Hadoop di HDInsight. Al termine verrà visualizzato il riquadro **HDInsightHadoop** elencato sotto **Riepilogo**. Selezionare il riquadro **HDInsightHadoop**. La soluzione HDInsightHadoop appare come segue:

![Visualizzazione della soluzione di monitoraggio di HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Poiché il cluster è nuovo, il report non visualizza tutte le attività.

## <a name="configuring-performance-counters"></a>Configurazione dei contatori delle prestazioni

Monitoraggio di Azure supporta la raccolta e l'analisi delle metriche delle prestazioni per i nodi del cluster. Pe altre informazioni, vedere [Origini dati delle prestazioni di Linux in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters).

## <a name="cluster-auditing"></a>Controllo dei cluster

HDInsight supporta il controllo del cluster con i log di Monitoraggio di Azure tramite l'importazione dei tipi di log seguenti:

* `log_gateway_audit_CL`: questa tabella contiene i log di controllo dei nodi del gateway del cluster con tentativi di accesso riusciti e non riusciti.
* `log_auth_CL`: questa tabella contiene i log SSH con tentativi di accesso riusciti e non riusciti.
* `log_ambari_audit_CL`: questa tabella contiene i log di controllo di Ambari.
* `log_ranger_audti_CL`: questa tabella contiene i log di controllo di Apache Ranger nei cluster ESP.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire query nei log di Monitoraggio di Azure per monitorare i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Come monitorare la disponibilità del cluster con Apache Ambari e i log di Monitoraggio di Azure](./hdinsight-cluster-availability.md)
