---
title: Computer in scheda per la gestione in base alla configurazione dello stato di automazione di AzureOnboard machines for management by Azure Automation State Configuration
description: Questo articolo descrive come configurare i computer per la gestione con la configurazione dello stato di automazione di Azure.This article describes how to set up machines for management with Azure Automation State Configuration.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406208"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Computer in scheda per la gestione in base alla configurazione dello stato di automazione di AzureOnboard machines for management by Azure Automation State Configuration

Configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired State Configuration) nei data center cloud o locali. È possibile accedere al servizio nel portale di Azure selezionando **Configurazione stato (DSC)** in **Gestione configurazione**. 

Con La configurazione dello stato di automazione di Azure, è possibile ottenere la scalabilità tra migliaia di computer in modo rapido e semplice da una posizione centrale e sicura. È possibile eseguire facilmente l'onboarding dei computer, assegnare loro configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni macchina allo stato specificato.

Il servizio Configurazione stato di automazione di Azure è per DSC quali runbook di Automazione di Azure sono per gli script di PowerShell.The Azure Automation State Configuration service is to DSC what Azure Automation runbooks are to PowerShell scripting. In altre parole, Automazione di Azure consente di gestire gli script di PowerShell e le configurazioni DSC. Per altre informazioni sui vantaggi dell'uso di Configurazione stato di Automazione di Azure, vedere [Panoramica di Configurazione stato di Automazione di Azure](automation-dsc-overview.md).

È possibile usare La configurazione dello stato di automazione di Azure per gestire un'ampia gamma di computer:You can use Azure Automation State Configuration to manage a variety of machines:

- Macchine virtuali di Azure
- Macchine virtuali di Azure (classica)
- Macchine Windows fisiche/virtuali in locale o in un cloud diverso da Azure (incluse le istanze di Amazon Web Services [AWS] Elastic Compute Cloud [EC2])
- Computer fisici/macchine virtuali Linux locali, in Azure o in un cloud diverso da Azure

Se non si è pronti a gestire la configurazione del computer dal cloud, è possibile usare la configurazione dello stato di automazione di Azure come endpoint solo report. Questa funzionalità consente di impostare o eseguire il push delle configurazioni tramite DSC e di visualizzare i dettagli dei report in Automazione di Azure.This feature allows you to set, or push, configurations through DSC and view reporting details in Azure Automation.

> [!NOTE]
> La gestione delle macchine virtuali di Azure con la configurazione dello stato di automazione di Azure è inclusa senza alcun costo aggiuntivo se l'estensione Configurazione stato desiderato della macchina virtuale di Azure è la versione 2.70 o successiva. Per ulteriori informazioni, vedere [Prezzi di automazione](https://azure.microsoft.com/pricing/details/automation/).

The following sections of this article outline how you can onboard the previously listed machines to Azure Automation State Configuration.

