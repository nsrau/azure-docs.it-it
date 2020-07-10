---
title: Distribuire un ruolo di lavoro ibrido per runbook Windows in Automazione di Azure
description: Questo articolo descrive come distribuire un ruolo di lavoro ibrido per Runbook che può essere usato per eseguire manuali operativi in computer basati su Windows nel Data Center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 6913ae8cbd8c73bd2763bd89172280feee9df973
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185620"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Windows

È possibile usare la funzionalità Hybrid Runbook Worker di automazione di Azure per eseguire manuali operativi direttamente sul computer che ospita il ruolo e sulle risorse nell'ambiente per gestire tali risorse locali. Automazione di Azure archivia e gestisce manuali operativi e li recapita a uno o più computer designati. Questo articolo descrive come distribuire un ruolo di lavoro ibrido per Runbook in un computer Windows, come rimuovere il ruolo di lavoro e come rimuovere un gruppo di ruolo di lavoro ibrido per Runbook.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre di quanto segue.

### <a name="a-log-analytics-workspace"></a>Area di lavoro Log Analytics

Il ruolo di lavoro ibrido per Runbook dipende da un'area di lavoro di monitoraggio di Azure Log Analytics per installare e configurare il ruolo. È possibile crearlo tramite [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), tramite [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)o nel [portale di Azure](../azure-monitor/learn/quick-create-workspace.md).

Se non si dispone di un'area di lavoro Log Analytics di monitoraggio di Azure, vedere le [indicazioni sulla progettazione dei log di monitoraggio di Azure](../azure-monitor/platform/design-logs-deployment.md) prima di creare l'area di lavoro

