---
title: Distribuire un ruolo di lavoro ibrido per runbook Windows in Automazione di Azure
description: Questo articolo descrive come distribuire un ruolo di lavoro ibrido per Runbook che può essere usato per eseguire manuali operativi in computer basati su Windows nel Data Center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: e77a90617d79dab8c71cdf0c7a6a4fb21e052fbd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182785"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Windows

È possibile usare la funzionalità user Hybrid Runbook Worker di automazione di Azure per eseguire manuali operativi direttamente nel computer Azure o non Azure, inclusi i server registrati con i [server abilitati per Azure Arc](../azure-arc/servers/overview.md). Dal computer o dal server che ospita il ruolo, è possibile eseguire manuali operativi direttamente e in base alle risorse nell'ambiente per gestire tali risorse locali.

Automazione di Azure archivia e gestisce manuali operativi e li recapita a uno o più computer designati. Questo articolo descrive come distribuire un ruolo di lavoro ibrido per Runbook utente in un computer Windows, come rimuovere il ruolo di lavoro e come rimuovere un gruppo di ruolo di lavoro ibrido per Runbook.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre di quanto segue.

### <a name="a-log-analytics-workspace"></a>Area di lavoro Log Analytics

Il ruolo di lavoro ibrido per Runbook dipende da un'area di lavoro di monitoraggio di Azure Log Analytics per installare e configurare il ruolo. È possibile crearlo tramite [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), tramite [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)o nel [portale di Azure](../azure-monitor/learn/quick-create-workspace.md).

Se non si dispone di un'area di lavoro Log Analytics di monitoraggio di Azure, vedere le [indicazioni sulla progettazione dei log di monitoraggio di Azure](../azure-monitor/platform/design-logs-deployment.md) prima di creare l'area di lavoro

### <a name="log-analytics-agent"></a>Agente di Log Analytics

Il ruolo di lavoro ibrido per Runbook richiede l' [agente di log Analytics](../azure-monitor/platform/log-analytics-agent.md) per il sistema operativo Windows supportato. Per i server o i computer ospitati all'esterno di Azure, è possibile installare l'agente di Log Analytics usando i [server abilitati per Azure Arc](../azure-arc/servers/overview.md).

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows supportato

La funzionalità Hybrid Runbook Worker supporta i sistemi operativi seguenti:

* Windows Server 2019
* Windows Server 2016, versione 1709 e 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (inclusa la versione multisessione) e Pro
* Windows 8 Enterprise e Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Requisiti minimi

I requisiti minimi per un sistema Windows e un ruolo di lavoro ibrido per Runbook utente sono i seguenti:

* Windows PowerShell 5,1 ([scaricare WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)). PowerShell Core non è supportato.
* .NET Framework 4.6.2 o versioni successive
* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

### <a name="network-configuration"></a>Configurazione di rete

