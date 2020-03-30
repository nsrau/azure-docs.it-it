---
title: Abilitare Monitoraggio di Azure per macchine virtuali con PowerShell o modelliEnable Azure Monitor for VMs with PowerShell or templates
description: Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per una o più macchine virtuali di Azure o set di scalabilità di macchine virtuali usando i modelli di Azure PowerShell o Azure Resource Manager.This article describes how you enable Azure Monitor for VMs for one or more Azure virtual machines or virtual machine scale sets by using Azure PowerShell or Azure Resource Manager templates.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 75d5203e7c475a44b6a00dbf9286f43114b7b54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480845"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>Abilitare Monitoraggio di Azure per le macchine virtuali usando i modelli di Azure PowerShell o Resource ManagerEnable Azure Monitor for VMs using Azure PowerShell or Resource Manager templates

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Questo articolo illustra come abilitare Monitoraggio di Azure per le macchine virtuali per le macchine virtuali di Azure o i set di scalabilità di macchine virtuali usando i modelli di Azure PowerShell o Azure Resource Manager.This article explains how to enable Azure Monitor for VMs for Azure virtual machines or virtual machine scale sets by using Azure PowerShell or Azure Resource Manager templates. Al termine di questo processo, sarà stato avviato correttamente il monitoraggio di tutte le macchine virtuali e verrà verificato se si verificano problemi di prestazioni o disponibilità.

## <a name="set-up-a-log-analytics-workspace"></a>Configurare un'area di lavoro Log Analytics

Se non si dispone di un'area di lavoro di Log Analytics, è necessario crearne una. Esaminare i metodi suggeriti nella sezione [Prerequisiti](vminsights-enable-overview.md#log-analytics) prima di continuare con la procedura per configurarlo. È quindi possibile completare la distribuzione di Monitoraggio di Azure per le macchine virtuali usando il metodo di modello di Azure Resource Manager.Then you can finish the deployment of Azure Monitor for VMs by using the Azure Resource Manager template method.

### <a name="install-the-vminsights-solution"></a>Installare la soluzione VMInsightsInstall the VMInsights solution

Questo metodo include un modello JSON che specifica la configurazione per abilitare i componenti della soluzione nell'area di lavoro Log Analytics.

Se non si sa come distribuire le risorse usando un modello, vedere:If you don't know how to deploy resources by using a template, see:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Per usare l'interfaccia della riga di comando di Azure, è innanzitutto necessario installare e usare l'interfaccia della riga di comando in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Per installare o aggiornare l'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli)di Azure.To install or upgrade the Azure CLI, see Install the Azure CLI .

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
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
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

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente e include il risultato:

        ```output
        provisioningState       : Succeeded
        ```

    * Eseguire il comando seguente usando l'interfaccia della riga di comando di Azure:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente e include il risultato:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Abilitare con i modelli di Azure Resource ManagerEnable with Azure Resource Manager templates

Sono stati creati modelli di Azure Resource Manager di esempio per l'onboarding delle macchine virtuali e dei set di scalabilità delle macchine virtuali. Questi modelli includono scenari che è possibile usare per abilitare il monitoraggio su una risorsa esistente e creare una nuova risorsa con il monitoraggio abilitato.

>[!NOTE]
>Il modello deve essere distribuito nello stesso gruppo di risorse della risorsa da portare a bordo.

Se non si sa come distribuire le risorse usando un modello, vedere:If you don't know how to deploy resources by using a template, see:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Per usare l'interfaccia della riga di comando di Azure, è innanzitutto necessario installare e usare l'interfaccia della riga di comando in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Per installare o aggiornare l'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli)di Azure.To install or upgrade the Azure CLI, see Install the Azure CLI .

### <a name="download-templates"></a>Scarica modelli