## <a name="onboard-azure-vms"></a>Caricare le macchine virtuali di Azure

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az per il ruolo di lavoro ibrido per runbook, vedere Installare il modulo di Azure PowerShell.For Az module installation instructions for your Hybrid Runbook Worker, see [Install the Azure PowerShell module.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Aggiorna moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

Poiché l'estensione di Azure viene eseguita in modo asincrono, vengono forniti i passaggi per tenere traccia dello stato di avanzamento o della risoluzione dei problemi nella sezione Risolvere i problemi di [onboarding](#troubleshoot-azure-virtual-machine-onboarding) delle macchine virtuali di Azure di questo articolo.

> [!NOTE]
> La distribuzione di DSC in un nodo Linux utilizza la cartella */tmp.* Moduli come `nxautomation` vengono scaricati temporaneamente per la verifica prima dell'installazione. Per assicurarsi che i moduli siano installati correttamente, l'agente di Log Analytics per Linux deve disporre delle autorizzazioni di lettura/scrittura per la cartella */tmp.*<br><br>
> L'agente log Analytics per Linux viene eseguito come utente *omsagent.* Per concedere l'autorizzazione di scrittura all'utente *omsagent,* eseguire il comando `setfacl -m u:omsagent:rwx /tmp`.

### <a name="onboard-vms-by-using-the-azure-portal"></a>Inchedicerare le macchine virtuali tramite il portale di AzureOnboard VMs by using the Azure portal

Per eseguire l'onboarding delle macchine virtuali di Azure nella configurazione dello stato di automazione di Azure tramite il portale di Azure:To onboard Azure VMs to Azure Automation State Configuration through the [Azure portal:](https://portal.azure.com/)

1. Passare all'account di Automazione di Azure in cui si vuole eseguire l'onboarding delle macchine virtuali. 

1. Nella pagina **Configurazione stato** selezionare la scheda **Nodi** e quindi **aggiungi**.

1. Scegliere una macchina virtuale per l'onboarding.

1. Se nel computer non è installata l'estensione Stato desiderato di PowerShell e lo stato di alimentazione è in esecuzione, selezionare **Connetti**.

1. In **Registrazione**immettere i valori di [Gestione configurazione locale di PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) necessari per il caso d'uso. Facoltativamente, è possibile immettere una configurazione di nodo da assegnare alla macchina virtuale.

![Riquadro di registrazione della macchina virtualeThe VM registration pane](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>Gestire le macchine virtuali usando i modelli di Azure Resource ManagerOnboard VMs by using Azure Resource Manager templates

È possibile distribuire e integrare una macchina virtuale in Configurazione stato automazione di Azure usando i modelli di Azure Resource Manager.You can deploy and onboard a VM to Azure Automation State Configuration by using Azure Resource Manager templates. Per un modello di esempio che si basa su una macchina virtuale esistente in Configurazione dello stato di automazione di Azure, vedere [Server gestito dal servizio Desired State Configuration.](https://azure.microsoft.com/resources/templates/101-automation-configuration/) Se si gestisce un set di scalabilità di macchine virtuali, vedere il modello di esempio in Configurazione del set di scalabilità di macchine virtuali gestito da Automazione di [Azure.If](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)you're managing a virtual machine scale set, see the example template in Virtual machine scale set configuration managed by Azure Automation .

### <a name="onboard-vms-by-using-powershell"></a>Eseguire l'onboarding delle macchine virtuali tramite PowerShellOnboard VMs by using PowerShell

È possibile usare il cmdlet Register-AzAutomationDscNode in PowerShell per l'onboarding delle macchine virtuali nella configurazione dello stato di automazione di Azure.You can use the [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet in PowerShell to onboard VMs to Azure Automation State Configuration. 

> [!NOTE]
>Il `Register-AzAutomationDscNode` cmdlet è attualmente implementato solo per i computer che eseguono Windows, perché il cmdlet attiva solo l'estensione di Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrare le macchine virtuali tra le sottoscrizioni di AzureRegister VMs across Azure subscriptions

Il modo migliore per registrare macchine virtuali da altre sottoscrizioni di Azure consiste nell'usare l'estensione DSC in un modello di distribuzione di Azure Resource Manager.The best way to register VMs from other Azure subscriptions is to use the DSC extension in an Azure Resource Manager deployment template. Esempi sono disponibili [nell'estensione Configurazione stato desiderato con](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)i modelli di Azure Resource Manager .

Per trovare la chiave di registrazione e l'URL di registrazione da utilizzare come parametri nel modello, vedere la sezione [Integrata tramite registrazione](#onboard-securely-by-using-registration) in questo articolo.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Sistemazioni Windows fisiche/virtuali in locale o in un cloud diverso da AzureOnboard physical/virtual Windows machines on-premises, or in a cloud other than Azure

È possibile eseguire l'onboarding di server Windows in esecuzione in locale o in altri ambienti cloud, incluse le istanze di AWS EC2, alla configurazione dello stato di automazione di Azure.You can onboard Windows servers that are running on-premises or in other cloud environments, including AWS EC2 instances, to Azure Automation State Configuration. I server devono disporre [dell'accesso in uscita ad Azure.](automation-dsc-overview.md#network-planning)

1. Assicurarsi che la versione più recente di Windows Management Framework 5 sia installata nei computer da installare in sistema per la configurazione dello stato di automazione di Azure.Make sure that the latest version of [Windows Management Framework 5](https://aka.ms/wmf5latest) is installed on the machines to be onboarded to Azure Automation State Configuration. Inoltre, Windows Management Framework 5 deve essere installato nel computer che si sta utilizzando per l'operazione di onboarding.
1. Per creare una cartella contenente le metaconfigurazioni DSC necessarie, seguire le istruzioni nella sezione [Generare metaconfigurazioni DSC.](#generate-dsc-metaconfigurations) 
1. Per applicare le metaconfigurazioni di PowerShell DSC in remoto ai computer che si desidera eseguire l'onboarding, utilizzare il cmdlet seguente: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in remoto, copiare la cartella delle **metaconfigurazioni** nei computer in cui si esegue l'onboarding. Aggiungere quindi il codice per chiamare [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) localmente nei computer.

1. Nel portale di Azure o usando i cmdlet verificare che i computer da eseguire l'onboarding vengano visualizzati come nodi di configurazione dello stato registrati nell'account di Automazione di Azure.In the Azure portal or by using cmdlets, verify that the machines to onboard appear as State Configuration nodes registered in your Azure Automation account.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Onboarding di macchine Linux fisiche/virtuali in locale o in un cloud diverso da AzureOnboard physical/virtual Linux machines on-premises, or in a cloud other than Azure

È possibile eseguire l'onboarding di server Linux in esecuzione in locale o in altri ambienti cloud per la configurazione dello stato di automazione di Azure.You can onboard Linux servers running on-premises or in other cloud environments to Azure Automation State Configuration. I server devono disporre [dell'accesso in uscita ad Azure.](automation-dsc-overview.md#network-planning)

1. Assicurarsi che la versione più recente di [PowerShell Desired State Configuration per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) sia installata nei computer per l'onboarding alla configurazione dello stato di automazione di Azure.Make sure that the latest version of PowerShell Desired State Configuration for Linux is installed on the machines to be onboarded to Azure Automation State Configuration.
1. Se le impostazioni predefinite di PowerShell DSC LCM corrispondono al caso d'uso e si vuole eseguire l'onboarding dei computer in modo che estraggano e segnalino alla configurazione dello stato di automazione di Azure, eseguire le operazioni seguenti:If the [PowerShell DSC LCM defaults](/powershell/scripting/dsc/managing-nodes/metaConfig4) match your use case, and you want to onboard machines so that they both pull from and report to Azure Automation State Configuration, do the following:

   a. In ogni computer Linux per l'onboarding `Register.py` alla configurazione dello stato di automazione di Azure, usare per eseguire l'onboarding usando le impostazioni predefinite di Gestione configurazione locale di PowerShell DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. Per trovare la chiave di registrazione e l'URL di registrazione per l'account Automation, vedere la sezione [Onboard in modo sicuro tramite la registrazione](#onboard-securely-by-using-registration) di questo articolo.  
   c. Andare al passaggio 4.
   
1. Se le impostazioni predefinite di PowerShell DSC LCM non corrispondono al caso d'uso o se si desidera eseguire l'onboarding di computer che segnalano solo la configurazione dello stato di automazione di Azure, seguire i passaggi a-d. In caso contrario, procedere direttamente al passaggio d.

    a. Per produrre una cartella contenente le metaconfigurazioni DSC necessarie, seguire le istruzioni nella sezione [Generare metaconfigurazioni DSC.](#generate-dsc-metaconfigurations)

    b. Assicurarsi che la versione più recente di [Windows Management Framework 5](https://aka.ms/wmf5latest) sia installata nel computer utilizzato per l'onboarding.

    c. Per applicare le metaconfigurazioni di PowerShell DSC in remoto ai computer che si desidera eseguire l'onboarding, aggiungere il codice seguente:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in remoto, copiare le metaconfigurazioni corrispondenti ai computer remoti dalla cartella descritta nel passaggio 3a ai computer Linux.

1. Aggiungere il codice per chiamare *Set-DscLocalConfigurationManager.py* in locale in ogni computer Linux da integrare nella configurazione dello stato di automazione di Azure.Add code to call locally on each Linux machine to be onboarded to Azure Automation State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Usando il portale o i cmdlet di Azure, verificare che i computer da collegare ora vengano visualizzati come nodi DSC registrati nell'account di Automazione di Azure.By using the Azure portal or cmdlets, ensure that the machines to onboard now show up as DSC nodes registered in your Azure Automation account.

## <a name="generate-dsc-metaconfigurations"></a>Generare metaconfigurazioni DSC

Per eseguire l'onboarding di qualsiasi computer alla configurazione dello stato di automazione di Azure, è possibile generare una [metaconfigurazione DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Questa configurazione indica all'agente DSC di eseguire il pull o creare report per la configurazione dello stato di automazione di Azure.This configuration tells the DSC agent to pull from or report to Azure Automation State Configuration. È possibile generare una metaconfigurazione DSC per la configurazione dello stato di automazione di Azure usando una configurazione DSC di PowerShell o i cmdlet di Azure Automation PowerShell.You can generate a DSC metaconfiguration for Azure Automation State Configuration by using either a PowerShell DSC configuration or the Azure Automation PowerShell cmdlets.

> [!NOTE]
> Le metaconfigurazioni DSC contengono i segreti necessari per l'onboarding di un computer in un account di automazione per la gestione. Assicurarsi di proteggere correttamente tutte le metaconfigurazioni DSC create o eliminarle dopo l'uso.

Il supporto proxy per le metaconfigurazioni è controllato da Gestione configurazione locale, ovvero il motore DSC di Windows PowerShell. Gestione configurazione locale viene eseguito su tutti i nodi di destinazione ed è responsabile della chiamata delle risorse di configurazione incluse in uno script di metaconfigurazione DSC. 

È possibile includere il `ConfigurationRepositoryWeb`supporto proxy in una metaconfigurazione includendo le definizioni dell'URL del proxy e della credenziale proxy in base alle esigenze nei blocchi , `ResourceRepositoryWeb`e `ReportServerWeb` . Vedere [Configurare Gestione configurazione locale](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>Generare metaconfigurazioni DSC utilizzando una configurazione DSC

1. Aprire Visual Studio Code (o l'editor preferito) come amministratore in un computer nell'ambiente locale. Nel computer deve essere installata la versione più recente di [Windows Management Framework 5.](https://aka.ms/wmf5latest)
1. Copiare localmente lo script seguente. Questo script contiene una configurazione DSC di PowerShell per la creazione delle metaconfigurazioni e un comando per avviare la creazione delle metaconfigurazioni.

    > [!NOTE]
    > I nomi di configurazione del nodo di configurazione dello stato fanno distinzione tra maiuscole e minuscole nel portale di Azure.State Configuration Node Configuration names are case-sensitive in the Azure portal. Se il caso non corrisponde, il nodo non verrà visualizzato nella scheda **Nodi.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
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

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. Specificare il codice di registrazione e l'URL per l'account di automazione, nonché i nomi dei computer da caricare. Tutti gli altri parametri sono facoltativi. Per trovare la chiave di registrazione e l'URL di registrazione per l'account Automation, vedere la sezione In linea di [bordo tramite registrazione.](#onboard-securely-by-using-registration)

1. Se si vuole che i computer segnalino le informazioni sullo stato DSC alla `ReportOnly` configurazione dello stato di automazione di Azure, ma non alla configurazione pull o ai moduli di PowerShell, impostare il parametro su *true*.

1. Se `ReportOnly` non è impostato, i computer segnalano le informazioni sullo stato DSC alla configurazione dello stato di automazione di Azure ed estraggono la configurazione o i moduli di PowerShell.If is not set, the machines report DSC status information to Azure Automation State Configuration and pull configuration or PowerShell modules. Impostare i parametri `ConfigurationRepositoryWeb` `ResourceRepositoryWeb`di `ReportServerWeb` conseguenza nei blocchi , e .

1. Eseguire lo script. A questo punto dovrebbe essere contenuta una cartella di directory di lavoro denominata *DscMetaConfigs*, che contiene le metaconfigurazioni di PowerShell DSC per i computer da eseguire l'onboarding (come amministratore).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>Generare metaconfigurazioni DSC utilizzando i cmdlet di Automazione di AzureGenerate DSC metaconfigurations by using Azure Automation cmdlets

Se le impostazioni predefinite di PowerShell DSC LCM corrispondono al caso d'uso e si vuole integrare i computer per eseguire il pull e il report alla configurazione dello stato di automazione di Azure, è possibile generare le metaconfigurazioni DSC necessarie in modo più semplice usando i cmdlet di Automazione di Azure.

1. Aprire la console di PowerShell o Visual Studio Code come amministratore in un computer nell'ambiente locale.
1. Connettersi ad Azure Resource Manager usando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
1. Dall'account di automazione in cui si stanno configurando i nodi, scaricare le metaconfigurazioni di PowerShell DSC per i computer che si desidera eseguire l'onboarding.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. A questo punto si dovrebbe avere una cartella *DscMetaConfigs* che contiene le metaconfigurazioni di PowerShell DSC per i computer che si desidera eseguire l'onboarding (come amministratore).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>Abordo sicuro utilizzando la registrazione

I computer possono essere caricati in modo sicuro in un account di Automazione di Azure tramite il protocollo di registrazione DSC di Windows Management Framework 5. Questo protocollo consente a un nodo DSC di eseguire l'autenticazione in un server di report o pull DSC di PowerShell, inclusa la configurazione dello stato di automazione di Azure.This protocol allows a DSC node to authenticate to a PowerShell DSC pull or report server, including Azure Automation State Configuration. Il nodo si registra con il server all'URL di registrazione e autentica utilizzando una chiave di registrazione. 

Durante la registrazione, il nodo DSC e il server di pull/report DSC negoziano un certificato univoco per il nodo da utilizzare per l'autenticazione al server dopo la registrazione. Questo processo impedisce ai nodi con connessione di rappresentare l'un l'altro (ad esempio, se un nodo viene compromesso e si comporta in modo dannoso). 

Dopo la registrazione, la chiave di registrazione non viene riutilizzata per l'autenticazione e viene eliminata dal nodo.

Per ottenere le informazioni necessarie per il protocollo di registrazione della configurazione dello stato, passare al portale di Azure e in **Impostazioni account**selezionare **Chiavi**. 

![Chiavi e URL di automazione di Azure nel riquadro ChiaviAzure automation keys and URL in the Keys pane](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **URL di registrazione**: il valore nella casella **URL.**
- **Chiave di registrazione**: il valore nella casella Chiave di **accesso primaria** o Chiave di **accesso secondaria.** È possibile utilizzare entrambe le chiavi.

Per una maggiore sicurezza, è possibile rigenerare le chiavi di accesso primarie e secondarie di un account di automazione in qualsiasi momento nel riquadro **Chiavi.** La rigenerazione delle chiavi impedisce alle registrazioni future dei nodi di utilizzare chiavi precedenti.

## <a name="re-register-a-node"></a>Registrare nuovamente un nodoRe-register a node

Dopo aver registrato un computer come nodo DSC nella configurazione dello stato di automazione di Azure, potrebbe essere necessario registrare nuovamente tale nodo in futuro per uno dei motivi seguenti:After you register a machine as a DSC node in Azure Automation State Configuration, you might need to re-register that node in the future for either of the following reasons:

- **Rinnovo del certificato:** per le versioni di Windows Server precedenti a Windows Server 2019, ogni nodo negozia automaticamente un certificato univoco per l'autenticazione che scade dopo un anno. Se un certificato scade senza rinnovo, il nodo non è in grado di comunicare con Automazione di Azure ed è contrassegnato come *Non risponde*. 

  Attualmente, il protocollo di registrazione DiPowerShell DSC non può rinnovare automaticamente i certificati quando stanno per scadere ed è necessario registrare nuovamente i nodi dopo un anno. Prima di registrare nuovamente, assicurarsi che ogni nodo esegua Windows Management Framework 5 RTM. 

    La nuova registrazione ha eseguito 90 o meno giorni dall'ora di scadenza del certificato o in qualsiasi momento dopo l'ora di scadenza del certificato comporta la generazione e l'utilizzo di un nuovo certificato. Una soluzione a questo problema è inclusa in Windows Server 2019 e versioni successive.

- **Modifiche ai valori di LCM DSC:** potrebbe essere necessario modificare i [valori di PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) impostati durante la registrazione iniziale del nodo, `ConfigurationMode`ad esempio . Attualmente, è possibile modificare questi valori dell'agente DSC solo tramite la nuova registrazione. L'unica eccezione è il valore Configurazione nodo assegnato al nodo. È possibile modificare direttamente questo valore in Automazione di Azure DSC.You can change this value in Azure Automation DSC directly.

È possibile registrare nuovamente un nodo come è stato registrato inizialmente, utilizzando uno dei metodi di onboarding descritti in questo documento. Non è necessario annullare la registrazione di un nodo dalla configurazione dello stato di automazione di Azure prima di registrarlo nuovamente.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Risolvere i problemi di onboarding delle macchine virtuali di AzureTroubleshoot Azure virtual machine onboarding

Configurazione stato di Automazione di Azure permette di caricare macchine virtuali Windows di Azure in tutta semplicità per la gestione della configurazione. In background viene usata l'estensione DSC (Desired State Configuration) per le macchine virtuali di Azure per registrare la macchina virtuale con Configurazione stato di Automazione di Azure. Poiché l'estensione Configurazione stato desiderato della macchina virtuale di Azure viene eseguita in modo asincrono, può essere importante tenere traccia dello stato di avanzamento e risoluzione dei problemi di esecuzione.

> [!NOTE]
> Qualsiasi metodo di onboarding di una macchina virtuale di Windows di Azure per la configurazione dello stato di automazione di Azure che usa l'estensione Configurazione stato desiderato della macchina virtuale di Azure può richiedere fino a un'ora per Automazione di Azure per visualizzarlo come registrato. Questo ritardo deriva dall'installazione di Windows Management Framework 5 nella macchina virtuale dall'estensione Configurazione stato desiderato della macchina virtuale di Azure, necessaria per l'onboarding della macchina virtuale nella configurazione dello stato di automazione di Azure.This delay results from the installation of Windows Management Framework 5 on the VM by the Azure VM Desired State Configuration extension, which is required to onboard the VM to Azure Automation State Configuration.

Per risolvere i problemi o visualizzare lo stato dell'estensione Configurazione stato desiderato della macchina virtuale di Azure:To troubleshoot or view the status of the Azure VM Desired State Configuration extension:

1. Nel portale di Azure passare alla macchina virtuale in fase di onboarding.
2. In **Impostazioni** selezionare **Estensioni**. 
3. Selezionare **DSC** o **DSCForLinux**, a seconda del sistema operativo. 
4. Per ulteriori informazioni sull'estensione, selezionare **Visualizza stato dettagliato**.

Per altre informazioni sulla risoluzione dei problemi, vedere Risolvere i problemi relativi alla configurazione dello stato desiderato [(DSC) di Automazione di Azure.For](./troubleshoot/desired-state-configuration.md)more information about troubleshooting, see Troubleshoot issues with Azure Automation Desired State Configuration (DSC) .

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere Introduzione alla configurazione dello stato di automazione di [Azure.](automation-dsc-getting-started.md)
- Per informazioni sulla compilazione di configurazioni DSC in modo da poterle assegnare ai nodi di destinazione, vedere Compilazione delle [configurazioni in Configurazione dello stato](automation-dsc-compile.md)di automazione di Azure.To learn about compiling DSC configurations so that you can assign them to target nodes, see Compile configurations in Azure Automation State Configuration .
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Per informazioni sui prezzi, vedere Prezzi per la configurazione dello stato di automazione di Azure.For pricing [information,](https://azure.microsoft.com/pricing/details/automation/)see Azure Automation State Configuration pricing .
- Per un esempio di utilizzo della configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere Distribuzione continua nelle macchine virtuali con Configurazione dello stato di automazione di [Azure e Chocolatey](automation-dsc-cd-chocolatey.md).