Per i requisiti di rete per il ruolo di lavoro ibrido per Runbook, vedere [configurazione della rete](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Aggiunta di un computer a un gruppo di ruolo di lavoro ibrido per Runbook

È possibile aggiungere il computer di lavoro a un gruppo di ruolo di lavoro ibrido per Runbook in uno degli account di automazione. Per i computer che ospitano il ruolo di lavoro ibrido per Runbook di sistema gestito da Gestione aggiornamenti, possono essere aggiunti a un gruppo di lavoro ibrido per Runbook. È tuttavia necessario usare lo stesso account di automazione sia per Gestione aggiornamenti che per l'appartenenza al gruppo di lavoro ibrido per Runbook.

>[!NOTE]
>Automazione di Azure [Gestione aggiornamenti](./update-management/overview.md) installa automaticamente il ruolo di lavoro ibrido per Runbook di sistema in un computer Azure o non Azure abilitato per l'gestione aggiornamenti. Questo ruolo di lavoro, tuttavia, non è registrato con alcun gruppo di ruolo di lavoro ibrido per Runbook nell'account di automazione. Per eseguire il manuali operativi in questi computer, è necessario aggiungerli a un gruppo di lavoro ibrido per Runbook. Seguire il passaggio 6 nella sezione [distribuzione manuale](#manual-deployment) per aggiungerlo a un gruppo.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Abilitare per la gestione con la configurazione dello stato di automazione di Azure

Per informazioni sull'abilitazione dei computer per la gestione con la configurazione dello stato di automazione di Azure, vedere [abilitare le macchine virtuali per la gestione mediante la configurazione dello stato di automazione](automation-dsc-onboarding.md)

> [!NOTE]
> Per gestire la configurazione dei computer che supportano il ruolo di lavoro ibrido per runbook con desired state Configuration (DSC), è necessario aggiungere i computer come nodi DSC.

## <a name="installation-options"></a>Opzione di installazione

Per installare e configurare un ruolo di lavoro ibrido per Runbook utente di Windows, è possibile usare uno dei metodi seguenti.

* Usare uno script di PowerShell fornito per [automatizzare](#automated-deployment) completamente il processo di configurazione di uno o più computer Windows. Questo è il metodo consigliato per i computer nel data center o in un altro ambiente cloud.
* Importare manualmente la soluzione di automazione, installare l'agente Log Analytics per Windows e configurare il ruolo di lavoro nel computer.

## <a name="automated-deployment"></a>Distribuzione automatizzata

Il metodo di distribuzione automatica usa lo script di PowerShell **New-OnPremiseHybridWorker.ps1** per automatizzare e configurare il ruolo di lavoro ibrido per Runbook di Windows. Esegue le operazioni seguenti:

* Installa i moduli necessari
* Accede con l'account Azure
* Verifica l'esistenza di un gruppo di risorse e di un account di automazione specificati
* Crea riferimenti agli attributi dell'account di automazione
* Crea un'area di lavoro di monitoraggio di Azure Log Analytics se non è specificata
* Abilitare la soluzione automazione di Azure nell'area di lavoro
* Scaricare e installare l'agente di Log Analytics per Windows
* Registrare il computer come ruolo di lavoro ibrido per Runbook

Eseguire la procedura seguente per installare il ruolo nel computer Windows tramite lo script.

1. Scaricare lo script **New-OnPremiseHybridWorker.ps1** da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Dopo aver scaricato lo script, copiarlo o eseguirlo nel computer di destinazione. Durante l'esecuzione lo script **New-OnPremiseHybridWorker.ps1** utilizza i seguenti parametri.

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

2. Aprire un prompt dei comandi di PowerShell con privilegi elevati a 64 bit.

3. Al prompt dei comandi di PowerShell passare alla cartella che contiene lo script scaricato. Modificare i valori per i parametri `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` e `WorkspaceName`. Quindi, eseguire lo script.

    Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script. È necessario accedere con un account membro del ruolo **amministratori della sottoscrizione** e coamministratore della sottoscrizione.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

4. Verrà chiesto di accettare l'installazione di NuGet e di eseguire l'autenticazione con le credenziali di Azure. Se non si dispone della versione più recente di NuGet, è possibile scaricarla dalle [versioni di distribuzione NuGet disponibili](https://www.nuget.org/downloads).

5. Verificare la distribuzione al termine dello script. Dalla pagina dei **gruppi di ruoli di lavoro ibridi per Runbook** nell'account di automazione, nella scheda **gruppo Runbook Worker ibrido utente** vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo dall'elenco della pagina, dal menu a sinistra scegliere ruoli di **lavoro ibridi** . Nella pagina ruoli di **lavoro ibridi** è possibile visualizzare tutti i membri del gruppo elencati.

## <a name="manual-deployment"></a>Distribuzione manuale

Per installare e configurare un ruolo di lavoro ibrido per Runbook di Windows, seguire questa procedura.

1. Abilitare la soluzione automazione di Azure nell'area di lavoro di Log Analytics eseguendo il comando seguente in un prompt dei comandi di PowerShell con privilegi elevati o in Cloud Shell nel [portale di Azure](https://portal.azure.com).

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Distribuire l'agente di Log Analytics nel computer di destinazione.

    * Per le macchine virtuali di Azure, installare l'agente di Log Analytics per Windows usando [l'estensione macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md). L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro Log Analytics esistente. È possibile usare un modello di Azure Resource Manager, PowerShell o un criterio di Azure per assegnare i criteri predefiniti [Distribuisci agente log Analytics per VM *Linux* o *Windows*](../governance/policy/samples/built-in-policies.md#monitoring) . Una volta installato l'agente, è possibile aggiungere il computer a un gruppo di ruolo di lavoro ibrido per Runbook nell'account di automazione.
    
    * Per i computer non Azure è possibile installare l'agente di Log Analytics usando i [server abilitati per Azure Arc](../azure-arc/servers/overview.md). I server abilitati per Arc supportano la distribuzione dell'agente di Log Analytics usando i metodi seguenti:
    
        - Uso del framework delle estensioni VM.
        
            Questa funzionalità in Azure Arc Enabled Server consente di distribuire l'estensione di macchina virtuale Log Analytics Agent in un server Windows e/o Linux non Azure. Le estensioni di macchina virtuale possono essere gestite usando i metodi seguenti nei computer ibridi o nei server gestiti da server abilitati per Arc:
        
            - Il[portale di Azure](../azure-arc/servers/manage-vm-extensions-portal.md)
            - L'[interfaccia della riga di comando di Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - [Modelli di gestione risorse](../azure-arc/servers/manage-vm-extensions-template.md) di Azure
        
        - Uso di criteri di Azure.
        
            Con questo approccio, è possibile usare i criteri di [distribuzione di log Analytics Agent di Azure Policy to Linux o Windows Azure Arc](../governance/policy/samples/built-in-policies.md#monitoring) per controllare se nel server abilitato per Arc è installato log Analytics Agent. Se l'agente non è installato, lo distribuisce automaticamente utilizzando un'attività di correzione. In alternativa, se si prevede di monitorare i computer con Monitoraggio di Azure per le macchine virtuali, usare invece l'iniziativa [abilita monitoraggio di Azure per le macchine virtuali](../governance/policy/samples/built-in-initiatives.md#monitoring) per installare e configurare l'agente di log Analytics.

    Si consiglia di installare l'agente di Log Analytics per Windows o Linux usando criteri di Azure.

3. Verificare che l'agente stia inviando report all'area di lavoro

    L'agente di Log Analytics per Windows connette i computer a un'area di lavoro Log Analytics di monitoraggio di Azure. Quando si installa l'agente nel computer e lo si connette all'area di lavoro, vengono scaricati automaticamente i componenti necessari per il ruolo di lavoro ibrido per Runbook.

    Quando l'agente si è connesso correttamente all'area di lavoro Log Analytics dopo alcuni minuti, è possibile eseguire la query seguente per verificare che invii dati di heartbeat all'area di lavoro.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nei risultati della ricerca verranno visualizzati i record heartbeat per il computer, a indicare che sono connessi e segnalati al servizio. Per impostazione predefinita, ogni agente trasmette un record di heartbeat all'area di lavoro assegnata. Per completare l'installazione e la configurazione dell'agente, eseguire queste operazioni.

4. Verificare la versione del ruolo di lavoro ibrido per Runbook nel computer che ospita l'agente di Log Analytics, individuare `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` e prendere nota della sottocartella **Version** . Questa cartella verrà visualizzata nel computer alcuni minuti dopo l'abilitazione della soluzione nell'area di lavoro.

5. Installare l'ambiente Runbook e connettersi ad automazione di Azure. Quando si configura un agente per la segnalazione a un'area di lavoro Log Analytics e si importa la soluzione di **automazione** , la soluzione esegue il push del `HybridRegistration` modulo PowerShell. Questo modulo contiene il `Add-HybridRunbookWorker` cmdlet. Usare questo cmdlet per installare l'ambiente Runbook nel computer e registrarlo in automazione di Azure.

    Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. Eseguire il `Add-HybridRunbookWorker` cmdlet specificando i valori per i parametri `Url` , `Key` e `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    È possibile ottenere le informazioni necessarie per i parametri `Url` e `Key` dalla pagina **chiavi** nell'account di automazione. Selezionare **chiavi** nella sezione **Impostazioni account** nella parte sinistra della pagina.

    ![Pagina Gestisci chiavi](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Per il `Url` parametro, copiare il valore di **URL**.

    * Per il `Key` parametro, copiare il valore della **chiave di accesso primaria**.

    * Per il parametro `GroupName`, usare il nome del gruppo di ruoli di lavoro ibridi per runbook. Se questo gruppo è già presente nell'account di automazione, viene aggiunto al computer corrente. Se questo gruppo non esiste, verrà aggiunto.

    * Se necessario, impostare il parametro `Verbose` per ricevere i dettagli sull'installazione.

7. Verificare la distribuzione dopo il completamento del comando. Dalla pagina dei **gruppi di ruoli di lavoro ibridi per Runbook** nell'account di automazione, nella scheda **gruppo Runbook Worker ibrido utente** viene visualizzato il gruppo nuovo o esistente e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo dall'elenco della pagina, dal menu a sinistra scegliere ruoli di **lavoro ibridi**. Nella pagina ruoli di **lavoro ibridi** è possibile visualizzare tutti i membri del gruppo elencati.

## <a name="install-powershell-modules"></a>Installare i moduli di PowerShell

I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure. Poiché questi moduli non vengono distribuiti automaticamente nei computer locali, è necessario installarli manualmente. Il modulo di Azure è l'eccezione. Tale modulo viene installato per impostazione predefinita e fornisce l'accesso ai cmdlet per tutti i servizi e le attività di Azure per Automazione di Azure.

Poiché lo scopo principale del ruolo di lavoro ibrido per runbook è quello di gestire le risorse locali, probabilmente sarà necessario installare i moduli che supportano queste risorse, particolarmente il modulo `PowerShellGet`. Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

I moduli installati devono trovarsi in un percorso a cui fa riferimento la variabile di ambiente `PSModulePath`, in modo che il ruolo di lavoro ibrido possa importarli automaticamente. Per altre informazioni, vedere [Installare moduli in PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Rimuovere il ruolo di lavoro ibrido per Runbook

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