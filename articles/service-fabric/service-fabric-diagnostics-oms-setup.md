---
title: Configurare il monitoraggio con i log di Monitoraggio di AzureSet up monitoring with Azure Monitor logs
description: Informazioni su come configurare i log di Monitoraggio di Azure per la visualizzazione e l'analisi degli eventi per monitorare i cluster di Azure Service Fabric.Learn how to set up Azure Monitor logs for visualizing and analyzing events to monitor your Azure Service Fabric clusters.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: cf0fab9942dcbb7ee09e554f2c9ba8738f208009
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609928"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Configurare i log di Monitoraggio di Azure per un clusterSet up Azure Monitor logs for a cluster

I log di Monitoraggio di Azure sono consigliati per il monitoraggio di eventi a livello di cluster. È possibile configurare l'area di lavoro Log Analytics tramite Azure Resource Manager, PowerShell o Azure Marketplace. Se si mantiene un modello di Resource Manager aggiornato della distribuzione per un uso futuro, usare lo stesso modello per configurare l'ambiente dei log di Monitoraggio di Azure.If you maintain an updated Resource Manager template of your deployment for future use, use the same template to set up your Azure Monitor logs environment. La distribuzione tramite Marketplace è più semplice se è già disponibile un cluster distribuito, con il servizio di diagnostica abilitato. Se non si ha accesso a livello di sottoscrizione nell'account in cui si sta eseguendo la distribuzione, eseguire la distribuzione usando PowerShell o il modello di Resource Manager.

> [!NOTE]
> Per configurare i log di Monitoraggio di Azure per monitorare il cluster, è necessario che la diagnostica sia abilitata per visualizzare gli eventi a livello di cluster o di piattaforma. Fare riferimento alle procedure per la [configurazione della diagnostica nei cluster Windows](service-fabric-diagnostics-event-aggregation-wad.md) e la [configurazione della diagnostica nei cluster Linux](service-fabric-diagnostics-oms-syslog.md) per altre informazioni.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Distribuire un'area di lavoro Log Analytics tramite Azure Marketplace

Se si intende aggiungere un'area di lavoro Log Analytics dopo aver distribuito un cluster, andare al portale di Azure Marketplace e cercare **Analisi Service Fabric**. Si tratta di una soluzione personalizzata per le distribuzioni di Service Fabric con dati specifici di Service Fabric. In questo processo verranno create la soluzione (dashboard per la visualizzazione di informazioni dettagliate) e l'area di lavoro (aggregazione dei dati del cluster sottostanti).

1. Selezionare **Nuovo** nel menu di spostamento a sinistra. 

2. Cercare **Analisi Service Fabric**. Selezionare la risorsa visualizzata.

