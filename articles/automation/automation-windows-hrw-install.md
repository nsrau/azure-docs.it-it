---
title: Ruolo di lavoro ibrido per runbook Windows di Automazione di Azure
description: Questo articolo fornisce informazioni sull'installazione di un ruolo di lavoro ibrido per runbook di Automazione di Azure che consente di eseguire i runbook su computer Windows nel data center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 2bd9b4f46e28a28f99045319d8ac606cdcee7216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536786"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Windows

È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. Automazione di Azure archivia e gestisce i runbook e quindi li recapita a uno o più computer designati. In questo articolo viene descritto come distribuire un ruolo di lavoro ibrido per runbook in un computer Windows.This article describes how to deploy a Hybrid Runbook Worker on a Windows machine.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Installazione e configurazione di Windows Hybrid Runbook Worker

Per installare e configurare un ruolo di lavoro ibrido di Windows, è possibile utilizzare uno dei metodi descritti di seguito.

* Per le macchine virtuali di Azure, installare l'agente log Analytics per Windows usando l'estensione della [macchina virtuale per Windows.](../virtual-machines/extensions/oms-windows.md) The extension installs the Log Analytics agent on Azure virtual machines, and enrolls virtual machines into an existing Log Analytics workspace using an Azure Resource Manager template or PowerShell. Dopo aver installato l'agente, la macchina virtuale può essere aggiunta a un gruppo Di lavoro ibrido per runbook nell'account di automazione. Fare riferimento ai passaggi 3 e 4 nella sezione [Distribuzione manuale.](#manual-deployment)

* Usa un runbook di automazione per automatizzare completamente il processo di configurazione di un computer Windows. Si tratta del metodo consigliato per i computer nel data center o in un altro ambiente cloud.

* Seguire una procedura dettagliata per installare e configurare manualmente il ruolo Hybrid Runbook Worker nella macchina virtuale non Azure.Follow a step-by-step procedure to manually install and configure the Hybrid Runbook Worker role on your non-Azure VM.

> [!NOTE]
> Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido runbook con DSC (Desired State Configuration), è necessario aggiungere i server come nodi DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Requisiti minimi per Windows Hybrid Runbook Worker

I requisiti minimi per un ruolo di lavoro ibrido per runbook di Windows sono i seguenti:

* Windows Server 2012 o versioni successive
* Windows PowerShell 5.1 o versione successiva ([download DI WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 o versioni successive
* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

### <a name="network-configuration"></a>Configurazione di rete

Per altri requisiti di rete per il ruolo di lavoro ibrido per runbook, vedere la sezione [Configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Onboarding del server per la gestione con Automation DSC

Per informazioni sull'onboarding dei server per la gestione con DSC, vedere Onboarding di macchine per la gestione da parte di [Azure Automation DSC.](automation-dsc-onboarding.md)

L'abilitazione della soluzione di gestione degli [aggiornamenti](../operations-management-suite/oms-solution-update-management.md) configura automaticamente qualsiasi computer Windows connesso all'area di lavoro di Log Analytics come ruolo di lavoro ibrido per eseguire la gestione dei runbook inclusi nella soluzione. Tuttavia, questo worker non è registrato con i gruppi Hybrid Runbook Worker già definiti nell'account di automazione.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Aggiunta del computer a un gruppo di lavoro ibrido per runbook

È possibile aggiungere il computer di lavoro a un gruppo di lavoro ibrido per runbook nell'account di automazione. Si noti che è necessario supportare i runbook di automazione finché si usa lo stesso account sia per la soluzione che per l'appartenenza al gruppo Hybrid Runbook Worker. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

## <a name="automated-deployment"></a>Distribuzione automatizzata

Nel computer di destinazione eseguire i passaggi seguenti per automatizzare l'installazione e la configurazione del ruolo Windows Hybrid Worker.

### <a name="step-1---download-the-powershell-script"></a>Passaggio 1 - Scaricare lo script di PowerShellStep 1 - Download the PowerShell script

Scaricare lo script **New-OnPremiseHybridWorker.ps1** da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Il download deve essere direttamente dal computer che esegue il ruolo di lavoro ibrido per runbook o da un altro computer nell'ambiente. Dopo aver scaricato lo script, copiarlo nel worker. Lo script **New-OnPremiseHybridWorker.ps1** utilizza i parametri descritti di seguito durante l'esecuzione.

| Parametro | Stato | Descrizione |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obbligatorio | nome del gruppo di risorse associato all'account di Automazione. |
| `AutomationAccountName` | Obbligatorio | nome dell'account di Automazione.
| `Credential` | Facoltativo | Credenziali da usare per l'accesso all'ambiente Azure.The credentials to use when logging in to the Azure environment. |
| `HybridGroupName` | Obbligatorio | nome di un gruppo di ruoli di lavoro ibridi per runbook specificato come destinazione per i runbook che supportano questo scenario. |
| `OMSResourceGroupName` | Facoltativo | nome del gruppo di risorse per l'area di lavoro Log Analytics. Se questo gruppo di risorse non `AAResourceGroupName` è specificato, viene utilizzato il valore di . |
| `SubscriptionID` | Obbligatorio | Identificatore della sottoscrizione di Azure associata all'account di Automazione. |
| `TenantID` | Facoltativo | Identificatore dell'organizzazione tenant associata all'account di Automazione. |
| `WorkspaceName` | Facoltativo | Nome dell'area di lavoro Log Analytics. Se non si dispone di un'area di lavoro Log Analytics, lo script ne crea e configura una. |

> [!NOTE]
> Quando si abilitano le soluzioni, Automazione di Azure supporta solo determinate aree per il collegamento di un'area di lavoro di Log Analytics e di un account di automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account](how-to/region-mappings.md)di automazione e l'area di lavoro di Log Analytics .

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Passaggio 2 - Aprire la shell della riga di comando di Windows PowerShellStep 2 - Open Windows PowerShell command line shell

Aprire **Windows PowerShell** dalla schermata **Start** in modalità amministratore.

### <a name="step-3---run-the-powershell-script"></a>Passaggio 3 - Eseguire lo script di PowerShellStep 3 - Run the PowerShell script

Nella shell della riga di comando di PowerShell passare alla cartella che contiene lo script scaricato. Modificare i valori `AutomationAccountName`per `AAResourceGroupName` `OMSResourceGroupName`i `HybridGroupName` `SubscriptionID`parametri `WorkspaceName`, , , , e . Quindi, eseguire lo script.

Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script. È necessario accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Passaggio 4 - Installare NuGetStep 4 - Install NuGet

Viene richiesto di accettare l'installazione di NuGet e l'autenticazione con le credenziali di Azure.You're prompted to agree to install NuGet, and to authenticate with your Azure credentials. Se non si dispone della versione più recente di NuGet, è possibile ottenerla da Versioni di [distribuzione NuGet disponibili.](https://www.nuget.org/downloads)

### <a name="step-5---verify-the-deployment"></a>Passaggio 5 - Verificare la distribuzioneStep 5 - Verify the deployment

Dopo il completamento dello script, nella pagina Gruppi di ruoli di lavoro ibridi vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo dall'elenco nella pagina Gruppi di lavoro ibridi e scegliere il riquadro **Lavoratori ibridi.** Nella pagina Lavoratori ibridi è possibile visualizzare ogni membro del gruppo elencato.

## <a name="manual-deployment"></a>Distribuzione manuale

Nel computer di destinazione eseguire i primi due passaggi una sola volta per l'ambiente di automazione. Eseguire quindi i passaggi rimanenti per ogni computer di lavoro.

### <a name="step-1---create-a-log-analytics-workspace"></a>Passaggio 1 - Creare un'area di lavoro di Log AnalyticsStep 1 - Create a Log Analytics workspace

Se non si dispone già di un'area di lavoro di Log Analytics, esaminare le linee guida di [progettazione](../azure-monitor/platform/design-logs-deployment.md) del log di monitoraggio di Azure prima di creare l'area di lavoro.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>Passaggio 2 - Aggiungere la soluzione di automazione all'area di lavoro di Log AnalyticsStep 2 - Add the Automation solution to the Log Analytics workspace

La soluzione di automazione aggiunge funzionalità per automazione di Azure, incluso il supporto per il ruolo di lavoro ibrido per runbook. Quando si aggiunge la soluzione all'area di lavoro di Log Analytics, viene automaticamente inserita nel computer agente i componenti di lavoro installati come descritto nel passaggio successivo.

Per aggiungere la soluzione di automazione all'area di lavoro, eseguire il cmdlet PowerShell seguente.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Passaggio 3 - Installare l'agente di Log Analytics per WindowsStep 3 - Install the Log Analytics agent for Windows

L'agente log Analytics per Windows connette i computer a un'area di lavoro di Log Analytics monitor. Quando si installa l'agente nel computer e lo si connette all'area di lavoro, vengono scaricati automaticamente i componenti necessari per il ruolo di lavoro ibrido per runbook.

Per installare l'agente nel computer, seguire le istruzioni in [Connettere computer Windows ai log di Azure Monitor](../log-analytics/log-analytics-windows-agent.md). È possibile ripetere questo processo per più computer per aggiungere più ruoli di lavoro nell'ambiente.

Quando l'agente si è connesso correttamente all'area di lavoro di Log Analytics dopo alcuni minuti, è possibile eseguire la query seguente per verificare che invii dati heartbeat all'area di lavoro.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nei risultati della ricerca dovrebbero essere visualizzati i record heartbeat per il computer, che indicano che è connesso e che è segnalato al servizio. Per impostazione predefinita, ogni agente inoltra un record heartbeat all'area di lavoro assegnata. 

Utilizzare la procedura seguente per completare l'installazione e l'installazione dell'agente.

1. Abilitare la soluzione per l'onboarding del computer agente. Consultate [Utilizzare I computer di bordo nell'area di lavoro.](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace)
2. Verificare che l'agente abbia scaricato correttamente la soluzione di automazione. Deve disporre di una cartella denominata **AzureAutomationFiles** in **C:**. 
3. Per confermare la versione del ruolo di lavoro ibrido per runbook, passare a C: , **Programmi , Microsoft Monitoring Agent , agente , AzureAutomation** e prendere nota della sottocartella della **versione.**

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Passaggio 4 - Installare l'ambiente del runbook e connettersi ad Automazione di AzureStep 4 - Install the runbook environment and connect to Azure Automation

Quando si configura un agente per la creazione di report in `HybridRegistration` un'area di `Add-HybridRunbookWorker` lavoro di Log Analytics, la soluzione di automazione spinge verso il basso il modulo di PowerShell, che contiene il cmdlet. Usare questo cmdlet per installare l'ambiente di runbook nel computer e registrarlo con Automazione di Azure.Use this cmdlet to install the runbook environment on the computer and register it with Azure Automation.

Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Eseguire ora `Add-HybridRunbookWorker` il cmdlet utilizzando la sintassi seguente.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

È possibile ottenere le informazioni necessarie per questo cmdlet dalla pagina Gestisci chiavi del portale di Azure. Apri questa pagina selezionando **Chiavi** nella pagina Impostazioni del tuo account Automation.

![Pagina Gestisci chiavi](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Per `GroupName` il parametro, utilizzare il nome del gruppo Hybrid Runbook Worker. Se il gruppo esiste già nell'account di automazione, il computer corrente vi verrà aggiunto direttamente. Se questo gruppo non esiste, verrà aggiunto.
* Per `EndPoint` il parametro, utilizzare la voce **URL** nella pagina Gestisci chiavi.
* Per `Token` il parametro, utilizzare la voce **PRIMARY ACCESS KEY** nella pagina Gestisci chiavi.
* Se necessario, `Verbose` impostare il parametro per ricevere i dettagli sull'installazione.

### <a name="step-5----install-powershell-modules"></a>Passaggio 5 - Installare i moduli di PowerShellStep 5 - Install PowerShell modules

I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure. Poiché questi moduli non vengono distribuiti automaticamente nei computer locali, è necessario installarli manualmente. L'eccezione è il modulo di Azure.The exception is the Azure module. Questo modulo viene installato per impostazione predefinita e fornisce l'accesso ai cmdlet per tutti i servizi e le attività di Azure per l'automazione di Azure.This module is installed by default and provides access to cmdlets for all Azure services and activities for Azure Automation.

Poiché lo scopo principale della funzionalità Hybrid Runbook Worker è gestire le risorse locali, è `PowerShellGet` molto probabile che sia necessario installare i moduli che supportano queste risorse, in particolare il modulo. Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

I moduli installati devono trovarsi in `PSModulePath` una posizione a cui fa riferimento la variabile di ambiente in modo che il worker ibrido possa importarli automaticamente. Per ulteriori informazioni, consultate [Installare moduli in PSModulePath.](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per istruzioni sulla rimozione dei ruoli di lavoro per runbook ibridi, vedere Rimuovere i ruoli di lavoro [per runbook ibridi](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)di automazione di Azure.For instructions on removing Hybrid Runbook Workers, see Remove Azure Automation Hybrid Runbook Workers .
* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro per runbook ibridi, vedere Risoluzione dei problemi relativi a [Windows Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#windows).
* Per ulteriori procedure per la risoluzione dei problemi di gestione degli aggiornamenti, vedere [Update Management: troubleshooting](troubleshoot/update-management.md).