I modelli di Azure Resource Manager vengono forniti in un file di archivio (con estensione zip) che è possibile scaricare dal repository GitHub.The Azure Resource Manager templates are provided in an archive file (.zip) that you can [download](https://aka.ms/VmInsightsARMTemplates) from our GitHub repo. Il contenuto del file include cartelle che rappresentano ogni scenario di distribuzione con un file modello e parametro. Prima di eseguirli, modificare il file dei parametri e specificare i valori necessari. Non modificare il file modello a meno che non sia necessario personalizzarlo per supportare i requisiti specifici. Dopo aver modificato il file dei parametri, è possibile distribuirlo utilizzando i metodi descritti più avanti in questo articolo.

Il file di download contiene i seguenti modelli per diversi scenari:

- Il modello **ExistingVmOnboarding** abilita Monitoraggio di Azure per le macchine virtuali se la macchina virtuale esiste già.
- Il modello **NewVmOnboarding** crea una macchina virtuale e consente a Monitoraggio di Azure per le macchine virtuali di monitorarla.
- Il modello **ExistingVmssOnboarding** abilita Monitoraggio di Azure per le macchine virtuali se il set di scalabilità della macchina virtuale esiste già.
- Il modello **NewVmssOnboarding** crea set di scalabilità delle macchine virtuali e consente a Monitoraggio di Azure per le macchine virtuali di monitorarli.
- Il modello **ConfigureWorkspace** configura l'area di lavoro di Log Analytics per supportare Monitoraggio di Azure per le macchine virtuali abilitando le soluzioni e la raccolta dei contatori delle prestazioni del sistema operativo Linux e Windows.

>[!NOTE]
>Se i set di scalabilità delle macchine virtuali erano già presenti e i criteri di aggiornamento sono impostati su **Manuale,** Monitoraggio di Azure per le macchine virtuali non verrà abilitato per le istanze per impostazione predefinita dopo l'esecuzione del modello di Azure Resource Manager **ExistingVmssOnboarding.** È necessario aggiornare manualmente le istanze.

### <a name="deploy-by-using-azure-powershell"></a>Eseguire la distribuzione tramite Azure PowerShell

Il passaggio seguente abilita il monitoraggio tramite Azure PowerShell.The following step enables monitoring by using Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente e include il risultato:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Distribuire tramite l'interfaccia della riga di comando di AzureDeploy by using the Azure CLI

The following step enables monitoring by using the Azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

L'output sarà simile al seguente:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Eseguire l'abilitazione con PowerShell

Per abilitare Monitoraggio di Azure per le macchine virtuali per più macchine virtuali o set di scalabilità di macchine virtuali, usare lo script di PowerShell Install-VMInsights.ps1.To enable Azure Monitor for VMs for multiple VMs or virtual machine scale sets, use the PowerShell script [Install-VMInsights.ps1.](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) È disponibile da Azure PowerShell Gallery.It's available from the Azure PowerShell Gallery. Questo script scorre:

- Ogni set di scalabilità di macchine virtuali e macchine virtuali nella sottoscrizione.
- Gruppo di risorse con ambito specificato da *ResourceGroup*.
- Set di scalabilità di una singola macchina virtuale o di macchina virtuale specificato da *Nome*.

Per ogni macchina virtuale o set di scalabilità di macchine virtuali, lo script verifica se l'estensione della macchina virtuale è già installata. Se è installata l'estensione della macchina virtuale, lo script tenta di reinstallarla. Se l'estensione della macchina virtuale non è installata, lo script installa le estensioni della macchina virtuale di Log Analytics e dell'agente di dipendenza.

Verificare di usare il modulo di Azure PowerShell `Enable-AzureRM` Az versione 1.0.0 o successiva con gli alias di compatibilità abilitati. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

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

Ora che il monitoraggio è abilitato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali.

- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).

- Per identificare i colli di bottiglia e l'utilizzo complessivo con le prestazioni della macchina virtuale, vedere [Visualizzare le prestazioni delle macchine virtuali](vminsights-performance.md)di Azure.To identify bottlenecks and overall utilization with your VM's performance, see View Azure VM Performance .
