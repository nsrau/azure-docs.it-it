---
title: Configurare i server sullo stato desiderato e gestire gli orientamenti con Automazione di Azure | Microsoft Docs
description: 'Esercitazione: Gestire configurazioni di server con Automation DSC per Azure'
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.openlocfilehash: 63a83e35ce29541de578cb264464448fb6ee3e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurare i server sullo stato desiderato e gestire gli orientamenti

Configurazione dello stato desiderato di automazione di Azure consente di specificare le configurazioni per i server e assicurarsi che tali server rimangano nello stato specificato nel tempo.



> [!div class="checklist"]
> * Eseguire l'onboarding di una VM in modo che sia gestita da Automation DSC per Azure
> * Caricare una configurazione in Automazione di Azure
> * Compilare una configurazione in una configurazione nodo
> * Assegnare una configurazione nodo a un nodo gestito
> * Controllare lo stato di conformità di un nodo gestito

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](automation-sec-configure-azure-runas-account.md).
* Una VM di Azure Resource Manager (non classica) che esegue Windows Server 2008 R2 o versioni successive. Per istruzioni sulla creazione di una VM, vedere [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
* Modulo Azure PowerShell 3.6 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Familiarità con DSC. Per informazioni su DSC, vedere [Panoramica di Windows PowerShell DSC (Desired State Configuration)](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Creare e caricare una configurazione in Automazione di Azure

Per questa esercitazione si userà una semplice configurazione DSC che assicura che IIS sia installato nella VM.

Per informazioni sulle configurazioni DSC, vedere [Configurazioni DSC](https://docs.microsoft.com/powershell/dsc/configurations).

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

Chiamare il cmdlet `Import-AzureRmAutomationDscConfiguration` per caricare la configurazione nell'account di Automazione:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilare una configurazione in una configurazione nodo

Una configurazione DSC deve essere compilata in una configurazione nodo affinché possa essere assegnata a un nodo.

Per altre informazioni sulla compilazione di configurazioni, vedere [Configurazioni DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Chiamare il cmdlet `Start-AzureRmAutomationDscCompilationJob` per compilare la configurazione `TestConfig` in una configurazione nodo:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Verrà creata una configurazione nodo denominata `TestConfig.WebServer` nell'account di Automazione.

## <a name="register-a-vm-to-be-managed-by-dsc"></a>Registrare una VM in modo che sia gestita da DSC

È possibile usare Automation DSC per Azure per gestire VM di Azure (sia classiche che di Resource Manager), VM locali, computer Linux, VM di AWS e computer fisici locali. Questo argomento descrive soltanto come registrare VM di Azure Resource Manager.
Per informazioni sulla registrazione di altri tipi di computer, vedere [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md).

Chiamare il cmdlet `Register-AzureRmAutomationDscNode` per registrare la VM con Automation DSC per Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

In questo modo viene registrata la VM specificata come nodo DSC nell'account di Automazione di Azure.

### <a name="specify-configuration-mode-settings"></a>Specificare le impostazioni delle modalità di configurazione

Quando si registra una VM come nodo gestito, è anche possibile specificare le proprietà della configurazione.
Ad esempio, è possibile specificare che lo stato della macchina deve essere applicato una sola volta (DSC non tenta di applicare la configurazione dopo il controllo iniziale) specificando `ApplyOnly` come valore della proprietà **ConfigurationMode**:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

È anche possibile specificare la frequenza con cui DSC controlla lo stato della configurazione usando la proprietà **ConfigurationModeFrequencyMins**:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

Per altre informazioni sull'impostazione delle proprietà di configurazione per un nodo gestito, vedere [Register-AzureRmAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0).

Per altre informazioni sulle impostazioni di configurazione DSC, vedere [Configuring the Local Configuration Manager](https://docs.microsoft.com/powershell/dsc/metaconfig)(Configurazione di Gestione configurazione locale).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Assegnare una configurazione nodo a un nodo gestito

Ora è possibile assegnare la configurazione nodo compilata alla VM che si vuole configurare.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

In questo modo si assegna la configurazione nodo denominata `TestConfig.WebServer` al nodo DSC registrato denominato `DscVm`.
Per impostazione predefinita, il nodo DSC viene verificato per la conformità con la configurazione nodo ogni 30 minuti.
Per informazioni su come modificare l'intervallo di controllo della conformità, vedere [Configuring the Local Configuration Manager](https://docs.microsoft.com/PowerShell/DSC/metaConfig) (Configurazione di Gestione configurazione locale)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Controllare lo stato di conformità di un nodo gestito

È possibile ottenere report sullo stato di conformità di un nodo DSC chiamando il cmdlet `Get-AzureRmAutomationDscNodeReport`:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports Get-Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$report[0]
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come caricare i nodi da gestire con Automation DSC per Azure, vedere [Onboarding di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md)
* Per informazioni su come usare il portale di Azure con Automation DSC, vedere [Introduzione ad Automation DSC per Azure](automation-dsc-getting-started.md)
* Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Automation DSC per Azure](automation-dsc-compile.md)
* Per informazioni di riferimento sui cmdlet di PowerShell per Automation DSC per Azure, vedere [Azure Automation DSC cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet di Automation DSC per Azure)
* Per informazioni sui prezzi, vedere [Prezzi di Automation DSC per Azure](https://azure.microsoft.com/pricing/details/automation/)
* Per vedere un esempio dell'uso di Automation DSC per Azure in una pipeline di distribuzione continua, vedere [Distribuzione continua in macchine virtuali IaaS tramite Automation DSC per Azure e Chocolatey](automation-dsc-cd-chocolatey.md)