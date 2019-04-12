---
title: Azure Service Fabric - configurare il monitoraggio con i log di monitoraggio di Azure | Microsoft Docs
description: Informazioni su come configurare i log di monitoraggio di Azure per visualizzare e analizzare gli eventi per monitorare i cluster Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: ba4d25c749a1c1b99559ce4033fe90d671701d66
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046479"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Configurare i log di monitoraggio di Azure per un cluster

I log di Monitoraggio di Azure sono consigliati per il monitoraggio di eventi a livello di cluster. È possibile configurare l'area di lavoro Log Analytics tramite Azure Resource Manager, PowerShell o Azure Marketplace. Se si gestisce un modello di Resource Manager aggiornato della distribuzione per un uso futuro, utilizzare lo stesso modello per configurare l'ambiente di log di monitoraggio di Azure. La distribuzione tramite Marketplace è più semplice se è già disponibile un cluster distribuito, con il servizio di diagnostica abilitato. Se non si ha accesso a livello di sottoscrizione nell'account in cui si sta eseguendo la distribuzione, eseguire la distribuzione usando PowerShell o il modello di Resource Manager.

> [!NOTE]
> Per configurare i log di monitoraggio di Azure per monitorare il cluster, è necessario per la diagnostica è abilitata per visualizzare gli eventi a livello di cluster o a livello di piattaforma. Fare riferimento alle procedure per la [configurazione della diagnostica nei cluster Windows](service-fabric-diagnostics-event-aggregation-wad.md) e la [configurazione della diagnostica nei cluster Linux](service-fabric-diagnostics-oms-syslog.md) per altre informazioni.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Distribuire un'area di lavoro Log Analytics tramite Azure Marketplace

Se si intende aggiungere un'area di lavoro Log Analytics dopo aver distribuito un cluster, andare al portale di Azure Marketplace e cercare **Analisi Service Fabric**. Si tratta di una soluzione personalizzata per le distribuzioni di Service Fabric con dati specifici di Service Fabric. In questo processo verranno create la soluzione (dashboard per la visualizzazione di informazioni dettagliate) e l'area di lavoro (aggregazione dei dati del cluster sottostanti).

1. Selezionare **Nuovo** nel menu di spostamento a sinistra. 

2. Cercare **Analisi Service Fabric**. Selezionare la risorsa visualizzata.

