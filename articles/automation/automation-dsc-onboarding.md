---
title: Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure
description: Come configurare i computer per la gestione con la configurazione dello stato di automazione di AzureHow to set up machines for management with Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c718b9a66b378044618c8c52eec3a1a498ace83c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383199"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Perché gestire computer tramite Configurazione stato di Automazione di Azure?

Configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired State Configuration) in qualsiasi data center cloud o locale. È possibile accedervi nel portale di Azure selezionando **Configurazione stato (DSC)** in **Gestione configurazione**. 

Questo servizio consente la scalabilità su migliaia di computer in modo rapido e semplice da una posizione centrale e sicura. È possibile integrare facilmente i computer, assegnare loro configurazioni dichiarative e visualizzare report che mostrino la conformità di ogni macchina con lo stato desiderato specificato.

Il servizio Configurazione stato di automazione di Azure è per DSC quali runbook di Automazione di Azure sono per gli script di PowerShell.The Azure Automation State Configuration service is to DSC what Azure Automation runbooks are to PowerShell scripting. In altre parole, Automazione di Azure consente di gestire gli script di PowerShell e le configurazioni DSC. Per altre informazioni sui vantaggi dell'uso di Configurazione stato di Automazione di Azure, vedere [Panoramica di Configurazione stato di Automazione di Azure](automation-dsc-overview.md).

È possibile usare Configurazione stato di Automazione di Azure per gestire macchine virtuali di diversi tipi:

- Macchine virtuali di Azure
- Macchine virtuali di Azure (classica)
- Macchine Windows fisiche/virtuali in locale o in un cloud diverso da Azure (incluse le istanze AWS EC2)
- Computer fisici/macchine virtuali Linux locali, in Azure o in un cloud diverso da Azure

Se non si è pronti per gestire la configurazione del computer dal cloud, è possibile usare la configurazione dello stato di automazione di Azure come endpoint solo report. Questa funzionalità consente di impostare (push) le configurazioni tramite DSC e visualizzare i dettagli dei report in Automazione di Azure.This feature allows you to set (push) configurations through DSC and view reporting details in Azure Automation.

> [!NOTE]
> La gestione delle macchine virtuali di Azure con la configurazione dello stato di automazione di Azure è inclusa senza alcun costo aggiuntivo se la versione dell'estensione della configurazione dello stato desiderato della macchina virtuale di Azure installata è maggiore di 2.70.Managing Azure VMs with Azure Automation State Configuration is included with no extra charge if the installed Azure VM Desired State Configuration extension version is greater than 2.70. Per ulteriori informazioni, vedere Pagina dei prezzi di [**automazione**](https://azure.microsoft.com/pricing/details/automation/).

The following sections of this article outline how you can onboard the machines listed above to Azure Automation State Configuration.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="onboarding-azure-vms"></a>Onboarding Azure VMs

