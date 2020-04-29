---
title: Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure
description: Come configurare i computer per la gestione con la configurazione dello stato di automazione di Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457740"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Perché gestire computer tramite Configurazione stato di Automazione di Azure?

La configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired state Configuration) in qualsiasi Data Center nel cloud o in locale. Si accede all'portale di Azure selezionando **state Configuration (DSC)** in **Gestione configurazione**. 

Questo servizio consente la scalabilità in migliaia di computer in modo rapido e semplice da una posizione centralizzata e sicura. È possibile caricare facilmente i computer, assegnare loro configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer allo stato desiderato specificato.

Il servizio di configurazione dello stato di automazione di Azure è DSC quali sono i manuali operativi di automazione di Azure per gli script di PowerShell. In altre parole, Automazione di Azure consente di gestire gli script di PowerShell e le configurazioni DSC. Per altre informazioni sui vantaggi dell'uso di Configurazione stato di Automazione di Azure, vedere [Panoramica di Configurazione stato di Automazione di Azure](automation-dsc-overview.md).

È possibile usare Configurazione stato di Automazione di Azure per gestire macchine virtuali di diversi tipi:

- Macchine virtuali di Azure
- Macchine virtuali di Azure (classica)
- Computer fisici/virtuali Windows locali o in un cloud diverso da Azure (incluse le istanze di AWS EC2)
- Computer fisici/macchine virtuali Linux locali, in Azure o in un cloud diverso da Azure

Se non si è pronti per gestire la configurazione del computer dal cloud, è possibile usare la configurazione dello stato di automazione di Azure come endpoint di sola segnalazione. Questa funzionalità consente di impostare (effettuare il push) delle configurazioni tramite DSC e visualizzare i dettagli dei report in automazione di Azure.

> [!NOTE]
> La gestione di macchine virtuali di Azure con la configurazione dello stato di automazione di Azure è inclusa senza costi aggiuntivi se la versione installata dell'estensione DSC (Desired state Configuration) della VM Azure è maggiore di 2,70 Per ulteriori informazioni, vedere la [**pagina dei prezzi di automazione**](https://azure.microsoft.com/pricing/details/automation/).

Le sezioni seguenti di questo articolo illustrano come è possibile caricare le macchine elencate in precedenza nella configurazione dello stato di automazione di Azure.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="onboarding-azure-vms"></a>Onboarding di macchine virtuali di Azure