3. Selezionare **Create**.

    ![Analisi Service Fabric in Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Nella finestra di creazione di Analisi Service Fabric scegliere **Selezionare un'area di lavoro** per il campo **Area di lavoro di OMS** e quindi su **Crea una nuova area di lavoro**. Compilare le voci necessarie. L'unico requisito è che la sottoscrizione per il cluster di Service Fabric e l'area di lavoro sia la stessa. Dopo aver convalidato le voci, verrà avviata la distribuzione dell'area di lavoro. La distribuzione richiede solo alcuni minuti.

5. Al termine, selezionare di nuovo **Crea** nella parte inferiore della finestra di creazione di Analisi Service Fabric. Assicurarsi che la nuova area di lavoro sia visualizzata in **Area di lavoro OMS**. Questa azione aggiunge la soluzione all'area di lavoro creata.

Se si usa Windows, continuare con la procedura seguente per connettere i log di monitoraggio di Azure nell'account di archiviazione in cui sono archiviati gli eventi cluster. 

>[!NOTE]
>La soluzione Service Fabric Analitica è supportata solo per i cluster di Windows. Per i cluster Linux, vedere l'articolo sulla [come configurare i log di monitoraggio di Azure per i cluster Linux](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Connettere l'area di lavoro Log Analytics al cluster 

1. L'area di lavoro deve essere connessa ai dati di diagnostica provenienti dal cluster. Andare al gruppo di risorse in cui è stata creata la soluzione Analisi Service Fabric. Selezionare **ServiceFabric\<NomeAreaDiLavoro\>** e andare alla relativa pagina di panoramica. Da qui è possibile modificare le impostazioni della soluzione, le impostazioni dell'area di lavoro e accedere all'area di lavoro Log Analytics.

2. Nel menu di spostamento a sinistra in **Origini dati dell'area di lavoro** selezionare **Log account di archiviazione**.

3. Nella pagina **Log account di archiviazione** selezionare **Aggiungi** nella parte superiore per aggiungere i log del cluster all'area di lavoro.

4. Selezionare **Account di archiviazione** per aggiungere l'account appropriato creato nel cluster. Se è stato usato il nome predefinito, l'account di archiviazione è **sfdg\<resourceGroupName\>**. È anche possibile verificare con il modello di Azure Resource Manager usato per distribuire il cluster, controllando il valore usato per **applicationDiagnosticsStorageAccountName**. Se il nome non viene visualizzato, scorrere verso il basso e selezionare **Carica altro**. Selezionare il nome dell'account di archiviazione.

5. Specificare il Tipo di dati. Impostarlo su **Eventi di Service Fabric**.

6. Assicurarsi che il valore di Origine sia impostato automaticamente su **WADServiceFabric\*EventTable**.

7. Selezionare **OK** per connettere l'area di lavoro ai log del cluster.

    ![Aggiungere log dell'account di archiviazione per i log di monitoraggio di Azure](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

L'account viene ora visualizzato come parte dei log degli account di archiviazione nelle origini dati dell'area di lavoro.

La soluzione Analisi Service Fabric è stata aggiunta in un'area di lavoro Log Analytics, che è ora connessa correttamente alla piattaforma del cluster e alla tabella del log applicazioni. È possibile aggiungere altre origini all'area di lavoro nello stesso modo.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Distribuire i log di monitoraggio di Azure con Azure Resource Manager

Quando si distribuisce un cluster con un modello di Resource Manager, il modello crea una nuova area di lavoro Log Analytics, aggiunge la soluzione Service Fabric all'area di lavoro e la configura per leggere i dati dalle tabelle di archiviazione appropriate.

È possibile usare e modificare [questo modello di esempio](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) per soddisfare i requisiti. Questo modello consente di:

* Creare un cluster di Service Fabric composto da 5 nodi
* Creare un'area di lavoro Log Analytics e una soluzione Service Fabric
* Configurare l'agente di Log Analytics in modo da raccogliere e inviare 2 contatori delle prestazioni di esempio all'area di lavoro
* Configurare WAD in modo da raccogliere i dati di Service Fabric e inviarli alle tabelle di archiviazione di Azure (WADServiceFabric*EventTable)
* Configurare l'area di lavoro Log Analytics per la lettura degli eventi da queste tabelle


È possibile distribuire il modello come un aggiornamento di Resource Manager per il cluster usando il `New-AzResourceGroupDeployment` API nel modulo Azure PowerShell. Un comando di esempio potrebbe essere:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager rileva che questo comando è un aggiornamento a una risorsa esistente. Elabora solo le modifiche tra il modello che definisce la distribuzione esistente e il nuovo modello fornito.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Distribuire i log di monitoraggio di Azure con Azure PowerShell

È anche possibile distribuire la risorsa di analitica log tramite PowerShell usando il `New-AzOperationalInsightsWorkspace` comando. Per usare questo metodo, assicurarsi che sia installato [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Usare questo script per creare una nuova area di lavoro Log Analytics e aggiungervi la soluzione Service Fabric: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
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

Al termine, seguire i passaggi nella sezione precedente per la connessione dei log di monitoraggio di Azure nell'account di archiviazione appropriato.

È anche possibile aggiungere altre soluzioni o apportare altre modifiche all'area di lavoro Log Analytics usando PowerShell. Per altre informazioni, vedere [monitoraggio di Azure di gestire i log tramite PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire l'agente di Log Analytics](service-fabric-diagnostics-oms-agent.md) sui nodi per raccogliere contatori delle prestazioni, statistiche Docker e registri per i contenitori
* Acquisire familiarità con le [ricerca log e l'esecuzione di query](../log-analytics/log-analytics-log-searches.md) funzionalità fornite come parte dei log di monitoraggio di Azure
* [Utilizzare Progettazione vista per creare visualizzazioni personalizzate nel log di monitoraggio di Azure](../azure-monitor/platform/view-designer.md)
