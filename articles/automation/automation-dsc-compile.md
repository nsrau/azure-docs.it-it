---
title: Compilazione di configurazioni in Configurazione stato di Automazione di Azure
description: Questo articolo descrive come compilare configurazioni Desired State Configuration (DSC) per l'automazione di Azure.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: 48593920bdfcf743fceaeaeec891c0d5c4f2e108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057642"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilazione di configurazioni DSC in Configurazione stato di Automazione di Azure

È possibile compilare le configurazioni DSC (Desired State Configuration) nella configurazione dello stato di automazione di Azure nei modi seguenti:You can compile Desired State Configuration (DSC) configurations in Azure Automation State Configuration in the following ways:

- Servizio di compilazione Configurazione stato di AzureAzure State Configuration compilation service
  - Metodo principiante con interfaccia utente interattiva
   - Facilità di controllo dello stato dei processi

- Windows PowerShell
  - Chiamata da Windows PowerShell su workstation locale o servizio di compilazione
  - Integrazione con la pipeline di test di sviluppo
  - Fornire valori di parametro complessiProvide complex parameter values
  - Utilizzare i dati di nodi e non di nodo su larga scalaWork with node and non-node data at scale
  - Miglioramento significativo delle prestazioni

Per informazioni dettagliate sulla compilazione, vedere Estensione Configurazione stato desiderato con i modelli di [Azure Resource Manager.](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details)

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compilazione di una configurazione DSC nella configurazione dello stato di AzureCompiling a DSC configuration in Azure State Configuration

### <a name="portal"></a>Portale

1. Nell'account di Automazione fare clic su **Configurazione stato (DSC)**.
1. Fare clic sulla scheda **Configurazioni** e quindi selezionare il nome della configurazione da compilare.
1. Fare clic su **Compila**.
1. Se la configurazione non dispone di parametri, viene richiesto di confermare se si desidera compilarla. Se la configurazione dispone di parametri, il pannello **Compila configurazione** si apre in modo che sia possibile fornire valori di parametro.
1. Viene aperta la pagina Processo di compilazione in modo che sia possibile tenere traccia dello stato del processo di compilazione. È anche possibile usare questa pagina per tenere traccia delle configurazioni dei nodi (documenti di configurazione MOF) inserite nel server di pull della configurazione dello stato di automazione di Azure.You can also use this page to track the node configurations (MOF configuration documents) placed on the Azure Automation State Configuration pull server.

### <a name="azure-powershell"></a>Azure PowerShell

È possibile usare Start-AzAutomationDscCompilationJob per avviare la compilazione con Windows PowerShell.You can use [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) to start compiling with Windows PowerShell. Il codice di esempio seguente inizia la compilazione di una configurazione DSC denominata SampleConfig.The following sample code begins compilation of a DSC configuration called SampleConfig.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDscCompilationJob** restituisce un oggetto processo di compilazione che è possibile utilizzare per tenere traccia dello stato del processo. È quindi possibile utilizzare questo oggetto processo di compilazione con [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) per determinare lo stato del processo di compilazione e [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) per visualizzare i relativi flussi (output). Nell'esempio seguente viene avviata la compilazione della configurazione SampleConfig, viene attesa fino al completamento e quindi vengono visualizzati i relativi flussi.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Dichiarare i parametri di baseDeclare basic parameters

La dichiarazione dei parametri nelle configurazioni DSC, inclusi i tipi e le proprietà dei parametri, funziona esattamente come nei runbook di Automazione di Azure. Per altre informazioni sui parametri dei runbook, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md) .

L'esempio seguente usa due parametri denominati *FeatureName* e *IsPresent*, per determinare i valori delle proprietà nella configurazione del nodo ParametersExample.sample, generati durante la compilazione.

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

È possibile compilare configurazioni DSC che usano parametri di base nel portale di configurazione dello stato di automazione di Azure o con Azure PowerShell.You can compile DSC configurations that use basic parameters in the Azure Automation State Configuration portal or with Azure PowerShell.

#### <a name="portal"></a>Portale

Nel portale è possibile immettere i valori dei parametri dopo avere fatto clic su **Compila**.

