---
title: Configurare i server allo stato desiderato e gestire la deviazione con Automazione di Azure
description: 'Esercitazione: Gestire le configurazioni dei server con Configurazione stato di Automazione di Azure'
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678700"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurare i server sullo stato desiderato e gestire gli orientamenti

Configurazione stato di Automazione di Azure consente di specificare le configurazioni per i server e verificare che tali server rimangano nello stato specificato nel corso del tempo.

> [!div class="checklist"]
> - Eseguire l'onboarding di una VM in modo che sia gestita da Automation DSC per Azure
> - Caricare una configurazione in Automazione di Azure
> - Compilare una configurazione in una configurazione nodo
> - Assegnare una configurazione nodo a un nodo gestito
> - Controllare lo stato di conformità di un nodo gestito

Per questa esercitazione, viene usata una [configurazione DSC](/powershell/scripting/dsc/configurations/configurations) semplice che garantisce che IIS sia installato nella macchina virtuale.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

- Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](automation-sec-configure-azure-runas-account.md).
- Una macchina virtuale Azure Resource Manager (non classica) che esegue Windows Server 2008 R2 o versione successiva. Per istruzioni sulla creazione di una VM, vedere [creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Modulo Azure PowerShell 3.6 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Familiarità con DSC (Desired State Configuration). Per informazioni su DSC, vedere [Panoramica di Windows PowerShell DSC (Desired state Configuration](/powershell/scripting/dsc/overview/overview)).

## <a name="support-for-partial-configurations"></a>Supporto per le configurazioni parziali

La configurazione dello stato di automazione di Azure supporta l'uso di [configurazioni parziali](/powershell/scripting/dsc/pull-server/partialconfigs). In questo scenario DSC è configurato per gestire più configurazioni in modo indipendente e ogni configurazione viene recuperata da automazione di Azure. Tuttavia, è possibile assegnare una sola configurazione a un nodo per ogni account di automazione. Ciò significa che se si usano due configurazioni per un nodo, saranno necessari due account di automazione.

Per informazioni dettagliate su come registrare una configurazione parziale da un servizio di pull, vedere la documentazione per le [configurazioni parziali](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Per altre informazioni su come i team possono collaborare per gestire i server in modo collaborativo usando la configurazione come codice, vedere [informazioni sul ruolo di DSC in una pipeline ci/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) e seguire le istruzioni visualizzate.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Creare e caricare una configurazione in Automazione di Azure


In un editor di testo digitare quanto segue e salvarlo localmente come **TestConfig. ps1**.

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
> Negli scenari più avanzati in cui è necessario importare più moduli che forniscono risorse DSC, assicurarsi che ogni modulo disponga di una riga `Import-DscResource` univoca nella configurazione.

Chiamare il cmdlet [Import-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) per caricare la configurazione nell'account di automazione.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilare una configurazione in una configurazione nodo

Una configurazione DSC deve essere compilata in una configurazione nodo affinché possa essere assegnata a un nodo. Vedere [Configurazioni DSC](/powershell/scripting/dsc/configurations/configurations).

Chiamare il cmdlet [Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) per compilare la `TestConfig` configurazione in una configurazione di nodo `TestConfig.WebServer` denominata nell'account di automazione.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrare una VM in modo che sia gestita da Configurazione stato

È possibile usare Configurazione stato di Automazione di Azure per gestire macchine virtuali di Azure (sia classiche sia Resource Manager), macchine virtuali locali, computer Linux, macchine virtuali AWS e computer fisici locali. Questo argomento descrive soltanto come registrare VM di Azure Resource Manager. Per informazioni sulla registrazione di altri tipi di computer, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md).

Chiamare il cmdlet [Register-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) per registrare la VM con la configurazione dello stato di automazione di Azure come nodo gestito. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Specificare le impostazioni delle modalità di configurazione

Usare il cmdlet [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) per registrare una macchina virtuale come nodo gestito e specificare le proprietà di configurazione. Ad esempio, è possibile specificare che lo stato del computer deve essere applicato una sola volta specificando `ApplyOnly` come valore della `ConfigurationMode` proprietà. La configurazione dello stato non tenta di applicare la configurazione dopo il controllo iniziale.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

È anche possibile specificare la frequenza con cui DSC controlla lo stato di configurazione `ConfigurationModeFrequencyMins` usando la proprietà. Per altre informazioni sulle impostazioni di configurazione DSC, vedere [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Configurazione di Gestione configurazione locale).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Assegnare una configurazione nodo a un nodo gestito

Ora è possibile assegnare la configurazione nodo compilata alla VM che si vuole configurare.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Questa operazione assegna la configurazione del nodo `TestConfig.WebServer` denominata al nodo `DscVm`DSC registrato. Per impostazione predefinita, il nodo DSC viene verificato per la conformità con la configurazione nodo ogni 30 minuti. Per informazioni su come modificare l'intervallo di controllo della conformità, vedere [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig) (Configurazione di Gestione configurazione locale).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Controllare lo stato di conformità di un nodo gestito

È possibile ottenere i report sullo stato di conformità di un nodo gestito usando il cmdlet [Get-AzAutomationDscNodeReport](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0) .

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Rimuovi nodi dal servizio

Quando si aggiunge un nodo alla configurazione dello stato di automazione di Azure, le impostazioni in Configuration Manager locali sono impostate per la registrazione con il servizio e le configurazioni pull e i moduli richiesti per configurare il computer.
Se si sceglie di rimuovere il nodo dal servizio, è possibile farlo usando il portale di Azure o i cmdlet AZ.

> [!NOTE]
> Se si annulla la registrazione di un nodo dal servizio, vengono impostate solo le impostazioni di Configuration Manager locali in modo che il nodo non si connetta più al servizio.
> Questa operazione non influisce sulla configurazione attualmente applicata al nodo.
> Per rimuovere la configurazione corrente, usare [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) o eliminare il file di configurazione locale (questa è l'unica opzione per i nodi Linux).

### <a name="azure-portal"></a>Portale di Azure

Da automazione di Azure fare clic su **configurazione stato (DSC)** nel sommario.
Fare quindi clic su **nodi** per visualizzare l'elenco di nodi registrati con il servizio.
Fare clic sul nome del nodo che si desidera rimuovere.
Nella visualizzazione nodi visualizzata fare clic su **Annulla registrazione**.

### <a name="powershell"></a>PowerShell

Per annullare la registrazione di un nodo dal servizio di configurazione dello stato di automazione di Azure tramite PowerShell, seguire la documentazione del cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Guida introduttiva alla configurazione dello stato di automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni su come caricare i nodi, vedere Caricamento [di computer per la gestione mediante la configurazione dello stato di automazione di Azure](automation-dsc-onboarding.md).
- Per informazioni sulla compilazione delle configurazioni DSC per poterle assegnare ai nodi di destinazione, vedere [compilazione di configurazioni in Azure Automation state Configuration](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [cmdlet di configurazione dello stato di automazione di Azure](/powershell/module/azurerm.automation/#automation).
- Per informazioni sui prezzi, vedere [prezzi di configurazione dello stato di automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
