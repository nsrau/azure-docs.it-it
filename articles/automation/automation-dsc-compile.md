---
title: Compilare configurazioni DSC in Configurazione stato di Automazione di Azure
description: Questo articolo descrive come compilare configurazioni Desired State Configuration (DSC) per l'automazione di Azure.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: de46f4e2fd53b888981076256fda28a2a14995af
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837043"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Compilare configurazioni DSC in Configurazione stato di Automazione di Azure

È possibile compilare configurazioni DSC (Desired State Configuration) in State Configuration di Automazione di Azure nei modi seguenti:

- Servizio di compilazione di Azure State Configuration
  - Metodo per principianti con interfaccia utente interattiva
   - Facilità di controllo dello stato dei processi

- Windows PowerShell
  - Chiamata da Windows PowerShell nella workstation locale o nel servizio di compilazione
  - Integrazione con la pipeline di testo per lo sviluppo
  - Fornitura di valori di parametri complessi
  - Uso di dati di nodi e non di nodi su larga scala
  - Miglioramento significativo delle prestazioni

È anche possibile usare i modelli di Azure Resource Manager con l'estensione Azure Desired State Configuration (DSC) per eseguire il push di configurazioni nelle macchine virtuali di Azure. L'estensione DSC di Azure usa il framework dell'agente VM di Azure per recapitare, applicare e generare report sulle configurazioni DSC in esecuzione nelle VM di Azure. Per dettagli sulla compilazione con i modelli di Azure Resource Manager, vedere [Estensione Desired State Configuration con modelli di Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details). 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Compilare una configurazione DSC in Azure State Configuration

### <a name="portal"></a>Portale

1. Nell'account di Automazione fare clic su **State Configuration (DSC)** .
1. Fare clic sulla scheda **Configurazioni** e quindi selezionare il nome della configurazione da compilare.
1. Fare clic su **Compila**.
1. Se la configurazione non ha alcun parametro, verrà richiesto di confermare se compilarla. Se la configurazione contiene parametri, verrà aperto il pannello **Compila configurazione** in cui sarà possibile specificare i valori dei parametri.
1. Verrà aperta la pagina Processo di compilazione che consente di tenere traccia dello stato dei processi di compilazione. È anche possibile usare questa pagina per tenere traccia delle configurazioni dei nodi (documenti di configurazione MOF) inserite nel server di pull di State Configuration di Automazione di Azure.

### <a name="azure-powershell"></a>Azure PowerShell

