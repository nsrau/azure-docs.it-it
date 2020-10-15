---
title: Abilitare State Configuration di Automazione di Azure
description: Questo articolo illustra come configurare i computer per la gestione con State Configuration di Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: ae268534a18a921cca012881fa172261c7ba1063
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186402"
---
# <a name="enable-azure-automation-state-configuration"></a>Abilitare State Configuration di Automazione di Azure

Questo argomento descrive come configurare i computer per la gestione con State Configuration di Automazione di Azure. Per informazioni dettagliate su questo servizio, vedere [Panoramica di State Configuration di Automazione di Azure](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Abilitare le macchine virtuali di Azure

State Configuration di Automazione di Azure consente di abilitare facilmente le VM di Azure per la gestione della configurazione usando il portale di Azure, i modelli di Azure Resource Manager o PowerShell. In background e senza che l'amministratore debba connettersi in remoto alla macchina virtuale, l'estensione DSC (Desired State Configuration) per le macchine virtuali di Azure registra la macchina virtuale con State Configuration di Automazione di Azure. L'estensione di Azure viene eseguita in modo asincrono, quindi i passaggi per tenere traccia dello stato sono disponibili in [Controllare lo stato di configurazione della macchina virtuale](#check-status-of-vm-setup).

> [!NOTE]
>La distribuzione di DSC in un nodo Linux usa la cartella **/tmp**. I moduli come `nxautomation` vengono scaricati temporaneamente per la verifica prima di installarli nelle rispettive posizioni. Per assicurarsi che i moduli vengano installati correttamente, l'agente di Log Analytics per Linux deve avere le autorizzazioni di lettura/scrittura per la cartella **/tmp**.<br><br>
>L'agente di Log Analytics per Linux viene eseguito come utente `omsagent`. Per concedere l'autorizzazione di scrittura per l'utente `omsagent`, eseguire il comando `setfacl -m u:omsagent:rwx /tmp`.

### <a name="enable-a-vm-using-azure-portal"></a>Abilitare una macchina virtuale usando il portale di Azure

Per abilitare una macchina virtuale di Azure per State Configuration tramite il [portale di Azure](https://portal.azure.com/):

1. Passare all'account di Automazione di Azure in cui abilitare le VM. 

2. Nella pagina State Configuration selezionare la scheda **Nodi**, quindi fare clic su **Aggiungi**.

3. Scegliere una VM da abilitare.

4. Se nel computer non è installata l'estensione di stato PowerShell desiderata e tramite il PowerShell e lo stato di alimentazione è "in esecuzione", fare clic su **Connetti**.

5. In **Registrazione** immettere i [valori di Configuration Manager locale per PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) necessari per il proprio caso d'uso. Facoltativamente è possibile immettere una configurazione del nodo da assegnare alla VM.

![abilitazione della macchina virtuale](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Abilitare una macchina virtuale usando modelli di Azure Resource Manager

È possibile installare e abilitare una macchina virtuale per State Configuration usando modelli di Azure Resource Manager. Per un modello di esempio che abilita una macchina virtuale esistente per State Configuration, vedere [Server gestito dal servizio Desired State Configuration](https://azure.microsoft.com/resources/templates/101-automation-configuration/). Se si gestisce un set di scalabilità di macchine virtuali, vedere il modello di esempio in [Configurazione del set di scalabilità di macchine virtuali gestito da Automazione di Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Abilitare le macchine con PowerShell

È possibile usare il cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) in PowerShell per abilitare le macchine virtuali per State Configuration. 

> [!NOTE]
>Il cmdlet `Register-AzAutomationDscNode` viene implementato attualmente solo per i computer che eseguono Windows, perché attiva solo l'estensione di Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrare le macchine virtuali tra sottoscrizioni di Azure

Il modo migliore per registrare le macchine virtuali da altre sottoscrizioni di Azure consiste nell'usare l'estensione DSC in un modello di distribuzione Azure Resource Manager. Gli esempi sono disponibili in [Estensione Desired State Configuration (DSC) con modelli di Azure Resource Manager](../virtual-machines/extensions/dsc-template.md).

Per trovare la chiave di registrazione e l'URL di registrazione da usare come parametri nel modello, vedere [Abilitare i computer in modo sicuro tramite la registrazione](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Abilitare computer fisici e macchine virtuali Windows

È possibile abilitare i server Windows in esecuzione in locale o in altri ambienti cloud (incluse le istanze di AWS EC2) per State Configuration di Automazione di Azure. I server devono avere l'[accesso in uscita ad Azure](automation-dsc-overview.md#network-planning).

1. Assicurarsi che nei computer sia installata la versione più recente di [WMF 5](https://aka.ms/wmf5latest) per abilitare State Configuration. È anche necessario che WMF 5 sia installato nel computer usato per abilitare le macchine.
1. Seguire le istruzioni riportate in [Generare metaconfigurazioni DSC](#generate-dsc-metaconfigurations) per creare una cartella contenente le metaconfigurazioni DSC richieste. 
1. Usare il cmdlet seguente per applicare le metaconfigurazioni di PowerShell DSC in remoto ai computer da abilitare. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in remoto, copiare la cartella delle **metaconfigurazioni** nei computer da abilitare. Aggiungere quindi il codice per chiamare [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) localmente nei computer.
1. Tramite il portale di Azure o i cmdlet verificare che i computer vengano visualizzati come nodi di State Configuration nell'account di Automazione di Azure.

## <a name="enable-physicalvirtual-linux-machines"></a>Abilitare computer fisici e macchine virtuali Linux

È possibile abilitare per State Configuration i server Linux in esecuzione in locale o in altri ambienti cloud. I server devono avere l'[accesso in uscita ad Azure](automation-dsc-overview.md#network-planning).

1. Assicurarsi che sia installata la versione più recente di [PowerShell DSC (Desired State Configuration) per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) nei computer da abilitare per State Configuration.
2. Se le [impostazioni predefinite di Configuration Manager locale per PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) corrispondono al caso d'uso e si vogliono abilitare i computer perché eseguano il pull e inviino report a State Configuration:

   - In ogni computer Linux da abilitare, usare `Register.py` per abilitare il computer con le impostazioni predefinite di Configuration Manager locale per PowerShell DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Per trovare la chiave e l'URL di registrazione per l'account di Automazione, vedere [Abilitare i computer in modo sicuro tramite la registrazione](#enable-machines-securely-using-registration).

3. Se i valori predefiniti di Configuration Manager locale per PowerShell DSC non corrispondono al caso d'uso oppure si vogliono abilitare i computer che inviano solo report a State Configuration di Automazione di Azure, seguire i passaggi 4-7. In caso contrario, andare direttamente al passaggio 7.

4. Seguire le istruzioni riportate nella sezione [Generare metaconfigurazioni DSC](#generate-dsc-metaconfigurations) per produrre una cartella contenente le metaconfigurazioni DSC richieste.

5. Assicurarsi che nei computer usati per abilitare le macchine virtuali per State Configuration sia installata la versione più recente di [WMF 5](https://aka.ms/wmf5latest).

6. Aggiungere il codice seguente per applicare le metaconfigurazioni di PowerShell DSC in remoto ai computer da abilitare.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in remoto, copiare le metaconfigurazioni corrispondenti ai computer remoti dalla cartella indicata nel passaggio 4 ai computer Linux.

8. Aggiungere il codice per chiamare `Set-DscLocalConfigurationManager.py` localmente in ogni computer Linux da abilitare per State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Tramite il portale di Azure o i cmdlet verificare che ora i computer da abilitare siano visualizzati come nodi DSC registrati nell'account di Automazione di Azure.

## <a name="generate-dsc-metaconfigurations"></a>Generare metaconfigurazioni DSC

Per abilitare qualsiasi computer per State Configuration è possibile generare una [metaconfigurazione DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Questa configurazione indica all'agente DSC di effettuare il pull da e/o di inviare report a State Configuration di Automazione di Azure. È possibile generare le metaconfigurazioni DSC per State Configuration di Automazione di Azure tramite una configurazione DSC di PowerShell o i cmdlet di PowerShell per Automazione di Azure.

> [!NOTE]
> Le metaconfigurazioni DSC contengono i segreti necessari per abilitare un computer in un account di Automazione per la gestione. Assicurarsi di proteggere adeguatamente le metaconfigurazioni DSC create oppure eliminarle dopo l'uso.

Il supporto del proxy per le metaconfigurazioni è controllato da [Configuration Manager locale](/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7), che è il motore DSC di Windows PowerShell. Viene eseguito in tutti i nodi di destinazione e ha la responsabilità di chiamare le risorse di configurazione incluse in uno script di metaconfigurazione DSC. È possibile integrare il supporto del proxy in una metaconfigurazione includendo le definizioni delle proprietà `ProxyURL` e `ProxyCredential` in base alle esigenze nei blocchi `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb` e `ReportServerWeb`. Un esempio di impostazione dell'URL è `ProxyURL = "http://172.16.3.6:3128";`. La proprietà `ProxyCredential` è impostata su un oggetto `PSCredential`, come descritto in [Gestire le credenziali in Automazione di Azure](shared-resources/credentials.md). 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generare metaconfigurazioni DSC usando una configurazione DSC

1. Aprire VSCode o l'editor preferito come amministratore in un computer dell'ambiente locale. Nel computer deve essere installata la versione più recente di [WMF 5](https://aka.ms/wmf5latest) .
1. Copiare localmente lo script seguente. Questo script contiene una configurazione DSC di PowerShell per la creazione delle metaconfigurazioni e un comando per avviare la creazione delle metaconfigurazioni.

    > [!NOTE]
    > I nomi di configurazione dei nodi di State Configuration fanno distinzione tra maiuscole e minuscole nel portale di Azure. Se le maiuscole e le minuscole non corrispondono, il nodo non verrà visualizzato nella scheda **Nodi**.

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

1. Specificare il codice di registrazione e l'URL per l'account di Automazione, nonché i nomi dei computer da abilitare. Tutti gli altri parametri sono facoltativi. Per trovare la chiave e l'URL di registrazione per l'account di Automazione, vedere [Abilitare i computer in modo sicuro tramite la registrazione](#enable-machines-securely-using-registration).

1. Se si vuole che i computer inviino informazioni sullo stato DSC a State Configuration di Automazione di Azure, ma senza eseguire il pull della configurazione o dei moduli di PowerShell, impostare il parametro `ReportOnly` su true.

1. Se `ReportOnly` non è impostato, i computer invieranno informazioni sullo stato DSC a State Configuration di Automazione di Azure ed eseguiranno il pull della configurazione o dei moduli di PowerShell. Impostare i parametri di conseguenza nei blocchi `ConfigurationRepositoryWeb`, `ResourceRepositoryWeb` e `ReportServerWeb`.

1. Eseguire lo script. A questo punto è disponibile una cartella denominata **DscMetaConfigs** nella directory di lavoro, contenente le metaconfigurazioni di PowerShell DSC per i computer da abilitare come amministratore:

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generare metaconfigurazioni DSC usando i cmdlet di Automazione di Azure

Se i valori predefiniti di Configuration Manager locale di PowerShell DSC corrispondono al caso d'uso e si vuole consentire ai computer di eseguire il pull e di inviare report a State Configuration di Automazione di Azure, è possibile generare le metaconfigurazioni DSC necessarie con maggiore semplicità usando i cmdlet di Automazione di Azure.

1. Aprire la console di PowerShell o VSCode come amministratore in un computer dell'ambiente locale.
2. Connettersi ad Azure Resource Manager con [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Dall'account di Automazione in cui si configureranno i nodi, scaricare le metaconfigurazioni di PowerShell DSC per i computer da abilitare:

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

1. A questo punto è disponibile una cartella denominata **DscMetaConfigs** contenente le metaconfigurazioni di PowerShell DSC per i computer da abilitare come amministratore:

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Abilitare i computer in modo sicuro tramite la registrazione

È possibile abilitare i computer in modo sicuro per un account di Automazione di Azure tramite il protocollo di registrazione DSC WMF 5. Questo protocollo consente a un nodo DSC di eseguire l'autenticazione a un server di pull o di report di PowerShell DSC, incluso State Configuration di Automazione di Azure. Il nodo si registra al server all'URL di registrazione ed esegue l'autenticazione con una chiave di registrazione. Durante la registrazione, il nodo DSC e il server di pull/report DSC negoziano un certificato univoco che il nodo dovrà usare per l'autenticazione al server dopo la registrazione. Questo processo impedisce ai nodi abilitati di rappresentarsi reciprocamente, ad esempio nel caso di un nodo compromesso e che presenta un comportamento dannoso. Dopo la registrazione, la chiave di registrazione non viene usata di nuovo per l'autenticazione e viene eliminata dal nodo.

È possibile ottenere le informazioni necessarie per il protocollo di registrazione di Configurazione stato da **Chiavi** in **Impostazioni account** nel portale di Azure. 

![Chiavi e URL di Automazione di Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- L'URL di registrazione si trova nel campo URL della pagina Chiavi.
- La chiave di registrazione è il valore del campo **Chiave di accesso primaria** o del campo **Chiave di accesso secondaria** nella pagina Chiavi. È possibile usare una delle due chiavi.

Per una maggiore sicurezza, nella pagina Chiavi è possibile rigenerare in qualsiasi momento le chiavi di accesso primaria e secondaria di un account di Automazione. La rigenerazione delle chiavi impedisce alle registrazioni dei nodi successive di usare le chiavi precedenti.

## <a name="re-register-a-node"></a>Ripetere la registrazione di un nodo

Dopo la registrazione di un computer come nodo DSC in State Configuration di Automazione di Azure, ci sono diversi motivi che richiedono la ripetizione della registrazione del nodo in futuro.

- **Rinnovo del certificato.** Per le versioni di Windows Server precedenti a Windows Server 2019, ogni nodo negozia automaticamente un certificato univoco per l'autenticazione che scade dopo un anno. Se un certificato scade senza rinnovo, il nodo non è in grado di comunicare con Automazione di Azure ed è contrassegnato come `Unresponsive`. Attualmente il protocollo di registrazione di PowerShell DSC non può rinnovare automaticamente i certificati quando si avvicina la scadenza, quindi è necessario registrare di nuovo i nodi dopo un anno. Prima di ripetere la registrazione, assicurarsi che ogni nodo esegua WMF 5 RTM. 

    Una registrazione eseguita 90 giorni o meno dalla scadenza del certificato, o in qualsiasi momento dopo la scadenza del certificato, comporterà la generazione e l'uso di un nuovo certificato. Una soluzione a questo problema è inclusa in Windows Server 2019 e versioni successive.

- **Modifiche ai valori di Configuration Manager locale DSC.** Potrebbe essere necessario modificare i [valori di Configuration Manager locale di PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) impostati durante la registrazione iniziale del nodo, ad esempio `ConfigurationMode`. Al momento è possibile modificare i valori dell'agente DSC solo tramite la ripetizione della registrazione. L'unica eccezione è rappresentata dal valore di Configurazione del nodo assegnato al nodo. È possibile modificare questo valore direttamente in Automation DSC per Azure.

È possibile registrare nuovamente un nodo proprio come è stato registrato inizialmente, usando uno dei metodi descritti in questo documento. Non è necessario annullare la registrazione di un nodo da State Configuration di Automazione di Azure prima di registrarlo di nuovo.

## <a name="check-status-of-vm-setup"></a>Controllare lo stato di configurazione della macchina virtuale

State Configuration di Automazione di Azure consente di abilitare facilmente macchine virtuali Windows di Azure per la gestione della configurazione. In background viene usata l'estensione DSC (Desired State Configuration) per le macchine virtuali di Azure per registrare la macchina virtuale con Configurazione stato di Automazione di Azure. L'estensione DSC (Desired State Configuration) per le VM di Azure viene eseguita in modalità asincrona, quindi può essere importante tenere traccia dell'avanzamento e risolvere i problemi di esecuzione.

> [!NOTE]
> Qualsiasi metodo per abilitare le macchine virtuali Windows di Azure per State Configuration che usi l'estensione DSC (Desired State Configuration) della VM di Azure può richiedere fino a un'ora prima che Automazione di Azure visualizzi le VM come registrate. Questo ritardo è dovuto all'installazione di WMF 5 nella macchina virtuale da parte dell'estensione DSC (Desired State Configuration) della VM di Azure, necessaria per abilitare le macchine virtuali per State Configuration.

Per visualizzare lo stato dell'estensione DSC (Desired State Configuration) della VM di Azure:

1. Nella portale di Azure passare alla macchina virtuale da abilitare.
2. Fare clic su **Estensioni** in **Impostazioni**. 
3. Selezionare quindi **DSC** o **DSCForLinux** a seconda del sistema operativo. 
4. Per altri dettagli, è possibile fare clic su **Visualizza stato dettagliato**.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [Introduzione a State Configuration di Automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni sulla compilazione di configurazioni DSC da assegnare ai nodi di destinazione, vedere [Compilare configurazioni DSC in State Configuration di Automazione di Azure](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Per informazioni sui prezzi, vedere [Prezzi di State Configuration di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per un esempio dell'uso di State Configuration di Automazione di Azure in una pipeline di distribuzione continua, vedere [Configurare la distribuzione continua con Chocolatey](automation-dsc-cd-chocolatey.md).
- Per informazioni sulla risoluzione dei problemi, vedere [Risolvere i problemi di State Configuration di Automazione di Azure](./troubleshoot/desired-state-configuration.md).
