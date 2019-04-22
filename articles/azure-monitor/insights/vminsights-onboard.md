---
title: Distribuire Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Questo articolo descrive distribuire e configurare Monitoraggio di Azure per le macchine virtuali per ottenere informazioni sulle prestazioni di un'applicazione distribuita e sui problemi di integrità identificati.
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
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: 1a4bfae22477e345176971bd40b0afa91c8867fb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885826"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Distribuire Monitoraggio di Azure per le macchine virtuali (anteprima)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Questo articolo descrive come configurare Monitoraggio di Azure per le macchine virtuali. Il servizio consente di monitorare l'integrità del sistema operativo delle macchine virtuali e dei set di scalabilità di macchine virtuali di Azure, oltre che delle macchine virtuali nell'ambiente in uso. Il monitoraggio include l'individuazione e il mapping delle dipendenze delle applicazioni eventualmente ospitate al loro interno. 

Si può abilitare Monitoraggio di Azure per le macchine virtuali usando uno dei metodi seguenti:

* Abilitare una singola macchina virtuale selezionando **Insights (anteprima)** direttamente dalla macchina virtuale.
* Abilitare due o più macchine virtuali di Azure usando Criteri di Azure. Tramite questo metodo, le dipendenze obbligatorie delle macchine virtuali nuove ed esistenti vengono installate e configurate correttamente. Le macchine virtuali non conformi vengono segnalate per permettere di scegliere se abilitarle e come correggere il problema.
* Abilitare due o più macchine virtuali di Azure o set di scalabilità di macchine virtuali in una sottoscrizione o un gruppo di risorse specificato, usando PowerShell.

Altre informazioni su ogni metodo sono disponibili più avanti nell'articolo.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare di aver compreso quanto illustrato nelle sezioni seguenti.

### <a name="log-analytics"></a>Log Analytics

Monitoraggio per le macchine virtuali di Azure supporta un'area di lavoro di Log Analitica nelle aree seguenti:

- Stati Uniti centro-occidentali
- Stati Uniti orientali
- Canada Central<sup>1</sup>
- Regno Unito meridionale<sup>1</sup>
- Europa occidentale
- Asia sud-orientale<sup>1</sup>

<sup>1</sup> Quest'area attualmente non supporta la funzionalità di integrità di Monitoraggio di Azure per le macchine virtuali.

>[!NOTE]
>Le macchine virtuali di Azure possono essere distribuite da qualsiasi area e non solo dalle aree supportate per l'area di lavoro Log Analytics.
>