Azure Automation State Configuration lets you easily onboard Azure VMs for configuration management, using the Azure portal, Azure Resource Manager templates, or PowerShell. Sotto il cofano e senza che un amministratore debba eseguire la connessione remota in una macchina virtuale, l'estensione Configurazione stato desiderato della macchina virtuale di Azure registra la macchina virtuale con Configurazione stato di automazione di Azure.Under the hood, and without an administrator having to remote into a VM, the Azure VM Desired State Configuration extension registers the VM with Azure Automation State Configuration. Poiché l'estensione di Azure viene eseguita in modo asincrono, i passaggi per tenere traccia dello stato di avanzamento o della risoluzione dei problemi vengono forniti nella sezione [Risoluzione dei problemi](#troubleshooting-azure-virtual-machine-onboarding) di onboarding delle macchine virtuali di Azure di questo articolo.

> [!NOTE]
>La distribuzione di DSC in un nodo Linux utilizza la cartella **/tmp.** Moduli come `nxautomation` vengono scaricati temporaneamente per la verifica prima di installarli nelle posizioni appropriate. Per garantire che i moduli vengano installati correttamente, l'agente di Log Analytics per Linux deve disporre delle autorizzazioni di lettura/scrittura per la cartella **/tmp.**<br><br>
>L'agente log Analytics per `omsagent` Linux viene eseguito come utente. Per concedere >autorizzazione `omsagent` di scrittura all'utente, eseguire il comando `setfacl -m u:omsagent:rwx /tmp`.

### <a name="onboard-a-vm-using-azure-portal"></a>Onboard di una macchina virtuale tramite il portale di AzureOnboard a VM using Azure portal

Per eseguire l'onboarding di una macchina virtuale di Azure in Configurazione dello stato di automazione di Azure tramite il portale di Azure:To onboard an Azure VM to Azure Automation State Configuration through the [Azure portal:](https://portal.azure.com/)

1. Passare all'account di Automazione di Azure in cui eseguire l'onboarding delle macchine virtuali. 

2. Nella pagina Configurazione stato selezionare la scheda **Nodi,** quindi fare clic su **Aggiungi**.

3. Scegliere una macchina virtuale per l'onboarding.

4. Se nel computer non è installata l'estensione dello stato desiderata di PowerShell e lo stato di alimentazione è in esecuzione, fare clic su **Connetti**.

5. In **Registrazione**immettere i valori di [Gestione configurazione locale di PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) necessari per il caso d'uso. Facoltativamente, è possibile immettere una configurazione di nodo da assegnare alla macchina virtuale.

![onboarding](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Creare l'onboarding di una macchina virtuale usando i modelli di Azure Resource ManagerOnboard a VM using Azure Resource Manager templates

È possibile distribuire e integrare una macchina virtuale in Configurazione stato automazione di Azure usando i modelli di Azure Resource Manager.You can deploy and onboard a VM to Azure Automation State Configuration using Azure Resource Manager templates. Vedere Server gestito dal servizio Desired State Configuration per un modello di esempio che intasa una macchina virtuale esistente nella configurazione dello stato di automazione di Azure.See [Server managed by Desired State Configuration service](https://azure.microsoft.com/resources/templates/101-automation-configuration/) for an example template that onboards an existing VM to Azure Automation State Configuration. Se si gestisce un set di scalabilità della macchina virtuale, vedere il modello di esempio in Configurazione set di [scalabilità vm gestito da Automazione di Azure.If](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)you are managing a VM Scale Set, see the example template in VM Scale Set Configuration managed by Azure Automation .

### <a name="onboard-machines-using-powershell"></a>Eseguire l'onboarding di computer con PowerShellOnboard machines using PowerShell

È possibile usare il cmdlet Register-AzAutomationDscNode in PowerShell per l'onboarding delle macchine virtuali nella configurazione dello stato di automazione di Azure.You can use the [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet in PowerShell to onboard VMs to Azure Automation State Configuration. 

> [!NOTE]
>Il `Register-AzAutomationDscNode` cmdlet è attualmente implementato solo per i computer che eseguono Windows, in quanto attiva solo l'estensione di Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrare le macchine virtuali tra le sottoscrizioni di AzureRegister VMs across Azure subscriptions

Il modo migliore per registrare macchine virtuali da altre sottoscrizioni di Azure consiste nell'usare l'estensione DSC in un modello di distribuzione di Azure Resource Manager.The best way to register VMs from other Azure subscriptions is to use the DSC extension in an Azure Resource Manager deployment template. Esempi sono disponibili [nell'estensione Configurazione stato desiderato con](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)i modelli di Azure Resource Manager .

Per trovare la chiave di registrazione e l'URL di registrazione da utilizzare come parametri nel modello, vedere la sezione [Onboarding](#onboarding-securely-using-registration) in modo sicuro tramite registrazione in questo articolo.

## <a name="onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>Onboarding di macchine Windows fisiche/virtuali in locale o in un cloud diverso da Azure (incluse le istanze AWS EC2)

È possibile eseguire l'onboarding di server Windows in esecuzione in locale o in altri ambienti cloud in Configurazione stato di automazione di Azure.You can onboard Windows servers running on-premises or in other cloud environments to Azure Automation State Configuration. I server devono disporre [dell'accesso in uscita ad Azure.](automation-dsc-overview.md#network-planning)

1. Assicurarsi che nei computer per l'onboarding alla configurazione dello stato di automazione di Azure sia installata la versione più recente di [WMF 5.](https://aka.ms/wmf5latest) Inoltre, WMF 5 deve essere installato nel computer che si sta utilizzando per l'operazione di onboarding.
1. Seguire le istruzioni nella sezione [Generazione di metaconfigurazioni DSC](#generating-dsc-metaconfigurations) per creare una cartella contenente le metaconfigurazioni DSC necessarie. 
1. Utilizzare il cmdlet seguente per applicare le metaconfigurazioni di PowerShell DSC in remoto ai computer che si desidera eseguire l'onboarding. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in remoto, copiare la cartella delle **metaconfigurazioni** nei computer in cui si esegue l'onboarding. Aggiungere quindi il `Set-DscLocalConfigurationManager` codice da chiamare localmente sui computer.
1. Usando il portale o i cmdlet di Azure, verificare che i computer da eseguire l'onboarding vengano visualizzati come nodi di configurazione dello stato registrati nell'account di Automazione di Azure.Using the Azure portal or cmdlets, verify that the machines to onboard appear as a State Configuration nodes registered in your Azure Automation account.

## <a name="onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Onboarding physical/virtual Linux machines on-premises, or in a cloud other than Azure

È possibile eseguire l'onboarding di server Linux in esecuzione in locale o in altri ambienti cloud per la configurazione dello stato di automazione di Azure.You can onboard Linux servers running on-premises or in other cloud environments to Azure Automation State Configuration. I server devono disporre [dell'accesso in uscita ad Azure.](automation-dsc-overview.md#network-planning)

1. Assicurarsi che la versione più recente di [PowerShell Desired State Configuration per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) sia installata nei computer per l'onboarding alla configurazione dello stato di automazione di Azure.Make sure that the latest version of PowerShell Desired State Configuration for Linux is installed on the machines to onboard to Azure Automation State Configuration.
2. Se le impostazioni predefinite di Gestione configurazione locale di PowerShell DSC corrispondono al caso d'uso e si vuole eseguire l'onboarding dei computer in modo che estraggano e segnalino alla configurazione dello stato di automazione di Azure:If the [PowerShell DSC Local Configuration Manager defaults](/powershell/scripting/dsc/managing-nodes/metaConfig4) match your use case, and you want to onboard machines so that they both pull from and report to Azure Automation State Configuration:

   - In ogni computer Linux per l'onboarding `Register.py` alla configurazione dello stato di automazione di Azure usare l'onboarding usando le impostazioni predefinite di Gestione configurazione locale di PowerShell DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Per trovare la chiave di registrazione e l'URL di registrazione per l'account di automazione, vedere la sezione [Onboarding in modo sicuro tramite la registrazione](#onboarding-securely-using-registration) di questo articolo.

3. Se le impostazioni predefinite di Gestione configurazione locale di PowerShell DSC non corrispondono al caso d'uso o si desidera eseguire l'onboarding di computer che segnalano solo la configurazione dello stato di automazione di Azure, seguire i passaggi da 4 a 7. In caso contrario, procedere direttamente al passaggio 7.

4. Seguire le istruzioni nella sezione Generazione di [metaconfigurazioni DSC](#generating-dsc-metaconfigurations) per produrre una cartella contenente le metaconfigurazioni DSC necessarie.

5. Assicurarsi che nel computer in uso per l'onboarding sia installata la versione più recente di [WMF 5.](https://aka.ms/wmf5latest)

6. Aggiungere il codice come segue per applicare le metaconfigurazioni di PowerShell DSC in remoto ai computer che si desidera eseguire l'onboarding.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in remoto, copiare le metaconfigurazioni corrispondenti ai computer remoti dalla cartella descritta nel passaggio 4 ai computer Linux.

8. Aggiungere codice `Set-DscLocalConfigurationManager.py` da chiamare in locale su ogni computer Linux per l'onboarding alla configurazione dello stato di automazione di Azure.Add code to call locally on each Linux machine to onboard to Azure Automation State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Usando il portale o i cmdlet di Azure, verificare che i computer da eseguire l'onboarding vengano ora visualizzati come nodi DSC registrati nell'account di Automazione di Azure.Using the Azure portal or cmdlets, ensure that the machines to onboard now show up as DSC nodes registered in your Azure Automation account.

## <a name="generating-dsc-metaconfigurations"></a>Generazione di metaconfigurazioni DSC

Per eseguire l'onboarding di qualsiasi computer alla configurazione dello stato di automazione di Azure, è possibile generare una [metaconfigurazione DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Questa configurazione indica all'agente DSC di eseguire il pull e/o creare report per la configurazione dello stato di automazione di Azure.This configuration tells the DSC agent to pull from and/or report to Azure Automation State Configuration. È possibile generare una metaconfigurazione DSC per la configurazione dello stato di automazione di Azure usando una configurazione DSC di PowerShell o i cmdlet di Azure Automation PowerShell.You can generate a DSC metaconfiguration for Azure Automation State Configuration using either a PowerShell DSC configuration or the Azure Automation PowerShell cmdlets.

> [!NOTE]
> Le metaconfigurazioni DSC contengono i segreti necessari per caricare un computer in un account di automazione per la relativa gestione. Assicurarsi di proteggere adeguatamente le metaconfigurazioni DSC create oppure eliminarle dopo l'uso.

Il supporto proxy per le metaconfigurazioni è controllato da Gestione configurazione locale, ovvero il motore DSC di Windows PowerShell. Gestione configurazione locale viene eseguito su tutti i nodi di destinazione ed è responsabile della chiamata delle risorse di configurazione incluse in uno script di metaconfigurazione DSC. È possibile includere il `ConfigurationRepositoryWeb`supporto proxy in una metaconfigurazione includendo le definizioni dell'URL del proxy e della credenziale proxy in base alle esigenze nei blocchi , `ResourceRepositoryWeb`e `ReportServerWeb` . Vedere [Configurazione di Gestione configurazione locale](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generare metaconfigurazioni DSC utilizzando una configurazione DSC

1. Aprire VSCode (o l'editor preferito) come amministratore in un computer nell'ambiente locale. Nel computer deve essere installata la versione più recente di [WMF 5](https://aka.ms/wmf5latest) .
1. Copiare localmente lo script seguente. Questo script contiene una configurazione DSC di PowerShell per la creazione delle metaconfigurazioni e un comando per avviare la creazione delle metaconfigurazioni.

    > [!NOTE]
    > I nomi di configurazione del nodo di configurazione dello stato fanno distinzione tra maiuscole e minuscole nel portale di Azure.State Configuration Node Configuration names are case-sensitive in the Azure portal. Se il caso non corrisponde, il nodo non verrà visualizzato nella scheda **Nodi.**

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

1. Specificare il codice di registrazione e l'URL per l'account di automazione, nonché i nomi dei computer da caricare. Tutti gli altri parametri sono facoltativi. Per trovare la chiave di registrazione e l'URL di registrazione per l'account Automation, vedere la sezione Onboarding in [modo sicuro tramite registrazione.](#onboarding-securely-using-registration)

1. Se si vuole che i computer segnalino le informazioni sullo stato DSC alla `ReportOnly` configurazione dello stato di automazione di Azure, ma non alla configurazione pull o ai moduli di PowerShell, impostare il parametro su true.

1. Se `ReportOnly` non è impostato, i computer segnalano le informazioni sullo stato DSC alla configurazione dello stato di automazione di Azure ed estraggono la configurazione o i moduli di PowerShell.If is not set, the machines report DSC status information to Azure Automation State Configuration and pull configuration or PowerShell modules. Impostare i parametri `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`di `ReportServerWeb` conseguenza nei blocchi , e .

1. Eseguire lo script. A questo punto dovrebbe essere una cartella di directory di lavoro denominata **DscMetaConfigs**, contenente le metaconfigurazioni di PowerShell DSC per i computer di onboarding (come amministratore).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generare metaconfigurazioni DSC usando i cmdlet di Automazione di AzureGenerate DSC metaconfigurations using Azure Automation cmdlets

Se le impostazioni predefinite di LCM di PowerShell DSC corrispondono al caso d'uso e si vuole integrare i computer per eseguire il pull e il report alla configurazione dello stato di automazione di Azure, è possibile generare le metaconfigurazioni DSC necessarie in modo più semplice usando i cmdlet di Automazione di Azure.

1. Aprire la console di PowerShell o VSCode come amministratore in un computer nell'ambiente locale.
2. Connettersi a Azure Resource Manager usando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Scaricare le metaconfigurazioni di PowerShell DSC per i computer da eseguire l'onboarding dall'account di automazione in cui si stanno configurando i nodi.

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

1. A questo punto si dovrebbe avere una cartella **DscMetaConfigs** contenente le metaconfigurazioni DSC di PowerShell per i computer per l'onboarding (come amministratore).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Onboarding sicuro tramite registrazione

I computer possono eseguire l'onboarding sicuro su un account di Automazione di Azure tramite il protocollo di registrazione DSC di WMF 5.Machines can securely onboard to an Azure Automation account through the WMF 5 DSC registration protocol. Questo protocollo consente a un nodo DSC di eseguire l'autenticazione in un server di report o pull DSC di PowerShell, inclusa la configurazione dello stato di automazione di Azure.This protocol allows a DSC node to authenticate to a PowerShell DSC pull or report server, including Azure Automation State Configuration. Il nodo si registra con il server all'URL di registrazione e si autentica utilizzando una chiave di registrazione. Durante la registrazione, il nodo DSC e il server di pull/report DSC negoziano un certificato univoco per il nodo da utilizzare per l'autenticazione al server dopo la registrazione. Questo processo impedisce ai nodi con connessione di rappresentare l'un l'altro, ad esempio se un nodo viene compromesso e si comporta in modo dannoso. Dopo la registrazione, la chiave di registrazione non viene utilizzata nuovamente per l'autenticazione e viene eliminata dal nodo.

È possibile ottenere le informazioni necessarie per il protocollo di registrazione di Configurazione stato da **Chiavi** in **Impostazioni account** nel portale di Azure. 

![Chiavi e URL di Automazione di Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- L'URL di registrazione è il campo URL nella pagina Chiavi.
- La chiave di registrazione è il valore del campo Chiave di **accesso primaria** o Chiave di **accesso secondaria** nella pagina Chiavi. È possibile usare una delle due chiavi.

Per una maggiore sicurezza, è possibile rigenerare le chiavi di accesso primarie e secondarie di un account di automazione in qualsiasi momento nella pagina Chiavi. La rigenerazione delle chiavi impedisce alle registrazioni future dei nodi di utilizzare chiavi precedenti.

## <a name="re-registering-a-node"></a>Registrazione nuovamente di un nodoRe-registering a node

Dopo aver registrato un computer come nodo DSC nella configurazione dello stato di automazione di Azure, potrebbe essere necessario registrare nuovamente tale nodo in futuro.

- **Rinnovo del certificato.** Per le versioni di Windows Server precedenti a Windows Server 2019, ogni nodo negozia automaticamente un certificato univoco per l'autenticazione che scade dopo un anno. Se un certificato scade senza rinnovo, il nodo non è `Unresponsive`in grado di comunicare con Automazione di Azure ed è contrassegnato come . Attualmente, il protocollo di registrazione DiPowerShell DSC non può rinnovare automaticamente i certificati quando stanno per scadere ed è necessario registrare nuovamente i nodi dopo un anno. Prima di eseguire nuovamente la registrazione, assicurarsi che ogni nodo esegua WMF 5 RTM. 

    La nuova registrazione eseguita 90 giorni o meno dall'ora di scadenza del certificato o in qualsiasi momento dopo l'ora di scadenza del certificato comporta la generazione e l'utilizzo di un nuovo certificato. Una soluzione a questo problema è inclusa in Windows Server 2019 e versioni successive.

- **Modifiche ai valori LCM DiSC.** Potrebbe essere necessario modificare i [valori di LCM di PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) impostati durante la registrazione iniziale del nodo, `ConfigurationMode`ad esempio . Attualmente, è possibile modificare questi valori dell'agente DSC solo tramite la nuova registrazione. L'unica eccezione è il valore Configurazione nodo assegnato al nodo. È possibile modificare direttamente questa impostazione in DSC di automazione di Azure.You can change this in Azure Automation DSC directly.

È possibile registrare nuovamente un nodo nello stesso modo in cui è stato registrato inizialmente il nodo, utilizzando uno dei metodi di onboarding descritti in questo documento. Non è necessario annullare la registrazione di un nodo dalla configurazione dello stato di automazione di Azure prima di registrarlo nuovamente.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Risoluzione dei problemi di caricamento di macchine virtuali di Azure

Configurazione stato di Automazione di Azure permette di caricare macchine virtuali Windows di Azure in tutta semplicità per la gestione della configurazione. In background viene usata l'estensione DSC (Desired State Configuration) per le macchine virtuali di Azure per registrare la macchina virtuale con Configurazione stato di Automazione di Azure. Poiché l'estensione Configurazione stato desiderato della macchina virtuale di Azure viene eseguita in modo asincrono, il rilevamento dello stato di avanzamento e la risoluzione dei problemi di esecuzione possono essere importanti.

> [!NOTE]
> Qualsiasi metodo di onboarding di una macchina virtuale di Windows di Azure per la configurazione dello stato di automazione di Azure che usa l'estensione Configurazione stato desiderato della macchina virtuale di Azure può richiedere fino a un'ora per Automazione di Azure per visualizzarlo come registrato. Questo ritardo è dovuto all'installazione di WMF 5 nella macchina virtuale dall'estensione Configurazione stato desiderato della macchina virtuale di Azure, necessaria per l'onboarding della macchina virtuale nella configurazione dello stato di automazione di Azure.This delay is due to the installation of WMF 5 on the VM by the Azure VM Desired State Configuration extension, which is required to onboard the VM to Azure Automation State Configuration.

Per risolvere i problemi o visualizzare lo stato dell'estensione Configurazione stato desiderato della macchina virtuale di Azure:To troubleshoot or view the status of the Azure VM Desired State Configuration extension:

1. Nel portale di Azure passare alla macchina virtuale in fase di onboarding.
2. Fare clic su **Estensioni** in **Impostazioni**. 
3. Ora selezionare **DSC** o **DSCForLinux**, a seconda del sistema operativo. 
4. Per altri dettagli, è possibile fare clic su **Visualizza stato dettagliato**.

Per altre informazioni sulla risoluzione dei problemi, vedere Risoluzione dei problemi relativi alla [configurazione dello stato desiderato (DSC) di Automazione di Azure](./troubleshoot/desired-state-configuration.md).For more information on troubleshooting, see Troubleshooting issues with Azure Automation Desired State Configuration (DSC) .

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere Introduzione alla configurazione dello stato di automazione di [Azure.](automation-dsc-getting-started.md)
- Per informazioni sulla compilazione di configurazioni DSC in modo da poterle assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione dello stato](automation-dsc-compile.md)di automazione di Azure.To learn about compiling DSC configurations so that you can assign them to target nodes, see Compilazioning configurations in Azure Automation State Configuration .
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per informazioni sui prezzi, vedere Prezzi per la configurazione dello stato di automazione di Azure.For pricing [information,](https://azure.microsoft.com/pricing/details/automation/)see Azure Automation State Configuration pricing .
- Per un esempio di utilizzo della configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere Esempio di utilizzo: distribuzione continua nelle macchine virtuali tramite Configurazione dello stato di automazione di [Azure e Chocolatey.For an](automation-dsc-cd-chocolatey.md)example of using Azure Automation State Configuration in a continuous deployment pipeline, see Usage Example: Continuous deployment to virtual machines Using Azure Automation State Configuration and Chocolatey .
