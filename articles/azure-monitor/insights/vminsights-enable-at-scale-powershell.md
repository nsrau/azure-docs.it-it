---
title: Abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) usando i modelli di Azure PowerShell o Resource Manager | Microsoft Docs
description: Questo articolo descrive la procedura per attivare il monitoraggio di Azure per le macchine virtuali per uno o più macchine virtuali o la scalabilità di macchine virtuali comporta usando Azure PowerShell o modelli di Azure Resource Manager.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ff284ea0adf6021ace84cd6a41f0a0e4e987a9c8
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144239"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) usando i modelli di Azure PowerShell o Resource Manager

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Questo articolo illustra come abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) per macchine virtuali di Azure o i set di scalabilità di macchine virtuali usando modelli di Azure PowerShell o Azure Resource Manager. Alla fine di questo processo, verrà è stata avviata la monitoraggio tutte le macchine virtuali e Scopri se qualsiasi in presenza di problemi di prestazioni o disponibilità.

## <a name="set-up-a-log-analytics-workspace"></a>Configurare un'area di lavoro Log Analytics 

Se non si dispone di un'area di lavoro di Log Analitica, è necessario crearne uno. Esaminare i metodi che verranno suggeriti i [prerequisiti](vminsights-enable-overview.md#log-analytics) sezione prima di continuare con i passaggi per configurarlo. È quindi possibile completare la distribuzione di monitoraggio di Azure per le macchine virtuali usando il metodo di modello di Azure Resource Manager.

### <a name="enable-performance-counters"></a>Abilitare i contatori delle prestazioni

