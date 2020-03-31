---
title: Risolvere gli errori con Gestione aggiornamenti di AzureTroubleshoot errors with Azure Update Management
description: Informazioni su come risolvere e risolvere i problemi relativi alla soluzione di gestione degli aggiornamenti in Azure.Learn how to troubleshoot and resolve issues with the Update Management solution in Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91bcdc65a7ff3bcaf09f12d69ba4c7aaeb84ffa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132865"
---
# <a name="troubleshooting-issues-with-update-management"></a>Risoluzione dei problemi con Gestione aggiornamenti

In questo articolo vengono illustrate le soluzioni ai problemi che possono verificarsi quando si utilizza Gestione aggiornamenti.

È disponibile uno strumento di risoluzione dei problemi dell'agente per l'agente di lavoro ibrido per determinare il problema sottostante. Per altre informazioni sullo strumento di risoluzione dei problemi, vedere [Risolvere i problemi dell'agente di aggiornamento](update-agent-issues.md). Per tutti gli altri problemi, utilizzare le seguenti indicazioni per la risoluzione dei problemi.

Se si verificano problemi durante il tentativo di eseguire l'onboarding della soluzione in una macchina virtuale (VM), controllare il registro di `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`Operations **Manager** in Registri applicazioni e **servizi** nel computer locale per gli eventi con ID evento 4502 e i dettagli dell'evento che contengono .

Nella sezione seguente vengono evidenziati i messaggi di errore specifici e le possibili soluzioni per ciascuno di essi. Per altri problemi di onboarding, vedere Risolvere i problemi di [onboarding della soluzione](onboarding.md).

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Scenario: viene visualizzato l'errore "Impossibile attivare la soluzione di aggiornamento"

### <a name="issue"></a>Problema

Quando si tenta di abilitare la soluzione di gestione degli aggiornamenti nell'account di automazione, si verifica il seguente errore:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Causa

Le cause di questo errore sono le seguenti:

* I requisiti del firewall di rete per l'agente di Log Analytics potrebbero non essere configurati correttamente, causando l'errore dell'agente durante la risoluzione degli URL DNS.

* Il targeting della soluzione non è configurato correttamente e il computer non riceve gli aggiornamenti come previsto.

* Si potrebbe anche notare che `Non-compliant` la macchina mostra lo stato di sotto **Conformità**. Allo stesso tempo, la preparazione dell'aggiornamento **dell'agente** segnala l'agente come `Disconnected`.

### <a name="resolution"></a>Risoluzione

* Eseguire lo strumento di risoluzione dei problemi per [Windows](update-agent-issues.md#troubleshoot-offline) o [Linux](update-agent-issues-linux.md#troubleshoot-offline), a seconda del sistema operativo.

* Passare a [Pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento di Gestione aggiornamenti.  

* Passare a [Pianificazione della rete](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento dell'agente di Log Analytics.Go to Network planning to learn about which addresses and ports must be allowed for the Log Analytics agent to work.

* Verificare la presenza di problemi di configurazione dell'ambito. [La configurazione dell'ambito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quali computer vengono configurati per la soluzione. Se il computer viene visualizzato nell'area di lavoro ma non nel portale di **gestione degli aggiornamenti,** è necessario impostare la configurazione dell'ambito per i computer di destinazione. Per informazioni sulla configurazione dell'ambito, vedere [Eseguire l'onboarding dei computer nell'area di lavoro.](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)

* Rimuovere la configurazione del lavoratore seguendo la procedura descritta in Eliminazione del ruolo di lavoro ibrido per [runbook.](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker) 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: aggiornamento sostituito indicato come mancante nella gestione degli aggiornamenti

### <a name="issue"></a>Problema

Gli aggiornamenti precedenti vengono visualizzati in Gestione aggiornamenti nell'account di automazione come mancante anche se sono stati sostituiti. Un aggiornamento sostituito è un aggiornamento che non deve essere installato perché è disponibile un aggiornamento successivo che corregge la stessa vulnerabilità. Gestione aggiornamenti ignora l'aggiornamento sostituito e lo rende non applicabile a favore dell'aggiornamento di sostituzione. Per informazioni su un problema correlato, vedere [Aggiornamento sostituito.](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="cause"></a>Causa

Gli aggiornamenti sostituiti non vengono indicati correttamente come rifiutati in modo che possano essere considerati non applicabili.

### <a name="resolution"></a>Risoluzione

Quando un aggiornamento sostituito diventa non applicabile al 100%, è `Declined`necessario modificare lo stato di approvazione di tale aggiornamento in . Per eseguire questa operazione per tutti gli aggiornamenti:

1. Nell'account di automazione selezionare **Gestione aggiornamenti** per visualizzare lo stato del computer. Consultate [Visualizzare le valutazioni degli aggiornamenti.](../manage-update-multi.md#view-an-update-assessment)

2. Controllare l'aggiornamento sostituito per assicurarsi che sia 100% non applicabile. 

3. Contrassegnare l'aggiornamento come rifiutato a meno che non si abbia una domanda sull'aggiornamento. 

4. Selezionare **Computer** e, nella colonna **Conformità,** forzare una rianalisi per la conformità. Consultate [Gestire gli aggiornamenti per più computer.](../manage-update-multi.md)

5. Ripetere i passaggi precedenti per gli altri aggiornamenti sostituiti.

6. Eseguire la pulizia guidata per eliminare i file dagli aggiornamenti rifiutati. 

7. Per WSUS, pulire manualmente tutti gli aggiornamenti sostituiti per aggiornare l'infrastruttura.

8. Ripetere questa procedura regolarmente per correggere il problema di visualizzazione e ridurre al minimo la quantità di spazio su disco utilizzata per la gestione degli aggiornamenti.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: i computer non vengono visualizzati nel portale in Gestione aggiornamenti

### <a name="issue"></a>Problema

Le macchine presentano i seguenti sintomi:

* Il computer `Not configured` viene visualizzato dalla visualizzazione Gestione aggiornamenti di una macchina virtuale.

* I computer non sono presenti nella visualizzazione Gestione aggiornamenti dell'account di Automazione di Azure.Your machines are missing on the Update Management view of your Azure Automation account.

* Si dispone di `Not assessed` macchine che vengono visualizzate come in **Conformità**. Tuttavia, vengono visualizzati i dati heartbeat nei log di Monitoraggio di Azure per il ruolo di lavoro ibrido per runbook, ma non per la gestione degli aggiornamenti.

### <a name="cause"></a>Causa

Questo problema può essere causato da problemi di configurazione locale o da una configurazione dell'ambito configurata in modo non corretto.

Potrebbe essere necessario registrare nuovamente e reinstallare il ruolo di lavoro ibrido per runbook.

È possibile che sia stata definita una quota nell'area di lavoro raggiunta e che impedisca un ulteriore spazio di archiviazione dei dati.

### <a name="resolution"></a>Risoluzione

* Eseguire lo strumento di risoluzione dei problemi per [Windows](update-agent-issues.md#troubleshoot-offline) o [Linux](update-agent-issues-linux.md#troubleshoot-offline), a seconda del sistema operativo.

* Assicurarsi che il computer stia segnalando all'area di lavoro corretta. Per istruzioni su come verificare questo aspetto, vedere Verificare la [connettività dell'agente a Log Analytics.](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics) Assicurarsi inoltre che questa area di lavoro sia collegata all'account di Automazione di Azure.Also make sure that this workspace is linked to your Azure Automation account. Per confermare, passare all'account di automazione e selezionare **Area di lavoro collegata** in **Risorse correlate**.

* Assicurarsi che i computer vengano visualizzati nell'area di lavoro di Log Analytics. Eseguire la query seguente nell'area di lavoro di Log Analytics collegata all'account di automazione:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
 
  Se il computer non viene visualizzato nei risultati della query, non è stato archiviato di recente, il che significa che probabilmente si è verificato un problema di configurazione locale ed è necessario [reinstallare l'agente.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows) Se il computer viene visualizzato nei risultati della query, è necessario verificare la configurazione dell'ambito specificata nell'elemento puntato successivo in questo elenco.

* Verificare la presenza di problemi di configurazione dell'ambito. [La configurazione dell'ambito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quali computer vengono configurati per la soluzione. Se il computer viene visualizzato nell'area di lavoro ma non nel portale di **gestione degli aggiornamenti,** è necessario configurare la configurazione dell'ambito per i computer di destinazione. Per informazioni su come eseguire questa operazione, vedere [I computer di bordo nell'area di lavoro](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Nell'area di lavoro eseguire la query seguente:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

  Se si `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` ottiene un risultato, è presente una quota definita nell'area di lavoro che è stata raggiunta e che ha impedito il salvataggio dei dati. Nell'area di lavoro passare a Utilizzo e gestione del volume di > **dati** dei **costi stimati e**controllare la quota o rimuoverla.

* Se questa procedura non consente di risolvere il problema, seguire la procedura descritta in Distribuire un ruolo di lavoro ibrido di Windows per reinstallare il ruolo di lavoro ibrido per Windows.If these steps don't resolve your problem, follow the steps in Deploy a [Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) to reinstall the Hybrid Worker for Windows. Per Linux, seguire i passaggi descritti in Distribuire un ruolo di lavoro [runbook ibrido Linux.](../automation-linux-hrw-install.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: impossibile registrare il provider di risorse di automazione per le sottoscrizioniScenario: Unable to register Automation Resource Provider for subscriptions

### <a name="issue"></a>Problema

Quando si utilizzano soluzioni nell'account di automazione, si verifica il seguente errore:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

Il provider di risorse di automazione non è registrato nella sottoscrizione.

### <a name="resolution"></a>Risoluzione

Per registrare il provider di risorse di automazione, seguire questi passaggi nel portale di Azure:To register the Automation Resource Provider, follow these steps in the Azure portal:

1. Nell'elenco Servizio di Azure nella parte inferiore del portale selezionare **Tutti i servizi**e quindi **Sottoscrizioni** nel gruppo di servizi Generale.
2. Selezionare la propria sottoscrizione.
3. In **Impostazioni**selezionare **Provider di risorse**.
4. Nell'elenco dei provider di `Microsoft.Automation` risorse verificare che il provider di risorse sia registrato.
5. Se non è presente nell'elenco, registrare il `Microsoft.Automation` provider seguendo la procedura descritta in Risolvere gli errori per la registrazione del provider di [risorse](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: componenti per la soluzione di gestione degli aggiornamenti abilitati, mentre la macchina virtuale continua a essere visualizzata come configurataScenario: Components for Update Management solution enabled, while VM continues to show as being configured

### <a name="issue"></a>Problema

Nella macchina virtuale continua a essere visualizzato il messaggio seguente 15 minuti dopo l'onboarding:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Le cause di questo errore sono le seguenti:

* La comunicazione con l'account di automazione è bloccata.

* Esiste un nome computer duplicato con ID computer di origine diversi. Questo scenario si verifica quando una macchina virtuale con un nome di computer specifico viene creata in gruppi di risorse diversi e segnala la stessa area di lavoro dell'agente logistico nella sottoscrizione.

* L'immagine della macchina virtuale in servizio potrebbe provenire da un computer clonato che non è stato preparato con la preparazione del sistema (sysprep) con Microsoft Monitoring Agent (MMA) installato.

### <a name="resolution"></a>Risoluzione

Per determinare il problema esatto della macchina virtuale, eseguire la query seguente nell'area di lavoro di Log Analytics collegata all'account di automazione:

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Comunicazione con l'account Automation bloccata

Passare a [Pianificazione della rete](../automation-update-management.md#ports) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento di Gestione aggiornamenti.

#### <a name="duplicate-computer-name"></a>Nome computer duplicato

Rinominare le macchine virtuali per garantire nomi univoci nel proprio ambiente.

#### <a name="onboarded-image-from-cloned-machine"></a>Immagine onboarding dalla macchina clonata

Se si utilizza un'immagine clonata, nomi di computer diversi hanno lo stesso ID computer di origine. In questo caso:

1. Nell'area di lavoro di Log Analytics rimuovere `MicrosoftDefaultScopeConfig-Updates` la macchina virtuale dalla ricerca salvata per la configurazione dell'ambito, se visualizzata. Le ricerche salvate sono disponibili in **Generale** nell'area di lavoro.

2. Eseguire il cmdlet seguente:

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Eseguire `Restart-Service HealthService` per riavviare il servizio integrità. Questa operazione ricrea la chiave e genera un nuovo UUID.

4. Se questo approccio non funziona, eseguire sysprep sull'immagine prima e quindi installare l'MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: viene visualizzato un errore di sottoscrizione collegata quando si crea una distribuzione di aggiornamento per i computer in un altro tenant di AzureScenario: You receive a linked subscription error when you create an update deployment for machines in another Azure tenant

### <a name="issue"></a>Problema

Si verifica il seguente errore quando si tenta di creare una distribuzione di aggiornamento per i computer in un altro tenant di Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando si crea una distribuzione di aggiornamento con macchine virtuali di Azure in un altro tenant incluso in una distribuzione di aggiornamento.

### <a name="resolution"></a>Risoluzione

Utilizzare la soluzione seguente per visualizzare questi elementi pianificati. È possibile usare il cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) con il `ForUpdate` parametro per creare una pianificazione. Quindi, utilizzare il cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passare i `NonAzureComputer` computer nell'altro tenant al parametro. L'esempio seguente illustra come farlo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: riavvii inspiegabili

### <a name="issue"></a>Problema

Anche se è stata impostata l'opzione **Controllo riavvio su** **Non riavviare mai**, i computer vengono comunque riavviati dopo l'installazione degli aggiornamenti.

### <a name="cause"></a>Causa

Windows Update può essere modificato da diverse chiavi del Registro di sistema, ognuna delle quali può modificare il comportamento di riavvio.

### <a name="resolution"></a>Risoluzione

Esaminare le chiavi del Registro di sistema elencate in Configurazione di [Aggiornamenti automatici modificando il Registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) di sistema e [le chiavi del Registro](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) di sistema utilizzate per gestire il riavvio per assicurarsi che i computer siano configurati correttamente.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: computer viene visualizzato "Impossibile avviare" in una distribuzione di aggiornamento

### <a name="issue"></a>Problema

Una macchina `Failed to start` mostra uno stato. Quando si visualizzano i dettagli specifici per la macchina, viene visualizzato il seguente errore:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi per uno dei motivi seguenti:

* La macchina non esiste più.
* La macchina è spenta e irraggiungibile.
* La macchina presenta un problema di connettività di rete e pertanto il ruolo di lavoro ibrido nel computer non è raggiungibile.
* Si è verificato un aggiornamento per mMA che ha modificato l'ID del computer di origine.
* L'esecuzione dell'aggiornamento è stata limitata se si raggiunge il limite di 2000 processi simultanei in un account di automazione. Ogni distribuzione è considerata un processo e ogni computer in una distribuzione di aggiornamento viene considerato come processo. Qualsiasi altro processo di automazione o distribuzione di aggiornamento attualmente in esecuzione nell'account di automazione viene conteggiato per il limite di processi simultanei.

### <a name="resolution"></a>Risoluzione

Se applicabile, utilizzare [i gruppi dinamici](../automation-update-management-groups.md) per le distribuzioni degli aggiornamenti. Inoltre:

* Verificare che il computer esista e sia ancora raggiungibile. 
* Se il computer non esiste, modificare la distribuzione e rimuoverla.
* Vedere la sezione [Pianificazione della rete](../automation-update-management.md#ports) per un elenco delle porte e degli indirizzi necessari per la gestione degli aggiornamenti e quindi verificare che il computer soddisfi questi requisiti.
* Verificare la connettività al ruolo di lavoro ibrido per runbook usando lo strumento di risoluzione dei problemi dell'agente di lavoro ibrido per runbook. Per altre informazioni sullo strumento di risoluzione dei problemi, vedere [Risolvere i problemi dell'agente di aggiornamento](update-agent-issues.md).
* Eseguire la query seguente in Log Analytics per trovare i computer nell'ambiente per cui è stato modificato l'ID del computer di origine. Cercare i computer che `Computer` hanno lo `SourceComputerId` stesso valore ma un valore diverso.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

* Dopo aver trovato i computer interessati, modificare le distribuzioni di aggiornamento destinate a tali computer, quindi rimuoverle e aggiungerle nuovamente in modo che `SourceComputerId` riflettano il valore corretto.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: gli aggiornamenti vengono installati senza una distribuzioneScenario: Updates are installed without a deployment

### <a name="issue"></a>Problema

Quando si registra un computer Windows in Gestione aggiornamenti, vengono visualizzati gli aggiornamenti installati senza una distribuzione.

### <a name="cause"></a>Causa

In Windows, gli aggiornamenti vengono installati automaticamente non appena sono disponibili. Questo comportamento può causare confusione se non è stato pianificato un aggiornamento da distribuire al computer.

### <a name="resolution"></a>Risoluzione

Il `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` valore predefinito della chiave del `auto download and install`Registro di sistema è l'impostazione 4: .

Per i client di gestione degli aggiornamenti, è consigliabile impostare questa chiave su 3: `auto download but do not auto install`.

Per ulteriori informazioni, vedere [Configurazione di Aggiornamenti automatici](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: Il computer è già registrato per un altro account

### <a name="issue"></a>Problema

Viene visualizzato il messaggio di errore seguente:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

La macchina è già stata caricata in un'altra area di lavoro per la gestione degli aggiornamenti.

### <a name="resolution"></a>Risoluzione

1. Seguire i passaggi descritti in [I computer non vengono visualizzati nel portale in Gestione aggiornamenti](#nologs) per assicurarsi che il computer segnali all'area di lavoro corretta.
2. Pulire gli elementi nel computer eliminando il gruppo ibrido di [runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), quindi riprovare.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: la macchina non può comunicare con il servizio

### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Causa

Un proxy, un gateway o un firewall potrebbe bloccare la comunicazione di rete. 

### <a name="resolution"></a>Risoluzione

Esaminare la rete e assicurarsi che le porte e gli indirizzi appropriati siano consentiti. Vedere [i requisiti](../automation-hybrid-runbook-worker.md#network-planning) di rete per un elenco delle porte e degli indirizzi richiesti da Update Management e Hybrid Runbook Workers.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: Impossibile creare un certificato autofirmato

### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per runbook non è in stato di generazione di un certificato autofirmato.

### <a name="resolution"></a>Risoluzione

Verificare che l'account di sistema disponga dell'accesso in lettura alla cartella **C:**

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: l'aggiornamento pianificato non è riuscito con un errore MaintenanceWindowExceeded

### <a name="issue"></a>Problema

La finestra di manutenzione predefinita per gli aggiornamenti è 120 minuti. È possibile aumentare la finestra di manutenzione a un massimo di 6 ore o 360 minuti.

### <a name="resolution"></a>Risoluzione

Modificare eventuali distribuzioni di aggiornamenti pianificate non riuscite e aumentare la finestra di manutenzione.

Per ulteriori informazioni sulle finestre di manutenzione, vedere [Installare gli aggiornamenti](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: il computer viene visualizzato come "Non valutato" e mostra un'eccezione HRESULT

### <a name="issue"></a>Problema

* Sono presenti computer `Not assessed` che vengono visualizzati come in **Conformità**e viene visualizzato un messaggio di eccezione sotto di essi.
* Viene visualizzato un codice di errore HRESULT nel portale.

### <a name="cause"></a>Causa

L'agente di aggiornamento (agente di Windows Update in Windows; il gestore di pacchetti per una distribuzione Linux) non è configurato correttamente. Gestione aggiornamenti si basa sull'agente di aggiornamento del computer per fornire gli aggiornamenti necessari, lo stato della patch e i risultati delle patch distribuite. Senza queste informazioni, Gestione aggiornamenti non è in grado di creare correttamente report sulle patch necessarie o installate.

### <a name="resolution"></a>Risoluzione

Provare a eseguire gli aggiornamenti in locale nel computer. Se l'operazione non riesce, in genere significa che si è verificato un errore di configurazione con l'agente di aggiornamento.

Questo problema è spesso causato da problemi di configurazione di rete e firewall. Attenersi alla procedura seguente:

* Per Linux, consultare la documentazione appropriata per assicurarsi di poter raggiungere l'endpoint di rete del repository dei pacchetti.
* Per Windows, controllare la configurazione dell'agente come elencato in [Gli aggiornamenti non vengono scaricati dall'endpoint Intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Se i computer sono configurati per Windows Update, assicurarsi di poter raggiungere gli endpoint descritti in [Problemi relativi a HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se i computer sono configurati per Windows Server Update Services (WSUS), assicurarsi che sia possibile raggiungere il server WSUS configurato dalla [chiave del Registro](/windows/deployment/update/waas-wu-settings)di sistema WUServer .

Se viene visualizzato un HRESULT, fare doppio clic sull'eccezione visualizzata in rosso per visualizzare l'intero messaggio di eccezione. Esaminare la tabella seguente per le soluzioni potenziali o le azioni consigliate:Review the following table for potential solutions or recommended actions:

|Eccezione  |Risoluzione o azione  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Cercare il codice di errore pertinente nell'elenco dei codici di errore di [Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) per trovare ulteriori dettagli sulla causa dell'eccezione.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Questi indicano problemi di connettività di rete. Assicurarsi che il computer disponga di connettività di rete a Gestione aggiornamenti. Vedere la sezione [Pianificazione della rete](../automation-update-management.md#ports) per un elenco delle porte e degli indirizzi necessari.        |
|`0x8024001E`| L'operazione di aggiornamento non è stata completata perché il servizio o il sistema è stato arrestato.|
|`0x8024002E`| Il servizio Windows Update è disabilitato.|
|`0x8024402C`     | Se si utilizza un server WSUS, assicurarsi `WUServer` `WUStatusServer` che `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` i valori del Registro di sistema per e sotto la chiave del Registro di sistema specificate il server WSUS corretto.        |
|`0x80072EE2`|Si è verificato un problema di connettività di rete o un problema nel comunicare con un server WSUS configurato. Controllare le impostazioni WSUS e assicurarsi che il servizio sia accessibile dal client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Assicurarsi che il servizio Windows Update (wuauserv) sia in esecuzione e non disabilitato.        |
|`0x80070005`| Un errore di accesso negato può essere causato da uno dei seguenti:<br> Computer infetto<br> Impostazioni di Windows Update non configurate correttamente<br> Errore di autorizzazione del file con la cartella %WinDir%/SoftwareDistribution<br> Spazio su disco insufficiente nell'unità di sistema (C:).
|Qualsiasi altra eccezione generica     | Eseguire una ricerca su Internet per le possibili soluzioni e lavorare con il supporto IT locale.         |

Anche la revisione del file %Windir%/Windowsupdate.log consente di determinare le possibili cause. Per ulteriori informazioni su come leggere il registro, vedere [Come leggere il file Windowsupdate.log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

È inoltre possibile scaricare ed eseguire lo strumento di risoluzione dei problemi di [Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) per verificare la presenza di eventuali problemi con Windows Update nel computer.

> [!NOTE]
> La documentazione per lo strumento di risoluzione dei problemi di [Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica che è utilizzabile nei client Windows, ma funziona anche su Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: l'esecuzione dell'aggiornamento restituisce lo stato "Failed" (Linux)

### <a name="issue"></a>Problema

Un'esecuzione dell'aggiornamento viene avviata ma rileva errori durante l'esecuzione.

### <a name="cause"></a>Causa

Possibili cause:

* Il gestore di pacchetti non è integro.
* Update Agent (WUA per Windows, gestore di pacchetti specifico per Linux) non è configurato correttamente.
* Pacchetti specifici interferiscono con l'applicazione di patch basata su cloud.
* La macchina è irraggiungibile.
* Gli aggiornamenti avevano dipendenze che non sono state risolte.

### <a name="resolution"></a>Risoluzione

Se si verificano errori durante l'esecuzione di un aggiornamento dopo l'avvio, [controllare l'output del processo](../manage-update-multi.md#view-results-of-an-update-deployment) dal computer interessato durante l'esecuzione. È possibile trovare messaggi di errore specifici dai computer che è possibile ricercare e intervenire. Gestione aggiornamenti richiede che la gestione pacchetti sia integra affinché le distribuzioni degli aggiornamenti abbiano esito positivo.

Se patch, pacchetti o aggiornamenti specifici vengono visualizzati immediatamente prima che il processo non riesca, è possibile provare [ad escluderli](../automation-tutorial-update-management.md#schedule-an-update-deployment) dalla distribuzione dell'aggiornamento successivo. Per raccogliere informazioni di registro da Windows Update, consultate File di registro di [Windows Update.](/windows/deployment/update/windows-update-logs)

Se non è possibile risolvere un problema di patch, creare una copia del file di registro seguente e conservarlo per la risoluzione dei problemi prima dell'inizio della distribuzione dell'aggiornamento successivo:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Le patch non sono installate

### <a name="machines-dont-install-updates"></a>I computer non installano gli aggiornamenti

Provare a eseguire gli aggiornamenti direttamente nel computer. Se il computer non è in grado di applicare gli aggiornamenti, consultare [l'elenco dei potenziali errori nella guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Se gli aggiornamenti vengono eseguiti localmente, provare a rimuovere e reinstallare l'agente nel computer seguendo le indicazioni fornite in [Remove a VM from Update Management](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>So che gli aggiornamenti sono disponibili, ma non vengono visualizzati come disponibili sui miei computer

Ciò si verifica spesso se i computer sono configurati per ottenere gli aggiornamenti da WSUS o Microsoft Endpoint Configuration Manager ma WSUS e Configuration Manager non hanno approvato gli aggiornamenti.

È possibile verificare se i computer sono configurati per WSUS e `UseWUServer` SCCM facendo riferimento incrociato alla chiave del Registro di sistema alle chiavi del Registro di sistema nella sezione Configurazione di [Aggiornamenti automatici modificando il Registro di sistema di questo articolo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Se gli aggiornamenti non vengono approvati in WSUS, non vengono installati. È possibile verificare la disponibilità di aggiornamenti non approvati in Log Analytics eseguendo la query seguente.

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Gli aggiornamenti vengono visualizzati come installati, ma non è possibile trovarli sul computer

Spesso gli aggiornamenti vengono sostituiti da altri aggiornamenti. Per ulteriori informazioni, vedere [aggiornamento viene sostituito](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) nella guida alla risoluzione dei problemi di Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Installazione degli aggiornamenti in base alla classificazione in Linux

La distribuzione degli aggiornamenti in Linux in base alla classificazione ("Aggiornamenti critici e della sicurezza") presenta avvertimenti importanti, in particolare per CentOS. Queste limitazioni sono documentate nella [pagina Panoramica](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)di Gestione aggiornamenti .

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 è costantemente mancante

KB2267602 è l'[aggiornamento delle definizioni di Windows Defender](https://www.microsoft.com/wdsi/definitions). Viene aggiornato ogni giorno.

## <a name="next-steps"></a>Passaggi successivi

Se non hai visto il problema o non riesci a risolvere il problema, prova uno dei seguenti canali per ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
