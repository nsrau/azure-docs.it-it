---
title: Configurare i computer a uno stato desiderato in Automazione di Azure
description: Questo articolo descrive come configurare i computer a uno stato desiderato usando State Configuration di Automazione di Azure.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 55c7522ad1dc6c7f91fae608a777dab3cd67d2ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86183171"
---
# <a name="configure-machines-to-a-desired-state"></a>Configurare i computer allo stato desiderato

Configurazione stato di Automazione di Azure consente di specificare le configurazioni per i server e verificare che tali server rimangano nello stato specificato nel corso del tempo.

> [!div class="checklist"]
> - Eseguire l'onboarding di una VM in modo che sia gestita da Automation DSC per Azure
> - Caricare una configurazione in Automazione di Azure
> - Compilare una configurazione in una configurazione nodo
> - Assegnare una configurazione nodo a un nodo gestito
> - Controllare lo stato di conformità di un nodo gestito

Per questa esercitazione si usa una semplice [configurazione DSC](/powershell/scripting/dsc/configurations/configurations) che assicura che IIS sia installato nella VM.

## <a name="prerequisites"></a>Prerequisiti

- Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](./manage-runas-account.md).
- Una VM di Azure Resource Manager (non classica) che esegue Windows Server 2008 R2 o versioni successive. Per istruzioni sulla creazione di una VM, vedere [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md)
- Modulo Azure PowerShell 3.6 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Familiarità con DSC (Desired State Configuration). Per informazioni su DSC, vedere [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Supporto di configurazioni parziali

State Configuration di Automazione di Azure supporta l'uso di [configurazioni parziali](/powershell/scripting/dsc/pull-server/partialconfigs). In questo scenario DSC è configurato per gestire più configurazioni in modo indipendente e ogni configurazione viene recuperata da Automazione di Azure. È tuttavia possibile assegnare una sola configurazione a un nodo per ogni account di Automazione. Ciò significa che se si usano due configurazioni per un nodo, saranno necessari due account di Automazione.

Per informazioni dettagliate su come registrare una configurazione parziale da un servizio di pull, vedere la documentazione per le [configurazioni parziali](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Per altre informazioni su come i team possono interagire per gestire i server in modo collaborativo usando la configurazione come codice, vedere [Informazioni sul ruolo DSC in una pipeline CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) e seguire le istruzioni visualizzate.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Creare e caricare una configurazione in Automazione di Azure


In un editor di testo digitare quanto segue e salvare il file in locale con il nome **TestConfig.ps1**.

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
> Negli scenari più avanzati in cui è necessario importare più moduli che forniscono risorse DSC, assicurarsi che ogni modulo abbia una riga `Import-DscResource` univoca nella configurazione.

Chiamare il cmdlet [Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) per caricare la configurazione nell'account di Automazione.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilare una configurazione in una configurazione nodo

Una configurazione DSC deve essere compilata in una configurazione nodo affinché possa essere assegnata a un nodo. Vedere [Configurazioni DSC](/powershell/scripting/dsc/configurations/configurations).

Chiamare il cmdlet [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) per compilare la configurazione di `TestConfig` in una configurazione nodo denominata `TestConfig.WebServer` nell'account di Automazione.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrare una VM in modo che sia gestita da Configurazione stato

È possibile usare Configurazione stato di Automazione di Azure per gestire macchine virtuali di Azure (sia classiche sia Resource Manager), macchine virtuali locali, computer Linux, macchine virtuali AWS e computer fisici locali. Questo argomento descrive soltanto come registrare VM di Azure Resource Manager. Per informazioni sulla registrazione di altri tipi di computer, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md).

Chiamare il cmdlet [Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) per registrare la VM con State Configuration di Automazione di Azure come nodo gestito. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Specificare le impostazioni delle modalità di configurazione

Usare il cmdlet [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) per registrare una macchina virtuale come nodo gestito e specificare le proprietà di configurazione. È ad esempio possibile specificare che lo stato del computer debba essere applicato una sola volta specificando `ApplyOnly` come valore della proprietà `ConfigurationMode`. State Configuration non prova ad applicare la configurazione dopo il controllo iniziale.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

È anche possibile specificare la frequenza con cui DSC controlla lo stato della configurazione usando la proprietà `ConfigurationModeFrequencyMins`. Per altre informazioni sulle impostazioni di configurazione DSC, vedere [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Configurazione di Gestione configurazione locale).

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

In questo modo si assegna la configurazione nodo denominata `TestConfig.WebServer` al nodo DSC registrato `DscVm`. Per impostazione predefinita, il nodo DSC viene verificato per la conformità con la configurazione nodo ogni 30 minuti. Per informazioni su come modificare l'intervallo di controllo della conformità, vedere [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig) (Configurazione di Gestione configurazione locale).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Controllare lo stato di conformità di un nodo gestito

È possibile ottenere report sullo stato di conformità di un nodo gestito usando il cmdlet [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0).

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Rimuovere i nodi dal servizio

Quando si aggiunge un nodo a State Configuration di Automazione di Azure, le impostazioni in Configuration Manager locale abilitano la registrazione con il servizio e il pull delle configurazioni e dei moduli richiesti per configurare il computer.
Se si sceglie di rimuovere il nodo dal servizio, è possibile farlo usando il portale di Azure o i cmdlet Az.

> [!NOTE]
> Se si annulla la registrazione di un nodo dal servizio, vengono definite solo le impostazioni di Configuration Manager locale e quindi il nodo non si connette più al servizio.
> Questa operazione non influisce sulla configurazione attualmente applicata al nodo.
> Per rimuovere la configurazione corrente, usare [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) o eliminare il file di configurazione locale (questa è l'unica opzione per i nodi Linux).

### <a name="azure-portal"></a>Portale di Azure

Da Automazione di Azure fare clic su **State configuration (DSC)** nel sommario.
Fare quindi clic su **Nodi** per visualizzare l'elenco dei nodi registrati con il servizio.
Fare clic sul nome del nodo che si vuole rimuovere.
Nella visualizzazione del nodo visualizzata fare clic su **Annulla registrazione**.

### <a name="powershell"></a>PowerShell

Per annullare la registrazione di un nodo dal servizio State Configuration di Automazione di Azure tramite PowerShell, seguire la documentazione del cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione a State Configuration di Automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni su come abilitare i nodi, vedere [Abilitare State Configuration di Automazione di Azure](automation-dsc-onboarding.md).
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilare configurazioni DSC in State Configuration di Automazione di Azure](automation-dsc-compile.md).
- Per un esempio dell'uso di State Configuration di Automazione di Azure in una pipeline di distribuzione continua, vedere [Configurare la distribuzione continua con Chocolatey](automation-dsc-cd-chocolatey.md).
- Per informazioni sui prezzi, vedere [Prezzi di State Configuration di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