3. Selezionare **Crea**.

    ![Analisi Service Fabric in Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Nella finestra di creazione di Analisi Service Fabric scegliere **Selezionare un'area di lavoro** per il campo **Area di lavoro di OMS** e quindi su **Crea una nuova area di lavoro**. Compilare le voci necessarie. L'unico requisito è che la sottoscrizione per il cluster di Service Fabric e l'area di lavoro sia la stessa. Dopo aver convalidato le voci, verrà avviata la distribuzione dell'area di lavoro. La distribuzione richiede solo alcuni minuti.

5. Al termine, selezionare di nuovo **Crea** nella parte inferiore della finestra di creazione di Analisi Service Fabric. Assicurarsi che la nuova area di lavoro sia visualizzata in **Area di lavoro OMS**. Questa azione aggiunge la soluzione all'area di lavoro creata.

Se si usa Windows, continuare con la procedura seguente per connettere i log di Monitoraggio di Azure all'account di archiviazione in cui sono archiviati gli eventi del cluster. 

>[!NOTE]
>La soluzione Service Fabric Analytics è supportata solo per i cluster Windows.The Service Fabric Analytics solution is only supported for Windows clusters. Per i cluster Linux, vedere l'articolo su come configurare i log di Monitoraggio di [Azure per i cluster Linux.For Linux clusters,](service-fabric-diagnostics-oms-syslog.md)check out our article on how to set up Azure Monitor logs for Linux clusters .  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Connettere l'area di lavoro Log Analytics al cluster 

1. L'area di lavoro deve essere connessa ai dati di diagnostica provenienti dal cluster. Andare al gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric. Selezionare **ServiceFabric\<NomeAreaDiLavoro\>** e andare alla relativa pagina di panoramica. Da qui è possibile modificare le impostazioni della soluzione, le impostazioni dell'area di lavoro e accedere all'area di lavoro Log Analytics.

2. Nel menu di spostamento a sinistra in **Origini dati dell'area di lavoro** selezionare **Log account di archiviazione**.

3. Nella pagina **Log account di archiviazione** selezionare **Aggiungi** nella parte superiore per aggiungere i log del cluster all'area di lavoro.

4. Selezionare **Account di archiviazione** per aggiungere l'account appropriato creato nel cluster. Se è stato usato il nome predefinito, l'account di archiviazione è **sfdg\<resourceGroupName\>**. È anche possibile verificare con il modello di Azure Resource Manager usato per distribuire il cluster, controllando il valore usato per **applicationDiagnosticsStorageAccountName**. Se il nome non viene visualizzato, scorrere verso il basso e selezionare **Carica altro**. Selezionare il nome dell'account di archiviazione.

5. Specificare il Tipo di dati. Impostarlo su **Eventi di Service Fabric**.

6. Assicurarsi che il valore di Origine sia impostato automaticamente su **WADServiceFabric\*EventTable**.

7. Selezionare **OK** per connettere l'area di lavoro ai log del cluster.

    ![Aggiungere log dell'account di archiviazione ai log di Monitoraggio di AzureAdd storage account logs to Azure Monitor logs](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

L'account viene ora visualizzato come parte dei log degli account di archiviazione nelle origini dati dell'area di lavoro.

La soluzione Analisi Service Fabric è stata aggiunta in un'area di lavoro Log Analytics, che è ora connessa correttamente alla piattaforma del cluster e alla tabella del log applicazioni. È possibile aggiungere altre origini all'area di lavoro nello stesso modo.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Distribuire i log di Monitoraggio di Azure con Azure Resource ManagerDeploy Azure Monitor logs with Azure Resource Manager

Quando si distribuisce un cluster con un modello di Resource Manager, il modello crea una nuova area di lavoro Log Analytics, aggiunge la soluzione Service Fabric all'area di lavoro e la configura per leggere i dati dalle tabelle di archiviazione appropriate.

È possibile usare e modificare [questo modello di esempio](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) per soddisfare i requisiti. Questo modello consente di:

* Creare un cluster di Service Fabric composto da 5 nodi
* Creare un'area di lavoro Log Analytics e una soluzione Service Fabric
* Configurare l'agente di Log Analytics in modo da raccogliere e inviare 2 contatori delle prestazioni di esempio all'area di lavoro
* Configurare WAD in modo da raccogliere i dati di Service Fabric e inviarli alle tabelle di archiviazione di Azure (WADServiceFabric*EventTable)
* Configurare l'area di lavoro Log Analytics per la lettura degli eventi da queste tabelle


È possibile distribuire il modello come aggiornamento di `New-AzResourceGroupDeployment` Resource Manager al cluster usando l'API nel modulo di Azure PowerShell.You can deploy the template as a Resource Manager upgrade to your cluster by using the API in the Azure PowerShell module. Un comando di esempio potrebbe essere:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager rileva che questo comando è un aggiornamento a una risorsa esistente. Elabora solo le modifiche tra il modello che definisce la distribuzione esistente e il nuovo modello fornito.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Distribuire i log di Monitoraggio di Azure con Azure PowerShellDeploy Azure Monitor logs with Azure PowerShell

È anche possibile distribuire la risorsa di `New-AzOperationalInsightsWorkspace` analisi dei log tramite PowerShell usando il comando. Per usare questo metodo, assicurarsi che sia installato [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Usare questo script per creare una nuova area di lavoro Log Analytics e aggiungervi la soluzione Service Fabric: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Al termine, seguire i passaggi nella sezione precedente per connettere i log di Monitoraggio di Azure all'account di archiviazione appropriato.

È anche possibile aggiungere altre soluzioni o apportare altre modifiche all'area di lavoro Log Analytics usando PowerShell. Per altre informazioni, vedere Gestire i log di [Monitoraggio di Azure tramite PowerShell.To](../azure-monitor/platform/powershell-workspace-configuration.md)learn more, see Manage Azure Monitor logs using PowerShell.

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire l'agente di Log Analytics](service-fabric-diagnostics-oms-agent.md) sui nodi per raccogliere contatori delle prestazioni, statistiche Docker e registri per i contenitori
* Acquisire familiarità con le funzionalità di [ricerca dei log e di query](../log-analytics/log-analytics-log-searches.md) offerte come parte dei log di Monitoraggio di Azure
* [Usare Progettazione viste per creare visualizzazioni personalizzate nei log di Monitoraggio di AzureUse View Designer to create custom views in Azure Monitor logs](../azure-monitor/platform/view-designer.md)
