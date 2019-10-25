---
title: Risolvere gli errori con Gestione aggiornamenti
description: Informazioni su come risolvere i problemi relativi a Gestione aggiornamenti.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 2aebcf05cbc818997943ed3bab19fb1fd8a83592
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786050"
---
# <a name="troubleshooting-issues-with-update-management"></a>Risoluzione dei problemi con Gestione aggiornamenti

Questo articolo illustra le soluzioni ai problemi che possono verificarsi quando si usa Gestione aggiornamenti.

Per l'agente di lavoro ibrido è disponibile uno strumento di risoluzione dei problemi dell'agente per determinare il problema sottostante. Per altre informazioni sullo strumento di risoluzione dei problemi, vedere [Risolvere i problemi dell'agente di aggiornamento](update-agent-issues.md). Per tutti gli altri problemi, utilizzare le seguenti linee guida per la risoluzione dei problemi.

Se si verificano problemi durante il tentativo di caricare la soluzione in una macchina virtuale (VM), controllare il **Operations Manager** registro in **registri applicazioni e servizi** nel computer locale per gli eventi con ID evento 4502 e dettagli evento contenenti **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**.

Nella sezione seguente vengono evidenziati i messaggi di errore specifici e le possibili risoluzioni per ciascuno di essi. Per altri problemi di onboarding, vedere risolvere i problemi di caricamento della [soluzione](onboarding.md).

## <a name="nologs"></a>Scenario: i computer non vengono visualizzati nel portale in Gestione aggiornamenti

### <a name="issue"></a>Problema

Si verificano i sintomi seguenti:

* Il computer visualizzato **non è configurato** dalla visualizzazione Gestione aggiornamenti di una macchina virtuale.

* I computer non sono presenti nella visualizzazione Gestione aggiornamenti dell'account di automazione di Azure.

* Sono presenti computer che non sono **valutati** in **conformità**. Tuttavia, vengono visualizzati i dati heartbeat nei log di monitoraggio di Azure per il ruolo di lavoro ibrido per Runbook, ma non per Gestione aggiornamenti.

### <a name="cause"></a>Causa

Questo problema può essere causato da problemi di configurazione locali o da una configurazione non corretta dell'ambito.

Potrebbe essere necessario registrare nuovamente e reinstallare il ruolo di lavoro ibrido per Runbook.

È possibile che sia stata definita una quota nell'area di lavoro che è stata raggiunta e che impedisce l'archiviazione di altri dati.

### <a name="resolution"></a>Risoluzione

* Eseguire lo strumento di risoluzione dei problemi per [Windows](update-agent-issues.md#troubleshoot-offline) o [Linux](update-agent-issues-linux.md#troubleshoot-offline), a seconda del sistema operativo.

* Verificare che il computer stia segnalando l'area di lavoro corretta. Per indicazioni su come verificare questo aspetto, vedere [verificare la connettività dell'agente a log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Assicurarsi anche che questa area di lavoro sia collegata all'account di automazione di Azure. Per confermare, passare all'account di automazione e selezionare **area di lavoro collegata** in **risorse correlate**.

* Verificare che i computer siano visualizzati nell'area di lavoro Log Analytics. Eseguire la query seguente nell'area di lavoro Log Analytics collegata all'account di automazione:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Se il computer non viene visualizzato nei risultati della query, non è stato archiviato di recente, il che significa che è probabile che si verifichi un problema di configurazione locale ed è necessario [reinstallare l'agente](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Se il computer viene visualizzato nei risultati della query, è necessario verificare la configurazione dell'ambito specificata nell'elemento puntato successivo nell'elenco.

* Verificare la presenza di problemi di configurazione dell'ambito. La [configurazione dell'ambito](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quali computer vengono configurati per la soluzione. Se il computer viene visualizzato nell'area di lavoro ma non nel portale di **Gestione aggiornamenti** , è necessario configurare la configurazione dell'ambito per le macchine virtuali. Per informazioni su come eseguire questa operazione, vedere l'articolo relativo all' [onboarding dei computer nell'area di lavoro](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Nell'area di lavoro eseguire la query seguente:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Se si ottiene un risultato `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, viene definita una quota per l'area di lavoro che è stata raggiunta e che ha interrotto il salvataggio dei dati. Nell'area di lavoro passare a **utilizzo e costi stimati**  > **gestione del volume di dati** e verificare la quota o rimuoverla.

* Se questi passaggi non consentono di risolvere il problema, seguire la procedura illustrata in [distribuire un ruolo di lavoro ibrido per Runbook Windows](../automation-windows-hrw-install.md) per reinstallare il ruolo di lavoro ibrido per Windows. In alternativa, per Linux, [distribuire un ruolo di lavoro ibrido per Runbook di Linux](../automation-linux-hrw-install.md).

## <a name="rp-register"></a>Scenario: non è possibile registrare il provider di risorse di automazione per le sottoscrizioni

### <a name="issue"></a>Problema

Quando si lavora con soluzioni nell'account di automazione, si verifica l'errore seguente:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

Il provider di risorse di automazione non è registrato nella sottoscrizione.

### <a name="resolution"></a>Risoluzione

Per registrare il provider di risorse di automazione, attenersi alla procedura seguente nella portale di Azure:

1. Nell'elenco dei servizi di Azure nella parte inferiore del portale selezionare **tutti i servizi**e quindi selezionare **sottoscrizioni** nel gruppo di servizi generale.
2. Selezionare la propria sottoscrizione.
3. In **Impostazioni**selezionare **provider di risorse**.
4. Dall'elenco dei provider di risorse, verificare che il provider di risorse **Microsoft. Automation** sia registrato.
5. Se non è elencato, registrare il provider **Microsoft. Automation** attenendosi alla procedura descritta in [risolvere gli errori per la registrazione del provider di risorse](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="components-enabled-not-working"></a>Scenario: i componenti per la soluzione Gestione aggiornamenti sono stati abilitati e ora è in corso la configurazione della macchina virtuale

### <a name="issue"></a>Problema

Nella macchina virtuale continua a essere visualizzato il messaggio seguente 15 minuti dopo l'onboarding:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi per i motivi seguenti:

- È in corso il blocco della comunicazione con l'account di automazione.
- La VM da caricare potrebbe provenire da un computer clonato che non è stato preparata con Sysprep con il Microsoft Monitoring Agent (MMA) installato.

### <a name="resolution"></a>Risoluzione

1. Passare a [pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento gestione aggiornamenti.
2. Se si usa un'immagine clonata:
   1. Nell'area di lavoro Log Analytics rimuovere la macchina virtuale dalla ricerca salvata per la configurazione dell'ambito `MicrosoftDefaultScopeConfig-Updates` se visualizzata. Le ricerche salvate sono disponibili in **Generale** nell'area di lavoro.
   2. Eseguire `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`.
   3. Eseguire `Restart-Service HealthService` per riavviare `HealthService`. La chiave viene ricreata e viene generato un nuovo UUID.
   4. Se questo approccio non funziona, eseguire prima Sysprep sull'immagine, quindi installare MMA.

## <a name="multi-tenant"></a>Scenario: si riceve un errore di sottoscrizione collegata quando si crea una distribuzione degli aggiornamenti per i computer in un altro tenant di Azure

### <a name="issue"></a>Problema

Si verifica l'errore seguente quando si tenta di creare una distribuzione degli aggiornamenti per i computer in un altro tenant di Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando si crea una distribuzione degli aggiornamenti con macchine virtuali di Azure in un altro tenant incluso in una distribuzione degli aggiornamenti.

### <a name="resolution"></a>Risoluzione

Usare la soluzione alternativa seguente per fare in modo che questi elementi siano pianificati. È possibile usare il cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) con l'opzione `-ForUpdate` per creare una pianificazione. Usare quindi il cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passare i computer nell'altro tenant al parametro `-NonAzureComputer`. L'esempio seguente illustra come farlo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Scenario: riavvii non spiegati

### <a name="issue"></a>Problema

Anche se è stata impostata l'opzione di **controllo riavvio** in modo che **non venga mai**riavviata, i computer continuano a essere riavviati dopo l'installazione degli aggiornamenti.

### <a name="cause"></a>Causa

Windows Update possono essere modificate da diverse chiavi del registro di sistema, le quali possono modificare il comportamento del riavvio.

### <a name="resolution"></a>Risoluzione

Esaminare le chiavi del registro di sistema elencate in [configurazione aggiornamenti automatici modificando il registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) di sistema e le [chiavi del registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) per assicurarsi che i computer siano configurati correttamente.

## <a name="failed-to-start"></a>Scenario: il computer Mostra "Impossibile avviare" in una distribuzione di aggiornamenti

### <a name="issue"></a>Problema

Un computer Mostra uno stato **di avvio non riuscito** . Quando si visualizzano i dettagli specifici per il computer, viene visualizzato l'errore seguente:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi per uno dei motivi seguenti:

* Il computer non esiste più.
* Il computer è disattivato e non è raggiungibile.
* Il computer ha un problema di connettività di rete e quindi il ruolo di lavoro ibrido nel computer non è raggiungibile.
* Si è verificato un aggiornamento di MMA che ha modificato il SourceComputerId.
* L'esecuzione dell'aggiornamento è stata limitata se si raggiunge il limite di 2.000 processi simultanei in un account di automazione. Ogni distribuzione viene considerata un processo e ogni computer in una distribuzione di aggiornamenti viene conteggiato come un processo. Qualsiasi altro processo di automazione o distribuzione di aggiornamenti attualmente in esecuzione nell'account di automazione viene conteggiato per il limite di processi simultanei.

### <a name="resolution"></a>Risoluzione

Quando applicabile, usare i [gruppi dinamici](../automation-update-management-groups.md) per le distribuzioni degli aggiornamenti. Inoltre

* Verificare che il computer esista ancora e che sia raggiungibile. Se non esiste, modificare la distribuzione e rimuovere il computer.
* Vedere la sezione relativa alla [pianificazione della rete](../automation-update-management.md#ports) per un elenco di porte e indirizzi necessari per gestione aggiornamenti, quindi verificare che il computer soddisfi questi requisiti.
* Eseguire la query seguente in Log Analytics per trovare i computer nell'ambiente in cui `SourceComputerId` è stato modificato. Cercare i computer con lo stesso valore di `Computer` ma con un valore `SourceComputerId` diverso. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   Dopo aver individuato i computer interessati, modificare le distribuzioni degli aggiornamenti destinate a tali computer, quindi rimuoverli e aggiungerli di nuovo in modo che `SourceComputerId` rispecchi il valore corretto.

## <a name="updates-nodeployment"></a>Scenario: gli aggiornamenti vengono installati senza una distribuzione

### <a name="issue"></a>Problema

Quando si registra un computer Windows in Gestione aggiornamenti, vengono visualizzati gli aggiornamenti installati senza una distribuzione.

### <a name="cause"></a>Causa

In Windows, gli aggiornamenti vengono installati automaticamente non appena sono disponibili. Questo comportamento può causare confusione se non è stato pianificato un aggiornamento da distribuire nel computer.

### <a name="resolution"></a>Risoluzione

Per impostazione predefinita, la chiave del registro di sistema `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` è impostata su 4: **download automatico e installazione**.

Per Gestione aggiornamenti client, è consigliabile impostare questa chiave su 3: **download automatico ma non installazione automatica**.

Per ulteriori informazioni, vedere [configurazione di aggiornamenti automatici](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Scenario: Il computer è già registrato per un altro account

### <a name="issue"></a>Problema

Viene visualizzato il messaggio di errore seguente:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

Il computer è già stato caricato in un'altra area di lavoro per Gestione aggiornamenti.

### <a name="resolution"></a>Risoluzione

1. Seguire i passaggi in [computer non vengono visualizzati nel portale in Gestione aggiornamenti](#nologs) per assicurarsi che il computer stia segnalando l'area di lavoro corretta.
2. Pulire gli elementi obsoleti nel computer [eliminando il gruppo Runbook ibrido](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), quindi riprovare.

## <a name="machine-unable-to-communicate"></a>Scenario: il computer non può comunicare con il servizio

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

### <a name="cause"></a>Causa

Un proxy, un gateway o un firewall potrebbe bloccare la comunicazione di rete.

### <a name="resolution"></a>Risoluzione

Verificare la rete e assicurarsi che siano consentite le porte e gli indirizzi appropriati. Vedere [requisiti di rete](../automation-hybrid-runbook-worker.md#network-planning) per un elenco di porte e indirizzi necessari per gestione aggiornamenti e ruoli di lavoro ibridi per Runbook.

## <a name="unable-to-create-selfsigned-cert"></a>Scenario: Impossibile creare un certificato autofirmato

### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per Runbook non è riuscito a generare un certificato autofirmato.

### <a name="resolution"></a>Risoluzione

Verificare che l'account di sistema disponga dell'accesso in lettura alla cartella **C:\ProgramData\Microsoft\Crypto\RSA** e riprovare.

## <a name="mw-exceeded"></a>Scenario: l'aggiornamento pianificato non è riuscito con un errore MaintenanceWindowExceeded

### <a name="issue"></a>Problema

La finestra di manutenzione predefinita per gli aggiornamenti è di 120 minuti. È possibile aumentare la finestra di manutenzione fino a un massimo di 6 ore o 360 minuti.

### <a name="resolution"></a>Risoluzione

Modificare le distribuzioni degli aggiornamenti pianificati con errori e aumentare la finestra di manutenzione.

Per ulteriori informazioni sulle finestre di manutenzione, vedere [Install Updates](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Scenario: il computer viene visualizzato come "non valutato" e Mostra un'eccezione HResult

### <a name="issue"></a>Problema

* Sono presenti computer che risultano **non valutati** in **Conformità** con un messaggio di eccezione sottostante.
* Sono presenti computer che vengono visualizzati come non valutati.
* Nel portale viene visualizzato un codice di errore HRESULT.

### <a name="cause"></a>Causa

L'agente di aggiornamento (agente Windows Update in Windows; Gestione pacchetti per una distribuzione Linux) non è configurato correttamente. Gestione aggiornamenti si basa sull'agente di aggiornamento del computer per fornire gli aggiornamenti necessari, lo stato della patch e i risultati delle patch distribuite. Senza queste informazioni, Gestione aggiornamenti non è in grado di segnalare correttamente le patch necessarie o installate.

### <a name="resolution"></a>Risoluzione

Provare a eseguire gli aggiornamenti localmente nel computer. In caso contrario, significa che si è verificato un errore di configurazione con l'agente di aggiornamento.

Questo problema è spesso causato da problemi di configurazione di rete e firewall. Attenersi alla procedura seguente:

* Per Linux, consultare la documentazione appropriata per assicurarsi che sia possibile raggiungere l'endpoint di rete del repository del pacchetto.
* Per Windows, verificare che la configurazione dell'agente come elencato negli [aggiornamenti non venga scaricata dall'endpoint Intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Se i computer sono configurati per Windows Update, assicurarsi che sia possibile raggiungere gli endpoint descritti in [problemi correlati a http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se i computer sono configurati per Windows Server Update Services (WSUS), verificare che sia possibile raggiungere il server WSUS configurato dalla [chiave del registro di sistema WUServer](/windows/deployment/update/waas-wu-settings).

Se viene visualizzato un HRESULT, fare doppio clic sull'eccezione visualizzata in rosso per visualizzare l'intero messaggio di eccezione. Esaminare la tabella seguente per individuare possibili soluzioni o azioni consigliate:

|Eccezione  |Risoluzione o azione  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Eseguire una ricerca nel codice di errore pertinente nell' [elenco dei codici di errore di Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) per trovare ulteriori dettagli sulla ragione dell'eccezione.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Che indicano problemi di connettività di rete. Verificare che il computer disponga della connettività di rete per Gestione aggiornamenti. Vedere la sezione relativa alla [pianificazione della rete](../automation-update-management.md#ports) per un elenco di porte e indirizzi obbligatori.        |
|`0x8024001E`| L'operazione di aggiornamento non è stata completata perché il servizio o il sistema è stato arrestato.|
|`0x8024002E`| Il servizio Windows Update è disabilitato.|
|`0x8024402C`     | Se si usa un server WSUS, assicurarsi che i valori del registro di sistema per `WUServer` e `WUStatusServer` sotto la chiave del registro di sistema `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` specifichino il server WSUS corretto.        |
|`0x80072EE2`|Si è verificato un problema di connettività di rete o si è verificato un problema durante la comunicazione con un server WSUS configurato. Controllare le impostazioni di WSUS e verificare che il servizio sia accessibile dal client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Verificare che il servizio Windows Update (wuauserv) sia in esecuzione e non sia disabilitato.        |
|Qualsiasi altra eccezione generica     | Eseguire una ricerca su Internet per individuare le possibili soluzioni e collaborare con il supporto IT locale.         |

La revisione del file windowsupdate. log consente inoltre di determinare le possibili cause. Per ulteriori informazioni sulla lettura del log, vedere [come leggere il file windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

È anche possibile scaricare ed eseguire lo strumento di [risoluzione dei problemi Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) per verificare la presenza di eventuali problemi con Windows Update nel computer.

> [!NOTE]
> La documentazione per la [risoluzione dei problemi Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica che è destinata all'uso nei client Windows, ma funziona anche in Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: l'esecuzione dell'aggiornamento restituisce lo stato "non riuscito" (Linux)

### <a name="issue"></a>Problema

Viene avviata un'operazione di aggiornamento, ma vengono rilevati errori durante l'esecuzione.

### <a name="cause"></a>Causa

Possibili cause:

* Gestione pacchetti non è integro.
* L'agente di aggiornamento (WUA per Windows, gestione pacchetti specifica della distribuzione per Linux) non è configurato correttamente.
* I pacchetti specifici interferiscono con l'applicazione di patch basata sul cloud.
* Il computer non è raggiungibile.
* Aggiornamenti con dipendenze non risolte.

### <a name="resolution"></a>Risoluzione

Se si verificano errori durante l'esecuzione di un aggiornamento dopo l'avvio, [controllare l'output del processo](../manage-update-multi.md#view-results-of-an-update-deployment) dal computer interessato nell'esecuzione. Potrebbero essere presenti messaggi di errore specifici dei computer su cui è possibile eseguire ricerche e su cui intervenire. Gestione aggiornamenti richiede che la gestione pacchetti sia integra affinché le distribuzioni degli aggiornamenti abbiano esito positivo.

Se vengono visualizzate patch, pacchetti o aggiornamenti specifici immediatamente prima che il processo abbia esito negativo, è possibile provare a [escluderli](../automation-tutorial-update-management.md#schedule-an-update-deployment) dalla successiva distribuzione degli aggiornamenti. Per raccogliere informazioni sul log da Windows Update, vedere [Windows Update file di log](/windows/deployment/update/windows-update-logs).

Se non è possibile risolvere un problema di applicazione di patch, effettuare una copia del file di log seguente e conservarlo per la risoluzione dei problemi *prima* che venga avviata la prossima distribuzione degli aggiornamenti:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Le patch non sono installate

### <a name="machines-dont-install-updates"></a>I computer non installano gli aggiornamenti

* Provare a eseguire gli aggiornamenti direttamente nel computer. Se il computer non può applicare gli aggiornamenti, consultare l' [elenco di potenziali errori nella Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Se gli aggiornamenti vengono eseguiti localmente, provare a rimuovere e reinstallare l'agente nel computer seguendo le istruzioni riportate in [rimuovere una macchina virtuale da Gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Sono consapevole che gli aggiornamenti sono disponibili, ma non vengono visualizzati come disponibile nei computer

* Questo problema si verifica spesso se i computer sono configurati per ottenere gli aggiornamenti da WSUS o System Center Configuration Manager (SCCM), ma WSUS e SCCM non hanno approvato gli aggiornamenti.
* È possibile verificare se i computer sono configurati per WSUS e SCCM facendo [riferimento incrociato alla chiave del registro di sistema UseWUServer alle chiavi del registro di sistema nella sezione "configurazione aggiornamenti automatici modifica del registro di sistema" di questo articolo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).
* Se gli aggiornamenti non sono approvati in WSUS, non verranno installati. È possibile verificare la presenza di aggiornamenti non approvati in Log Analytics eseguendo la query seguente:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Gli aggiornamenti vengono visualizzati come installati, ma non riescono a trovarli nel computer

* Spesso gli aggiornamenti vengono sostituiti da altri aggiornamenti. Per ulteriori informazioni, vedere l'articolo relativo all' [aggiornamento sostituito](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) nella Windows Update Guida alla risoluzione dei problemi.

### <a name="installing-updates-by-classification-on-linux"></a>Installazione degli aggiornamenti in base alla classificazione in Linux

* La distribuzione degli aggiornamenti in Linux in base alla classificazione ("Aggiornamenti critici e della sicurezza") presenta avvertimenti importanti, in particolare per CentOS. Queste limitazioni sono documentate nella [pagina panoramica gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 è sempre mancante

* KB2267602 è l'[aggiornamento delle definizioni di Windows Defender](https://www.microsoft.com/wdsi/definitions). Viene aggiornato ogni giorno.

## <a name="next-steps"></a>Passaggi successivi

Se il problema non è stato riscontrato o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* È possibile connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiedi supporto**.