![Parametri per la compilazione della configurazione](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell richiede parametri in una [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables), in cui la chiave corrisponde al nome del parametro e il valore è uguale al valore del parametro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Per informazioni sul passaggio di PSCredentials come parametri, vedere [Asset credenziali](#credential-assets) più avanti.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Compilare configurazioni contenenti risorse composite in Automazione di AzureCompile configurations containing composite resources in Azure Automation

La funzionalità **Risorse composite** consente di usare le configurazioni DSC come risorse annidate all'interno di una configurazione. Questa funzionalità consente l'applicazione di più configurazioni a una singola risorsa. Per altre informazioni sulle risorse composite, vedere [Risorse composite: uso di una configurazione DSC come risorsa.](/powershell/scripting/dsc/resources/authoringresourcecomposite)

> [!NOTE]
> In modo che le configurazioni contenenti risorse composte vengano compilate correttamente, è innanzitutto necessario importare in Automazione di Azure tutte le risorse DSC su cui si basano i compositi. Adding a DSC composite resource is no different from adding any PowerShell module to Azure Automation. Questo processo è documentato in [Gestisci moduli in Automazione di Azure.This](/azure/automation/shared-resources/modules)process is documented in Manage Modules in Azure Automation .

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Gestire ConfigurationData durante la compilazione delle configurazioni in Automazione di AzureManage ConfigurationData when compiling configurations in Azure Automation

**ConfigurationData** consente di separare la configurazione strutturale dalla configurazione specifica di qualsiasi ambiente mentre si usa PowerShell DSC. Per ulteriori informazioni, vedere Separazione di ["Cosa" da "Dove" in PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Durante la compilazione nella configurazione dello stato di automazione di Azure, è possibile usare ConfigurationData in Azure PowerShell ma non nel portale di Azure.When compiling in Azure Automation State Configuration, you can use **ConfigurationData** in Azure PowerShell but not in the Azure portal.

La configurazione DSC di esempio seguente usa **ConfigurationData** con le parole chiave $ConfigurationData e $AllNodes. È inoltre necessario il [modulo xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) per questo esempio.

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

È possibile compilare la configurazione DSC precedente con Windows PowerShell.You can compile the preceding DSC configuration with Windows PowerShell. The following script adds two node configurations to the Azure Automation State Configuration pull service: ConfigurationDataSample.MyVM1 and ConfigurationDataSample.MyVM3.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Usare le risorse in Automazione di Azure durante la compilazioneWork with assets in Azure Automation during compilation

I riferimenti agli asset sono gli stessi sia nella configurazione dello stato di automazione di Azure che nei runbook. Per altre informazioni, vedere gli argomenti seguenti: 

- [Certificati](automation-certificates.md)
- [Connessioni](automation-connections.md)
- [Credenziali](automation-credentials.md)
- [Variabili](automation-variables.md)

#### <a name="credential-assets"></a>Risorse credenziali

Le configurazioni DSC in Automazione di Azure possono fare riferimento agli asset delle credenziali di automazione usando il cmdlet Get-AutomationPSCredential.DSC configurations in Azure Automation can reference Automation credential assets using the **Get-AutomationPSCredential** cmdlet. Se una configurazione include un parametro che specifica un oggetto **PSCredential,** usare **Get-AutomationPSCredential** passando il nome di stringa di un asset delle credenziali di Automazione di Azure al cmdlet per recuperare le credenziali. Utilizzare quindi tale oggetto per il parametro che richiede l'oggetto **PSCredential.** In background, l'asset credenziali di Automazione di Azure con tale nome viene recuperato e passato alla configurazione. L'esempio seguente mostra questo scenario in azione.

Per garantire la sicurezza delle credenziali nelle configurazioni dei nodi (documenti di configurazione MOF), è necessario crittografare le credenziali nel file MOF delle configurazioni dei nodi. Attualmente è necessario concedere a PowerShell DSC l'autorizzazione per restituire le credenziali in testo normale durante la generazione MOF di configurazione del nodo. PowerShell DSC non è a conoscenza del fatto che Automazione di Azure crittografa l'intero file MOF dopo la generazione tramite un processo di compilazione.

È possibile indicare a PowerShell DSC che è possibile che le credenziali vengano emesse in testo normale nei MOF di configurazione del nodo generato usando i dati di configurazione. È necessario `PSDscAllowPlainTextPassword = $true` passare tramite **ConfigurationData** per ogni nome di blocco del nodo visualizzato nella configurazione DSC e le credenziali.

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

È possibile compilare la configurazione DSC precedente con PowerShell. The following PowerShell code adds two node configurations to the Azure Automation State Configuration pull server: CredentialSample.MyVM1 and CredentialSample.MyVM2.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Al termine della compilazione, è possibile che venga visualizzato il messaggio di errore "Il modulo 'Microsoft.PowerShell.Management' non è stato importato perché lo snap-in 'Microsoft.PowerShell.Management' è già stato importato." È possibile ignorare questo messaggio.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Compilazione della configurazione DSC in Windows PowerShell

È anche possibile importare configurazioni di nodo (MOF) compilate all'esterno di Azure. L'importazione include la compilazione da una workstation di sviluppo o in un servizio come DevOps di [Azure.The](https://dev.azure.com)import includes compilation from a developer workstation or in a service such as Azure DevOps . Questo approccio presenta diversi vantaggi, tra cui prestazioni e affidabilità.

La compilazione in Windows PowerShell offre anche la possibilità di firmare il contenuto della configurazione. L'agente DSC verifica una configurazione di nodo firmato localmente in un nodo gestito. La verifica garantisce che la configurazione applicata al nodo provenga da un'origine autorizzata.

> [!NOTE]
> Il file di configurazione del nodo deve essere superiore a 1 MB per consentire l'importazione in Automazione di Azure.

Per ulteriori informazioni sulla firma delle configurazioni dei nodi, vedere [Miglioramenti in WMF 5.1 - Come firmare la configurazione e](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)il modulo .

### <a name="compile-the-dsc-configuration"></a>Compilare la configurazione DSC

Il processo di compilazione delle configurazioni DSC in Windows PowerShell è incluso nella documentazione di PowerShell DSC [Write, Compile e Apply a Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
È possibile eseguire questo processo da una workstation di sviluppo o all'interno di un servizio di compilazione, ad esempio DevOps di Azure.You can execute this process from a developer workstation or within a build service, such as [Azure DevOps](https://dev.azure.com). È quindi possibile importare i file MOF generati dalla compilazione della configurazione nel servizio Configurazione stato di Azure.You can then import the MOF file(s) produced by compiling the configuration into the Azure State Configuration service.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importare una configurazione di nodo nel portale di AzureImport a node configuration in the Azure portal

1. Dall'account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC), fare clic sulla scheda **Configurazioni,** quindi fare clic su **Aggiungi**.
1. Nella pagina Importa fare clic sull'icona della cartella accanto alla casella di testo File di **configurazione** nodo per cercare un file di configurazione del nodo (MOF) nel computer locale.

   ![Cercare il file locale](./media/automation-dsc-compile/import-browse.png)

1. Immettere un nome nel campo **Nome configurazione.** Il nome deve corrispondere al nome della configurazione da cui è stata compilata la configurazione del nodo.
1. Fare clic su **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importare una configurazione di nodo con Azure PowerShellImport a node configuration with Azure PowerShell

È possibile utilizzare il cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) per importare una configurazione di nodo nell'account di automazione.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere Introduzione alla configurazione dello stato di automazione di [Azure.](automation-dsc-getting-started.md)
- Per informazioni sulla compilazione di configurazioni DSC in modo da poterle assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione dello stato](automation-dsc-compile.md)di automazione di Azure.To learn about compiling DSC configurations so that you can assign them to target nodes, see Compilazioning configurations in Azure Automation State Configuration .
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere Cmdlet per la configurazione dello stato di [automazione di Azure.](/powershell/module/az.automation)
- Per informazioni sui prezzi, vedere Prezzi per la configurazione dello stato di automazione di Azure.For pricing [information,](https://azure.microsoft.com/pricing/details/automation/)see Azure Automation State Configuration pricing .
- Per un esempio di utilizzo della configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere Distribuzione continua nelle macchine virtuali con Configurazione dello stato di [automazione di Azure e Chocolatey.](automation-dsc-cd-chocolatey.md)
