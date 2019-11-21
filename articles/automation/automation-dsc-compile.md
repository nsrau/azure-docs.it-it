---
title: Compilazione di configurazioni in Configurazione stato di Automazione di Azure
description: Questo articolo descrive come compilare configurazioni Desired State Configuration (DSC) per l'automazione di Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d3cca9d8954d9ac158d88b393c46672da3faa19
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231724"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilazione di configurazioni DSC in Configurazione stato di Automazione di Azure

You can compile Desired State Configuration (DSC) configurations in two ways with Azure Automation State Configuration: in Azure and in Windows PowerShell. La tabella seguente consente di determinare quando usare ciascun metodo in base alle caratteristiche specifiche:

- Azure State Configuration compilation service
  - Beginner method with interactive user interface
   - Facilità di controllo dello stato dei processi

- Windows PowerShell
  - Call from Windows PowerShell on local workstation or build service
  - Integrate with development test pipeline
  - Provide complex parameter values
  - Work with node and non-node data at scale
  - Significant performance improvement

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compiling a DSC Configuration in Azure State Configuration

### <a name="portal"></a>di Microsoft Azure

1. Nell'account di Automazione fare clic su **Configurazione stato (DSC)** .
1. Fare clic sulla scheda **Configurazioni** e quindi selezionare il nome della configurazione da compilare.
1. Fare clic su **Compila**.
1. Se la configurazione non ha alcun parametro, verrà richiesto di confermare se compilarla. Se la configurazione contiene parametri, verrà aperto il pannello **Compila configurazione** in cui sarà possibile specificare i valori dei parametri. Per altri dettagli sui parametri, vedere la sezione seguente [**Parametri di base**](#basic-parameters).
1. Viene aperta la pagina **Processo di compilazione** in cui è possibile tenere traccia dello stato del processo di compilazione e delle configurazioni dei nodi (documenti di configurazione MOF) che sono state inserite nel server di pull di Configurazione stato di Automazione di Azure.

### <a name="azure-powershell"></a>Azure PowerShell

È possibile usare [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) per avviare la compilazione con Windows PowerShell. Il codice di esempio seguente avvia la compilazione di una configurazione DSC denominata **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` restituisce un oggetto processo di compilazione che è possibile usare per tenere traccia dello stato. È quindi possibile usare questo oggetto processo di compilazione con [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)
per determinare lo stato del processo di compilazione e con [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)
per visualizzarne i flussi (output). Il codice di esempio seguente avvia la compilazione della configurazione **SampleConfig** , attende che venga completata e quindi ne visualizza i flussi.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>Parametri di base

La dichiarazione dei parametri nelle configurazioni DSC, inclusi i tipi e le proprietà dei parametri, funziona esattamente come nei runbook di Automazione di Azure. Per altre informazioni sui parametri dei runbook, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md) .

L'esempio seguente usa due parametri denominati **FeatureName** e **IsPresent**, per determinare i valori delle proprietà nella configurazione del nodo **ParametersExample.sample**, generati durante la compilazione.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

È possibile compilare configurazioni DSC che usano parametri di base nel portale di Configurazione stato di Automazione di Azure o con Azure PowerShell:

#### <a name="portal"></a>di Microsoft Azure

Nel portale è possibile immettere i valori dei parametri dopo avere fatto clic su **Compila**.

![Parametri per la compilazione della configurazione](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell richiede i parametri in un elemento [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) dove la chiave corrisponde al nome del parametro e il valore è uguale al valore del parametro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Per informazioni sul passaggio di PSCredentials come parametri, vedere [Asset credenziali](#credential-assets) più avanti.

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Compiling configurations in Azure Automation that contain Composite Resources

Le **risorse composite** consentono di usare le configurazioni DSC come risorse annidate in una configurazione. Questo consente di applicare più configurazioni a un'unica risorsa. Per informazioni sulle **risorse composite**, vedere [Risorse composite: uso di una configurazione DSC come risorsa](/powershell/scripting/dsc/resources/authoringresourcecomposite).

> [!NOTE]
> In order for configurations containing **Composite Resources** to compile correctly, you must first ensure that any DSC Resources that the composite relies on are first imported in to Azure Automation.

Adding a DSC **Composite Resource** is no different than adding any PowerShell module to Azure Automation.
The step by step instruction for this process is documented in the article [Manage Modules in Azure Automation](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Managing ConfigurationData when compiling configuration in Azure Automation

**ConfigurationData** consente di separare la configurazione strutturale dalla configurazione specifica di qualsiasi ambiente mentre si usa PowerShell DSC. Vedere il blog relativo alla [distinzione tra "cosa" e "dove" in PowerShell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) per altre informazioni su **ConfigurationData**.

> [!NOTE]
> You can use **ConfigurationData** when compiling in Azure Automation State Configuration using Azure PowerShell but not in the Azure portal.

La configurazione DSC di esempio seguente usa **ConfigurationData** con le parole chiave **$ConfigurationData** e **$AllNodes**. Per questo esempio è necessario anche il [modulo **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/):

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

You can compile the preceding DSC configuration with Windows PowerShell. The following script adds two node configurations to the Azure Automation State Configuration Pull Service: **ConfigurationDataSample.MyVM1** and **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Working with Assets in Azure Automation during compilation

I riferimenti agli asset sono gli stessi in Configurazione stato di Automazione di Azure e nei runbook. Per altre informazioni, vedere gli argomenti seguenti:

- [Certificates](automation-certificates.md)
- [Connessioni](automation-connections.md)
- [Credenziali](automation-credentials.md)
- [Variabili](automation-variables.md)

#### <a name="credential-assets"></a>Asset credenziali

Le configurazioni DSC in Automazione di Azure possono fare riferimento agli asset credenziali di Automazione con il cmdlet `Get-AutomationPSCredential`. Se una configurazione non ha un parametro con un tipo **PSCredential** è possibile usare il cmdlet `Get-AutomationPSCredential` passando il nome stringa di un asset credenziali di Automazione di Azure al cmdlet per recuperare le credenziali. È quindi possibile usare tale oggetto per il parametro che richiede l'oggetto **PSCredential**. In background, l'asset credenziali di Automazione di Azure con tale nome viene recuperato e passato alla configurazione. L'esempio seguente illustra questa azione.

Per garantire la sicurezza delle credenziali nelle configurazioni dei nodi (documenti di configurazione MOF), è necessario crittografare le credenziali nel file MOF delle configurazioni dei nodi. Attualmente è però necessario comunicare a PowerShell DSC che l'output delle credenziali in testo normale durante la generazione dei file MOF delle configurazioni dei nodi è corretto, perché PowerShell DSC non sa che dopo la generazione Automazione di Azure crittograferà l'intero file MOF tramite un processo di compilazione.

You can tell PowerShell DSC that it is okay for credentials to be outputted in plain text in the generated node configuration MOFs using Configuration Data. È consigliabile passare `PSDscAllowPlainTextPassword = $true` tramite **ConfigurationData** per il nome di ogni blocco di nodi visualizzato nella configurazione DSC che usa le credenziali.

L'esempio seguente mostra una configurazione DSC che usa un asset credenziali di Automazione.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

È possibile compilare la configurazione DSC precedente con PowerShell. Il cmdlet di PowerShell seguente aggiunge due configurazioni del nodo al server di pull di Configurazione stato di Automazione di Azure, **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Al termine della compilazione è possibile che venga visualizzato un messaggio per segnalare che **il modulo Microsoft.PowerShell.Management non è stato importato perché è già stato importato lo snap-in Microsoft.PowerShell.Management.** Questo avviso può essere ignorato.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Compiling configurations in Windows PowerShell and publishing to Azure Automation

È anche possibile importare configurazioni di nodo (MOF) compilate all'esterno di Azure.
This includes compiling from a developer workstation or in a service such as [Azure DevOps](https://dev.azure.com).
There are multiple advantages to this approach including performance and reliability.
Compiling in Windows PowerShell also provides the option to sign configuration content.
Una configurazione del nodo firmata viene verificata in locale in un nodo gestito dall'agente DSC, garantendo che la configurazione applicata al nodo provenga da una fonte autorizzata.

> [!NOTE]
> Il file di configurazione del nodo deve essere superiore a 1 MB per consentire l'importazione in Automazione di Azure.

Per altre informazioni su come firmare le configurazioni del nodo, vedere [Improvements in WMF 5.1 - How to sign configuration and module](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations) (Miglioramenti in WMF 5.1: come firmare la configurazione e il modulo).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Compiling a configuration in Windows PowerShell

The process to compile DSC configurations in Windows PowerShell is included in the PowerShell DSC documentation [Write, Compile, and Apply a Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
This can be executed from a developer workstation or within a build service such as [Azure DevOps](https://dev.azure.com).

The MOF file or files produced by compiling the configuration can then be imported directly in to the Azure State Configuration service.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importazione di una configurazione del nodo nel portale di Azure

1. Dall'account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina **Configurazione stato (DSC)** fare clic sulla scheda **Configurazioni** e quindi su **+ Aggiungi**.
1. Nella pagina **Importa** fare clic sull'icona della cartella accanto alla casella di testo **File di configurazione nodo** per cercare un file di configurazione del nodo (MOF) nel computer locale.

   ![Cercare il file locale](./media/automation-dsc-compile/import-browse.png)

1. Immettere un nome nella casella di testo **Nome configurazione**. Il nome deve corrispondere al nome della configurazione da cui è stata compilata la configurazione del nodo.
1. Fare clic su **OK**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Importing a node configuration with Azure PowerShell

È possibile usare il cmdlet [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) per importare una configurazione del nodo nell'account di automazione.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione alla configurazione dello stato di Automazione di Azure](automation-dsc-getting-started.md)
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md)
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per informazioni sui prezzi, vedere [Prezzi di Configurazione stato di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