Se non si ha un'area di lavoro, è possibile crearne una con uno dei metodi seguenti:
* [L’interfaccia della riga di comando di Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Il portale di Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Se si abilita il monitoraggio per una singola macchina virtuale di Azure nel portale di Azure, è possibile creare un'area di lavoro durante questo processo.

Per abilitare la soluzione per lo scenario su larga scala, prima di tutto configurare quanto segue nell'area di lavoro Log Analytics:

* Installare le soluzioni ServiceMap e InfrastructureInsights. È possibile completare l'installazione solo usando un modello di Azure Resource Manager fornito in questo articolo.
* Configurare l'area di lavoro Log Analytics per raccogliere i contatori delle prestazioni.

Per configurare l'area di lavoro per lo scenario su larga scala, consultare Configurare l'area di lavoro Log Analytics per la distribuzione su larga scala.

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

La tabella seguente elenca le versioni dei sistemi operativi Windows e Linux supportati con Monitoraggio di Azure per le macchine virtuali. L'elenco completo con le versioni principali e secondarie del kernel e del sistema operativo Linux supportate è disponibile più avanti in questa sezione.

|Versione del sistema operativo |Prestazioni |Mappe |Integrità |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14.04, 16.04, 18.04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11, 12 | X | X | X |
|Debian 8, 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> La funzionalità relativa alle prestazioni di Monitoraggio di Azure per le macchine virtuali è disponibile solo da Monitoraggio di Azure. Non è disponibile quando si accede direttamente dal riquadro a sinistra della macchina virtuale di Azure.

>[!NOTE]
>Le informazioni seguenti si applicano al supporto del sistema operativo Linux:
> - Sono supportate solo versioni predefinita e SMP del kernel Linux.
> - Le versioni del kernel non standard, ad esempio Estensione indirizzo fisico (Physical Address Extension, PAE) e Xen, non sono supportate per le distribuzioni Linux. Un sistema con stringa di versione *2.6.16.21-0.8-xen*, ad esempio, non è supportato.
> - I kernel personalizzati, tra cui le ricompilazioni dei kernel standard, non sono supportati.
> - Il kernel CentOSPlus è supportato.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Versione del sistema operativo | Versione del kernel
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versione del sistema operativo | Versione del kernel
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent
La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. Dependency Agent dipende dall'agente di Log Analytics per la connessione a Log Analytics. Di conseguenza, in un sistema l'agente di Log Analytics deve essere installato e configurato con Dependency Agent.

Che si abiliti Monitoraggio di Azure per le macchine virtuali per una singola macchina virtuale di Azure o si usi il metodo per la distribuzione su larga scala, l'estensione Dependency Agent della macchina virtuale di Azure è comunque necessaria per installare l'agente come parte dell'esperienza.

In un ambiente ibrido, è possibile scaricare e installare Dependency Agent in due modi: manualmente o usando un metodo di distribuzione automatizzata per le macchine virtuali ospitate all'esterno di Azure.

La tabella seguente descrive le origini connesse supportate dalla funzionalità di mappa in un ambiente ibrido.

| Origine connessa | Supportato | DESCRIZIONE |
|:--|:--|:--|
| Agenti di Windows | Sì | Oltre all'[agente di Log Analytics per Windows](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Windows richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Agenti Linux | Sì | Oltre all'[agente di Log Analytics per Linux](../../azure-monitor/platform/log-analytics-agent.md), gli agenti Linux richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No  | |

È possibile scaricare Dependency Agent dalle posizioni seguenti:

| File | OS | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
Per abilitare e accedere alle funzionalità in Monitoraggio di Azure per le macchine virtuali, è necessario avere i ruoli di accesso seguenti:

- Per abilitare la soluzione, è necessario essere membri del ruolo *Collaboratore di Log Analytics*.

- Per visualizzare i dati su prestazioni, integrità e mappe, è necessario avere il ruolo con autorizzazioni di *lettura dei dati di monitoraggio* per la macchina virtuale di Azure. L'area di lavoro Log Analytics deve essere configurata per Monitoraggio di Azure per le macchine virtuali.

Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Abilitare il monitoraggio nel portale di Azure
Per abilitare il monitoraggio della macchina virtuale di Azure nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Macchine virtuali**.

1. Selezionare una macchina virtuale dall'elenco.

1. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)**.

1. Nella pagina **Insights (anteprima)** selezionare **Prova adesso**.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. Se nella pagina **Azure Monitor Insights Onboarding** (Onboarding di Insights per Monitoraggio di Azure) è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione, selezionarla nell'elenco a discesa.  
    Nell'elenco sono preselezionate l'area di lavoro e la località predefinite in cui è distribuita la macchina virtuale nella sottoscrizione. 

    >[!NOTE]
    >Se si vuole creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio della macchina virtuale, seguire le istruzioni in [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) in una delle aree supportate elencate in precedenza.

Dopo aver abilitato il monitoraggio, possono essere necessari circa 10 minuti prima di poter visualizzare le metriche relative all'integrità per la macchina virtuale.

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Distribuire su larga scala
In questa sezione si distribuisce Monitoraggio di Azure per le macchine virtuali su larga scala tramite Criteri di Azure o Azure PowerShell.

