---
title: Ruolo di lavoro ibrido per runbook Windows di Automazione di Azure
description: Questo articolo fornisce informazioni sull'installazione di un ruolo di lavoro ibrido per runbook di Automazione di Azure che consente di eseguire i runbook su computer Windows nel data center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 04b5727a1c5abf8eb44ada566847456c2100b2a9
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111501"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Windows

È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer designati. In questo articolo viene illustrato come installare il ruolo di lavoro ibrido per runbook in un computer Windows.

> [!NOTE]
Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.3.0). Per le istruzioni di installazione di AZ Module, vedere [Install the Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.4.0).

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installazione del ruolo di lavoro ibrido per runbook di Windows

Per installare e configurare un ruolo di lavoro ibrido per Runbook di Windows, è possibile usare uno dei tre metodi seguenti:

* Per le macchine virtuali di Azure, è possibile installare l'agente di Log Analytics per Windows usando l' [estensione della macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md). L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro di Log Analytics esistente usando un modello di Azure Resource Manager o con PowerShell. Una volta installato l'agente, la macchina virtuale può essere aggiunta a un gruppo di ruolo di lavoro ibrido per Runbook nell'account di automazione, nel **passaggio 4** della sezione relativa alla [distribuzione manuale](#manual-deployment) riportata di seguito.

* Usare un Runbook di automazione per automatizzare completamente il processo di configurazione di un computer Windows. Questo è il metodo consigliato per i computer nel Data Center o in un altro ambiente cloud.

* Seguire una procedura dettagliata per installare e configurare manualmente il ruolo di lavoro ibrido per Runbook nella macchina virtuale non di Azure.

> [!NOTE]
> Per gestire la configurazione dei server che supportano il ruolo di lavoro ibrido per runbook con DSC (Desired State Configuration), è necessario aggiungerli come nodi DSC.

I requisiti minimi per un ruolo di lavoro ibrido per runbook di Windows sono i seguenti:

* Windows Server 2012 o versioni successive
* Windows PowerShell 5,1 o versione successiva ([scaricare WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 o versioni successive
* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

Per altri requisiti di rete per il ruolo di lavoro ibrido per runbook, vedere la sezione [Configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

Per informazioni sul caricamento dei server per la gestione con DSC, vedere [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md).
Se si Abilita la [soluzione Gestione aggiornamenti](../operations-management-suite/oms-solution-update-management.md), qualsiasi computer Windows connesso all'area di lavoro di log Analytics viene configurato automaticamente come ruolo di lavoro ibrido per Runbook per supportare manuali operativi incluso in questa soluzione. Non viene però eseguita la registrazione con i gruppi di ruoli di lavoro ibridi già definiti nell'account di Automazione. 

È possibile aggiungere il computer a un gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione per supportare i runbook di Automazione, purché si usi lo stesso account sia per la soluzione che per l'appartenenza al gruppo di ruoli di lavoro ibridi per runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

### <a name="automated-deployment"></a>Distribuzione automatizzata

Seguire questa procedura per automatizzare l'installazione e la configurazione del ruolo di lavoro ibrido di Windows:

1. Scaricare lo script New-OnPremiseHybridWorker.ps1 da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) direttamente dal computer che esegue il ruolo di lavoro ibrido per runbook o da un altro computer nell'ambiente. Copiare lo script sul ruolo di lavoro. Per l'esecuzione dello script New-OnPremiseHybridWorker.ps1 sono necessari i parametri seguenti:

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

2. Nel computer in uso aprire **Windows PowerShell** dalla schermata **Start** in modalità amministratore.
3. Dalla shell della riga di comando di PowerShell, passare alla cartella che contiene lo script scaricato. Modificare i valori per i parametri *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* e *-WorkspaceName*. Quindi, eseguire lo script.

     > [!NOTE]
     > Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script. È *necessario* accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Verrà chiesto di accettare di installare NuGet e di eseguire l'autenticazione con le credenziali di Azure.

5. Dopo il completamento dello script, nella pagina **Gruppi di ruoli di lavoro ibridi** vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo dall'elenco nella pagina **gruppi di ruoli di lavoro ibridi** e selezionare il riquadro ruoli di lavoro **ibridi** . Nella pagina **Ruoli di lavoro per runbook** sono elencati i membri del gruppo.

### <a name="manual-deployment"></a>Distribuzione manuale

Eseguire i primi due passaggi una volta per l'ambiente di automazione e quindi ripetere i passaggi rimanenti per ogni computer di lavoro.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. creare un'area di lavoro Log Analytics

Se non si dispone già di un'area di lavoro Log Analytics, prima di creare un'area di lavoro esaminare le [linee guida](../azure-monitor/platform/design-logs-deployment.md) per la progettazione dei log di monitoraggio di Azure. 

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. aggiungere la soluzione di automazione all'area di lavoro Log Analytics

La soluzione di automazione aggiunge funzionalità per Automazione di Azure, incluso il supporto per il ruolo di lavoro ibrido per runbook. Quando si aggiunge la soluzione all'area di lavoro Log Analytics, inserisce automaticamente i componenti del ruolo di lavoro nel computer agente che verrà installato nel passaggio successivo.

Per aggiungere la soluzione di **automazione** all'area di lavoro, eseguire il cmdlet di PowerShell seguente.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

#### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. installare l'agente di Log Analytics per Windows

L'agente di Log Analytics per Windows connette i computer a un'area di lavoro Log Analytics di monitoraggio di Azure. Quando si installa l'agente nel computer e lo si connette all'area di lavoro, vengono scaricati automaticamente i componenti necessari per il ruolo di lavoro ibrido per Runbook.

Per installare l'agente nel computer, seguire le istruzioni riportate in [connettere i computer Windows ai log di monitoraggio di Azure](../log-analytics/log-analytics-windows-agent.md). È possibile ripetere questo processo per più computer per aggiungere più ruoli di lavoro nell'ambiente.

Quando l'agente si è connesso correttamente all'area di lavoro di Log Analytics dopo alcuni minuti, è possibile eseguire la query seguente per verificare che invii i dati heartbeat all'area di lavoro:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nei risultati della ricerca restituiti, devono venire visualizzati i record di heartbeat per il computer indicanti che è connesso e che invia report al servizio. Per impostazione predefinita, il record heartbeat viene inviato da ogni agente all'area di lavoro assegnata. È possibile verificare che l'agente abbia scaricato correttamente la soluzione di automazione se include una cartella **AzureAutomationFiles** in C:\Programmi\Microsoft Monitoring Agent\Agent. Per confermare la versione del ruolo di lavoro ibrido per Runbook, passare a C:\Programmi\Microsoft Monitoring Agent\Agent\AzureAutomation\ e prendere nota della sottocartella \\*Version* .

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. installare l'ambiente Runbook e connettersi ad automazione di Azure

Quando si configura un agente per la segnalazione a un'area di lavoro Log Analytics, la soluzione di automazione esegue il push del modulo **HybridRegistration** di PowerShell, che contiene il cmdlet **Add-HybridRunbookWorker** . È possibile usare questo cmdlet per installare l'ambiente runbook nel computer e registrarlo in Automazione di Azure.

Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Eseguire quindi il cmdlet **Add-HybridRunbookWorker** con la sintassi seguente:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

È possibile ottenere le informazioni necessarie per questo cmdlet dalla pagina **Gestisci chiavi** del portale di Azure. Aprire la pagina selezionando l'opzione **Chiavi** nella pagina **Impostazioni** dell'account di Automazione.

![Pagina "Gestisci chiavi"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** è il nome del gruppo Ruolo di lavoro ibrido per runbook. Se il gruppo esiste già nell'account di automazione, il computer corrente vi verrà aggiunto direttamente. Se questo gruppo non esiste, verrà aggiunto.
* **EndPoint** è il valore della voce **URL** nella pagina **Gestisci chiavi**.
* **Token** è la voce **CHIAVE DI ACCESSO PRIMARIA** nella pagina **Gestisci chiavi**.

Per ricevere informazioni dettagliate sull'installazione, usare l'opzione **-Verbose** con **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. installare i moduli di PowerShell

I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure. Questi moduli tuttavia non vengono distribuiti automaticamente nei computer locali, pertanto è necessario installarli manualmente. L'unica eccezione è rappresentata dal modulo Azure, che viene installato per impostazione predefinita, garantendo l'accesso ai cmdlet per tutti i servizi e le attività di Azure per Automazione di Azure.

Poiché lo scopo principale della funzionalità ruolo di lavoro ibrido per runbook è gestire le risorse locali, probabilmente sarà necessario installare i moduli che supportano queste risorse. Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere il [relativo articolo](/powershell/scripting/developer/windows-powershell). 

I moduli installati devono trovarsi in un percorso a cui fa riferimento la variabile di ambiente **PSModulePath** , in modo che il ruolo di lavoro ibrido possa importarli automaticamente. Per altre informazioni, vedere [Modifying the PSModulePath Installation Path](/powershell/scripting/developer/windows-powershell) (Modifica del percorso di installazione di PSModulePath).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).
* Per istruzioni su come rimuovere i ruoli di lavoro ibridi per runbook, vedere [Rimuovere ruoli di lavoro ibridi di Automazione di Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Per informazioni su come risolvere i problemi del ruolo di lavoro ibrido per runbook, vedere [Troubleshooting Windows Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#windows) (Risoluzione dei problemi dei ruoli di lavoro ibridi per runbook di Windows)
* Per altre procedure di risoluzione dei problemi con Gestione aggiornamenti, vedere [Gestione degli aggiornamenti - Risoluzione dei problemi](troubleshoot/update-management.md).
