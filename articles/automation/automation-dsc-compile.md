---
title: Compilazione di configurazioni in Configurazione stato di Automazione di Azure
description: Questo articolo descrive come compilare configurazioni Desired State Configuration (DSC) per l'automazione di Azure.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: a4a46837e366caf2860aa0d6597ec7aebc94905a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373518"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Compilazione di configurazioni DSC in Configurazione stato di Automazione di Azure

È possibile compilare configurazioni DSC (Desired state Configuration) nella configurazione dello stato di automazione di Azure nei modi seguenti:

- Servizio di compilazione configurazione stato di Azure
  - Metodo per principianti con interfaccia utente interattiva
   - Facilità di controllo dello stato dei processi

- Windows PowerShell
  - Chiamata da Windows PowerShell nella workstation locale o nel servizio di compilazione
  - Eseguire l'integrazione con la pipeline di test di sviluppo
  - Fornire valori di parametri complessi
  - Usare i dati di nodi e non nodi su larga scala
  - Miglioramento significativo delle prestazioni

Per informazioni dettagliate sulla compilazione, vedere estensione DSC ( [desired state Configuration) con modelli Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details).

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compilazione di una configurazione DSC in Azure state Configuration

### <a name="portal"></a>Portale

1. Nell'account di Automazione fare clic su **Configurazione stato (DSC)** .
1. Fare clic sulla scheda **Configurazioni** e quindi selezionare il nome della configurazione da compilare.
1. Fare clic su **Compila**.
1. Se la configurazione non ha parametri, viene richiesto di confermare se si desidera compilarla. Se la configurazione contiene parametri, verrà aperto il pannello **Compila configurazione** in modo da poter specificare i valori dei parametri.
1. Viene aperta la pagina processo di compilazione in cui è possibile tenere traccia dello stato del processo di compilazione. È anche possibile usare questa pagina per tenere traccia delle configurazioni dei nodi (documenti di configurazione MOF) inserite nel server di pull della configurazione dello stato di automazione di Azure.

### <a name="azure-powershell"></a>Azure PowerShell

È possibile usare [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) per avviare la compilazione con Windows PowerShell. Il codice di esempio seguente avvia la compilazione di una configurazione DSC denominata SampleConfig.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

**Start-AzAutomationDscCompilationJob** restituisce un oggetto processo di compilazione che è possibile usare per tenere traccia dello stato del processo. È quindi possibile usare questo oggetto processo di compilazione con [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) per determinare lo stato del processo di compilazione e [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) per visualizzarne i flussi (output). L'esempio seguente avvia la compilazione della configurazione SampleConfig, ne attende il completamento e quindi ne Visualizza i flussi.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Dichiarare i parametri di base

La dichiarazione dei parametri nelle configurazioni DSC, inclusi i tipi e le proprietà dei parametri, funziona esattamente come nei runbook di Automazione di Azure. Per altre informazioni sui parametri dei runbook, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md) .

Nell'esempio seguente vengono utilizzati due parametri denominati *FeatureName* e *Unpresent*, per determinare i valori delle proprietà nella configurazione del nodo ParametersExample. Sample, generati durante la compilazione.

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

È possibile compilare configurazioni DSC che usano parametri di base nel portale di configurazione dello stato di automazione di Azure o con Azure PowerShell.

#### <a name="portal"></a>Portale

Nel portale è possibile immettere i valori dei parametri dopo avere fatto clic su **Compila**.

