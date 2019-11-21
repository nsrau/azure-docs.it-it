---
title: Configurare i server allo stato desiderato e gestire la deviazione con Automazione di Azure
description: 'Esercitazione: Gestire le configurazioni dei server con Configurazione stato di Automazione di Azure'
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 72e5018dc1212e57dc190c05cc54158d37ca7fe1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231491"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurare i server sullo stato desiderato e gestire gli orientamenti

Configurazione stato di Automazione di Azure consente di specificare le configurazioni per i server e verificare che tali server rimangano nello stato specificato nel corso del tempo.

> [!div class="checklist"]
> - Eseguire l'onboarding di una VM in modo che sia gestita da Automation DSC per Azure
> - Caricare una configurazione in Automazione di Azure
> - Compilare una configurazione in una configurazione nodo
> - Assegnare una configurazione nodo a un nodo gestito
> - Controllare lo stato di conformità di un nodo gestito

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](automation-sec-configure-azure-runas-account.md).
- Una VM di Azure Resource Manager (non classica) che esegue Windows Server 2008 R2 o versioni successive. Per istruzioni sulla creazione di una VM, vedere [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Modulo Azure PowerShell 3.6 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Familiarità con DSC (Desired State Configuration). Per informazioni su DSC, vedere [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Creare e caricare una configurazione in Automazione di Azure

Per questa esercitazione si userà una semplice configurazione DSC che assicura che IIS sia installato nella VM.

Per informazioni sulle configurazioni DSC, vedere [Configurazioni DSC](/powershell/scripting/dsc/configurations/configurations).

In un editor di testo digitare quanto segue e salvarlo in locale con il nome `TestConfig.ps1`.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> In more advanced scenarios where you require multiple modules to be imported that provide DSC Resources, make sure each module has a unique `Import-DscResource` line in your configuration.

Chiamare il cmdlet `Import-AzureRmAutomationDscConfiguration` per caricare la configurazione nell'account di Automazione:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilare una configurazione in una configurazione nodo

Una configurazione DSC deve essere compilata in una configurazione nodo affinché possa essere assegnata a un nodo.

Per altre informazioni sulla compilazione di configurazioni, vedere [Configurazioni DSC](/powershell/scripting/dsc/configurations/configurations).

Chiamare il cmdlet `Start-AzureRmAutomationDscCompilationJob` per compilare la configurazione `TestConfig` in una configurazione nodo:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Verrà creata una configurazione nodo denominata `TestConfig.WebServer` nell'account di Automazione.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrare una VM in modo che sia gestita da Configurazione stato

È possibile usare Configurazione stato di Automazione di Azure per gestire macchine virtuali di Azure (sia classiche sia Resource Manager), macchine virtuali locali, computer Linux, macchine virtuali AWS e computer fisici locali. Questo argomento descrive soltanto come registrare VM di Azure Resource Manager. Per informazioni sulla registrazione di altri tipi di computer, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md).

Chiamare il cmdlet `Register-AzureRmAutomationDscNode` per registrare la VM con Configurazione stato di Automazione di Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

In questo modo la VM specificata viene registrata come nodo gestito in Configurazione stato.

### <a name="specify-configuration-mode-settings"></a>Specificare le impostazioni delle modalità di configurazione

Quando si registra una VM come nodo gestito, è anche possibile specificare le proprietà della configurazione. Ad esempio, è possibile specificare che lo stato della macchina deve essere applicato una sola volta (DSC non tenta di applicare la configurazione dopo il controllo iniziale) specificando `ApplyOnly` come valore della proprietà **ConfigurationMode**:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

È anche possibile specificare la frequenza con cui DSC controlla lo stato della configurazione usando la proprietà **ConfigurationModeFrequencyMins**:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Per altre informazioni sull'impostazione delle proprietà di configurazione per un nodo gestito, vedere [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Per altre informazioni sulle impostazioni di configurazione DSC, vedere [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Configurazione di Gestione configurazione locale).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Assegnare una configurazione nodo a un nodo gestito

Ora è possibile assegnare la configurazione nodo compilata alla VM che si vuole configurare.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

In questo modo si assegna la configurazione nodo denominata `TestConfig.WebServer` al nodo DSC registrato denominato `DscVm`.
Per impostazione predefinita, il nodo DSC viene verificato per la conformità con la configurazione nodo ogni 30 minuti.
Per informazioni su come modificare l'intervallo di controllo della conformità, vedere [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig) (Configurazione di Gestione configurazione locale).

## <a name="working-with-partial-configurations"></a>Working with Partial Configurations

Azure Automation State Configuration supports usage of [partial configurations](/powershell/scripting/dsc/pull-server/partialconfigs).
In this scenario, DSC is configured to manage multiple configurations independently, and each configuration is retrieved from Azure Automation.
However, only one configuration can be assigned to a node per automation account.
This means if you are using two configurations for a node you will require two automation accounts.

For details about how to register a partial configuration from pull service, see the documentation for [partial configurations](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

For more information about how teams can work together to collaboratively manage servers using configuration as code see [Understanding DSC's role in a CI/CD Pipeline](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Controllare lo stato di conformità di un nodo gestito

È possibile ottenere report sullo stato di conformità di un nodo gestito chiamando il cmdlet `Get-AzureRmAutomationDscNodeReport`:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Removing nodes from service

When you add a node to Azure Automation State Configuration, the settings in Local Configuration Manager are set to register with the service and pull configurations and required modules to configure the machine.
If you choose to remove the node from the service, you can do so using either the Azure portal or the Az cmdlets.

> [!NOTE]
> Unregistering a node from the service only sets the Local Configuration Manager settings so the node is no longer connecting to the service.
> This does not effect the configuration that is currently applied to the node.
> To remove the current configuration, use the [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) or delete the local configuration file (this is the only option for Linux nodes).

### <a name="azure-portal"></a>Portale di Azure

From Azure Automation, click on **State configuration (DSC)** in the table of contents.
Next click **Nodes** to view the list of nodes that are registered with the service.
Click on the name of the node you wish to remove.
In the Node view that opens, click **Unregister**.

### <a name="powershell"></a>PowerShell

To unregister a node from Azure Automation State Configuration service using PowerShell, follow the documentation for the cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione alla configurazione dello stato di Automazione di Azure](automation-dsc-getting-started.md)
- Per informazioni sull'onboarding dei nodi, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md)
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md)
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per informazioni sui prezzi, vedere [Prezzi di Configurazione stato di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
