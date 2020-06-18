---
title: Distribuire un ruolo di lavoro ibrido per runbook Windows in Automazione di Azure
description: Questo articolo descrive come distribuire un ruolo di lavoro ibrido per runbook che può essere usato per eseguire runbook in computer basati su Windows nel data center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 8a21f6a58e8dc657d3b60aac33661504363072e2
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309916"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Windows

È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. Automazione di Azure archivia e gestisce i runbook e quindi li distribuisce a uno o più computer deisgnati. Questo articolo illustra come installare un ruolo di lavoro ibrido per runbook in un computer Windows.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Installazione di un ruolo di lavoro ibrido per runbook di Windows

Per installare e configurare un ruolo di lavoro ibrido per runbook di Windows, è possibile usare uno dei metodi seguenti.

* Per le macchine virtuali di Azure, installare l'agente di Log Analytics per Windows usando [l'estensione macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md). L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro Log Analytics esistente usando un modello di Azure Resource Manager o di PowerShell. Dopo che l'agente è installato, la macchina virtuale può essere aggiunta a un gruppo di ruoli di lavoro ibridi per runbook nell'account di automazione. Vedere i passaggi 3 e 4 nella sezione [Distribuzione manuale](#manual-deployment).

* Usare un runbook di automazione per automatizzare completamente il processo di configurazione di un computer Windows. Questo è il metodo consigliato per i computer nel data center o in un altro ambiente cloud.

* Seguire una procedura dettagliata per installare e configurare manualmente il ruolo di lavoro ibrido per runbook nella macchina virtuale non di Azure.

> [!NOTE]
> Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido per runbook con DSC (Desired State Configuration), è necessario aggiungerli come nodi DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Requisiti minimi per il ruolo di lavoro ibrido per runbook di Windows

I requisiti minimi per un ruolo di lavoro ibrido per runbook di Windows sono i seguenti:

* Windows Server 2012 o versioni successive
* Windows PowerShell 5.1 o versioni successive ([scaricare WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 o versioni successive
* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

### <a name="network-configuration"></a>Configurazione di rete

Per altri requisiti di rete per il ruolo di lavoro ibrido per runbook, vedere la sezione [Configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

### <a name="enabling-servers-for-management-with-azure-automation-state-configuration"></a>Abilitazione dei server per la gestione con State Configuration di Automazione di Azure

Per informazioni sull'abilitazione dei server per la gestione con State Configuration di Automazione di Azure, vedere [Abilitare computer per la gestione tramite State Configuration di Automazione di Azure](automation-dsc-onboarding.md).

Se si abilita [Gestione aggiornamenti](automation-update-management.md) di Automazione di Azure, si configura automaticamente qualsiasi computer Windows connesso all'area di lavoro Log Analytics come ruolo di lavoro ibrido per runbook per supportare gli aggiornamenti dei runbook. Tale ruolo di lavoro, tuttavia, non è registrato con alcun gruppo di ruoli di lavoro ibridi per runbook già definito nell'account di automazione.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Aggiunta del computer a un gruppo di ruoli di lavoro ibridi per runbook

È possibile aggiungere il computer di lavoro a un gruppo di ruoli di lavoro ibridi per Runbook nell'account di automazione. Si noti che è necessario supportare runbook di automazione, purché si usi lo stesso account sia per la funzionalità Automazione di Azure sia per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

## <a name="automated-deployment"></a>Distribuzione automatizzata

Nel computer di destinazione, seguire questa procedura per automatizzare l'installazione e la configurazione del ruolo di lavoro ibrido di Windows.

### <a name="step-1---download-the-powershell-script"></a>Passaggio 1 - Scaricare lo script di PowerShell

Scaricare lo script **New-OnPremiseHybridWorker.ps1** da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Il download deve essere eseguito direttamente dal computer che esegue il ruolo di lavoro ibrido per runbook o da un altro computer nell'ambiente in uso. Dopo aver scaricato lo script, copiarlo nel proprio ruolo di lavoro. Lo script **New-OnPremiseHybridWorker.ps1** usa i parametri descritti di seguito durante l'esecuzione.

| Parametro | Stato | Descrizione |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obbligatorio | nome del gruppo di risorse associato all'account di Automazione. |
| `AutomationAccountName` | Obbligatorio | nome dell'account di Automazione.
| `Credential` | Facoltativo | Credenziali da usare per l'accesso all'ambiente di Azure. |
| `HybridGroupName` | Obbligatorio | nome di un gruppo di ruoli di lavoro ibridi per runbook specificato come destinazione per i runbook che supportano questo scenario. |
| `OMSResourceGroupName` | Facoltativo | nome del gruppo di risorse per l'area di lavoro Log Analytics. Se questo gruppo di risorse non è specificato, viene usato il valore di `AAResourceGroupName`. |
| `SubscriptionID` | Obbligatorio | Identificatore della sottoscrizione di Azure associata all'account di automazione. |
| `TenantID` | Facoltativo | Identificatore dell'organizzazione tenant associato all'account di automazione. |
| `WorkspaceName` | Facoltativo | Nome dell'area di lavoro Log Analytics. Se non si dispone di un'area di lavoro Log Analytics, lo script ne crea e configura una. |

> [!NOTE]
> Quando si abilitano le funzionalità, Automazione di Azure supporta solo determinate aree per collegare un'area di lavoro Log Analytics e un account di automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Passaggio 2 - Aprire la shell della riga di comando di Windows PowerShell

Aprire **Windows PowerShell** nella schermata **Avvio** in modalità amministratore.

### <a name="step-3---run-the-powershell-script"></a>Passaggio 3 - Eseguire lo script di PowerShell

Nella shell della riga di comando di PowerShell, passare alla cartella che contiene lo script scaricato. Modificare i valori per i parametri `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` e `WorkspaceName`. Quindi, eseguire lo script.

Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script. È necessario accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Passaggio 4 - Installare NuGet

Verrà chiesto di accettare l'installazione di NuGet e di eseguire l'autenticazione con le credenziali di Azure. Se non si dispone della versione più recente di NuGet, è possibile ottenerla dalle [versioni di distribuzione NuGet disponibili](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Passaggio 5 - Verificare la distribuzione

Dopo il completamento dello script, nella pagina Gruppi di ruoli di lavoro ibridi vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo nell'elenco della pagina Gruppi di ruoli di lavoro ibridi e selezionare il riquadro **Ruoli di lavoro ibridi**. Nella pagina Ruoli di lavoro ibridi sono elencati i membri del gruppo.

## <a name="manual-deployment"></a>Distribuzione manuale

Nel computer di destinazione eseguire i primi due passaggi una volta per l'ambiente di automazione. Eseguire quindi i passaggi rimanenti per ogni computer del ruolo di lavoro.

### <a name="step-1---create-a-log-analytics-workspace"></a>Passaggio 1 - Creare un'area di lavoro Log Analytics

Se non si dispone già di un'area di lavoro Log Analytics, esaminare le [ linee guida per la progettazione dei log di Monitoraggio di Azure](../azure-monitor/platform/design-logs-deployment.md) prima di creare l'area di lavoro.

### <a name="step-2---add-an-azure-automation-feature-to-the-log-analytics-workspace"></a>Passaggio 2 - Aggiungere una funzionalità di Automazione di Azure all'area di lavoro Log Analytics

Aggiungere funzionalità per Automazione di Azure, incluso il supporto per il ruolo di lavoro ibrido per runbook. Quando si abilita una funzionalità di Automazione di Azure nell'area di lavoro Log Analytics, i componenti del ruolo di lavoro vengono inseriti automaticamente nel computer agente.

Per aggiungere una funzionalità di Automazione di Azure, ad esempio Gestione aggiornamenti, all'area di lavoro, eseguire il cmdlet PowerShell seguente:

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Passaggio 3 - Installare l'agente di Log Analytics per Windows

L'agente di Log Analytics per Windows connette i computer a un'area di lavoro Log Analytics di Monitoraggio di Azure. Quando si installa l'agente nel computer e lo si connette all'area di lavoro, viene eseguito automaticamente il download dei componenti necessari per il ruolo di lavoro ibrido per runbook.

Per installare l'agente nel computer, seguire le istruzioni indicate in [Connettere i computer Windows ai log di Monitoraggio di Azure](../log-analytics/log-analytics-windows-agent.md). È possibile ripetere questo processo per più computer per aggiungere più ruoli di lavoro nell'ambiente.

Quando l'agente si è connesso correttamente all'area di lavoro Log Analytics dopo alcuni minuti, è possibile eseguire la query seguente per verificare che invii dati di heartbeat all'area di lavoro.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nei risultati della ricerca devono essere visualizzati i record di heartbeat per il computer indicanti che è connesso e che invia report al servizio. Per impostazione predefinita, ogni agente trasmette un record di heartbeat all'area di lavoro assegnata. Per completare l'installazione e la configurazione dell'agente, eseguire queste operazioni.

1. Abilitare la funzionalità per aggiungere il computer agente. Vedere [Abilitare i computer nell'area di lavoro](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Verificare che l'agente abbia scaricato correttamente la funzionalità di Automazione di Azure. 
3. Per verificare la versione del ruolo di lavoro ibrido per runbook, passare a **C:\Programmi\Microsoft Monitoring Agent\Agent\AzureAutomation** e prendere nota della sottocartella **version**.

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Passaggio 4 - Installare l'ambiente runbook e connettersi ad Automazione di Azure

Quando si configura un agente per la segnalazione a un'area di lavoro Log Analytics, la funzionalità di Automazione di Azure esegue il push del modulo PowerShell `HybridRegistration`, che contiene il cmdlet `Add-HybridRunbookWorker`. Usare questo cmdlet per installare l'ambiente runbook nel computer e registrarlo in Automazione di Azure.

Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

A questo punto, eseguire il cmdlet `Add-HybridRunbookWorker` tramite la sintassi seguente.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

È possibile ottenere le informazioni necessarie per questo cmdlet nella pagina Gestisci chiavi del portale di Azure. Aprire questa pagina selezionando **Chiavi** nella pagina delle impostazioni dell'account di automazione.

![Pagina Gestisci chiavi](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Per il parametro `GroupName`, usare il nome del gruppo di ruoli di lavoro ibridi per runbook. Se il gruppo esiste già nell'account di automazione, il computer corrente vi verrà aggiunto direttamente. Se questo gruppo non esiste, verrà aggiunto.
* Per il parametro `EndPoint`, usare la voce **URL** nella pagina Gestisci chiavi.
* Per il parametro `Token`, usare la voce **CHIAVE DI ACCESSO PRIMARIA** nella pagina Gestisci chiavi.
* Se necessario, impostare il parametro `Verbose` per ricevere i dettagli sull'installazione.

### <a name="step-5----install-powershell-modules"></a>Passaggio 5 - Installare i moduli di PowerShell

I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure. Questi moduli tuttavia non vengono distribuiti automaticamente nei computer locali, pertanto è necessario installarli manualmente. Il modulo di Azure è l'eccezione. Tale modulo viene installato per impostazione predefinita e fornisce l'accesso ai cmdlet per tutti i servizi e le attività di Azure per Automazione di Azure.

Poiché lo scopo principale del ruolo di lavoro ibrido per runbook è quello di gestire le risorse locali, probabilmente sarà necessario installare i moduli che supportano queste risorse, particolarmente il modulo `PowerShellGet`. Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

I moduli installati devono trovarsi in un percorso a cui fa riferimento la variabile di ambiente `PSModulePath`, in modo che il ruolo di lavoro ibrido possa importarli automaticamente. Per altre informazioni, vedere [Installare moduli in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-computer"></a><a name="remove-windows-hybrid-runbook-worker"></a>Rimuovere il ruolo di lavoro ibrido per runbook da un computer Windows locale

1. Nel portale di Azure passare all'account di Automazione.
2. In **Impostazioni Account** selezionare **Chiavi** e prendere nota dei valori di **URL** e **Chiave di accesso primaria**.

3. Aprire una sessione di PowerShell in modalità amministratore ed eseguire questo comando con i valori dell'URL e della chiave di accesso primaria. Usare il parametro `Verbose` per un log dettagliato del processo di rimozione. Per rimuovere le macchine non aggiornate dal gruppo di ruoli di lavoro ibridi, usare il parametro facoltativo `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Rimuovere un gruppo di ruoli di lavoro ibridi

Per rimuovere un gruppo di ruoli di lavoro ibridi per runbook, è necessario prima di tutto rimuovere il ruolo di lavoro ibrido per runbook da ogni computer membro del gruppo. Eseguire quindi queste operazioni per rimuovere il gruppo:

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Gruppi di ruolo di lavoro ibridi** in **Automazione processi**. Selezionare il gruppo che si vuole eliminare. Viene visualizzata la pagina delle proprietà per quel gruppo.

   ![Pagina Proprietà](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Nella pagina delle proprietà del gruppo selezionato selezionare **Elimina**. Compare un messaggio che chiede di confermare. Selezionare **Sì** se si è certi di voler continuare.

   ![Messaggio di conferma](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook](troubleshoot/hybrid-runbook-worker.md#general).
