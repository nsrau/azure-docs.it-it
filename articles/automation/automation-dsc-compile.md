---
title: Compilazione di configurazioni in Azure Automation DSC | Documentazione Microsoft
description: Questo articolo descrive come compilare configurazioni Desired State Configuration (DSC) per l'automazione di Azure.
services: automation
documentationcenter: na
author: georgewallace
manager: carmonm
ms.assetid: 49f20b31-4fa5-4712-b1c7-8f4409f1aecc
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 03/02/2018
ms.author: magoedte; gwallace
ms.openlocfilehash: b267f64a836851e1142475568556eebf74adf2dd
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Compilazione di configurazioni in Azure Automation DSC

È possibile compilare configurazioni dello stato desiderato (DSC, Desired State Configuration) in due modi con Automazione di Azure: nel portale di Azure e con Windows PowerShell. La tabella seguente consente di determinare quando usare ciascun metodo in base alle caratteristiche specifiche:

### <a name="azure-portal"></a>Portale di Azure

* Metodo più semplice con interfaccia utente interattiva
* Modulo per specificare valori di parametri semplici
* Facilità di controllo dello stato dei processi
* Accesso autenticato con l'accesso di Azure

### <a name="windows-powershell"></a>Windows PowerShell

* Chiamata mediante cmdlet di Windows PowerShell nella riga di comando
* Possibilità di inclusione in una soluzione automatizzata con più passaggi
* Possibilità di specificare valori di parametri semplici e complessi
* Possibilità di controllare lo stato dei processi
* Obbligo per il client di supporto dei cmdlet di PowerShell
* Passaggio di ConfigurationData
* Compilazione di configurazioni che usano credenziali

Dopo avere scelto il metodo di compilazione, usare le procedure seguenti per avviare la compilazione.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Compilazione di una configurazione DSC con il portale di Azure

