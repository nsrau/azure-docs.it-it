---
title: Eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali | Microsoft Docs
description: Questo articolo descrive come eseguire l'onboarding e la configurazione di Monitoraggio di Azure per le macchine virtuali per ottenere informazioni sulle prestazioni di un'applicazione distribuita e sui problemi di integrità identificati.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: e5421ca791ae9d0059639000f33b77be57f4d891
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968034"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>Come eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali 
Questo articolo descrive come configurare Monitoraggio di Azure per le macchine virtuali per monitorare l'integrità del sistema operativo delle macchine virtuali di Azure e individuare e mappare le dipendenze delle applicazioni ospitate in tali macchine.  

Per abilitare Monitoraggio di Azure per le macchine virtuali è possibile usare uno dei metodi seguenti. Più avanti nell'articolo vengono fornite informazioni dettagliate sull'uso di ogni metodo.  

* Una singola macchina virtuale selezionando **Insights (anteprima)** direttamente dalla macchina virtuale.
* Più macchine virtuali di Azure usando Criteri di Azure per garantire che le macchine virtuali nuove ed esistenti valutate abbiano le dipendenze necessarie installate e siano configurate correttamente.  Le macchine virtuali non conformi vengono segnalate per permettere di scegliere il rimedio appropriato in base a ciò che non è conforme.  
* Più macchine virtuali di Azure o set di scalabilità di macchine virtuali in una sottoscrizione o un gruppo di risorse specificato, usando PowerShell.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare di disporre di quanto descritto nelle sottosezioni seguenti.

### <a name="log-analytics"></a>Log Analytics 

Un'area di lavoro di Log Analytics nelle aree attualmente supportate indicate di seguito:

  - Stati Uniti centro-occidentali  
  - Stati Uniti orientali  
  - Europa occidentale  
  - Asia sud-orientale<sup>1</sup>  

<sup>1</sup> Quest'area attualmente non supporta la funzionalità di integrità di Monitoraggio di Azure per le macchine virtuali   

Se non si ha un'area di lavoro, è possibile crearla tramite [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) o il [portale di Azure](../log-analytics/log-analytics-quick-create-workspace.md).  

