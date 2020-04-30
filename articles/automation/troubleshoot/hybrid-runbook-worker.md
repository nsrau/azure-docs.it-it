---
title: Risoluzione dei problemi di automazione di Azure Runbook Worker
description: Questo articolo fornisce informazioni per la risoluzione dei problemi relativi ai ruoli di lavoro ibridi per Runbook di automazione di Azure
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679325"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Risolvere i problemi di ruoli di lavoro ibridi per runbook

Questo articolo contiene informazioni sulla risoluzione di problemi relativi ai ruoli di lavoro ibridi per runbook.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).

## <a name="general"></a>Generale

Il ruolo di lavoro ibrido per runbook dipende da un agente per comunicare con l'account di Automazione di Azure per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Per Windows, questo agente è l'agente Log Analytics per Windows. Per Linux, si tratta dell'agente Log Analytics per Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: l'esecuzione del runbook ha esito negativo

#### <a name="issue"></a>Problema

L'esecuzione di Runbook non riesce e viene visualizzato l'errore seguente.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Il Runbook viene sospeso poco dopo il tentativo di esecuzione tre volte. Sono presenti condizioni che possono interrompere il completamento del Runbook. Il messaggio di errore correlato potrebbe non includere informazioni aggiuntive.

#### <a name="cause"></a>Causa

Le possibili cause sono le seguenti:

* Manuali operativi non è in grado di eseguire l'autenticazione con le risorse locali.
* Il ruolo di lavoro ibrido è protetto da proxy o firewall.
* Il computer configurato per eseguire il ruolo di lavoro ibrido per Runbook non soddisfa i requisiti hardware minimi.

#### <a name="resolution"></a>Soluzione

Verificare che il computer abbia accesso in uscita a ***. Azure-Automation.NET** sulla porta 443.

I computer che eseguono il ruolo di lavoro ibrido per Runbook devono soddisfare i requisiti hardware minimi prima che il thread di lavoro sia configurato per ospitare questa funzionalità. Manuali operativi e il processo in background che usano potrebbero causare il sovrautilizzo del sistema e causare ritardi o timeout del processo Runbook.

Verificare che il computer in cui è in esecuzione la funzionalità Hybrid Runbook Workers soddisfi i requisiti hardware minimi. In caso affermativo, monitorare l'utilizzo di CPU e memoria per determinare eventuali correlazioni tra le prestazioni dei processi del ruolo di lavoro ibrido per runbook e Windows. Qualsiasi pressione della memoria o della CPU può indicare la necessità di aggiornare le risorse. È anche possibile selezionare una risorsa di calcolo diversa che supporti i requisiti minimi e la scalabilità quando le richieste di carico di lavoro indicano che è necessario un aumento.

Per un evento corrispondente con descrizione `Win32 Process Exited with code [4294967295]`, controllare il registro eventi di **Microsoft-SMA** . La ragione di questo errore è che non è stata configurata l'autenticazione nella manuali operativi o sono state specificate le credenziali RunAs per il gruppo di lavoro ibrido per Runbook. Verificare le autorizzazioni di Runbook nell' [esecuzione di manuali operativi in un ruolo di lavoro ibrido per Runbook](../automation-hrw-run-runbooks.md) per verificare di avere configurato correttamente l'autenticazione per la manuali operativi.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: evento 15011 nel ruolo di lavoro ibrido per Runbook

#### <a name="issue"></a>Problema

