---
title: Onboarding di computer per la gestione con Automation DSC per Azure | Documentazione Microsoft
description: Come configurare i computer per la gestione con Automation DSC per Azure
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
ms.assetid: da13e1f5-2a1c-443b-8e3b-9f0d6f9e4810
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 12/13/2016
ms.author: eslesar
translationtype: Human Translation
ms.sourcegitcommit: e2257730f0c62dbc0313ce7953fc5f953dae8ac3
ms.openlocfilehash: f81536322ad1bb16e4af326e0b053da47690619c
ms.lasthandoff: 02/15/2017


---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Caricamento di computer per la gestione con Automation DSC per Azure

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Perché gestire computer con Automation DSC per Azure

Come [PowerShell DSC (Desired State Configuration)](https://technet.microsoft.com/library/dn249912.aspx), Automation DSC (Desired State Configuration) per Azure è un servizio semplice ma potente per la gestione della configurazione dei nodi DSC (macchine virtuali e computer fisici) in qualsiasi data center nel cloud o locale. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato. Il livello di gestione di Automation DSC per Azure è per DSC ciò che il livello di gestione di Automazione di Azure rappresenta per gli script di PowerShell. In altre parole, Automazione di Azure consente di gestire gli script di PowerShell e le configurazioni DSC. Per altre informazioni sui vantaggi dell'uso di Automation DSC per Azure, vedere [Panoramica della piattaforma DSC di Automazione di Azure](automation-dsc-overview.md).

È possibile usare Automation DSC per Azure in molti tipi di computer.

* Macchine virtuali di Azure (classica)
* Macchine virtuali di Azure
* Macchine virtuali di Amazon Web Services (AWS)
* Computer fisici/macchine virtuali Windows locali o in un cloud diverso da Azure/AWS
* Computer fisici/macchine virtuali Linux locali, in Azure o in un cloud diverso da Azure

Inoltre, se non si è pronti a gestire la configurazione delle macchina virtuali dal cloud, Automation DSC per Azure può anche essere usato come endpoint solo per i report. In questo modo è possibile impostare (push) la configurazione desiderata tramite DSC locale e visualizzare i dettagli dei report sulla conformità dei nodi con lo stato scelto in Automazione di Azure.

Le sezioni seguenti illustrano come caricare ogni tipo di computer in Automation DSC per Azure.

## <a name="azure-virtual-machines-classic"></a>Macchine virtuali di Azure (classica)

Con Automation DSC per Azure è possibile caricare facilmente macchine virtuali di Azure (classica) per la gestione della configurazione tramite il portale di Azure o PowerShell. Dietro le quinte, e senza che l'amministratore debba connettersi in remoto alla VM, l'estensione DSC (Desired State Configuration) per le VM di Azure regista la VM con Automation DSC per Azure. Poiché l'estensione DSC per le VM di Azure viene eseguita in modalità asincrona, nella sezione seguente [**Risoluzione dei problemi di caricamento di macchine virtuali di Azure**](#troubleshooting-azure-virtual-machine-onboarding) è disponibile la procedura per tenere traccia dell'avanzamento o risolverne i problemi.

### <a name="azure-portal"></a>Portale di Azure

Nel [portale di Azure](http://portal.azure.com/) fare clic su **Esplora** -> **Macchine virtuali (classico)**. Selezionare la macchina virtuale Windows da caricare. Nel pannello del dashboard della macchina virtuale fare clic su **Tutte le impostazioni** -> **Estensioni** -> **Aggiungi** -> **Automation DSC per Azure** -> **Crea**. Immettere i [valori di Gestione configurazione locale per PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) necessari per il proprio caso d'uso, la chiave e l'URL di registrazione dell'account di automazione e facoltativamente una configurazione del nodo da assegnare alla VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Per trovare l'URL e la chiave di registrazione dell'account di automazione in cui caricare la macchina virtuale, vedere la sezione [**Registrazione sicura**](#secure-registration) è disponibile la procedura per tenere traccia dell'avanzamento o risolverne i problemi.

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Add-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
    AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

## <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

Automation DSC per Azure consente di caricare facilmente macchine virtuali di Azure per la gestione della configurazione tramite il portale di Azure, i modelli di Gestione risorse di Azure o PowerShell. Dietro le quinte, e senza che l'amministratore debba connettersi in remoto alla VM, l'estensione DSC (Desired State Configuration) per le VM di Azure regista la VM con Automation DSC per Azure. Poiché l'estensione DSC per le VM di Azure viene eseguita in modalità asincrona, nella sezione seguente [**Risoluzione dei problemi di caricamento di macchine virtuali di Azure**](#troubleshooting-azure-virtual-machine-onboarding) è disponibile la procedura per tenere traccia dell'avanzamento o risolverne i problemi.

### <a name="azure-portal"></a>Portale di Azure

Nel [portale di Azure](https://portal.azure.com/)passare all'account di Automazione di Azure in cui caricare le macchine virtuali. Nel dashboard dell'account di automazione fare clic su **Nodi DSC** -> **Aggiungi macchina virtuale di Azure**.

In **Seleziona macchine virtuali da caricare**selezionare una o più macchine virtuali di Azure da caricare.

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)

In **Configura dati di registrazione**immettere i [valori di Gestione configurazione locale per PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) necessari per il proprio caso d'uso e facoltativamente una configurazione del nodo da assegnare alla VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

### <a name="azure-resource-manager-templates"></a>Modelli di Gestione risorse di Azure

Le macchine virtuali di Azure possono essere distribuite e caricate in Automation DSC di Azure usando i modelli di Gestione risorse di Azure. Per un modello di esempio che carica una VM esistente in Automation DSC per Azure, vedere [Configurare una VM tramite l'estensione DSC e Automation DSC per Azure](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) . Per trovare la chiave e l'URL di registrazione accettati come input in questo modello, vedere la sezione [**Registrazione sicura**](#secure-registration) è disponibile la procedura per tenere traccia dell'avanzamento o risolverne i problemi.

### <a name="powershell"></a>PowerShell

Il cmdlet [Register-AzureRmAutomationDscNode](https://msdn.microsoft.com/library/mt603833.aspx) può essere usato per caricare macchine virtuali nel portale di Azure tramite PowerShell.

## <a name="amazon-web-services-aws-virtual-machines"></a>Macchine virtuali di Amazon Web Services (AWS)

È possibile caricare con facilità le macchine virtuali Amazon Web Services per la gestione della configurazione da Automation DSC per Azure mediante il toolkit DSC AWS. Altre informazioni sul toolkit sono disponibili [qui](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Computer fisici/macchine virtuali Windows locali o in un cloud diverso da Azure/AWS

I computer Windows locali e le macchine virtuali Windows in cloud non di Azure, ad esempio Amazon Web Services, possono essere caricati in Automation DSC per Azure con pochi e semplici passaggi, a condizione che abbiano l'accesso in uscita a Internet:

1. Assicurarsi che nei computer da caricare in Automation DSC per Azure sia installata la versione più recente di [WMF 5](http://aka.ms/wmf5latest) .
2. Seguire le istruzioni riportate nella sezione [**Generazione di metaconfigurazioni DSC**](#generating-dsc-metaconfigurations) di seguito per generare una cartella contenente le metaconfigurazioni DSC necessarie.
3. Applicare in remoto la metaconfigurazione di PowerShell DSC ai computer da caricare. **Nel computer dal quale viene eseguito questo comando deve essere installata la versione più recente di [WMF 5](http://aka.ms/wmf5latest) **:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in remoto, copiare la cartella delle metaconfigurazioni dal passaggio 2 in ogni computer da caricare. Chiamare quindi **Set-DscLocalConfigurationManager** localmente in ogni computer da caricare.
5. Tramite il portale di Azure o i cmdlet verificare che ora i computer da caricare siano visualizzati come nodi DSC registrati nell'account di Automazione di Azure.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Computer fisici/macchine virtuali Linux locali, in Azure o in un cloud diverso da Azure

I computer Linux locali, i computer Linux in Azure, e le macchine virtuali Linux in cloud non di Azure possono essere caricati in Automation DSC per Azure con pochi e semplici passaggi, a condizione che abbiano l'accesso in uscita a Internet:

1. Assicurarsi che nei computer da caricare in Automation DSC per Azure sia installata la versione più recente dell' [agente DSC per Linux](http://www.microsoft.com/download/details.aspx?id=49150) .
2. Se i [valori predefiniti di Gestione configurazione locale per PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) corrispondono al caso d'uso corrente e si vogliono caricare i computer in modo che **entrambi** eseguano il pull e inviino informazioni ad Automation DSC per Azure:

   + In tutti i computer Linux da caricare in Automation DSC per Azure usare Register.py per caricarli con i valori predefiniti di Gestione configurazione locale per PowerShell DSC:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Per trovare la chiave e l'URL di registrazione per l'account di automazione, vedere la sezione [**Registrazione sicura**](#secure-registration) è disponibile la procedura per tenere traccia dell'avanzamento o risolverne i problemi.

     Se i valori predefiniti di Gestione configurazione locale per PowerShell DSC **no****n** corrispondono al caso d'uso corrente o si vogliono caricare i computer in modo che inviino informazioni solo ad Automation DSC per Azure, ma non eseguano il pull della configurazione o dei moduli di PowerShell, seguire i passaggi da 3 a 6. In caso contrario, andare direttamente al passaggio 6.

3. Seguire le istruzioni riportate di seguito nella sezione [**Generazione di metaconfigurazioni DSC**](#generating-dsc-metaconfigurations) per generare una cartella contenente le metaconfigurazioni DSC necessarie.
4. Applicare in remoto la metaconfigurazione di PowerShell DSC ai computer da caricare:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Nel computer dal quale viene eseguito questo comando deve essere installata la versione più recente di [WMF 5](http://aka.ms/wmf5latest) .

1. Se non è possibile caricare le metaconfigurazioni di PowerShell DSC in remoto, per ogni computer Linux da caricare copiare la metaconfigurazione corrispondente a quel computer dalla cartella nel passaggio 5 nel computer Linux. Chiamare quindi `SetDscLocalConfigurationManager.py` localmente in ogni computer Linux da caricare in Automation DSC per Azure:

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Tramite il portale di Azure o i cmdlet verificare che ora i computer da caricare siano visualizzati come nodi DSC registrati nell'account di Automazione di Azure.

## <a name="generating-dsc-metaconfigurations"></a>Generazione di metaconfigurazioni DSC

Per caricare in modo generico qualsiasi computer in Automation DSC per Azure, può essere generata una [metaconfigurazione DSC](https://msdn.microsoft.com/en-us/powershell/dsc/metaconfig) che, quando applicata, indichi all'agente DSC nel computer di eseguire il pull e/o inviare informazioni ad Automation DSC per Azure. Le metaconfigurazioni DSC per Automation DSC per Azure possono essere generate tramite una configurazione DSC di PowerShell o tramite i cmdlet PowerShell di Automazione di Azure.

> [!NOTE]
> Le metaconfigurazioni DSC contengono i segreti necessari per caricare un computer in un account di automazione per la relativa gestione. Assicurarsi di proteggere adeguatamente le metaconfigurazioni DSC create oppure eliminarle dopo l'uso.

### <a name="using-a-dsc-configuration"></a>Uso di una configurazione DSC

1. Aprire PowerShell ISE come amministratore in un computer nell'ambiente locale. Nel computer deve essere installata la versione più recente di [WMF 5](http://aka.ms/wmf5latest) .
2. Copiare localmente lo script seguente. Questo script contiene una configurazione DSC di PowerShell per la creazione delle metaconfigurazioni e un comando per avviare la creazione delle metaconfigurazioni.

    ```powershell
    # The DSC configuration that will generate metaconfigurations
    [DscLocalConfigurationManager()]
    Configuration DscMetaConfigs
    {

        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
    # TODO: edit the below as needed for your use case
    $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
    }

    # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    DscMetaConfigs @Params
    ```

3. Specificare il codice di registrazione e l'URL per l'account di automazione, nonché i nomi dei computer da caricare. Tutti gli altri parametri sono facoltativi. Per trovare la chiave e l'URL di registrazione per l'account di automazione, vedere la sezione [**Registrazione sicura**](#secure-registration) è disponibile la procedura per tenere traccia dell'avanzamento o risolverne i problemi.
4. Se si vuole che i computer inviino informazioni sullo stato DSC ad Automation DSC per Azure ma non eseguano il pull della configurazione o dei moduli di PowerShell, impostare il parametro **ReportOnly** su true.
5. Eseguire lo script. A questo punto è disponibile una cartella denominata **DscMetaConfigs** nella directory di lavoro, contenente le metaconfigurazioni di PowerShell DSC per i computer da caricare (come amministratore):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Uso dei cmdlet di Automazione di Azure

Se i valori predefiniti di Gestione configurazione locale per PowerShell DSC corrispondono al caso d'uso corrente e si vuole caricare i computer in modo che entrambi eseguano il pull e inviino informazioni ad Automation DSC per Azure, i cmdlet di Automazione di Azure offrono un metodo semplificato per generare le metaconfigurazioni DSC necessarie:

1. Aprire la console di PowerShell o PowerShell ISE come amministratore in un computer nell'ambiente locale.
2. Connettersi ad Azure Resource Manager con **Add-AzureRmAccount**
3. Dall'account di automazione in cui si caricheranno i nodi, scaricare le metaconfigurazioni di PowerShell DSC per i computer da caricare:

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. A questo punto è disponibile una cartella denominata ***DscMetaConfigs***, contenente le metaconfigurazioni di PowerShell DSC per i computer da caricare (come amministratore):
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Registrazione sicura

I computer possono essere caricati in modo sicuro in un account di Automazione di Azure tramite il protocollo di registrazione DSC WMF 5, che consente a un nodo DSC di autenticarsi con un server di pull o di report di PowerShell DSC V2, incluso Automation DSC per Azure. Il nodo si registra al server con un **URL di registrazione**, autenticandosi con una **Chiave di registrazione**. Durante la registrazione, il nodo DSC e il server di pull/report DSC negoziano un certificato univoco per questo nodo da usare per l'autenticazione con il server successivamente alla registrazione. Questo processo impedisce ai nodi caricati di rappresentarsi reciprocamente, ad esempio nel caso di un nodo compromesso e che presenta un comportamento dannoso. Dopo la registrazione, la chiave di registrazione non viene usata di nuovo per l'autenticazione e viene eliminata dal nodo.

È possibile ottenere le informazioni richieste per il protocollo di registrazione DSC nel pannello **Gestisci chiavi** del portale di anteprima di Azure. Aprire questo pannello facendo clic sull'icona della chiave nel pannello **Informazioni di base** dell'account di automazione.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* L'URL di registrazione si trova nel campo URL del pannello Gestisci chiavi.
* La chiave di registrazione si trova nel campo Chiave di accesso primaria o Chiave di accesso secondaria del pannello Gestisci chiavi. È possibile usare una delle due chiavi.

Per maggiore sicurezza, le chiavi di accesso primaria e secondaria di un account di automazione possono essere rigenerate in qualsiasi momento, tramite il pannello **Gestisci chiavi** , per evitare l'esecuzione di registrazioni di nodi future con chiavi precedenti.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Risoluzione dei problemi di caricamento di macchine virtuali di Azure

Automation DSC di Azure consente di caricare macchine virtuali Windows di Azure per la gestione della configurazione. Dietro le quinte, viene usata l'estensione DSC (Desired State Configuration) per le VM di Azure per registrare la VM con Automation DSC per Azure. Poiché l'estensione DSC (Desired State Configuration) per le VM di Azure viene eseguita in modalità asincrona, può essere importante tenere traccia dell'avanzamento e risolvere i problemi di esecuzione.

> [!NOTE]
> Qualsiasi metodo di caricamento di una VM Windows di Azure in Automation DSC per Azure che usa l'estensione DSC (Desired State Configuration) per le VM di Azure può richiedere fino a un'ora prima che il modo appaia come registrato in Automazione di Azure. Questo comportamento è causato dall'installazione di Windows Management Framework 5.0 nella macchina virtuale da parte dell'estensione DSC per le VM di Azure, necessario per caricare la VM in Automation DSC per Azure.

Per risolvere i problemi o visualizzare lo stato dell'estensione DSC per le macchine virtuali di Azure, nel portale di Azure passare alla macchina virtuali da caricare, quindi fare clic su -> **Tutte le impostazioni** -> **Estensioni** -> **DSC**. Per altri dettagli, è possibile fare clic su **Visualizza stato dettagliato**.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## <a name="certificate-expiration-and-reregistration"></a>Scadenza del certificato e nuova registrazione

Dopo la registrazione di un computer come nodo DSC in Automation DSC per Azure, esistono diversi motivi, che rendono necessario ripetere la registrazione del nodo in futuro:

* Dopo la registrazione, ogni nodo negozia automaticamente un certificato univoco per l'autenticazione che scade dopo un anno. Attualmente il protocollo di registrazione di PowerShell DSC non può rinnovare automaticamente i certificati quando si avvicina la scadenza, quindi è necessario registrare di nuovo i nodi dopo un anno. Prima di registrare di nuovo, verificare che ogni nodo esegua Windows Management Framework 5.0 RTM. Se il certificato di autenticazione di un nodo scade e se il nodo non è registrato, il nodo non sarà in grado di comunicare con Automazione di Azure e sarà indicato che "Non risponde". Una registrazione eseguita 90 giorni o meno dall'ora di scadenza del certificato, o in qualsiasi momento dopo l’ora di scadenza del certificato. comporterà un nuovo certificato che viene generato e utilizzato.
* Per modificare qualsiasi [valore di Gestione configurazione locale per PowerShell DS](https://msdn.microsoft.com/powershell/dsc/metaconfig4) impostato durante la registrazione iniziale del nodo, ad esempio ConfigurationMode. Attualmente, i valori dell'agente DSC possono essere modificati solo tramite la registrazione. L'unica eccezione è il valore di Configurazione del nodo assegnato al nodo che può essere modificato direttamente in Automation DSC per Azure.

La ripetizione della registrazione può essere eseguita così' come è stato registrato il nodo inizialmente, usando uno dei metodi di caricamento descritti in questo documento. Non è necessario annullare la registrazione di un nodo da Automation DSC per Azure prima di registrarlo di nuovo.

## <a name="related-articles"></a>Articoli correlati

* [Panoramica di Automation DSC per Azure](automation-dsc-overview.md)
* [Cmdlet di Automation DSC per Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [Prezzi di Automation DSC per Azure](https://azure.microsoft.com/pricing/details/automation/)