Prima di distribuire le macchine virtuali, preconfigurare l'area di lavoro Log Analytics eseguendo queste operazioni:

1. Se non si ha un'area di lavoro, crearne una in grado di supportare Monitoraggio di Azure per le macchine virtuali.  
    Prima di procedere, vedere [Gestire le aree di lavoro](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) per comprendere le considerazioni relative a costi, gestione e conformità.

1. Creare una nuova area di lavoro se non ne esiste già una che può essere usata per supportare Monitoraggio di Azure per le macchine virtuali. Prima di creare una nuova area di lavoro, vedere [Gestire le aree di lavoro](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json) per conoscere le considerazioni sui costi, la gestione e la conformità.

1. Abilitare i contatori delle prestazioni nell'area di lavoro per la raccolta in macchine virtuali Windows e Linux.

1. Installare e abilitare le soluzioni ServiceMap e InfrastructureInsights nell'area di lavoro.

### <a name="set-up-a-log-analytics-workspace"></a>Configurare un'area di lavoro Log Analytics
Se non si ha un'area di lavoro Log Analytics, crearla esaminando i metodi suggeriti nella sezione ["Prerequisiti"](#log-analytics).

#### <a name="enable-performance-counters"></a>Abilitare i contatori delle prestazioni
Se l'area di lavoro Log Analytics a cui fa riferimento la soluzione non è già configurata per la raccolta dei contatori delle prestazioni richiesti dalla soluzione, sarà necessario abilitare i contatori. È possibile farlo in due modi:
* Manualmente, come descritto in [Origini dati per le prestazioni di Windows e Linux in Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Scaricando ed eseguendo uno script di PowerShell disponibile in [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Installare le soluzioni ServiceMap e InfrastructureInsights
Questo metodo include un modello JSON che specifica la configurazione per abilitare i componenti della soluzione nell'area di lavoro Log Analytics.

Se non si ha familiarità con la distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Eseguire il comando seguente usando l'interfaccia della riga di comando di Azure:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

        ```azurecli
        provisioningState       : Succeeded

### Enable by using Azure Policy
To enable Azure Monitor for VMs at scale in a way that helps ensure consistent compliance and the automatic enabling of the newly provisioned VMs, we recommend [Azure Policy](../../governance/policy/overview.md). These policies:

* Deploy the Log Analytics agent and the Dependency agent.
* Report on compliance results.
* Remediate for non-compliant VMs.

To enable Azure Monitor for VMs by using Azure Policy in your tenant:

- Assign the initiative to a scope: management group, subscription, or resource group
- Review and remediate compliance results

For more information about assigning Azure Policy, see [Azure Policy overview](../../governance/policy/overview.md#policy-assignment) and review the [overview of management groups](../../governance/management-groups/index.md) before you continue.

The policy definitions are listed in the following table:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Enable Azure Monitor for VMs |Enable Azure Monitor for the Virtual Machines (VMs) in the specified scope (management group, subscription, or resource group). Takes Log Analytics workspace as a parameter. |Initiative |
|[Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Linux VMs |Deploy Dependency Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Windows VMs |Deploy Dependency Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Linux VMs |Deploy Log Analytics Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Windows VMs |Deploy Log Analytics Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |

Standalone policy (not included with the initiative) is described here:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Audit Log Analytics Workspace for VM - Report Mismatch |Report VMs as non-compliant if they aren't logging to the Log Analytics workspace specified in the policy/initiative assignment. |Policy |

#### Assign the Azure Monitor initiative
With this initial release, you can create the policy assignment only in the Azure portal. To understand how to complete these steps, see [Create a policy assignment from the Azure portal](../../governance/policy/assign-policy-portal.md).

1. To launch the Azure Policy service in the Azure portal, select **All services**, and then search for and select **Policy**.

1. In the left pane of the Azure Policy page, select **Assignments**.  
    An assignment is a policy that has been assigned to take place within a specific scope.
    
1. At the top of the **Policy - Assignments** page, select **Assign Initiative**.

1. On the **Assign Initiative** page, select the **Scope** by clicking the ellipsis (...), and select a management group or subscription.  
    In our example, a scope limits the policy assignment to a grouping of virtual machines for enforcement.
    
1. At the bottom of the **Scope** page, save your changes by selecting **Select**.

1. (Optional) To remove one or more resources from the scope, select **Exclusions**.

1. Select the **Initiative definition** ellipsis (...) to display the list of available definitions, select **[Preview] Enable Azure Monitor for VMs**, and then select **Select**.  
    The **Assignment name** box is automatically populated with the initiative name you selected, but you can change it. You can also add an optional description. The **Assigned by** box is automatically populated based on who is logged in, and this value is optional.
    
1. In the **Log Analytics workspace** drop-down list for the supported region, select a workspace.

   > [!NOTE]
   > If the workspace is beyond the scope of the assignment, grant *Log Analytics Contributor* permissions to the policy assignment's Principal ID. If you don't do this, you might see a deployment failure such as: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`
   > To grant access, review [how to manually configure the managed identity](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  The **Managed Identity** check box is selected, because the initiative being assigned includes a policy with the *deployIfNotExists* effect.
    
1. In the **Manage Identity location** drop-down list, select the appropriate region.

1. Select **Assign**.

#### Review and remediate the compliance results

You can learn how to review compliance results by reading [identify non-compliance results](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). In the left pane, select **Compliance**, and then locate the **[Preview] Enable Azure Monitor for VMs** initiative for VMs that aren't compliant according to the assignment you created.

![Policy compliance for Azure VMs](./media/vminsights-onboard/policy-view-compliance-01.png)

Based on the results of the policies included with the initiative, VMs are reported as non-compliant in the following scenarios:

* Log Analytics or the Dependency agent isn't deployed.  
    This scenario is typical for a scope with existing VMs. To mitigate it, deploy the required agents by [creating remediation tasks](../../governance/policy/how-to/remediate-resources.md) on a non-compliant policy.  
    - [Preview]: Deploy Dependency Agent for Linux VMs
    - [Preview]: Deploy Dependency Agent for Windows VMs
    - [Preview]: Deploy Log Analytics Agent for Linux VMs
    - [Preview]: Deploy Log Analytics Agent for Windows VMs

* VM Image (OS) isn't identified in the policy definition.  
    The criteria of the deployment policy include only VMs that are deployed from well-known Azure VM images. Check the documentation to see whether the VM OS is supported. If it isn't supported, duplicate the deployment policy and update or modify it to make the image compliant.  
    - [Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted
    - [Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted

* VMs aren't logging in to the specified Log Analytics workspace.  
    It's possible that some VMs in the initiative scope are logging in to a Log Analytics workspace other than the one that's specified in the policy assignment. This policy is a tool to identify which VMs are reporting to a non-compliant workspace.  
    - [Preview]: Audit Log Analytics Workspace for VM - Report Mismatch

### Enable with PowerShell
To enable Azure Monitor for VMs for multiple VMs or virtual machine scale sets, you can use the PowerShell script [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), available from the Azure PowerShell Gallery. This script iterates through every virtual machine and virtual machine scale set in your subscription, in the scoped resource group that's specified by *ResourceGroup*, or to a single VM or virtual machine scale set that's specified by *Name*. For each VM or virtual machine scale set, the script verifies whether the VM extension is already installed. If the VM extension is not installed, the script tries to reinstall it. If the VM extension is installed, the script installs the Log Analytics and Dependency agent VM extensions.

This script requires Azure PowerShell module Az version 1.0.0 or later. Run `Get-Module -ListAvailable Az` to find the version. If you need to upgrade, see [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps). If you're running PowerShell locally, you also need to run `Connect-AzAccount` to create a connection with Azure.

To get a list of the script's argument details and example usage, run `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


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

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

L'esempio seguente illustra l'uso dei comandi di PowerShell nella cartella per abilitare Monitoraggio di Azure per le macchine virtuali e comprendere l'output previsto:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

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

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-a-hybrid-environment"></a>Eseguire l'abilitazione per un ambiente ibrido
Questa sezione illustra come distribuire macchine virtuali o computer fisici ospitati in un data center o in un altro ambiente cloud per il monitoraggio tramite Monitoraggio di Azure per le macchine virtuali.

Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali non trasmette dati e non richiede modifiche ai firewall o alle porte. I dati della mappa vengono sempre trasmessi dall'agente di Log Analytics al servizio Monitoraggio di Azure, direttamente o tramite il [gateway OMS](../../azure-monitor/platform/gateway.md), se i criteri di sicurezza IT non permettono la connessione dei computer in rete a Internet.

Esaminare i requisiti e i metodi di distribuzione per l'[agente di Log Analytics per Linux e Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Ecco un riepilogo dei passaggi:

1. Installare l'agente di Log Analytics per Windows o Linux.

1. Scaricare e installare Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali per [Windows](https://aka.ms/dependencyagentwindows) o [Linux](https://aka.ms/dependencyagentlinux).

1. Abilitare la raccolta dei contatori delle prestazioni.

1. Distribuire Monitoraggio di Azure per le macchine virtuali.

### <a name="install-the-dependency-agent-on-windows"></a>Installare Dependency Agent in Windows
Dependency Agent può essere installato manualmente nei computer Windows eseguendo `InstallDependencyAgent-Windows.exe`. Se si esegue questo file eseguibile senza opzioni, si avvia una procedura guidata che consente di completare l'installazione dell'agente in modo interattivo.

>[!NOTE]
>Per installare o disinstallare l'agente sono necessari i privilegi di *Amministratore*.

La tabella seguente illustra i parametri supportati dal programma di installazione per l'agente dalla riga di comando.

| Parametro | DESCRIZIONE |
|:--|:--|
| /? | Restituisce un elenco delle opzioni della riga di comando. |
| /S | Esegue un'installazione invisibile all'utente, senza interazione da parte dell'utente. |

Per eseguire, ad esempio, il programma di installazione con il parametro `/?`, digitare **InstallDependencyAgent-Windows.exe /?**.

Per impostazione predefinita, i file di Dependency Agent per Windows si trovano in *C:\Programmi\Microsoft Dependency Agent*. Se Dependency Agent non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory dei log è *%Programfiles%\Microsoft Dependency Agent\logs*.

### <a name="install-the-dependency-agent-on-linux"></a>Installare Dependency Agent in Linux
Dependency Agent viene installato nei server Linux con *InstallDependencyAgent-Linux64.bin*, uno script della shell con un file binario autoestraente. È possibile eseguire il file con `sh` oppure aggiungere autorizzazioni di esecuzione al file stesso.

>[!NOTE]
> Per installare o configurare l'agente è necessario l'accesso alla radice.
>

| Parametro | DESCRIZIONE |
|:--|:--|
| -help | Ottenere un elenco delle opzioni della riga di comando. |
| -s | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| --check | Controllare le autorizzazioni e il sistema operativo senza installare l'agente. |

Ad esempio, per eseguire il programma di installazione con il parametro `-help`, digitare **InstallDependencyAgent-Linux64.bin -help**.

Installare Dependency Agent per Linux come utente ROOT eseguendo il comando `sh InstallDependencyAgent-Linux64.bin`.

Se Dependency Agent non si avvia, controllare i log per vedere le informazioni dettagliate sull'errore. Per gli agenti Linux la directory di log è */var/opt/microsoft/dependency-agent/log*.

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti:

| File | Località |
|:--|:--|
| File core | /opt/microsoft/dependency-agent |
| File di log | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| File eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Abilitare i contatori delle prestazioni
Se l'area di lavoro Log Analytics a cui fa riferimento la soluzione non è già configurata per la raccolta dei contatori delle prestazioni richiesti dalla soluzione, è necessario abilitare i contatori. È possibile farlo in due modi:
* Manualmente, come descritto in [Origini dati per le prestazioni di Windows e Linux in Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Scaricando ed eseguendo uno script di PowerShell disponibile in [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="deploy-azure-monitor-for-vms"></a>Distribuire Monitoraggio di Azure per le macchine virtuali
Questo metodo include un modello JSON che specifica la configurazione per abilitare i componenti della soluzione nell'area di lavoro Log Analytics.

Se non si ha familiarità con la distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

#### <a name="create-and-execute-a-template"></a>Creare ed eseguire un modello

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

1. Modificare i valori di *WorkspaceName*, *ResourceGroupName* e *WorkspaceLocation*. Il valore per *WorkspaceName* è l'ID risorsa completo dell'area di lavoro Log Analytics, che include il nome dell'area di lavoro. Il valore per *WorkspaceLocation* è l'area in cui è definita l'area di lavoro.

1. È possibile distribuire il modello usando il comando di PowerShell seguente:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

    ```powershell
    provisioningState       : Succeeded
    ```
   Dopo avere abilitato il monitoraggio, possono essere necessari circa 10 minuti prima di poter visualizzare lo stato di integrità e le metriche per il computer ibrido.

## <a name="performance-counters-enabled"></a>Contatori delle prestazioni abilitati
Monitoraggio di Azure per le macchine virtuali configura un'area di lavoro Log Analytics per raccogliere i contatori delle prestazioni usati dalla soluzione. La tabella seguente elenca gli oggetti e i contatori configurati dalla soluzione che vengono raccolti ogni 60 secondi.

### <a name="windows-performance-counters"></a>Contatori delle prestazioni di Windows

|Nome dell'oggetto |Nome contatore |
|------------|-------------|
|LogicalDisk |% Free Space |
|LogicalDisk |Avg. Disk sec/Read |
|LogicalDisk |Avg. Disk sec/Transfer |
|LogicalDisk |Avg. Disk sec/Write |
|LogicalDisk |Byte da/a disco/sec |
|LogicalDisk |Byte letti da disco/sec  |
|LogicalDisk |Letture disco/sec  |
|LogicalDisk |Disk Transfers/sec |
|LogicalDisk |Byte scritti su disco/sec |
|LogicalDisk |Scritture disco/sec |
|LogicalDisk |Free Megabytes |
|Memoria |MByte disponibili |
|Scheda di rete |Byte ricevuti/sec |
|Scheda di rete |Byte inviati/sec |
|Processore |% di tempo processore |

### <a name="linux-performance-counters"></a>Contatori delle prestazioni di Linux

|Nome dell'oggetto |Nome contatore |
|------------|-------------|
|Logical Disk |% Used Space |
|Logical Disk |Byte letti da disco/sec  |
|Logical Disk |Letture disco/sec  |
|Logical Disk |Disk Transfers/sec |
|Logical Disk |Byte scritti su disco/sec |
|Logical Disk |Scritture disco/sec |
|Logical Disk |Free Megabytes |
|Logical Disk |Logical Disk Bytes/sec |
|Memoria |Available MBytes Memory |
|Rete |Total Bytes Received |
|Rete |Total Bytes Transmitted |
|Processore |% di tempo processore |

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo
Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per garantire e migliorare la qualità, la sicurezza e l'integrità del servizio. Per offrire capacità di risoluzione dei problemi accurate ed efficienti, i dati della funzionalità di mappa includono informazioni sulla configurazione del software, come il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>Passaggi successivi

Una volta abilitato il monitoraggio per la macchina virtuale, le informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali. Per informazioni su come usare la funzionalità di integrità, vedere [Visualizzare l'integrità di Monitoraggio di Azure per le macchine virtuali](vminsights-health.md). Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).