Il ruolo di lavoro ibrido per Runbook riceve l'evento 15011, che indica che il risultato di una query non è valido. L'errore seguente viene visualizzato quando il thread di lavoro tenta di aprire una connessione con il [Server SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per Runbook non è stato configurato correttamente per la soluzione di distribuzione automatica. Questa soluzione contiene una parte che connette la macchina virtuale all'area di lavoro Log Analytics. Lo script di PowerShell cerca l'area di lavoro nella sottoscrizione con il nome fornito. In questo caso, l'area di lavoro Log Analytics si trova in una sottoscrizione diversa. Lo script non è in grado di trovare l'area di lavoro e tenta di crearne uno, ma il nome è già stato creato. In questo modo la distribuzione ha esito negativo.

#### <a name="resolution"></a>Soluzione

Per risolvere il problema, sono disponibili due opzioni:

* Modificare lo script di PowerShell per cercare l'area di lavoro Log Analytics in un'altra sottoscrizione. Si tratta di una soluzione efficace se si prevede di distribuire molti computer di lavoro ibrido per Runbook in futuro.

* Configurare manualmente il computer di lavoro per l'esecuzione in una sandbox dell'agente di orchestrazione. Eseguire quindi un Runbook creato nell'account di automazione di Azure nel ruolo di lavoro per verificare la funzionalità.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: macchine virtuali di Windows Azure eliminate automaticamente da un gruppo di lavoro ibrido

#### <a name="issue"></a>Problema

Non è possibile visualizzare le VM o il ruolo di lavoro ibrido per runbook quando il computer di lavoro è stato disattivato per molto tempo.

#### <a name="cause"></a>Causa

Il computer di lavoro ibrido per Runbook non ha sottoposto a ping automazione di Azure per più di 30 giorni. Di conseguenza, l'automazione ha eliminato il gruppo di lavoro ibrido per Runbook o il gruppo di lavoro del sistema. 

#### <a name="resolution"></a>Soluzione

Avviare il computer di lavoro e quindi rereregister con automazione di Azure. Vedere le istruzioni per l'installazione dell'ambiente Runbook e la connessione ad automazione di Azure in [distribuire un ruolo di lavoro ibrido per Runbook Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: non è stato trovato alcun certificato nell'archivio certificati del ruolo di lavoro ibrido per Runbook

#### <a name="issue"></a>Problema

Un Runbook in esecuzione in un ruolo di lavoro ibrido per Runbook non riesce con il messaggio di errore seguente.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Causa

Questo errore si verifica quando si tenta di usare un [account RunAs](../manage-runas-account.md) in un Runbook in esecuzione in un ruolo di lavoro ibrido per Runbook in cui non è presente il certificato dell'account RunAs. Per impostazione predefinita, i ruoli di lavoro ibridi per Runbook non hanno l'asset di certificato localmente. L'account RunAs richiede che questo asset funzioni correttamente.

#### <a name="resolution"></a>Soluzione

Se il ruolo di lavoro ibrido per Runbook è una macchina virtuale di Azure, è invece possibile usare [identità gestite per le risorse di Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Questo scenario semplifica l'autenticazione consentendo di eseguire l'autenticazione alle risorse di Azure usando l'identità gestita della macchina virtuale di Azure anziché l'account RunAs. Quando il ruolo di lavoro ibrido per Runbook è un computer locale, è necessario installare il certificato dell'account RunAs nel computer. Per informazioni su come installare il certificato, vedere la procedura per eseguire il Runbook **Export-RunAsCertificateToHybridWorker** di PowerShell in esecuzione di manuali operativi in un ruolo di [lavoro ibrido per Runbook](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: errore 403 durante la registrazione del ruolo di lavoro ibrido per Runbook

#### <a name="issue"></a>Problema

La fase di registrazione iniziale del processo di lavoro ha esito negativo e viene visualizzato l'errore seguente (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Causa

Le possibili cause sono le seguenti:

* È presente un ID dell'area di lavoro o una chiave dell'area di lavoro (primaria) non tipizzata nelle impostazioni dell'agente. 

* Il ruolo di lavoro ibrido per Runbook non può scaricare la configurazione, causando un errore di collegamento dell'account. Quando Azure Abilita soluzioni, supporta solo determinate aree per collegare un'area di lavoro Log Analytics e un account di automazione. È anche possibile che nel computer sia impostata una data e/o un'ora errate. Se l'ora è +/-15 minuti dall'ora corrente, il caricamento ha esito negativo.

#### <a name="resolution"></a>Soluzione

##### <a name="mistyped-workspace-idkey"></a>ID/chiave dell'area di lavoro con digitazione
Per verificare se l'ID dell'area di lavoro dell'agente o la chiave dell'area di lavoro è stata digitata in modo non riuscito, vedere [aggiunta o rimozione di un'area di lavoro-agente Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) per l'agente Windows o [aggiunta o rimozione di un'area di lavoro-agente Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) per l'agente Linux  Assicurarsi di selezionare la stringa completa dall'portale di Azure e copiarla e incollarla con cautela.

##### <a name="configuration-not-downloaded"></a>Configurazione non scaricata

L'area di lavoro Log Analytics e l'account di automazione devono trovarsi in un'area collegata. Per un elenco delle aree supportate, vedere [automazione di Azure e mapping dell'area di lavoro log Analytics](../how-to/region-mappings.md).

Potrebbe inoltre essere necessario aggiornare la data e/o il fuso orario del computer. Se si seleziona un intervallo di tempo personalizzato, assicurarsi che l'intervallo sia in formato UTC, che può essere diverso dal fuso orario locale.

## <a name="linux"></a>Linux

Il ruolo di lavoro ibrido per Runbook di Linux dipende dall' [agente log Analytics per Linux](../../azure-monitor/platform/log-analytics-agent.md) per comunicare con l'account di automazione per registrare il ruolo di lavoro, ricevere i processi Runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: l'agente Log Analytics per Linux non è in esecuzione

#### <a name="issue"></a>Problema

L'agente di Log Analytics per Linux non è in esecuzione

#### <a name="cause"></a>Causa

Se l'agente non è in esecuzione, impedisce al ruolo di lavoro ibrido per Runbook di Linux di comunicare con automazione di Azure. Per diversi motivi è possibile che l'agente non sia in esecuzione.

#### <a name="resolution"></a>Soluzione

 Verificare che l'agente sia in esecuzione immettendo il comando `ps -ef | grep python`. Verrà visualizzato un output simile al seguente, ovvero i processi Python con l'account utente **nxautomation** . Se il Gestione aggiornamenti o la soluzione di automazione di Azure non è abilitata, non è in esecuzione nessuno dei seguenti processi.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

L'elenco seguente mostra i processi avviati per un ruolo di lavoro ibrido per runbook di Linux. Si trovano tutti nella directory **/var/opt/Microsoft/omsagent/state/automationworker/**

* **OMS. conf** : processo di gestione del ruolo di lavoro. Viene avviato direttamente da DSC.

* **Worker. conf** : processo di lavoro ibrido registrato automaticamente. Viene avviato dal gestore del ruolo di lavoro. Questo processo viene usato da Gestione aggiornamenti ed è trasparente all'utente. Questo processo non è presente se la soluzione Gestione aggiornamenti non è abilitata nel computer.

* **DIY/Worker. conf** : il processo di lavoro ibrido DIY. Il processo del ruolo di lavoro ibrido registrato manualmente viene usato per eseguire i runbook dell'utente nel ruolo di lavoro ibrido per runbook. Si differenzia solo dal processo di lavoro ibrido registrato automaticamente nei dettagli chiave che usa una configurazione diversa. Questo processo non è presente se la soluzione automazione di Azure è disabilitata e il ruolo di lavoro ibrido per Linux DIY non è registrato.

Se l'agente non è in esecuzione, eseguire il comando seguente per avviare il `sudo /opt/microsoft/omsagent/bin/service_control restart`servizio:.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: la classe specificata non esiste

Se viene visualizzato l'errore `The specified class does not exist..` in **/var/opt/Microsoft/omsconfig/omsconfig.log**, è necessario aggiornare l'agente di log Analytics per Linux. Eseguire il comando seguente per reinstallare l'agente:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Il ruolo di lavoro ibrido per Runbook di Windows dipende dall' [agente di log Analytics per Windows](../../azure-monitor/platform/log-analytics-agent.md) per comunicare con l'account di automazione per registrare il ruolo di lavoro, ricevere i processi Runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro ha esito negativo, questa sezione include alcuni motivi possibili.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenario: l'agente di Log Analytics per Windows non è in esecuzione

#### <a name="issue"></a>Problema

Il `healthservice` non è in esecuzione nel computer di lavoro ibrido per Runbook.

#### <a name="cause"></a>Causa

Se il Log Analytics per il servizio Windows non è in esecuzione, il ruolo di lavoro ibrido per Runbook non può comunicare con automazione di Azure.

#### <a name="resolution"></a>Soluzione

Verificare che l'agente sia in esecuzione immettendo il comando seguente in `Get-Service healthservice`PowerShell:. Se il servizio viene arrestato, immettere il comando seguente in PowerShell per avviare il servizio: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Scenario: evento 4502 nel log Operations Manager

#### <a name="issue"></a>Problema

Nel registro eventi di **Servizi\operations Manager dell'applicazione e dei servizi** vengono visualizzati l'evento 4502 e eventMessage `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` che contiene con la seguente descrizione:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Causa

Questo problema può essere causato dal firewall proxy o di rete che blocca la comunicazione con Microsoft Azure. Verificare che il computer abbia accesso in uscita a ***. Azure-Automation.NET** sulla porta 443. 

#### <a name="resolution"></a>Soluzione

I log vengono archiviati localmente in ogni ruolo di lavoro ibrido in **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. È possibile verificare se sono presenti eventi di avviso o di errore nei registri eventi di **Logs\Microsoft-SMA\Operations** e dell'applicazione e dei servizi **servizi\operations Manager** . Questi log indicano una connettività o un altro tipo di problema che influiscono sull'onboarding del ruolo in automazione di Azure o su un problema riscontrato in normali operazioni. Per ulteriori informazioni sulla risoluzione dei problemi relativi all'agente di Log Analytics, vedere [risolvere i problemi relativi all'agente di log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

I ruoli di lavoro ibridi inviano [messaggi e output Runbook](../automation-runbook-output-and-messages.md) ad automazione di Azure nello stesso modo in cui i processi Runbook in esecuzione nel cloud inviano l'output e i messaggi. È possibile abilitare i flussi dettagliati e di avanzamento esattamente come per manuali operativi.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Scenario: l'agente di orchestrazione. sandbox. exe non è in grado di connettersi a Office 365 tramite proxy

#### <a name="issue"></a>Problema

Uno script in esecuzione in un ruolo di lavoro ibrido per Runbook Windows non può connettersi come previsto per Office 365 su un agente di orchestrazione sandbox. Lo script usa [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) per la connessione. 

Se si modifica l'agente di **orchestrazione. sandbox. exe. config** per impostare il proxy e l'elenco di bypass, il sandbox non si connette ancora correttamente. Un file **powershell_ise. exe. config** con lo stesso proxy e le stesse impostazioni dell'elenco di bypass sembrano funzionare come previsto. I log di Service Management Automation (SMA) e i log di PowerShell non forniscono informazioni sul proxy.

#### <a name="cause"></a>Causa

La connessione al Active Directory Federation Services (ADFS) nel server non può ignorare il proxy. Tenere presente che una sandbox di PowerShell viene eseguita come utente registrato. Tuttavia, un agente di orchestrazione sandbox è molto personalizzato e potrebbe ignorare le impostazioni del file dell'agente di **orchestrazione. sandbox. exe. config** . Dispone di codice speciale per gestire le impostazioni del proxy del computer o MMA, ma non per la gestione di altre impostazioni proxy personalizzate. 

#### <a name="resolution"></a>Soluzione

È possibile risolvere il problema per l'agente di orchestrazione sandbox eseguendo la migrazione dello script in modo da usare i moduli Azure AD anziché il modulo MSOnline per i cmdlet di PowerShell. Vedere [migrazione dall'agente di orchestrazione ad automazione di Azure (beta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Se si vuole continuare a usare i cmdlet del modulo MSOnline, modificare lo script in modo che usi [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Specificare i valori per `ComputerName` i `Credential` parametri e. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Questa modifica del codice avvia una sessione di PowerShell completamente nuova nel contesto delle credenziali specificate. Deve consentire il flusso del traffico attraverso un server proxy che esegue l'autenticazione dell'utente attivo.

>[!NOTE]
>Questa soluzione rende inutile modificare il file di configurazione sandbox. Anche se il file di configurazione è stato eseguito correttamente con lo script, il file viene cancellato ogni volta che l'agente di lavoro ibrido per Runbook viene aggiornato.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: il ruolo di lavoro ibrido per Runbook non segnala

#### <a name="issue"></a>Problema

Il computer del ruolo di lavoro ibrido per runbook è in esecuzione, ma non vengono visualizzati i dati di heartbeat per il computer nell'area di lavoro.

La query di esempio seguente mostra i computer di un'area di lavoro e l'ultimo heartbeat generato:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Causa

Questo problema può essere causato da una cache danneggiata nel ruolo di lavoro ibrido per runbook.

#### <a name="resolution"></a>Soluzione

Per risolvere questo problema, accedere al ruolo di lavoro ibrido per runbook ed eseguire lo script seguente. Questo script interrompe l'agente di Log Analytics per Windows, rimuove la cache e riavvia il servizio. Questa azione costringe il ruolo di lavoro ibrido per runbook a scaricare nuovamente la configurazione da Automazione di Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: non è possibile aggiungere un ruolo di lavoro ibrido per Runbook

#### <a name="issue"></a>Problema

Quando si prova ad aggiungere un ruolo di lavoro ibrido per runbook usando il cmdlet `Add-HybridRunbookWorker` viene visualizzato il messaggio seguente.

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Questo problema può essere causato dal fatto che il computer è già registrato con un account di automazione diverso o se si prova a leggere il ruolo di lavoro ibrido per runbook dopo averlo rimosso da un computer.

#### <a name="resolution"></a>Soluzione

Per risolvere il problema, rimuovere la seguente chiave del registro di sistema `HealthService`, riavviare e riprovare `Add-HybridRunbookWorker` .

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Passaggi successivi

Se il problema non viene visualizzato in precedenza o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi a [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.