È possibile usare [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) per avviare la compilazione con Windows PowerShell. Il codice di esempio seguente avvia la compilazione di una configurazione DSC denominata **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` restituisce un oggetto processo di compilazione che è possibile usare per tenere traccia dello stato del processo. È quindi possibile usare questo oggetto processo di compilazione con [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) per determinare lo stato del processo di compilazione e [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) per visualizzare i rispettivi flussi (output). L'esempio seguente avvia la compilazione della configurazione SampleConfig, attende che venga completata e quindi ne visualizza i flussi.

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

L'esempio seguente usa i parametri `FeatureName` e `IsPresent` per determinare i valori delle proprietà nella configurazione del nodo **ParametersExample.sample**, generato durante la compilazione.

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

È possibile compilare configurazioni DSC che usano parametri di base nel portale di State Configuration di Automazione di Azure o con Azure PowerShell.

#### <a name="portal"></a>Portale

Nel portale è possibile immettere i valori dei parametri dopo avere fatto clic su **Compila**.

![Parametri per la compilazione della configurazione](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell richiede i parametri in un elemento [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) dove la chiave corrisponde al nome del parametro e il valore è uguale al valore del parametro.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Per informazioni sul passaggio di oggetti `PSCredential` come parametri, vedere [Asset di credenziali](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Compilare configurazioni contenenti risorse composite in Automazione di Azure

La funzionalità **Risorse composite** consente di usare le configurazioni DSC come risorse annidate in una configurazione. Questa funzionalità consente l'applicazione di più configurazioni a una singola risorsa. Per informazioni sulle risorse composite, vedere [Risorse composite: uso di una configurazione DSC come risorsa](/powershell/scripting/dsc/resources/authoringresourcecomposite) per ottenere altre informazioni sulle risorse composite.

> [!NOTE]
> Per consentire la compilazione corretta delle configurazioni che contengono risorse composite, è prima di tutto necessario importare in Automazione di Azure eventuali risorse DSC su cui si basano le risorse composite. L'aggiunta di una risorsa composita di DSC non è diversa dall'aggiunta di un modulo di PowerShell in Automazione di Azure. Questo processo è documentato in [Gestire i moduli in Automazione di Azure](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Gestire ConfigurationData durante la compilazione di configurazioni in Automazione di Azure

`ConfigurationData` è un parametro di DSC predefinito che consente di separare la configurazione strutturale dalla configurazione specifica di qualsiasi ambiente mentre si usa PowerShell DSC. Per altre informazioni, vedere [Separazione di "cosa" da "dove" in PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Durante la compilazione di State Configuration di Automazione di Azure è possibile usare `ConfigurationData` in Azure PowerShell ma non nel portale di Azure.

L'esempio seguente di configurazione DSC usa `ConfigurationData` tramite le parole chiave `$ConfigurationData` e `$AllNodes`. Per questo esempio è necessario anche il [modulo xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/).

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

È possibile compilare la configurazione DSC precedente con Windows PowerShell. Lo script seguente aggiunge due configurazioni del nodo al server di pull di State Configuration di Automazione di Azure, **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Usare asset in Automazione di Azure durante la compilazione

I riferimenti agli asset sono gli stessi in State Configuration di Automazione di Azure e nei runbook. Per altre informazioni, vedere gli argomenti seguenti:

- [Certificati](automation-certificates.md)
- [Connessioni](automation-connections.md)
- [Credenziali](automation-credentials.md)
- [Variabili](automation-variables.md)

#### <a name="credential-assets"></a>Asset di credenziali

Le configurazioni DSC in Automazione di Azure possono fare riferimento agli asset credenziali di Automazione con il cmdlet `Get-AutomationPSCredential`. Se una configurazione ha un parametro che specifica un oggetto `PSCredential`, usare `Get-AutomationPSCredential` passando il nome di stringa di un asset di credenziali di Automazione di Azure al cmdlet per recuperare le credenziali. Usare quindi tale oggetto per il parametro che richiede l'oggetto `PSCredential`. In background, l'asset credenziali di Automazione di Azure con tale nome viene recuperato e passato alla configurazione. L'esempio seguente illustra il funzionamento di questo scenario.

Per garantire la sicurezza delle credenziali nelle configurazioni dei nodi (documenti di configurazione MOF), è necessario crittografare le credenziali nel file MOF delle configurazioni dei nodi. È attualmente necessario fornire a PowerShell DSC l'autorizzazione per restituire come output le credenziali in testo normale durante la generazione del file MOF della configurazione del nodo. PowerShell DSC non è consapevole del fatto che Automazione di Azure crittografa l'intero file MOF dopo la rispettiva generazione tramite un processo di compilazione.

Per comunicare a PowerShell DSC che l'output delle credenziali in testo normale nei file MOF delle configurazioni dei nodi generati è corretto, è possibile usare ConfigurationData. È consigliabile passare `PSDscAllowPlainTextPassword = $true` tramite `ConfigurationData` per il nome di ogni blocco di nodi visualizzato nella configurazione DSC che usa le credenziali.

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

È possibile compilare la configurazione DSC precedente con PowerShell. Il codice di PowerShell seguente aggiunge due configurazioni del nodo al server di pull di State Configuration di Automazione di Azure, **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

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
> Al termine della compilazione, è possibile che venga visualizzato il messaggio di errore `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.`. È possibile ignorare questo messaggio.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Compilare la configurazione DSC in Windows PowerShell

Il processo di compilazione delle configurazioni DSC in Windows PowerShell è incluso nella documentazione di PowerShell DSC in [Scrivere, compilare e applicare una configurazione](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
È possibile eseguire questo processo da una workstation per sviluppatori o in un servizio di compilazione, ad esempio [Azure DevOps](https://dev.azure.com). È quindi possibile importare i file MOF prodotti compilando la configurazione nel servizio Azure State Configuration.

La compilazione in Windows PowerShell offre anche la possibilità di firmare il contenuto della configurazione. L'agente DSC verifica la configurazione di un nodo firmata in locale in un nodo gestito. La verifica consente di assicurare che la configurazione applicata al nodo provenga da un'origine autorizzata.

È anche possibile importare configurazioni di nodo (file MOF) compilate all'esterno di Azure. L'importazione include la compilazione da una workstation per sviluppatori o in un servizio quale [Azure DevOps](https://dev.azure.com). Questo approccio offre molti vantaggi, tra cui prestazioni e affidabilità.

> [!NOTE]
> Il file di configurazione del nodo deve essere superiore a 1 MB per consentire l'importazione in Automazione di Azure.

Per altre informazioni su come firmare le configurazioni del nodo, vedere [Miglioramenti in WMF 5.1: come firmare la configurazione e il modulo](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importare una configurazione del nodo nel portale di Azure

1. Nell'account di Automazione selezionare **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Configurazioni**, quindi fare clic su **Aggiungi**.
1. Nella pagina Importa fare clic sull'icona della cartella accanto al campo **File di configurazione nodo** per cercare un file MOD di configurazione del nodo nel computer locale.

   ![Cercare il file locale](./media/automation-dsc-compile/import-browse.png)

1. Immettere un nome nel campo **Nome configurazione**. Il nome deve corrispondere al nome della configurazione da cui è stata compilata la configurazione del nodo.
1. Fare clic su **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importare una configurazione del nodo con Azure PowerShell

È possibile usare il cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) per importare una configurazione del nodo nell'account di Automazione.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione a State Configuration di Automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilare configurazioni DSC in State Configuration di Automazione di Azure](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per informazioni sui prezzi, vedere [Prezzi di State Configuration di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per un esempio relativo all'uso di State Configuration in una pipeline di distribuzione continua, vedere [Configurare la distribuzione continua con Chocolatey](automation-dsc-cd-chocolatey.md).