Se l'area di lavoro Log Analytics a cui fa riferimento la soluzione non è già configurata per la raccolta dei contatori delle prestazioni richiesti dalla soluzione, sarà necessario abilitare i contatori. È possibile farlo in uno dei due modi:
* Manualmente, come descritto in [Origini dati per le prestazioni di Windows e Linux in Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Scaricando ed eseguendo uno script di PowerShell che è disponibile il [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Installare le soluzioni ServiceMap e InfrastructureInsights
Questo metodo include un modello JSON che specifica la configurazione per abilitare i componenti della soluzione nell'area di lavoro Log Analytics.

Se non si sa come distribuire le risorse usando un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Per usare il comando di Azure, è necessario innanzitutto installare e usare l'interfaccia della riga di comando in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Per installare o aggiornare la CLI di Azure, vedere [installare CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Salvare il file come *installsolutionsforvminsights.json* in una cartella locale.

1. Acquisire i valori di *WorkspaceName*, *ResourceGroupName* e *WorkspaceLocation*. Il valore per *WorkspaceName* è il nome dell'area di lavoro Log Analytics. Il valore per *WorkspaceLocation* è l'area in cui è definita l'area di lavoro.

1. A questo punto è possibile distribuire il modello.
 
    * Usare i comandi di PowerShell seguenti nella cartella che contiene il modello:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        La modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio che è simile al seguente che include il risultato:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Eseguire il comando seguente usando l'interfaccia della riga di comando di Azure:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        La modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio che è simile al seguente che include il risultato:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Abilitare con modelli Azure Resource Manager
Sono stati creati modelli di Azure Resource Manager di esempio per l'onboarding delle macchine virtuali e set di scalabilità di macchine virtuali. Questi modelli includono scenari che è possibile usare per abilitare il monitoraggio in una risorsa esistente e creare una nuova risorsa che è abilitato il monitoraggio.

>[!NOTE]
>Il modello deve essere distribuito nello stesso gruppo di risorse della risorsa per portare a bordo.

Se non si sa come distribuire le risorse usando un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Per usare il comando di Azure, è necessario innanzitutto installare e usare l'interfaccia della riga di comando in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Per installare o aggiornare la CLI di Azure, vedere [installare CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Scaricare i modelli

I modelli di Azure Resource Manager forniti in un file di archivio (zip) che può essere [scaricare](https://aka.ms/VmInsightsARMTemplates) dal repository GitHub. Contenuto del file include le cartelle che rappresentano ogni scenario di distribuzione con un file di modello e dei parametri. Prima di eseguirli, modificare il file dei parametri e specificare i valori richiesti. Non modificare il file di modello, a meno che non è necessario personalizzare in modo da supportare esigenze specifiche. Dopo avere modificato il file dei parametri, è possibile distribuirlo usando i metodi seguenti, descritti più avanti in questo articolo. 

Il file di download contiene i modelli seguenti per scenari diversi:

- **ExistingVmOnboarding** modello Abilita monitoraggio di Azure per le macchine virtuali se la macchina virtuale esiste già.
- **NewVmOnboarding** modello crea una macchina virtuale e attiva il monitoraggio di Azure per le macchine virtuali per monitorarlo.
- **ExistingVmssOnboarding** modello Abilita monitoraggio di Azure per le macchine virtuali se è presente la scalabilità di macchine virtuali già impostata.
- **NewVmssOnboarding** modello Crea set di scalabilità di macchine virtuali e abilita il monitoraggio di Azure per le macchine virtuali monitorate.
- **ConfigureWorksapce** modello configura area di lavoro di Log Analitica per supportare il monitoraggio di Azure per le macchine virtuali abilitando la raccolta di contatori delle prestazioni del sistema operativo Windows e Linux e soluzioni.

>[!NOTE]
>Se il set di scalabilità di macchine virtuali erano già presente e i criteri di aggiornamento sono impostato su **manuali**, non verrà attivato il monitoraggio di Azure per le macchine virtuali per le istanze per impostazione predefinita dopo l'esecuzione di **ExistingVmssOnboarding** Modello di Azure Resource Manager. È necessario aggiornare manualmente le istanze.

### <a name="deploy-by-using-azure-powershell"></a>Eseguire la distribuzione tramite Azure PowerShell

Il passaggio seguente Abilita il monitoraggio tramite Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
La modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio che è simile al seguente che include il risultato:

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-by-using-the-azure-cli"></a>Distribuire tramite la CLI di Azure

Il passaggio seguente Abilita il monitoraggio tramite la CLI di Azure.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

L'output sarà simile al seguente:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Eseguire l'abilitazione con PowerShell

Per abilitare il monitoraggio di Azure per le macchine virtuali per più macchine virtuali o set di scalabilità di macchine virtuali, usare lo script di PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). È disponibile nella raccolta di Azure PowerShell. Questo script esegue l'iterazione tramite:

- Ogni macchina virtuale e nella sottoscrizione di set di scalabilità di macchine virtuali.
- Il gruppo di risorse con ambito specificato da *ResourceGroup*. 
- Un singolo set di scalabilità di macchine Virtuali o una macchina virtuale specificato da *nome*.

Per ogni macchina virtuale o set di scalabilità di macchine virtuali, lo script verifica se l'estensione della macchina virtuale è già installata. Se non è installata l'estensione VM, lo script cerca di reinstallarla. Se l'estensione della macchina virtuale è installata, lo script installa le estensioni della macchina virtuale dell'agente di Log Analytics e di Dependency Agent.

Questo script richiede modulo Azure PowerShell Az versione 1.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

Per ottenere un elenco di dettagli sugli argomenti dello script con esempi di utilizzo, eseguire `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

L'esempio seguente illustra l'uso dei comandi di PowerShell nella cartella per abilitare Monitoraggio di Azure per le macchine virtuali e comprendere l'output previsto:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è attivato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con monitoraggio di Azure per le macchine virtuali.
 
- Per informazioni su come usare la funzionalità di integrità, vedere [visualizzazione di monitoraggio di Azure per l'integrità di macchine virtuali](vminsights-health.md). 
- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). 
- Per identificare i colli di bottiglia e utilizzo complessivo delle prestazioni della VM, vedere [visualizzare le prestazioni della macchina virtuale di Azure](vminsights-performance.md). 
- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).