Se si dispone di un'area di lavoro, ma non è collegata all'account di automazione, l'abilitazione di una funzionalità di automazione aggiunge funzionalità per automazione di Azure, incluso il supporto per il ruolo di lavoro ibrido per Runbook. Quando si abilita una delle funzionalità di automazione di Azure nell'area di lavoro Log Analytics, in particolare [Gestione aggiornamenti](automation-update-management.md) o [rilevamento modifiche e l'inventario](change-tracking.md), i componenti del ruolo di lavoro vengono inseriti automaticamente nel computer agente.

   Per aggiungere la funzionalità Gestione aggiornamenti all'area di lavoro, eseguire il cmdlet PowerShell seguente:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   Per aggiungere la funzionalità di Rilevamento modifiche e inventario all'area di lavoro, eseguire il cmdlet di PowerShell seguente:

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Agente di Log Analytics

Il ruolo di lavoro ibrido per Runbook richiede l' [agente di log Analytics](../azure-monitor/platform/log-analytics-agent.md) per il sistema operativo Windows supportato.

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows supportato

Le versioni seguenti del sistema operativo Windows sono ufficialmente supportate per un ruolo di lavoro ibrido per Runbook Windows:

* Windows Server 2019
* Windows Server 2016, versione 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (inclusa la versione multisessione) e Pro
* Windows 8 Enterprise e Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Requisiti minimi

I requisiti minimi per un ruolo di lavoro ibrido per runbook di Windows sono i seguenti:

* Windows PowerShell 5.1 o versioni successive ([scaricare WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 o versioni successive
* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

### <a name="network-configuration"></a>Configurazione di rete

Per altri requisiti di rete per il ruolo di lavoro ibrido per runbook, vedere la sezione [Configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Aggiunta di un computer a un gruppo di ruolo di lavoro ibrido per Runbook

È possibile aggiungere il computer di lavoro a un gruppo di ruolo di lavoro ibrido per Runbook nell'account di automazione. Si noti che è necessario supportare runbook di automazione, purché si usi lo stesso account sia per la funzionalità Automazione di Azure sia per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

>[!NOTE]
>L'abilitazione di automazione di Azure [Gestione aggiornamenti](automation-update-management.md) configura automaticamente qualsiasi computer Windows connesso all'area di lavoro di log Analytics come ruolo di lavoro ibrido per Runbook per supportare la gestione degli aggiornamenti del sistema operativo. Tale ruolo di lavoro, tuttavia, non è registrato con alcun gruppo di ruoli di lavoro ibridi per runbook già definito nell'account di automazione.

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Abilitazione dei computer per la gestione con la configurazione dello stato di automazione di Azure

Per informazioni sull'abilitazione dei computer per la gestione con la configurazione dello stato di automazione di Azure, vedere [abilitare le macchine virtuali per la gestione mediante la configurazione dello stato di automazione](automation-dsc-onboarding.md)

> [!NOTE]
> Per gestire la configurazione dei computer che supportano il ruolo di lavoro ibrido per runbook con desired state Configuration (DSC), è necessario aggiungere i computer come nodi DSC.

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Opzioni di installazione di Windows Hybrid Runbook Worker

Per installare e configurare un ruolo di lavoro ibrido per runbook di Windows, è possibile usare uno dei metodi seguenti.

* Per le macchine virtuali di Azure, installare l'agente di Log Analytics per Windows usando [l'estensione macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md). L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro Log Analytics esistente usando un modello di Azure Resource Manager o di PowerShell. Dopo che l'agente è installato, la macchina virtuale può essere aggiunta a un gruppo di ruoli di lavoro ibridi per runbook nell'account di automazione.

* Per le macchine virtuali non di Azure, installare l'agente Log Analytics per Windows usando le opzioni di distribuzione descritte nell'articolo [connettere computer Windows a monitoraggio di Azure](../azure-monitor/platform/agent-windows.md) . È possibile ripetere questo processo per più computer per aggiungere più ruoli di lavoro all'ambiente. Una volta installato l'agente, è possibile aggiungere le macchine virtuali a un gruppo di ruolo di lavoro ibrido per Runbook nell'account di automazione.

* Usare uno script di PowerShell fornito per [automatizzare](#automated-deployment) completamente il processo di configurazione di uno o più computer Windows. Questo è il metodo consigliato per i computer nel data center o in un altro ambiente cloud.

## <a name="automated-deployment"></a>Distribuzione automatizzata

Nel computer di destinazione seguire questa procedura per automatizzare l'installazione e la configurazione del ruolo di lavoro ibrido di Windows usando lo script di PowerShell **New-OnPremiseHybridWorker.ps1**. Lo script esegue le operazioni seguenti:

* Installa i moduli necessari
* Accede con l'account Azure
* Verifica l'esistenza di un gruppo di risorse e di un account di automazione specificati
* Crea riferimenti agli attributi dell'account di automazione
* Crea un'area di lavoro di monitoraggio di Azure Log Analytics se non è specificata
* Abilitare la soluzione automazione di Azure nell'area di lavoro
* Scaricare e installare l'agente di Log Analytics per Windows
* Registrare il computer come ruolo di lavoro ibrido per Runbook

### <a name="step-1---download-the-powershell-script"></a>Passaggio 1 - Scaricare lo script di PowerShell

Scaricare lo script **New-OnPremiseHybridWorker.ps1** da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Dopo aver scaricato lo script, copiarlo o eseguirlo nel computer di destinazione. Lo script **New-OnPremiseHybridWorker.ps1** usa i parametri descritti di seguito durante l'esecuzione.

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

Dal **menu Start** fare clic **su Start**, digitare **PowerShell**, fare clic con il pulsante destro del mouse su **Windows PowerShell**, quindi scegliere **Esegui come amministratore**.

### <a name="step-3---run-the-powershell-script"></a>Passaggio 3 - Eseguire lo script di PowerShell

Nella shell della riga di comando di PowerShell, passare alla cartella che contiene lo script scaricato. Modificare i valori per i parametri `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` e `WorkspaceName`. Quindi, eseguire lo script.

Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script. È necessario accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <nameOfAutomationAccount> -AAResourceGroupName <nameOfResourceGroup>`
-OMSResourceGroupName <nameOfOResourceGroup> -HybridGroupName <nameOfHRWGroup> `
-SubscriptionID <subscriptionId> -WorkspaceName <nameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Passaggio 4 - Installare NuGet

Verrà chiesto di accettare l'installazione di NuGet e di eseguire l'autenticazione con le credenziali di Azure. Se non si dispone della versione più recente di NuGet, è possibile scaricarla dalle [versioni di distribuzione NuGet disponibili](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Passaggio 5 - Verificare la distribuzione

Al termine dello script, nella pagina gruppi di lavoro ibridi dell'account di automazione vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo nell'elenco della pagina Gruppi di ruoli di lavoro ibridi e selezionare il riquadro **Ruoli di lavoro ibridi**. Nella pagina Ruoli di lavoro ibridi sono elencati i membri del gruppo.

## <a name="manual-deployment"></a>Distribuzione manuale

Per installare e configurare un ruolo di lavoro ibrido per Runbook di Windows, seguire questa procedura.

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>Passaggio 1: verificare che Agent stia inviando report all'area di lavoro

L'agente di Log Analytics per Windows connette i computer a un'area di lavoro Log Analytics di monitoraggio di Azure. Quando si installa l'agente nel computer e lo si connette all'area di lavoro, vengono scaricati automaticamente i componenti necessari per il ruolo di lavoro ibrido per Runbook.

Quando l'agente si è connesso correttamente all'area di lavoro Log Analytics dopo alcuni minuti, è possibile eseguire la query seguente per verificare che invii dati di heartbeat all'area di lavoro.

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

Nei risultati della ricerca verranno visualizzati i record heartbeat per il computer, a indicare che sono connessi e segnalati al servizio. Per impostazione predefinita, ogni agente trasmette un record di heartbeat all'area di lavoro assegnata. Per completare l'installazione e la configurazione dell'agente, eseguire queste operazioni.

1. Abilitare la funzionalità per aggiungere il computer agente. Per Gestione aggiornamenti e le macchine virtuali di Azure, vedere [abilitare le VM di Azure](automation-onboard-solutions-from-automation-account.md#enable-azure-vms)e per le macchine virtuali non di Azure vedere [abilitare i computer nell'area di lavoro](automation-onboard-solutions-from-automation-account.md#enable-machines-in-the-workspace). Per Rilevamento modifiche e le macchine virtuali di Azure, vedere [abilitare le VM di Azure](automation-enable-changes-from-auto-acct.md#enable-azure-vms)e per le macchine virtuali non di Azure vedere [abilitare i computer nell'area di lavoro](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace).

2. Per confermare la versione del ruolo di lavoro ibrido per Runbook, individuare `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` e prendere nota della sottocartella **Version** .

### <a name="step-3---install-the-runbook-environment-and-connect-to-azure-automation"></a>Passaggio 3: installare l'ambiente Runbook e connettersi ad automazione di Azure

Quando si configura un agente per la segnalazione a un'area di lavoro Log Analytics, la funzionalità di Automazione di Azure esegue il push del modulo PowerShell `HybridRegistration`, che contiene il cmdlet `Add-HybridRunbookWorker`. Usare questo cmdlet per installare l'ambiente Runbook nel computer e registrarlo in automazione di Azure.

Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

A questo punto, eseguire il cmdlet `Add-HybridRunbookWorker` tramite la sintassi seguente.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

È possibile ottenere le informazioni necessarie per i parametri `Url` e `Key` dalla pagina **chiavi** nell'account di automazione. Selezionare **chiavi** nella sezione **Impostazioni account** nella parte sinistra della pagina.

![Pagina Gestisci chiavi](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Per il `Url` parametro, copiare il valore di **URL**.

* Per il `Key` parametro, copiare il valore della **chiave di accesso primaria**.

* Per il parametro `GroupName`, usare il nome del gruppo di ruoli di lavoro ibridi per runbook. Se questo gruppo è già presente nell'account di automazione, viene aggiunto al computer corrente. Se questo gruppo non esiste, verrà aggiunto.

* Se necessario, impostare il parametro `Verbose` per ricevere i dettagli sull'installazione.

### <a name="step-4----install-powershell-modules"></a>Passaggio 4: installare i moduli di PowerShell

I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure. Poiché questi moduli non vengono distribuiti automaticamente nei computer locali, è necessario installarli manualmente. Il modulo di Azure è l'eccezione. Tale modulo viene installato per impostazione predefinita e fornisce l'accesso ai cmdlet per tutti i servizi e le attività di Azure per Automazione di Azure.

Poiché lo scopo principale del ruolo di lavoro ibrido per runbook è quello di gestire le risorse locali, probabilmente sarà necessario installare i moduli che supportano queste risorse, particolarmente il modulo `PowerShellGet`. Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

I moduli installati devono trovarsi in un percorso a cui fa riferimento la variabile di ambiente `PSModulePath`, in modo che il ruolo di lavoro ibrido possa importarli automaticamente. Per altre informazioni, vedere [Installare moduli in PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>Rimuovere il ruolo di lavoro ibrido per Runbook da un computer Windows locale

1. Nel portale di Azure passare all'account di Automazione.

2. In **Impostazioni Account** selezionare **Chiavi** e prendere nota dei valori di **URL** e **Chiave di accesso primaria**.

3. Aprire una sessione di PowerShell in modalità amministratore ed eseguire questo comando con i valori dell'URL e della chiave di accesso primaria. Usare il parametro `Verbose` per un log dettagliato del processo di rimozione. Per rimuovere le macchine non aggiornate dal gruppo di ruoli di lavoro ibridi, usare il parametro facoltativo `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Rimuovere un gruppo di ruoli di lavoro ibridi

Per rimuovere un gruppo di ruolo di lavoro ibrido per Runbook, è necessario prima di tutto rimuovere il ruolo di lavoro ibrido per Runbook da ogni computer membro del gruppo. Eseguire quindi queste operazioni per rimuovere il gruppo:

1. Nel portale di Azure aprire l'account di automazione.

2. Selezionare **Gruppi di ruolo di lavoro ibridi** in **Automazione processi**. Selezionare il gruppo che si vuole eliminare. Viene visualizzata la pagina delle proprietà per quel gruppo.

   ![Pagina Proprietà](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Nella pagina delle proprietà del gruppo selezionato selezionare **Elimina**. Compare un messaggio che chiede di confermare. Selezionare **Sì** se si è certi di voler continuare.

   ![Messaggio di conferma](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook](troubleshoot/hybrid-runbook-worker.md#general).