La configurazione dello stato di automazione di Azure consente di caricare facilmente le VM di Azure per la gestione della configurazione, usando il portale di Azure, i modelli di Azure Resource Manager o PowerShell. Dietro le quinte e senza un amministratore che debba accedere in remoto a una macchina virtuale, l'estensione DSC (Desired state Configuration) della VM di Azure registra la VM con la configurazione dello stato di automazione di Azure. Poiché l'estensione di Azure viene eseguita in modo asincrono, i passaggi per monitorarne lo stato di avanzamento o la risoluzione dei problemi sono disponibili nella sezione risoluzione dei problemi di caricamento della [macchina virtuale di Azure](#troubleshooting-azure-virtual-machine-onboarding) di questo articolo.

> [!NOTE]
>La distribuzione di DSC in un nodo Linux usa la cartella **tmp** . I moduli, `nxautomation` ad esempio, vengono scaricati temporaneamente per la verifica prima di installarli nelle rispettive posizioni appropriate. Per assicurarsi che i moduli vengano installati correttamente, l'agente di Log Analytics per Linux deve avere le autorizzazioni di lettura/scrittura per la cartella/ **tmp** .<br><br>
>L'agente di Log Analytics per Linux viene eseguito `omsagent` come utente. Per concedere >autorizzazione di scrittura per `omsagent` l'utente, eseguire il `setfacl -m u:omsagent:rwx /tmp`comando.

### <a name="onboard-a-vm-using-azure-portal"></a>Onboarding di una VM con portale di Azure

Per caricare una macchina virtuale di Azure nella configurazione dello stato di automazione di Azure tramite la [portale di Azure](https://portal.azure.com/):

1. Passare all'account di automazione di Azure in cui eseguire l'onboarding delle macchine virtuali. 

2. Nella pagina configurazione stato selezionare la scheda **nodi** , quindi fare clic su **Aggiungi**.

3. Scegliere una macchina virtuale da caricare.

4. Se nel computer non è installata l'estensione di stato desired PowerShell e lo stato di alimentazione è in esecuzione, fare clic su **Connetti**.

5. In **registrazione**immettere i [valori Configuration Manager locali di PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) richiesti per il caso d'uso. Facoltativamente, è possibile immettere una configurazione del nodo da assegnare alla VM.

![onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Caricare una VM usando modelli di Azure Resource Manager

È possibile distribuire e caricare una macchina virtuale nella configurazione dello stato di automazione di Azure usando i modelli Azure Resource Manager. Per un modello di esempio che carica una macchina virtuale esistente nella configurazione dello stato di automazione di Azure, vedere [server gestito dal servizio di configurazione dello stato desiderato](https://azure.microsoft.com/resources/templates/101-automation-configuration/) . Se si gestisce un set di scalabilità di macchine virtuali, vedere il modello di esempio nella [configurazione del set di scalabilità di macchine virtuali gestito da automazione di Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Caricare i computer con PowerShell

È possibile usare il cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) in PowerShell per eseguire l'onboarding delle macchine virtuali nella configurazione dello stato di automazione di Azure. 

> [!NOTE]
>Il `Register-AzAutomationDscNode` cmdlet viene implementato attualmente solo per i computer che eseguono Windows, perché attiva solo l'estensione di Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrare le macchine virtuali tra sottoscrizioni di Azure

Il modo migliore per registrare le macchine virtuali da altre sottoscrizioni di Azure consiste nell'usare l'estensione DSC in un modello di distribuzione Azure Resource Manager. Gli esempi sono disponibili nell'estensione DSC ( [desired state Configuration) con i modelli Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Per trovare la chiave di registrazione e l'URL di registrazione da usare come parametri nel modello, vedere la sezione [onboarding in modo sicuro tramite registrazione](#onboarding-securely-using-registration) in questo articolo.

## <a name="onboarding-physicalvirtual-windows-machines"></a>Onboarding di computer fisici/virtuali Windows

È possibile caricare i server Windows in esecuzione in locale o in altri ambienti cloud (incluse le istanze di AWS EC2) nella configurazione dello stato di automazione di Azure. Il server deve avere [accesso in uscita ad Azure](automation-dsc-overview.md#network-planning).

1. Assicurarsi che sia installata la versione più recente di [WMF 5](https://aka.ms/wmf5latest) nei computer da caricare in configurazione dello stato di automazione di Azure. Inoltre, è necessario installare WMF 5 nel computer utilizzato per l'operazione di onboarding.
1. Seguire le istruzioni riportate nella sezione [generazione di metaconfigurazioni DSC](#generating-dsc-metaconfigurations) per creare una cartella contenente le metaconfigurazioni DSC richieste. 
1. Usare il cmdlet seguente per applicare le metaconfigurazioni di PowerShell DSC in remoto ai computer che si vuole caricare. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in modalità remota, copiare la cartella **metaconfigurations** nei computer da caricare. Aggiungere quindi il codice per chiamare [set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) localmente nei computer.
1. Usando il portale di Azure o i cmdlet, verificare che i computer da caricare vengano visualizzati come nodi di configurazione dello stato registrati nell'account di automazione di Azure.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Onboarding di computer fisici/virtuali Linux

È possibile caricare i server Linux in esecuzione in locale o in altri ambienti cloud per la configurazione dello stato di automazione di Azure. Il server deve avere [accesso in uscita ad Azure](automation-dsc-overview.md#network-planning).

1. Assicurarsi che la versione più recente di [PowerShell DSC (Desired state Configuration) per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) sia installata nei computer da caricare in configurazione dello stato di automazione di Azure.
2. Se i [valori predefiniti Configuration Manager locali di PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) corrispondono al caso d'uso e si vuole caricare i computer in modo da eseguire il pull e il report alla configurazione dello stato di automazione di Azure:

   - In ogni computer Linux da caricare in configurazione dello stato di automazione di Azure `Register.py` , usare per eseguire l'onboarding usando le impostazioni predefinite Configuration Manager locali di PowerShell DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Per trovare la chiave di registrazione e l'URL di registrazione per l'account di automazione, vedere la sezione [onboarding in modo sicuro usando la registrazione](#onboarding-securely-using-registration) di questo articolo.

3. Se i valori predefiniti di Configuration Manager locale di PowerShell DSC non corrispondono al caso d'uso o si vuole caricare computer che riportano solo la configurazione dello stato di automazione di Azure, seguire i passaggi 4-7. In caso contrario, procedere direttamente con il passaggio 7.

4. Seguire le istruzioni riportate nella sezione [generazione di metaconfigurazioni DSC](#generating-dsc-metaconfigurations) per generare una cartella contenente le metaconfigurazioni DSC richieste.

5. Assicurarsi che sia installata la versione più recente di [WMF 5](https://aka.ms/wmf5latest) nel computer usato per l'onboarding.

6. Aggiungere il codice come segue per applicare le metaconfigurazioni di PowerShell DSC in remoto ai computer che si vuole caricare.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in modalità remota, copiare le metaconfigurazioni corrispondenti ai computer remoti dalla cartella descritta nel passaggio 4 ai computer Linux.

8. Aggiungere il codice per `Set-DscLocalConfigurationManager.py` chiamare localmente in ogni computer Linux per eseguire l'onboarding nella configurazione dello stato di automazione di Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Usando il portale di Azure o i cmdlet, assicurarsi che i computer da caricare ora vengano visualizzati come nodi DSC registrati nell'account di automazione di Azure.

## <a name="generating-dsc-metaconfigurations"></a>Generazione di metaconfigurazioni DSC

Per caricare qualsiasi computer nella configurazione dello stato di automazione di Azure, è possibile generare una [metaconfigurazione DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Questa configurazione indica all'agente DSC di effettuare il pull da e/o creare report nella configurazione dello stato di automazione di Azure. È possibile generare una metaconfigurazione DSC per la configurazione dello stato di automazione di Azure usando una configurazione DSC di PowerShell o i cmdlet di PowerShell per automazione di Azure.

> [!NOTE]
> Le metaconfigurazioni DSC contengono i segreti necessari per caricare un computer in un account di automazione per la relativa gestione. Assicurarsi di proteggere adeguatamente le metaconfigurazioni DSC create oppure eliminarle dopo l'uso.

Il supporto del proxy per le metaconfigurazioni è controllato da Gestione configurazione locale, che è il motore DSC di Windows PowerShell. Gestione configurazione locale viene eseguito in tutti i nodi di destinazione e ha la responsabilità di chiamare le risorse di configurazione incluse in uno script di metaconfigurazione DSC. È possibile includere il supporto del proxy in una metaconfigurazione includendo le definizioni dell'URL del proxy e le credenziali proxy in `ConfigurationRepositoryWeb`base `ResourceRepositoryWeb`alle esigenze `ReportServerWeb` nei blocchi, e. Vedere [configurazione del Configuration Manager locale](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generare metaconfigurazioni DSC usando una configurazione DSC

1. Aprire VSCode (o l'editor preferito) come amministratore in un computer nell'ambiente locale. Nel computer deve essere installata la versione più recente di [WMF 5](https://aka.ms/wmf5latest) .
1. Copiare localmente lo script seguente. Questo script contiene una configurazione DSC di PowerShell per la creazione delle metaconfigurazioni e un comando per avviare la creazione delle metaconfigurazioni.

    > [!NOTE]
    > I nomi di configurazione del nodo di configurazione dello stato fanno distinzione tra maiuscole e minuscole nella portale di Azure. Se il case non corrisponde, il nodo non verrà visualizzato nella scheda **nodi** .

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

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
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

1. Specificare il codice di registrazione e l'URL per l'account di automazione, nonché i nomi dei computer da caricare. Tutti gli altri parametri sono facoltativi. Per trovare la chiave di registrazione e l'URL di registrazione per l'account di automazione, vedere la sezione [onboarding in modo sicuro usando la registrazione](#onboarding-securely-using-registration) .

1. Se si vuole che i computer segnalino le informazioni sullo stato DSC alla configurazione dello stato di automazione di Azure, ma non la configurazione `ReportOnly` pull o i moduli di PowerShell, impostare il parametro su true.

1. Se `ReportOnly` non è impostato, i computer segnalano le informazioni sullo stato DSC ai moduli configurazione dello stato di automazione di Azure e configurazione pull o PowerShell. Impostare i `ConfigurationRepositoryWeb`parametri di conseguenza nei `ResourceRepositoryWeb`blocchi, `ReportServerWeb` e.

1. Eseguire lo script. A questo punto dovrebbe essere presente una cartella directory di lavoro denominata **DscMetaConfigs**, contenente le metaconfigurazioni di PowerShell DSC per i computer da caricare (come amministratore).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generare metaconfigurazioni DSC usando i cmdlet di automazione di Azure

Se i valori predefiniti di gestione configurazione locale di PowerShell DSC corrispondono al caso d'uso e si vuole caricare i computer sia per eseguire il pull da che per segnalare la configurazione dello stato di automazione di Azure, è possibile generare le metaconfigurazioni DSC necessarie in modo più semplice usando i cmdlet di automazione di Azure.

1. Aprire la console di PowerShell o VSCode come amministratore in un computer nell'ambiente locale.
2. Connettersi a Azure Resource Manager usando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Scaricare le metaconfigurazioni di PowerShell DSC per i computer che si vuole caricare dall'account di automazione in cui si stanno configurando i nodi.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. A questo punto dovrebbe essere disponibile una cartella **DscMetaConfigs** contenente le metaconfigurazioni di PowerShell DSC per i computer da caricare (come amministratore).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Onboarding in modo sicuro con la registrazione

I computer possono essere caricati in modo sicuro in un account di automazione di Azure tramite il protocollo di registrazione DSC di WMF 5. Questo protocollo consente a un nodo DSC di eseguire l'autenticazione a un server di pull o di report di PowerShell DSC, inclusa la configurazione dello stato di automazione di Azure. Il nodo viene registrato con il server nell'URL di registrazione ed esegue l'autenticazione con una chiave di registrazione. Durante la registrazione, il nodo DSC e il server di pull/report DSC negoziano un certificato univoco per il nodo da usare per l'autenticazione per la post-registrazione del server. Questo processo impedisce la rappresentazione dei nodi caricati, ad esempio se un nodo viene compromesso e si comporta in modo dannoso. Dopo la registrazione, la chiave di registrazione non viene usata di nuovo per l'autenticazione e viene eliminata dal nodo.

È possibile ottenere le informazioni necessarie per il protocollo di registrazione di Configurazione stato da **Chiavi** in **Impostazioni account** nel portale di Azure. 

![Chiavi e URL di Automazione di Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL di registrazione è il campo URL nella pagina chiavi.
- Chiave di registrazione è il valore del campo **chiave di accesso primaria** o del campo **chiave di accesso secondaria** nella pagina chiavi. È possibile usare una delle due chiavi.

Per una maggiore sicurezza, è possibile rigenerare le chiavi di accesso primarie e secondarie di un account di automazione in qualsiasi momento nella pagina chiavi. La rigenerazione delle chiavi impedisce alle registrazioni dei nodi successive di usare le chiavi precedenti.

## <a name="re-registering-a-node"></a>Ripetizione della registrazione di un nodo

Dopo la registrazione di un computer come nodo DSC nella configurazione dello stato di automazione di Azure, esistono diversi motivi per cui potrebbe essere necessario ripetere la registrazione del nodo in futuro.

- **Rinnovo del certificato.** Per le versioni di Windows Server precedenti a Windows Server 2019, ogni nodo negozia automaticamente un certificato univoco per l'autenticazione che scade dopo un anno. Se un certificato scade senza rinnovo, il nodo non è in grado di comunicare con automazione di Azure ed `Unresponsive`è contrassegnato. Attualmente, il protocollo di registrazione di PowerShell DSC non può rinnovare automaticamente i certificati quando si avvicina la scadenza ed è necessario registrare di nuovo i nodi dopo un anno. Prima di ripetere la registrazione, assicurarsi che ogni nodo esegua WMF 5 RTM. 

    La ripetizione della registrazione eseguita 90 giorni o meno dall'ora di scadenza del certificato, o in qualsiasi momento dopo l'ora di scadenza del certificato, comporta la generazione e l'utilizzo di un nuovo certificato. Una soluzione a questo problema è inclusa in Windows Server 2019 e versioni successive.

- **Modifiche ai valori di gestione configurazione locale DSC.** Potrebbe essere necessario modificare [i valori](/powershell/scripting/dsc/managing-nodes/metaConfig4) di gestione configurazione locale di PowerShell durante la registrazione iniziale del nodo, ad `ConfigurationMode`esempio. Attualmente, è possibile modificare i valori di questi agenti DSC solo tramite la ripetizione della registrazione. L'unica eccezione è rappresentata dal valore di configurazione del nodo assegnato al nodo. È possibile modificare questa operazione direttamente in Azure Automation DSC.

È possibile registrare nuovamente un nodo nello stesso modo in cui è stato registrato inizialmente il nodo, usando uno dei metodi di onboarding descritti in questo documento. Non è necessario annullare la registrazione di un nodo dalla configurazione dello stato di automazione di Azure prima di registrarlo di nuovo.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Risoluzione dei problemi di caricamento di macchine virtuali di Azure

Configurazione stato di Automazione di Azure permette di caricare macchine virtuali Windows di Azure in tutta semplicità per la gestione della configurazione. In background viene usata l'estensione DSC (Desired State Configuration) per le macchine virtuali di Azure per registrare la macchina virtuale con Configurazione stato di Automazione di Azure. Poiché l'estensione DSC (Desired state Configuration) della VM di Azure viene eseguita in modo asincrono, è importante tenere traccia dello stato di avanzamento e della risoluzione dei problemi.

> [!NOTE]
> Qualsiasi metodo di onboarding di una macchina virtuale Windows di Azure nella configurazione dello stato di automazione di Azure che usa l'estensione DSC (Desired state Configuration) della VM di Azure può richiedere fino a un'ora prima che automazione di Azure lo mostri come registrato. Questo ritardo è dovuto all'installazione di WMF 5 nella macchina virtuale dall'estensione DSC (Desired state Configuration) della VM di Azure, necessaria per caricare la macchina virtuale nella configurazione dello stato di automazione di Azure.

Per risolvere i problemi o visualizzare lo stato dell'estensione DSC (Desired state Configuration) della VM di Azure:

1. Nella portale di Azure passare alla macchina virtuale da caricare.
2. Fare clic su **estensioni** in **Impostazioni**. 
3. A questo punto selezionare **DSC** o **DSCForLinux**, a seconda del sistema operativo. 
4. Per altri dettagli, è possibile fare clic su **Visualizza stato dettagliato**.

Per altre informazioni sulla risoluzione dei problemi, vedere [risoluzione dei problemi di automazione DSC (Desired state Configuration) di automazione di Azure](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Guida introduttiva alla configurazione dello stato di automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni sulla compilazione delle configurazioni DSC per poterle assegnare ai nodi di destinazione, vedere [compilazione di configurazioni in Azure Automation state Configuration](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per informazioni sui prezzi, vedere [prezzi di configurazione dello stato di automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per un esempio di come usare la configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [esempio di utilizzo: distribuzione continua in macchine virtuali con la configurazione dello stato di automazione di Azure e cioccolato](automation-dsc-cd-chocolatey.md).
