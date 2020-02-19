---
title: Ruolo di lavoro ibrido per runbook Windows di Automazione di Azure
description: Questo articolo fornisce informazioni sull'installazione di un ruolo di lavoro ibrido per runbook di Automazione di Azure che consente di eseguire i runbook su computer Windows nel data center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: a6d2e2d912f176a88dc993803d750e37cff1acb6
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443665"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Windows

È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer designati. Questo articolo descrive come distribuire il ruolo di lavoro ibrido per Runbook in un computer Windows.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Installazione e configurazione di Windows Hybrid Runbook Worker

Per installare e configurare un ruolo di lavoro ibrido per Runbook di Windows, è possibile usare uno dei metodi seguenti.

* Per le macchine virtuali di Azure, è possibile installare l'agente di Log Analytics per Windows usando l' [estensione della macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md). L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro di Log Analytics esistente usando un modello di Azure Resource Manager o con PowerShell. Una volta installato l'agente, la macchina virtuale può essere aggiunta a un gruppo di ruolo di lavoro ibrido per Runbook nell'account di automazione, nel **passaggio 4** della sezione relativa alla [distribuzione manuale](#manual-deployment) riportata di seguito.

* Usare un Runbook di automazione per automatizzare completamente il processo di configurazione di un computer Windows. Questo è il metodo consigliato per i computer nel Data Center o in un altro ambiente cloud.

* Seguire una procedura dettagliata per installare e configurare manualmente il ruolo di lavoro ibrido per Runbook nella macchina virtuale non di Azure.

> [!NOTE]
> Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido per runbook con DSC (Desired State Configuration), è necessario aggiungerli come nodi DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Requisiti minimi per il ruolo di lavoro ibrido per Runbook Windows

I requisiti minimi per un ruolo di lavoro ibrido per runbook di Windows sono i seguenti:

* Windows Server 2012 o versioni successive
* Windows PowerShell 5,1 o versione successiva ([scaricare WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 o versioni successive
* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

### <a name="network-configuration"></a>Configurazione di rete

Per altri requisiti di rete per il ruolo di lavoro ibrido per runbook, vedere la sezione [Configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Onboarding del server per la gestione con Automation DSC

Per informazioni sul caricamento dei server per la gestione con DSC, vedere [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md).
Se si Abilita la [soluzione Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md), qualsiasi computer Windows connesso all'area di lavoro di log Analytics viene configurato automaticamente come ruolo di lavoro ibrido per Runbook per supportare manuali operativi incluso in questa soluzione. Non viene però eseguita la registrazione con i gruppi di ruoli di lavoro ibridi già definiti nell'account di Automazione. 

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>Aggiunta del computer a un gruppo di ruolo di lavoro ibrido per Runbook

È possibile aggiungere il computer a un gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione per supportare i runbook di Automazione, purché si usi lo stesso account sia per la soluzione che per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

## <a name="automated-deployment"></a>Distribuzione automatizzata

Nel computer di destinazione seguire questa procedura per automatizzare l'installazione e la configurazione del ruolo di lavoro ibrido di Windows.

### <a name="1-download-the-powershell-script"></a>1. scaricare lo script di PowerShell

Scaricare lo script New-OnPremiseHybridWorker.ps1 da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) direttamente dal computer che esegue il ruolo di lavoro ibrido per runbook o da un altro computer nell'ambiente. Copiare lo script sul ruolo di lavoro. Per l'esecuzione dello script New-OnPremiseHybridWorker.ps1 sono necessari i parametri seguenti:

   * *AAResourceGroupName* (obbligatorio): nome del gruppo di risorse associato all'account di automazione.
   * *OMSResourceGroupName* (facoltativo): nome del gruppo di risorse per l'area di lavoro Log Analytics. Se il gruppo di risorse non è specificato, verrà usato *AAResourceGroupName*.
   * *SubscriptionId* (obbligatorio): ID sottoscrizione di Azure in cui si trova l'account di automazione.
   * *TenantId* (facoltativo): identificatore dell'organizzazione tenant associato all'account di automazione.
   * *WorkspaceName* (facoltativo): nome dell'area di lavoro Log Analytics. Se non si dispone di un'area di lavoro Log Analytics, lo script ne crea e configura una.
   * *AutomationAccountName* (obbligatorio): nome dell'account di automazione.
   * *HybridGroupName* (obbligatorio): nome di un gruppo di ruoli di lavoro ibridi per runbook specificato come destinazione per i runbook che supportano questo scenario.
   * *Credential* (facoltativo): credenziali da usare per l'accesso all'ambiente Azure.
  
   > [!NOTE]
   > Quando si abilitano soluzioni, sono supportate solo determinate aree per il collegamento a un'area di lavoro Log Analytics e un account di Automazione.
   >
   > Per un elenco delle coppie di mapping supportate, vedere [mapping delle aree per l'account di automazione e l'area di lavoro log Analytics](how-to/region-mappings.md).

### <a name="2-open-windows-powershell-command-line-shell"></a>2. Aprire la shell della riga di comando di Windows PowerShell

Aprire **Windows PowerShell** dalla schermata **Start** in modalità amministratore.

### <a name="3-run-the-powershell-script"></a>3. eseguire lo script di PowerShell

Dalla shell della riga di comando di PowerShell passare alla cartella che contiene lo script scaricato. Modificare i valori per i parametri *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* e *-WorkspaceName*. Quindi, eseguire lo script.

Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script. È necessario accedere con un account membro del ruolo amministratori della sottoscrizione e coamministratore della sottoscrizione.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. installare NuGet

Viene richiesto di accettare di installare NuGet e di eseguire l'autenticazione con le credenziali di Azure. Se non si dispone della versione più recente di NuGet, è possibile ottenerla dalle [versioni di distribuzione NuGet disponibili](https://www.nuget.org/downloads).

### <a name="5-verify-the-deployment"></a>5. verificare la distribuzione

Dopo il completamento dello script, nella pagina **Gruppi di ruoli di lavoro ibridi** vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo dall'elenco nella pagina **gruppi di ruoli di lavoro ibridi** e selezionare il riquadro ruoli di lavoro **ibridi** . Nella pagina **Ruoli di lavoro per runbook** sono elencati i membri del gruppo.

## <a name="manual-deployment"></a>Distribuzione manuale

Nel computer di destinazione eseguire i primi due passaggi una volta per l'ambiente di automazione. Eseguire quindi i passaggi rimanenti per ogni computer di lavoro.

### <a name="1-create-a-log-analytics-workspace"></a>1. creare un'area di lavoro Log Analytics

Se non si dispone già di un'area di lavoro Log Analytics, prima di creare un'area di lavoro esaminare le [linee guida](../azure-monitor/platform/design-logs-deployment.md) per la progettazione dei log di monitoraggio di Azure.

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. aggiungere la soluzione di automazione all'area di lavoro Log Analytics

La soluzione di automazione aggiunge funzionalità per Automazione di Azure, incluso il supporto per il ruolo di lavoro ibrido per runbook. Quando si aggiunge la soluzione all'area di lavoro Log Analytics, inserisce automaticamente i componenti del ruolo di lavoro nel computer agente che verrà installato nel passaggio successivo.

Per aggiungere la soluzione di **automazione** all'area di lavoro, eseguire il cmdlet di PowerShell seguente.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. installare l'agente di Log Analytics per Windows

L'agente di Log Analytics per Windows connette i computer a un'area di lavoro Log Analytics di monitoraggio di Azure. Quando si installa l'agente nel computer e lo si connette all'area di lavoro, vengono scaricati automaticamente i componenti necessari per il ruolo di lavoro ibrido per Runbook.

Per installare l'agente nel computer, seguire le istruzioni riportate in [connettere i computer Windows ai log di monitoraggio di Azure](../log-analytics/log-analytics-windows-agent.md). È possibile ripetere questo processo per più computer per aggiungere più ruoli di lavoro nell'ambiente.

Quando l'agente si è connesso correttamente all'area di lavoro di Log Analytics dopo alcuni minuti, è possibile eseguire la query seguente per verificare che invii i dati heartbeat all'area di lavoro:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nei risultati della ricerca verranno visualizzati i record heartbeat per il computer, a indicare che sono connessi e segnalati al servizio. Per impostazione predefinita, ogni agente trasmette un record heartbeat all'area di lavoro assegnata. È possibile verificare che l'agente abbia scaricato correttamente la soluzione di automazione quando dispone di una cartella denominata AzureAutomationFiles in C:\Programmi\Microsoft Monitoring Agent\Agent. Per confermare la versione del ruolo di lavoro ibrido per Runbook, passare a C:\Programmi\Microsoft Monitoring Agent\Agent\AzureAutomation\ e prendere nota della sottocartella \\*Version* .

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. installare l'ambiente Runbook e connettersi ad automazione di Azure

Quando si configura un agente per la segnalazione a un'area di lavoro Log Analytics, la soluzione di automazione esegue il push del modulo HybridRegistration di PowerShell, che contiene il cmdlet **Add-HybridRunbookWorker** . Usare questo cmdlet per installare l'ambiente Runbook nel computer e registrarlo in automazione di Azure.

Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Eseguire quindi il cmdlet **Add-HybridRunbookWorker** usando la sintassi seguente.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

È possibile ottenere le informazioni necessarie per questo cmdlet dalla pagina "Gestisci chiavi" del portale di Azure. Aprire la pagina selezionando l'opzione **Chiavi** nella pagina **Impostazioni** dell'account di Automazione.

![Pagina "Gestisci chiavi"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Per il parametro *GroupName* , usare il nome del gruppo di lavoro ibrido per Runbook. Se il gruppo esiste già nell'account di automazione, il computer corrente vi verrà aggiunto direttamente. Se questo gruppo non esiste, verrà aggiunto.
* Per il parametro *endpoint* , usare la voce **URL** nella pagina Gestisci chiavi.
* Per il parametro *token* , usare la voce **chiave di accesso primaria** nella pagina Gestisci chiavi.

Per ricevere informazioni dettagliate sull'installazione, usare l'opzione *-Verbose* con **Add-HybridRunbookWorker**.

### <a name="5-install-powershell-modules"></a>5. installare i moduli di PowerShell

I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure. Questi moduli tuttavia non vengono distribuiti automaticamente nei computer locali, pertanto è necessario installarli manualmente. L'eccezione è il modulo di Azure. Questo modulo viene installato per impostazione predefinita e fornisce l'accesso ai cmdlet per tutti i servizi e le attività di Azure per automazione di Azure.

Poiché lo scopo principale della funzionalità Hybrid Runbook Worker è quello di gestire le risorse locali, è molto probabile che sia necessario installare i moduli che supportano queste risorse, in particolare il modulo PowerShellGet. Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

I moduli installati devono trovarsi in un percorso a cui fa riferimento la variabile di ambiente PSModulePath, in modo che il ruolo di lavoro ibrido possa importarli automaticamente. Per altre informazioni, vedere [Install modules in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per istruzioni su come rimuovere i ruoli di lavoro ibridi per runbook, vedere [Rimuovere ruoli di lavoro ibridi di Automazione di Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Per informazioni su come risolvere i problemi del ruolo di lavoro ibrido per runbook, vedere [Troubleshooting Windows Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#windows) (Risoluzione dei problemi dei ruoli di lavoro ibridi per runbook di Windows)
* Per altre procedure di risoluzione dei problemi con Gestione aggiornamenti, vedere [Gestione degli aggiornamenti - Risoluzione dei problemi](troubleshoot/update-management.md).