Per abilitare la soluzione, è necessario essere membri del ruolo Collaboratore di Log Analytics. Per altre informazioni su come controllare l'accesso a un'area di lavoro di Log Analytics, vedere [Gestire le aree di lavoro](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Le versioni seguenti dei sistemi operativi Windows e Linux sono ufficialmente supportate con Monitoraggio di Azure per le macchine virtuali:

|Versione del sistema operativo |Prestazioni |Mappe |Integrità |  
|-----------|------------|-----|-------|  
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X| X |  
|Cent OS Linux 7, 6 | X | X| X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> La funzionalità relativa alle prestazioni di Monitoraggio di Azure per le macchine virtuali è disponibile solo da Monitoraggio di Azure e non quando si accede direttamente dal riquadro a sinistra della macchina virtuale.  

### <a name="hybrid-environment-connected-sources"></a>Origini connesse dell'ambiente ibrido
La mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. Dependency Agent dipende dall'agente di Log Analytics per la connessione a Log Analytics. Ciò significa che in un sistema l'agente di Log Analytics deve essere installato e configurato con Dependency Agent.  La tabella seguente descrive le origini connesse supportate dalla funzionalità di mappa in un ambiente ibrido.

| Origine connessa | Supportato | DESCRIZIONE |
|:--|:--|:--|
| Agenti di Windows | Yes | Oltre all'[agente di Log Analytics per Windows](../log-analytics/log-analytics-concept-hybrid.md), gli agenti Windows richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Agenti Linux | Yes | Oltre all'[agente di Log Analytics per Linux](../log-analytics/log-analytics-concept-hybrid.md), gli agenti Linux richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No  | |  

In Windows, Microsoft Monitoring Agent (MMA) viene usato sia da System Center Operations Manager che da Log Analytics per raccogliere e inviare dati di monitoraggio. System Center Operations Manager e Log Analytics offrono versioni diverse dell'agente pronte all'uso. Ognuna di queste versioni può inviare segnalazioni a System Center Operations Manager, Log Analytics o entrambi.  

In Linux, l'agente di Log Analytics per Linux raccoglie e invia i dati di monitoraggio a Log Analytics.   

Se i computer Windows o Linux in uso non possono connettersi direttamente al servizio, è necessario configurare l'agente di Log Analytics per la connessione a Log Analytics tramite il gateway OMS. Per altre informazioni su come distribuire e configurare il gateway OMS, vedere [Connettere computer senza accesso a Internet tramite il gateway OMS](../log-analytics/log-analytics-oms-gateway.md).  

È possibile scaricare Dependency Agent dalla posizione seguente.

| File | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.5.0 | 8B8FE0F6B0A9F589C4B7B52945C2C25DF008058EB4D4866DC45EE2485062C9D7 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.5.1 | 09D56EF43703A350FF586B774900E1F48E72FE3671144B5C99BB1A494C201E9E |

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo
Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per garantire e migliorare la qualità, la sicurezza e l'integrità del servizio. Per offrire capacità di risoluzione dei problemi accurate ed efficienti, i dati della funzionalità di mappa includono informazioni sulla configurazione del software, come il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>Contatori delle prestazioni abilitati
Monitoraggio di Azure per le macchine virtuali configura un'area di lavoro di Log Analytics per raccogliere i contatori delle prestazioni usati dalla soluzione.  La tabella seguente elenca gli oggetti e i contatori configurati dalla soluzione che vengono raccolti ogni 60 secondi.

### <a name="windows-performance-counters"></a>Contatori delle prestazioni di Windows

|Nome dell'oggetto |Nome contatore |  
|------------|-------------|  
|LogicalDisk |% Free Space |  
|LogicalDisk |Avg. Disk sec/Read |  
|LogicalDisk |Avg. Disk sec/Transfer |  
|LogicalDisk |Avg. Disk sec/Write |  
|LogicalDisk |Byte da/a disco/sec |  
|LogicalDisk |Byte letti da disco/sec  |  
|LogicalDisk |Letture disco/sec  |  
|LogicalDisk |Disk Transfers/sec |  
|LogicalDisk |Byte scritti su disco/sec |  
|LogicalDisk |Scritture disco/sec |  
|LogicalDisk |Free Megabytes |  
|Memoria |MByte disponibili |  
|Scheda di rete |Byte ricevuti/sec |  
|Scheda di rete |Byte inviati/sec |  
|Processore |% di tempo processore |  

### <a name="linux-performance-counters"></a>Contatori delle prestazioni di Linux

|Nome dell'oggetto |Nome contatore |  
|------------|-------------|  
|Logical Disk |% Used Space |  
|Logical Disk |Byte letti da disco/sec  |  
|Logical Disk |Letture disco/sec  |  
|Logical Disk |Disk Transfers/sec |  
|Logical Disk |Byte scritti su disco/sec |  
|Logical Disk |Scritture disco/sec |  
|Logical Disk |Free Megabytes |  
|Logical Disk |Logical Disk Bytes/sec |  
|Memoria |Available MBytes Memory |  
|Rete |Total Bytes Received |  
|Rete |Total Bytes Transmitted |  
|Processore |% di tempo processore |  

## <a name="enable-from-the-azure-portal"></a>Eseguire l'abilitazione dal portale di Azure
Per abilitare il monitoraggio della macchina virtuale di Azure nel portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare **Macchine virtuali**. 
2. Selezionare una macchina virtuale dall'elenco. 
3. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)**.
4. Nella pagina **Insights (anteprima)** selezionare **Prova adesso**.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. Se nella pagina **Azure Monitor Insights Onboarding** (Onboarding di Insights per Monitoraggio di Azure) è già presente un'area di lavoro di Log Analytics nella stessa sottoscrizione, selezionarla nell'elenco a discesa.  Nell'elenco sono preselezionate l'area di lavoro e la località predefinite in cui è distribuita la macchina virtuale nella sottoscrizione. 

    >[!NOTE]
    >Se si vuole creare una nuova area di lavoro di Log Analytics per archiviare i dati di monitoraggio della macchina virtuale, seguire le istruzioni in [Creare un'area di lavoro di Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Assicurarsi di creare l'area di lavoro nella stessa sottoscrizione in cui è distribuita la macchina virtuale. 

Dopo aver abilitato il monitoraggio, possono essere necessari circa 10 minuti prima di poter visualizzare le metriche relative all'integrità per la macchina virtuale. 

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>Eseguire l'abilitazione con Criteri di Azure
Per abilitare la soluzione per più macchine virtuali di Azure in modo da garantire conformità coerente e abilitazione automatica per le nuove macchine virtuali di cui viene effettuato il provisioning, è consigliabile usare [Criteri di Azure](../azure-policy/azure-policy-introduction.md).  L'uso di Criteri di Azure con i criteri forniti offre i vantaggi seguenti per le nuove macchine virtuali:

* Abilitare Monitoraggio di Azure per le macchine virtuali per ogni macchina virtuale nell'ambito definito
* Distribuire l'agente di Log Analytics 
* Distribuire Dependency Agent per individuare le dipendenze delle applicazioni e visualizzarle nella mappa
* Controllare se l'immagine del sistema operativo della macchina virtuale di Azure è inclusa in un elenco predefinito nella definizione dei criteri  
* Controllare se la macchina virtuale di Azure sta accedendo a un'area di lavoro diversa da quella specificata
* Creare report con i risultati relativi alla conformità 
* Supportare le correzioni per le macchine virtuali non conformi

Per l'attivazione per il tenant, il processo richiede:

- Configurare un'area di lavoro di Log Analytics seguendo la procedura illustrata in questo articolo
- Importare la definizione dell'iniziativa nel tenant (a livello di sottoscrizione o di gruppo di gestione)
- Assegnare i criteri all'ambito desiderato
- Esaminare i risultati relativi alla conformità

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>Aggiungere i criteri e l'iniziativa alla sottoscrizione
Per usare i criteri, è possibile usare uno script di PowerShell fornito ([Add-VMInsightsPolicy.ps1](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2)) disponibile in Azure PowerShell Gallery per il completamento di questa attività. Lo script aggiunge i criteri e un'iniziativa alla sottoscrizione.  Seguire questa procedura per configurare Criteri di Azure nella sottoscrizione. 

1. Scaricare lo script di PowerShell nel file system locale.

2. Usare il comando di PowerShell seguente nella cartella per aggiungere i criteri. Lo script supporta i parametri facoltativi seguenti: 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >Nota: se si prevede di assegnare l'iniziativa o i criteri a più sottoscrizioni, le definizioni devono essere archiviate nel gruppo di gestione che contiene le sottoscrizioni a cui verranno assegnati i criteri. È quindi necessario usate il parametro -ManagementGroupID.
    >
   
    Esempio senza parametri: `.\Add-VMInsightsPolicy.ps1`

### <a name="create-a-policy-assignment"></a>Creare un'assegnazione dei criteri
Dopo avere eseguito lo script di PowerShell `Add-VMInsightsPolicy.ps1`, vengono aggiunti l'iniziativa e i criteri seguenti:

* **Deploy Log Analytics Agent for Windows VMs - Preview** (Distribuisci agente di Log Analytics per VM Windows - Anteprima)
* **Deploy Log Analytics Agent for Linux VMs - Preview** (Distribuisci agente di Log Analytics per VM Linux - Anteprima)
* **Deploy Dependency Agent for Windows VMs - Preview** (Distribuisci Dependency Agent per VM Windows - Anteprima)
* **Deploy Dependency Agent for Linux VMs - Preview** (Distribuisci Dependency Agent per VM Linux - Anteprima)
* **Audit Log Analytics Agent Deployment - VM Image (OS) unlisted - Preview** (Controlla distribuzione agente di Log Analytics - Immagine VM (sistema operativo) non pubblicata)
* **Audit Dependency Agent Deployment - VM Image (OS) unlisted - Preview** (Controlla distribuzione Dependency Agent - Immagine VM (sistema operativo) non pubblicata)

Viene aggiunto il parametro di iniziativa seguente:

- **Area di lavoro di Log Analytics** (se si applica un'assegnazione con PowerShell o l'interfaccia della riga di comando, è necessario specificare il valore di ResourceID dell'area di lavoro)

    Per le macchine virtuali che risultano non conformi dai criteri di controllo **VMs not in OS scope** (VM non nell'ambito del sistema operativo), i criteri di distribuzione includono solo le macchine virtuali distribuite da immagini di macchine virtuali di Azure note. Controllare nella documentazione se il sistema operativo della macchina virtuale è supportato o meno.  Se non lo è, sarà necessario duplicare i criteri di distribuzione e aggiornarli o modificarli per fare in modo che l'immagine rientri nell'ambito.

Vengono aggiunti i criteri facoltativi autonomi seguenti:

- **VM is configured for mismatched Log Analytics Workspace - Preview** (VM configurata per area di lavoro di Log Analytics non corrispondente - Anteprima)

    Questi criteri possono essere usati per identificare le macchine virtuali già configurate con l'[estensione di macchina virtuale di Log Analytics](../virtual-machines/extensions/oms-windows.md), ma con un'area di lavoro diversa da quella prevista (in base a quanto indicato dall'assegnazione dei criteri). Questi criteri accettano un parametro per WorkspaceID.

Con questa versione iniziale, è possibile creare un'assegnazione dei criteri solo dal portale di Azure. Per informazioni su come completare questi passaggi, vedere [Create a policy assignment from the Azure portal](../azure-policy/assign-policy-definition.md) (Creare un'assegnazione dei criteri dal portale di Azure).

## <a name="enable-with-powershell"></a>Eseguire l'abilitazione con PowerShell
Per abilitare Monitoraggio di Azure per le macchine virtuali per più macchine virtuali o set di scalabilità di macchine virtuali, è possibile usare uno script di PowerShell fornito ([Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)) disponibile in Azure PowerShell Gallery per il completamento di questa attività.  Lo script eseguirà l'iterazione in ogni macchina virtuale e set di scalabilità di macchine virtuali nella sottoscrizione, nel gruppo di risorse incluso nell'ambito specificato da *ResourceGroup* o in una singola macchina virtuale o un singolo set di scalabilità specificato da *Name*.  Per ogni macchina virtuale o set di scalabilità di macchine virtuali, lo script verifica se l'estensione della macchina virtuale è già installata e, in caso negativo, cerca di reinstallarla.  In caso contrario, procede all'installazione delle estensioni della macchina virtuale dell'agente di Log Analytics e di Dependency Agent.   

Questo script richiede il modulo Azure PowerShell versione 5.7.0 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

Per ottenere informazioni sullo script, è possibile eseguire `Get-Help` per visualizzare un elenco di dettagli sugli argomenti con esempi di utilizzo.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

L'esempio seguente illustra l'uso dei comandi di PowerShell nella cartella per abilitare Monitoraggio di Azure per le macchine virtuali e comprendere l'output previsto:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>Eseguire l'abilitazione per l'ambiente ibrido
Questa sezione illustra come eseguire l'onboarding di macchine virtuali o computer fisici ospitati in un data center o in un altro ambiente cloud per il monitoraggio tramite Monitoraggio di Azure per le macchine virtuali.  

Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali non trasmette dati e non richiede modifiche ai firewall o alle porte. I dati nella mappa vengono sempre trasmessi dall'agente di Log Analytics al servizio Monitoraggio di Azure, direttamente o tramite il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md), se i criteri di sicurezza IT non permettono la connessione dei computer in rete a Internet.

Esaminare i requisiti e i metodi di distribuzione per l'[agente di Log Analytics per Linux e Windows](../log-analytics/log-analytics-concept-hybrid.md).

Riepilogo dei passaggi:

1. Installare l'agente di Log Analytics per Windows o Linux
2. Installare Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali
3. Abilitare la raccolta dei contatori delle prestazioni
4. Eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali

### <a name="install-the-dependency-agent-on-windows"></a>Installare Dependency Agent in Windows 
Dependency Agent può essere installato manualmente nei computer Windows eseguendo `InstallDependencyAgent-Windows.exe`. Se si esegue questo file eseguibile senza opzioni, si avvia una procedura guidata che consente di completare l'installazione in modo interattivo.  

>[!NOTE]
>Per installare o disinstallare l'agente sono necessari i privilegi di amministratore.

La tabella seguente illustra i parametri specifici supportati dal programma di installazione per l'agente dalla riga di comando.  

| Parametro | DESCRIZIONE |
|:--|:--|
| /? | Restituisce un elenco delle opzioni della riga di comando. |
| /S | Esegue un'installazione invisibile all'utente, senza interazione da parte dell'utente. |

Per eseguire, ad esempio, il programma di installazione con il parametro `/?`, digitare `InstallDependencyAgent-Windows.exe /?`

I file di Dependency Agent per Windows vengono installati in `C:\Program Files\Microsoft Dependency Agent` per impostazione predefinita.  Se Dependency Agent non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory dei log è `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Installare Dependency Agent in Linux
Dependency Agent viene installato nei server Linux tramite `InstallDependencyAgent-Linux64.bin`, uno script shell con un file binario autoestraente. È possibile eseguire il file con `sh` oppure aggiungere autorizzazioni di esecuzione al file stesso.

>[!NOTE]
> Per installare o configurare l'agente è necessario l'accesso alla radice.
> 

| Parametro | DESCRIZIONE |
|:--|:--|
| -help | Ottenere un elenco delle opzioni della riga di comando. |
| -s | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| --check | Controllare le autorizzazioni e il sistema operativo senza installare l'agente. |

Per eseguire, ad esempio, il programma di installazione con il parametro `-help`, digitare `InstallDependencyAgent-Linux64.bin -help`.

Installare Dependency Agent per Linux come utente ROOT eseguendo il comando `sh InstallDependencyAgent-Linux64.bin`
    
Se Dependency Agent non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Linux la directory dei log è `/var/opt/microsoft/dependency-agent/log`.

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti:

| File | Località |
|:--|:--|
| File core | /opt/microsoft/dependency-agent |
| File di log | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| File eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Abilitare i contatori delle prestazioni
Se l'area di lavoro di Log Analytics a cui fa riferimento la soluzione non è configurata per la raccolta dei contatori delle prestazioni richiesti dalla soluzione, sarà necessario abilitare i contatori. Questa operazione può essere eseguita manualmente come descritto [qui](../log-analytics/log-analytics-data-sources-performance-counters.md) oppure scaricando ed eseguendo uno script di PowerShell disponibile in [Azure Powershell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali
Questo metodo include un modello JSON che specifica la configurazione per abilitare i componenti della soluzione per l'area di lavoro di Log Analytics.  

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Creare ed eseguire un modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Salvare il file come **installsolutionsforvminsights.json** in una cartella locale.
3. Modificare i valori di **WorkspaceName**, **ResourceGroupName** e **WorkspaceLocation**.  Il valore di **WorkspaceName** è l'ID risorsa completo dell'area di lavoro di Log Analytics, che include il nome dell'area di lavoro, e il valore di **WorkspaceLocation** è l'area in cui è definita l'area di lavoro.
4. È possibile distribuire il modello usando il comando di PowerShell seguente:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

    ```powershell
    provisioningState       : Succeeded
    ```
Dopo avere abilitato il monitoraggio, possono essere necessari circa 10 minuti prima di poter visualizzare lo stato di integrità e le metriche per il computer ibrido. 

## <a name="next-steps"></a>Passaggi successivi

Con il monitoraggio abilitato per la macchina virtuale, le informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali.  Per informazioni su come usare la funzionalità di integrità, vedere [View Azure Monitor for VMs Health](monitoring-vminsights-health.md) (Visualizzare l'integrità di Monitoraggio di Azure per le macchine virtuali) oppure, per visualizzare le dipendenze delle applicazioni individuate, vedere [View Azure Monitor for VMs Map](monitoring-vminsights-maps.md) (Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali).  