![Parametri per la compilazione della configurazione](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell richiede parametri in una [tabella hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables), dove la chiave corrisponde al nome del parametro e il valore è uguale al valore del parametro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Per informazioni sul passaggio di PSCredentials come parametri, vedere [Asset credenziali](#credential-assets) più avanti.

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Compilare configurazioni contenenti risorse composite in automazione di Azure

La funzionalità di **risorse composite** consente di usare le configurazioni DSC come risorse nidificate all'interno di una configurazione. Questa funzionalità consente l'applicazione di più configurazioni a una singola risorsa. Per altre informazioni sulle risorse composite, vedere [risorse Composite: uso di una configurazione DSC come risorsa](/powershell/scripting/dsc/resources/authoringresourcecomposite) .

> [!NOTE]
> In modo che le configurazioni contenenti risorse composite vengano compilate correttamente, è necessario prima importare in automazione di Azure tutte le risorse DSC su cui si basano i compositi. L'aggiunta di una risorsa composita DSC non è diversa dall'aggiunta di un modulo di PowerShell ad automazione di Azure. Questo processo è documentato in [gestire i moduli in automazione di Azure](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Gestire ConfigurationData durante la compilazione di configurazioni in automazione di Azure

**ConfigurationData** consente di separare la configurazione strutturale dalla configurazione specifica di qualsiasi ambiente mentre si usa PowerShell DSC. Per ulteriori informazioni, vedere la sezione relativa alla [separazione di "What" da "Where" in PowerShell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx).

> [!NOTE]
> Quando si esegue la compilazione nella configurazione dello stato di automazione di Azure, è possibile usare **configurationData** in Azure PowerShell ma non nel portale di Azure.

La configurazione DSC di esempio seguente usa **configurationData** con le parole chiave $ConfigurationData e $AllNodes. Per questo esempio è necessario anche il [modulo xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) .

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

È possibile compilare la configurazione DSC precedente con Windows PowerShell. Lo script seguente aggiunge due configurazioni del nodo al servizio di pull della configurazione dello stato di automazione di Azure: ConfigurationDataSample. MyVM1 e ConfigurationDataSample. MyVM3.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Usare gli asset in automazione di Azure durante la compilazione

I riferimenti agli asset sono gli stessi nella configurazione dello stato di automazione di Azure e in manuali operativi. Per altre informazioni, vedere gli argomenti seguenti:

- [Certificati](automation-certificates.md)
- [Connessioni](automation-connections.md)
- [Credenziali](automation-credentials.md)
- [Variabili](automation-variables.md)

#### <a name="credential-assets"></a>Asset credenziali

Le configurazioni DSC in automazione di Azure possono fare riferimento ad asset delle credenziali di automazione usando il cmdlet **Get-AutomationPSCredential** . Se una configurazione include un parametro che specifica un oggetto **PSCredential** , usare **Get-AutomationPSCredential** passando il nome di stringa di un asset delle credenziali di automazione di Azure al cmdlet per recuperare le credenziali. Usare quindi l'oggetto per il parametro che richiede l'oggetto **PSCredential** . In background, l'asset credenziali di Automazione di Azure con tale nome viene recuperato e passato alla configurazione. Nell'esempio seguente viene illustrato questo scenario in azione.

Per garantire la sicurezza delle credenziali nelle configurazioni dei nodi (documenti di configurazione MOF), è necessario crittografare le credenziali nel file MOF delle configurazioni dei nodi. Attualmente è necessario concedere l'autorizzazione PowerShell DSC per l'output delle credenziali in testo normale durante la generazione MOF della configurazione del nodo. PowerShell DSC non è consapevole del fatto che automazione di Azure crittografa l'intero file MOF dopo la generazione tramite un processo di compilazione.

È possibile indicare a PowerShell DSC che è corretto che le credenziali vengano restituite in testo normale nella configurazione del nodo generata file MOF usando i dati di configurazione. È necessario passare `PSDscAllowPlainTextPassword = $true` tramite **configurationData** per ogni nome di blocco del nodo visualizzato nella configurazione DSC e usa le credenziali.

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

È possibile compilare la configurazione DSC precedente con PowerShell. Il codice di PowerShell seguente aggiunge due configurazioni del nodo al server di pull della configurazione dello stato di automazione di Azure: CredentialSample. MyVM1 e CredentialSample. MyVM2.

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
> Al termine della compilazione, è possibile che venga visualizzato il messaggio di errore "il modulo ' Microsoft. PowerShell. Management ' non è stato importato perché lo snap-in ' Microsoft. PowerShell. Management ' è già stato importato". Questo messaggio può essere ignorato in modo sicuro.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Compilazione della configurazione DSC in Windows PowerShell

È anche possibile importare configurazioni di nodo (MOF) compilate all'esterno di Azure. L'importazione include la compilazione da una workstation per sviluppatori o un servizio come [Azure DevOps](https://dev.azure.com). Questo approccio offre diversi vantaggi, tra cui le prestazioni e l'affidabilità.

La compilazione in Windows PowerShell fornisce anche la possibilità di firmare il contenuto della configurazione. L'agente DSC verifica una configurazione del nodo firmato localmente in un nodo gestito. La verifica garantisce che la configurazione applicata al nodo provenga da un'origine autorizzata.

> [!NOTE]
> Il file di configurazione del nodo deve essere superiore a 1 MB per consentire l'importazione in Automazione di Azure.

Per altre informazioni sulla firma delle configurazioni dei nodi, vedere [miglioramenti in WMF 5,1-come firmare la configurazione e il modulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compile-the-dsc-configuration"></a>Compilare la configurazione DSC

Il processo di compilazione delle configurazioni DSC in Windows PowerShell è incluso nella documentazione di PowerShell DSC [scrivere, compilare e applicare una configurazione](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
È possibile eseguire questo processo da una workstation per sviluppatori o all'interno di un servizio di compilazione, ad esempio [Azure DevOps](https://dev.azure.com). È quindi possibile importare i file MOF prodotti dalla compilazione della configurazione nel servizio di configurazione dello stato di Azure.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importare una configurazione del nodo nel portale di Azure

1. Dall'account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina configurazione stato (DSC) fare clic sulla scheda **configurazioni** , quindi fare clic su **+ Aggiungi**.
1. Nella pagina Importa fare clic sull'icona della cartella accanto alla casella di testo **file di configurazione nodo** per cercare un file di configurazione del nodo (MOF) nel computer locale.

   ![Cercare il file locale](./media/automation-dsc-compile/import-browse.png)

1. Immettere un nome nel campo **Nome configurazione** . Il nome deve corrispondere al nome della configurazione da cui è stata compilata la configurazione del nodo.
1. Fare clic su **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importare una configurazione nodo con Azure PowerShell

È possibile usare il cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) per importare una configurazione del nodo nell'account di automazione.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Guida introduttiva alla configurazione dello stato di automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni sulla compilazione delle configurazioni DSC per poterle assegnare ai nodi di destinazione, vedere [compilazione di configurazioni in Azure Automation state Configuration](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [cmdlet di configurazione dello stato di automazione di Azure](/powershell/module/az.automation).
- Per informazioni sui prezzi, vedere [prezzi di configurazione dello stato di automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per un esempio di come usare la configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [distribuzione continua in macchine virtuali con la configurazione dello stato di automazione di Azure e cioccolato](automation-dsc-cd-chocolatey.md).