1. Nell'account di Automazione fare clic su **Configurazioni DSC**.
2. Fare clic su una configurazione per aprirne il pannello.
3. Fare clic su **Compila**.
4. Se la configurazione non ha alcun parametro, verrà richiesto di confermare se compilarla. Se la configurazione contiene parametri, verrà aperto il pannello **Compila configurazione** in cui sarà possibile specificare i valori dei parametri. Per altri dettagli sui parametri, vedere la sezione seguente [**Parametri di base**](#basic-parameters).
5. Viene aperto il pannello **Processo di compilazione** in cui è possibile tenere traccia dello stato del processo di compilazione e delle configurazioni dei nodi (documenti di configurazione MOF) che sono state inserite nel server di pull di Azure Automation DSC.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Compilazione di una configurazione DSC con Windows PowerShell

È possibile usare [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) per avviare la compilazione con Windows PowerShell. Il codice di esempio seguente avvia la compilazione di una configurazione DSC denominata **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` restituisce un oggetto processo di compilazione che è possibile usare per tenere traccia dello stato. È quindi possibile usare questo oggetto processo di compilazione con [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) per determinare lo stato del processo di compilazione e con [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) per visualizzarne i flussi (output). Il codice di esempio seguente avvia la compilazione della configurazione **SampleConfig** , attende che venga completata e quindi ne visualizza i flussi.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Parametri di base
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

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

È possibile compilare configurazioni DSC che usano parametri di base nel portale di Automation DSC per Azure o con Azure PowerShell:

### <a name="portal"></a>Portale

Nel portale è possibile immettere i valori dei parametri dopo avere fatto clic su **Compila**.

![testo alternativo](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell richiede i parametri in un elemento [hashtable](http://technet.microsoft.com/library/hh847780.aspx) dove la chiave corrisponde al nome del parametro e il valore è uguale al valore del parametro.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Per informazioni sul passaggio di PSCredentials come parametri, vedere <a href="#credential-assets">**Asset credenziali**</a> più avanti.

## <a name="composite-resources"></a>Risorse composite

Le **risorse composite** consentono di usare le configurazioni DSC come risorse annidate in una configurazione. Questo consente di applicare più configurazioni a un'unica risorsa. Per informazioni sulle **risorse composite**, vedere [Risorse composite: uso di una configurazione DSC come risorsa](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite)

> [!NOTE]
> Per compilare le **risorse composite** nel modo corretto, verificare che le risorse DSC su cui si basa la risorsa composita siano installate nel repository del modulo Account di automazione di Azure altrimenti non vengono importate nel modo corretto.

Per aggiungere una **risorsa composita** DSC, aggiungere il modulo di risorsa a un archivio (*.zip). Accedere al repository dei moduli nell'account di automazione di Azure e fare clic sul pulsante Aggiungi un modulo.

![Aggiungi modulo](./media/automation-dsc-compile/add_module.png)

Passare alla directory in cui si trova l'archivio. Selezionare il file di archivio e fare clic su OK.

![Seleziona modulo](./media/automation-dsc-compile/select_dscresource.png)

Si viene indirizzati alla directory dei moduli da dove è possibile monitorare lo stato della **risorsa composita** mentre viene decompressa e registrata in Automazione di Azure.

![Importare le risorse composite](./media/automation-dsc-compile/register_composite_resource.png)

Fare clic sul modulo registrato per verificare che le **risorse composite** siano disponibili per l'uso in una configurazione.

![Verificare le risorse composite](./media/automation-dsc-compile/validate_composite_resource.png)

A questo punto è possibile chiamare la **risorsa composita** nella configurazione in questo modo:

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** consente di separare la configurazione strutturale dalla configurazione specifica di qualsiasi ambiente mentre si usa PowerShell DSC. Vedere il blog relativo alla [distinzione tra "cosa" e "dove" in PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) per altre informazioni su **ConfigurationData**.

> [!NOTE]
> È possibile usare **ConfigurationData** quando si compila in Azure Automation DSC con Azure PowerShell, ma non nel portale di Azure.

La configurazione DSC di esempio seguente usa **ConfigurationData** con le parole chiave **$ConfigurationData** e **$AllNodes**. Per questo esempio è necessario anche il [modulo **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/):

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

È possibile compilare la configurazione DSC precedente con PowerShell. Il cmdlet di PowerShell seguente aggiunge due configurazioni di nodo al server di pull di Automation DSC per Azure, **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Asset

I riferimenti agli asset sono gli stessi nelle configurazioni di Azure Automation DSC e nei runbook. Per altre informazioni, vedere quanto segue:

* [Certificati](automation-certificates.md)
* [Connessioni](automation-connections.md)
* [Credenziali](automation-credentials.md)
* [Variabili](automation-variables.md)

### <a name="credential-assets"></a>Asset credenziali

Anche se le configurazioni DSC in Automazione di Azure possono fare riferimento ad asset credenziali con **Get-AutomationPSCredential**, gli asset credenziali possono essere passati anche con i parametri, se necessario. Se una configurazione accetta un parametro di tipo **PSCredential** , è necessario passare il nome stringa di un asset di credenziali di Automazione di Azure come valore di tale parametro, invece di un oggetto PSCredential. In background, l'asset credenziali di Automazione di Azure con tale nome viene recuperato e passato alla configurazione.

Per garantire la sicurezza delle credenziali nelle configurazioni dei nodi (documenti di configurazione MOF), è necessario crittografare le credenziali nel file MOF delle configurazioni dei nodi. Automazione di Azure va oltre e crittografa l'intero file MOF. Attualmente è però necessario comunicare a PowerShell DSC che l'output delle credenziali in testo normale durante la generazione dei file MOF delle configurazioni dei nodi è corretto, perché PowerShell DSC non sa che dopo la generazione Automazione di Azure crittograferà l'intero file MOF tramite un processo di compilazione.

Per comunicare a PowerShell DSC che l'output delle credenziali in testo normale nei file MOF delle configurazioni dei nodi generati è corretto, è possibile usare [**ConfigurationData**](#configurationdata). È consigliabile passare `PSDscAllowPlainTextPassword = $true` tramite **ConfigurationData** per il nome di ogni blocco di nodi visualizzato nella configurazione DSC che usa le credenziali.

L'esempio seguente mostra una configurazione DSC che usa un asset credenziali di Automazione.

```powershell
Configuration CredentialSample
{
    $Cred = Get-AutomationPSCredential "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

È possibile compilare la configurazione DSC precedente con PowerShell. Il cmdlet di PowerShell seguente aggiunge due configurazioni del nodo al server di pull di Automation DSC per Azure, **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```

## <a name="importing-node-configurations"></a>Importazione delle configurazioni di nodo

È anche possibile importare configurazioni di nodo (MOF) compilate all'esterno di Azure. Uno dei vantaggi di questa operazione consiste nel fatto che le configurazioni di nodo possono essere firmate.
Una configurazione del nodo firmata viene verificata in locale in un nodo gestito dall'agente DSC, garantendo che la configurazione applicata al nodo provenga da una fonte autorizzata.

> [!NOTE]
> È possibile importare configurazioni firmate nell'account di Automazione di Azure, che tuttavia attualmente non supporta la compilazione di configurazioni firmate.

> [!NOTE]
> Il file di configurazione del nodo deve essere superiore a 1 MB per consentire l'importazione in Automazione di Azure.

Altre informazioni su come firmare le configurazioni del nodo firmate sono reperibili all'indirizzo: https://msdn.microsoft.com/en-us/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importazione di una configurazione del nodo nel portale di Azure

1. Nell'account di automazione fare clic su **Configurazioni del nodo DSC** in **Gestione della configurazione**.

    ![Configurazioni del nodo DSC](./media/automation-dsc-compile/node-config.png)
2. Nel pannello **Configurazioni del nodo DSC** fare clic su **Add a NodeConfiguration** (Aggiungi una configurazione nodo).
3. Nel pannello **Importa** fare clic sull'icona della cartella accanto alla casella di testo **Node Configuration File** (File di configurazione nodo) per cercare un file di configurazione del nodo (MOF) nel computer locale.

    ![Cercare il file locale](./media/automation-dsc-compile/import-browse.png)
4. Immettere un nome nella casella di testo **Nome configurazione**. Il nome deve corrispondere al nome della configurazione da cui è stata compilata la configurazione del nodo.
5. Fare clic su **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importazione di una configurazione del nodo con PowerShell

È possibile usare il cmdlet [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) per importare una configurazione del nodo nell'account di automazione.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



