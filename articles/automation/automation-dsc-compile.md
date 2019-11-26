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

È possibile compilare configurazioni DSC (Desired state Configuration) in due modi con la configurazione dello stato di automazione di Azure: in Azure e in Windows PowerShell. La tabella seguente consente di determinare quando usare ciascun metodo in base alle caratteristiche specifiche:

- Servizio di compilazione configurazione stato di Azure
  - Metodo per principianti con interfaccia utente interattiva
   - Facilità di controllo dello stato dei processi

- Windows PowerShell
  - Chiamata da Windows PowerShell nella workstation locale o nel servizio di compilazione
  - Eseguire l'integrazione con la pipeline di test di sviluppo
  - Fornire valori di parametri complessi
  - Usare i dati di nodi e non nodi su larga scala
  - Miglioramento significativo delle prestazioni

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Compilazione di una configurazione DSC in Azure state Configuration

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

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>Compilazione di configurazioni in automazione di Azure che contengono risorse composite

Le **risorse composite** consentono di usare le configurazioni DSC come risorse annidate in una configurazione. Questo consente di applicare più configurazioni a un'unica risorsa. Per informazioni sulle [risorse composite](/powershell/scripting/dsc/resources/authoringresourcecomposite), vedere **Risorse composite: uso di una configurazione DSC come risorsa**.

> [!NOTE]
> Per consentire la compilazione corretta delle configurazioni contenenti **risorse composite** , è necessario prima di tutto assicurarsi che tutte le risorse DSC su cui si basa il composito vengano prima importate in automazione di Azure.

L'aggiunta di una **risorsa composita** DSC non è diversa dall'aggiunta di un modulo di PowerShell ad automazione di Azure.
L'istruzione dettagliata per questo processo è documentata nell'articolo gestire i [moduli in automazione di Azure](/azure/automation/shared-resources/modules).

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Gestione di ConfigurationData durante la compilazione della configurazione in automazione di Azure

**ConfigurationData** consente di separare la configurazione strutturale dalla configurazione specifica di qualsiasi ambiente mentre si usa PowerShell DSC. Vedere il blog relativo alla [distinzione tra "cosa" e "dove" in PowerShell DSC](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) per altre informazioni su **ConfigurationData**.

> [!NOTE]
> È possibile usare **configurationData** durante la compilazione nella configurazione dello stato di automazione di Azure usando Azure PowerShell ma non nel portale di Azure.

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

È possibile compilare la configurazione DSC precedente con Windows PowerShell. Lo script seguente aggiunge due configurazioni del nodo al servizio di pull della configurazione dello stato di automazione di Azure: **ConfigurationDataSample. MyVM1** e **ConfigurationDataSample. MyVM3**:

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

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>Uso di asset in automazione di Azure durante la compilazione

I riferimenti agli asset sono gli stessi in Configurazione stato di Automazione di Azure e nei runbook. Per altre informazioni, vedere quanto segue:

- [Certificati](automation-certificates.md)
- [Connessioni](automation-connections.md)
- [Credenziali](automation-credentials.md)
- [Variabili](automation-variables.md)

#### <a name="credential-assets"></a>Asset credenziali

Le configurazioni DSC in Automazione di Azure possono fare riferimento agli asset credenziali di Automazione con il cmdlet `Get-AutomationPSCredential`. Se una configurazione non ha un parametro con un tipo **PSCredential** è possibile usare il cmdlet `Get-AutomationPSCredential` passando il nome stringa di un asset credenziali di Automazione di Azure al cmdlet per recuperare le credenziali. È quindi possibile usare tale oggetto per il parametro che richiede l'oggetto **PSCredential**. In background, l'asset credenziali di Automazione di Azure con tale nome viene recuperato e passato alla configurazione. L'esempio seguente illustra questa azione.

Per garantire la sicurezza delle credenziali nelle configurazioni dei nodi (documenti di configurazione MOF), è necessario crittografare le credenziali nel file MOF delle configurazioni dei nodi. Attualmente è però necessario comunicare a PowerShell DSC che l'output delle credenziali in testo normale durante la generazione dei file MOF delle configurazioni dei nodi è corretto, perché PowerShell DSC non sa che dopo la generazione Automazione di Azure crittograferà l'intero file MOF tramite un processo di compilazione.

È possibile indicare a PowerShell DSC che è corretto che le credenziali vengano restituite in testo normale nella configurazione del nodo generata file MOF usando i dati di configurazione. È consigliabile passare `PSDscAllowPlainTextPassword = $true` tramite **ConfigurationData** per il nome di ogni blocco di nodi visualizzato nella configurazione DSC che usa le credenziali.

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

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Compilazione di configurazioni in Windows PowerShell e pubblicazione in automazione di Azure

È anche possibile importare configurazioni di nodo (MOF) compilate all'esterno di Azure.
Ciò include la compilazione da una workstation per sviluppatori o un servizio come [Azure DevOps](https://dev.azure.com).
Questo approccio offre diversi vantaggi, tra cui le prestazioni e l'affidabilità.
La compilazione in Windows PowerShell fornisce anche la possibilità di firmare il contenuto della configurazione.
Una configurazione del nodo firmata viene verificata in locale in un nodo gestito dall'agente DSC, garantendo che la configurazione applicata al nodo provenga da una fonte autorizzata.

> [!NOTE]
> Il file di configurazione del nodo deve essere superiore a 1 MB per consentire l'importazione in Automazione di Azure.

Per altre informazioni su come firmare le configurazioni del nodo, vedere [Improvements in WMF 5.1 - How to sign configuration and module](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations) (Miglioramenti in WMF 5.1: come firmare la configurazione e il modulo).

### <a name="compiling-a-configuration-in-windows-powershell"></a>Compilazione di una configurazione in Windows PowerShell

Il processo di compilazione delle configurazioni DSC in Windows PowerShell è incluso nella documentazione di PowerShell DSC [scrivere, compilare e applicare una configurazione](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Questa operazione può essere eseguita da una workstation per sviluppatori o all'interno di un servizio di compilazione, ad esempio [Azure DevOps](https://dev.azure.com).

Il file MOF o i file generati compilando la configurazione possono quindi essere importati direttamente nel servizio di configurazione dello stato di Azure.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importazione di una configurazione del nodo nel portale di Azure

1. Dall'account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina **Configurazione stato (DSC)** fare clic sulla scheda **Configurazioni** e quindi su **+ Aggiungi**.
1. Nella pagina **Importa** fare clic sull'icona della cartella accanto alla casella di testo **File di configurazione nodo** per cercare un file di configurazione del nodo (MOF) nel computer locale.

   ![Cercare il file locale](./media/automation-dsc-compile/import-browse.png)

1. Immettere un nome nella casella di testo **Nome configurazione**. Il nome deve corrispondere al nome della configurazione da cui è stata compilata la configurazione del nodo.
1. Fare clic su **OK**.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Importazione di una configurazione del nodo con Azure PowerShell

È possibile usare il cmdlet [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) per importare una configurazione del nodo nell'account di automazione.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione a Configurazione stato di Automazione di Azure](automation-dsc-getting-started.md)
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md)
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per informazioni sui prezzi, vedere [Prezzi di Configurazione stato di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
