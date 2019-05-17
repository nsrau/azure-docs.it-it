---
title: Risolvere gli errori con Gestione aggiornamenti
description: Informazioni su come risolvere i problemi con Gestione aggiornamenti
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/07/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f286877c6a9e787c06a8a846efaf94668c04fc4e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787700"
---
# <a name="troubleshooting-issues-with-update-management"></a>Risoluzione dei problemi con Gestione aggiornamenti

Questo articolo illustra alcune procedure per la risoluzione dei problemi che si possono riscontrare quando si usa Gestione aggiornamenti.

È disponibile uno strumento di risoluzione dei problemi dell'agente che consente all'agente del ruolo di lavoro ibrido di determinare il problema sottostante. Per altre informazioni sullo strumento di risoluzione dei problemi, vedere [Risolvere i problemi dell'agente di aggiornamento](update-agent-issues.md). Per tutti gli altri problemi, vedere le informazioni dettagliate seguenti sui possibili problemi.

## <a name="general"></a>Generale

### <a name="components-enabled-not-working"></a>Scenario: i componenti per la soluzione di Gestione aggiornamenti sono stati abilitati ed è in corso la configurazione della macchina virtuale

#### <a name="issue"></a>Problema

Nella macchina virtuale continua a essere visualizzato il messaggio seguente 15 minuti dopo l'onboarding:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Causa

Questo errore può dipendere dalle cause seguenti:

1. Le comunicazioni verso l'account di Automazione sono bloccate.
2. La macchina virtuale di cui si esegue l'onboarding può provenire da una macchina clonata che non è stata preparata tramite Sysprep con Microsoft Monitoring Agent installato.

#### <a name="resolution"></a>Risoluzione

1. Vedere [Configurare la rete](../automation-hybrid-runbook-worker.md#network-planning) per informazioni sugli indirizzi e sulle porte da abilitare per il funzionamento di Gestione aggiornamenti.
2. Se si usa un'immagine clonata:
   1. Nell'area di lavoro di Log Analitica, rimuovere la macchina virtuale dalla ricerca salvata per la configurazione dell'ambito `MicrosoftDefaultScopeConfig-Updates` se viene visualizzato. Le ricerche salvate sono disponibili in **Generale** nell'area di lavoro.
   2. Eseguire `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Eseguire `Restart-Service HealthService` per riavviare `HealthService`. In questo modo viene ricreata la chiave e viene creato un nuovo UUID.
   4. Se il problema persiste, eseguire sysprep sull'immagine prima e installare l'agente MMA al termine dell'attività.

### <a name="multi-tenant"></a>Scenario: viene visualizzato un errore di sottoscrizione collegata durante la creazione di una distribuzione degli aggiornamenti per i computer in un altro tenant di Azure.

#### <a name="issue"></a>Problema

viene visualizzato il seguente durante la creazione di una distribuzione degli aggiornamenti per i computer in un altro tenant di Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando si crea una distribuzione degli aggiornamenti con macchine virtuali di Azure in un altro tenant incluso in una distribuzione degli aggiornamenti.

#### <a name="resolution"></a>Risoluzione

Occorrerà usare la seguente soluzione alternativa per la pianificazione. È possibile usare il cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) con l'opzione `-ForUpdate` per creare una pianificazione, usare il cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passare i computer nell'altro tenant al parametro `-NonAzureComputer`. Segue un esempio di come effettuare questa operazione:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Scenario: Aggiornare i dati di gestione non vengono visualizzati nel log di monitoraggio di Azure per una macchina

#### <a name="issue"></a>Problema

Si hanno macchine che mostrano come **non valutati** sotto **conformità**, ma vengono visualizzati i dati di heartbeat nei log di monitoraggio di Azure per il lavoro ibrido per Runbook ma non la gestione degli aggiornamenti.

#### <a name="cause"></a>Causa

È possibile che il ruolo di lavoro ibrido per runbook debba essere registrato nuovamente e reinstallato.

#### <a name="resolution"></a>Risoluzione

Seguire i passi descritti in [Distribuire un ruolo di lavoro ibrido per runbook di Windows](../automation-windows-hrw-install.md) per reinstallare il ruolo di lavoro ibrido per Windows o [Distribuire un ruolo di lavoro ibrido per runbook di Linux](../automation-linux-hrw-install.md) per Linux.

## <a name="windows"></a>Windows

Se si riscontrano problemi durante il tentativo di caricare la soluzione o una macchina virtuale, nel computer locale cercare nel log eventi di **Operations Manager**, in **Registri applicazioni e servizi**, gli eventi con ID **4502** e il messaggio di evento contenente **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

La sezione seguente evidenzia i messaggi di errore specifici e una possibile risoluzione per ognuno. Per altri problemi di onboarding, vedere la sezione relativa alla [risoluzione dei problemi di onboarding delle soluzioni](onboarding.md).

### <a name="machine-already-registered"></a>Scenario: il computer è già registrato per un altro account

#### <a name="issue"></a>Problema

Viene visualizzato il messaggio di errore seguente:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Causa

Il computer è già caricato in un'altra area di lavoro per Gestione aggiornamenti.

#### <a name="resolution"></a>Risoluzione

Eseguire la pulizia degli elementi obsoleti nel computer [eliminando il gruppo di runbook ibridi](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) e riprovare.

### <a name="machine-unable-to-communicate"></a>Scenario: il computer non è in grado di comunicare con il servizio

#### <a name="issue"></a>Problema

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

#### <a name="cause"></a>Causa

La comunicazione di rete potrebbe essere bloccata da un proxy, un gateway o un firewall.

#### <a name="resolution"></a>Risoluzione

Esaminare le funzionalità di rete e verificare che le porte e gli indirizzi appropriati siano consentiti. Vedere i [requisiti di rete](../automation-hybrid-runbook-worker.md#network-planning) per un elenco di porte e indirizzi richiesti da Gestione aggiornamenti e i ruoli di lavoro ibrido per runbook.

### <a name="unable-to-create-selfsigned-cert"></a>Scenario: impossibile creare un certificato autofirmato

#### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per runbook non è stato in grado di generare un certificato autofirmato.

#### <a name="resolution"></a>Risoluzione

Verificare che l'account di sistema abbia accesso in lettura alla cartella **C:\ProgramData\Microsoft\Crypto\RSA** e riprovare.

### <a name="failed-to-start"></a>Scenario: Una macchina Mostra non è stato possibile avviare in una distribuzione degli aggiornamenti

#### <a name="issue"></a>Problema

Un computer con lo stato **non è stato possibile avviare** per una macchina. Quando si visualizzano i dettagli specifici per il computer viene visualizzato l'errore seguente:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Causa

Questo errore può verificarsi a causa di uno dei motivi seguenti:

* La macchina non esiste più.
* La macchina è disattivata disattivato e non raggiungibile.
* Il computer disponga di un problema di connettività di rete e il ruolo di lavoro ibrido nel computer non raggiungibile.
* Si è verificato un aggiornamento a Microsoft Monitoring Agent che è stato modificato il SourceComputerId
* Operazione di aggiornamento potrebbe avere limitato se si raggiunge il limite di 2.000 processi simultanei in un Account di automazione. Ogni distribuzione è considerata un processo e ogni computer in un numero di distribuzioni di aggiornamento come processo. Qualsiasi altra automazione processi o aggiornamento distribuzione attualmente in esecuzione il numero di Account di automazione ai fini del limite di processi simultanei.

#### <a name="resolution"></a>Risoluzione

Quando si usa applicabile [i gruppi dinamici](../automation-update-management.md#using-dynamic-groups) per le distribuzioni di aggiornamento.

* Verificare la macchina esista e sia raggiungibile. Se non esiste, modificare la distribuzione e rimuove il computer.
* Vedere la sezione sulla [pianificazione di rete](../automation-update-management.md#ports) per un elenco di porte e indirizzi che sono necessari per la gestione degli aggiornamenti e verificare che il computer soddisfi questi requisiti.
* Eseguire la query seguente nel Log Analitica per individuare dei computer nell'ambiente in uso la cui proprietà `SourceComputerId` modificato. Cercare i computer che hanno gli stessi `Computer` valore, ma con una diversa `SourceComputerId` valore. Dopo aver trovato i computer interessati, è necessario modificare le distribuzioni di aggiornamento che tali macchine, di destinazione e rimuovere e aggiungere nuovamente le macchine in modo che il `SourceComputerId` riflette il valore corretto.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Scenario: il computer viene indicato come non valutato e genera a un'eccezione HResult

#### <a name="issue"></a>Problema

Sono presenti computer che risultano **non valutati** in **Conformità** con un messaggio di eccezione sottostante.

#### <a name="cause"></a>Causa

Windows Update o Windows Server Update Services non sono configurati correttamente nel computer. Gestione aggiornamenti si basa su Windows Update o Windows Server Update Services per fornire gli aggiornamenti necessari, lo stato delle patch e i risultati delle patch distribuite. Senza queste informazioni Gestione aggiornamenti non può segnalare correttamente segnalare le patch necessarie o installate.

#### <a name="resolution"></a>Risoluzione

Fare doppio clic sull'eccezione in rosso per visualizzare il messaggio completo. Nella tabella seguente individuare possibili soluzioni o azioni da intraprendere:

|Eccezione  |Risoluzione o azione  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Per maggiori dettagli sulla causa dell'eccezione, cercare il codice di errore pertinente nell'[elenco codici di errore relativi a Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list).        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Sono problemi di connettività di rete. Assicurarsi che il computer disponga della connettività di rete appropriata per Gestione aggiornamenti. Per un elenco di porte e indirizzi necessari, vedere la sezione sulla [pianificazione della rete](../automation-update-management.md#ports).        |
|`0x8024001E`| L'operazione di aggiornamento non è stata completata perché il servizio o il sistema è stato arrestato.|
|`0x8024002E`| Servizio Windows Update è disabilitato.|
|`0x8024402C`     | Se si usa un server WSUS, assicurarsi che i valori del Registro di sistema per `WUServer` e `WUStatusServer` nella chiave del Registro di sistema `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` contengano il server WSUS corretto.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Assicurarsi che il servizio Windows Update (wuauserv) sia in esecuzione e non sia disattivato.        |
|Qualsiasi altra eccezione generica     | Ricercare possibili soluzioni in Internet e collaborare con il supporto tecnico IT locale.         |

Esaminare il `windowsupdate.log` possono aiutarti a provare a determinare le possibili cause. Per altre informazioni sulla lettura del log, vedere [come leggere il file di windowsupdate. log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

È inoltre possibile scaricare ed eseguire lo [strumento di risoluzione dei problemi di Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) per verificare se sono presenti problemi con Windows Update nel computer.

> [!NOTE]
> Lo [strumento di risoluzione dei problemi di Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) è destinato all’uso con i client Windows, ma funziona anche su Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenario: impossibile avviare le operazioni di aggiornamento

#### <a name="issue"></a>Problema

Le operazioni di aggiornamento non vengono avviate in un computer Linux.

#### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido Linux non è integro.

#### <a name="resolution"></a>Risoluzione

Creare una copia del file di log seguente e conservarlo per la risoluzione dei problemi:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenario: l'operazione di aggiornamento viene avviata, ma si verificano errori

#### <a name="issue"></a>Problema

Un'operazione di aggiornamento viene avviata, ma rileva errori durante l'esecuzione.

#### <a name="cause"></a>Causa

Le possibili cause sono:

* La gestione pacchetti non è integra
* Pacchetti specifici possono interferire con l'applicazione di patch basata su cloud
* Altri motivi

#### <a name="resolution"></a>Risoluzione

Se si verificano errori durante un'operazione di aggiornamento dopo un normale avvio in Linux, controllare l'output del processo del computer interessato. È possibile trovare messaggi di errore specifici generati dalla gestione pacchetti del computer che possono essere esaminati per intervenire di conseguenza. Gestione aggiornamenti richiede che la gestione pacchetti sia integra affinché le distribuzioni degli aggiornamenti abbiano esito positivo.

In alcuni casi gli aggiornamenti dei pacchetti possono interferire con Gestione aggiornamenti e impedire il completamento della distribuzione di un aggiornamento. Se si riscontra questo problema, è necessario escludere questi pacchetti dalle future operazioni di aggiornamento oppure installarli manualmente.

Se non è possibile risolvere un problema di patch, creare una copia del seguente file di log e conservarla **prima** dell'inizio della successiva distribuzione degli aggiornamenti ai fini della risoluzione dei problemi:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
