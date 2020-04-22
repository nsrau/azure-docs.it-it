---
title: Risoluzione dei problemi relativi ai ruoli di runbook ibrido di automazione di AzureTroubleshooting Azure Automation Hybrid Runbook
description: Questo articolo fornisce informazioni per la risoluzione dei problemi relativi ai ruoli di esecuzione ibrido di Automazione di Azure.This article provides information for troubleshooting Azure Automation Hybrid Runbook Workers.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679325"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Risolvere i problemi di ruoli di lavoro ibridi per runbook

Questo articolo contiene informazioni sulla risoluzione di problemi relativi ai ruoli di lavoro ibridi per runbook.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="general"></a>Generale

Il ruolo di lavoro ibrido per runbook dipende da un agente per comunicare con l'account di Automazione di Azure per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Per Windows, questo agente è l'agente di Log Analytics per Windows. For Linux, it's the Log Analytics agent for Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: l'esecuzione del runbook ha esito negativo

#### <a name="issue"></a>Problema

L'esecuzione del Runbook non riesce e viene visualizzato il seguente errore.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Il runbook viene sospeso poco dopo i tentativi di esecuzione tre volte. Esistono condizioni che possono interrompere il completamento del runbook. Il messaggio di errore correlato potrebbe non includere informazioni aggiuntive.

#### <a name="cause"></a>Causa

Le possibili cause sono le seguenti:

* I runbook non possono eseguire l'autenticazione con le risorse locali.
* Il ruolo di lavoro ibrido è protetto da proxy o firewall.
* Il computer configurato per l'esecuzione di Hybrid Runbook Worker non soddisfa i requisiti hardware minimi.

#### <a name="resolution"></a>Risoluzione

Verificare che il computer disponga dell'accesso in uscita a **.azure-automation.net** sulla porta 443.

I computer che eseguono il ruolo di lavoro ibrido per runbook devono soddisfare i requisiti hardware minimi prima che il worker sia configurato per ospitare questa funzionalità. I Runbook e il processo in background che usano potrebbero causare un esadio utilizzato dal sistema e causare ritardi o timeout dei processi del runbook.

Verificare che il computer per eseguire la funzionalità Hybrid Runbook Worker soddisfi i requisiti hardware minimi. In caso affermativo, monitorare l'utilizzo di CPU e memoria per determinare eventuali correlazioni tra le prestazioni dei processi del ruolo di lavoro ibrido per runbook e Windows. Qualsiasi pressione della memoria o della CPU può indicare la necessità di aggiornare le risorse. È inoltre possibile selezionare una risorsa di calcolo diversa che supporti i requisiti minimi e la scalabilità quando le richieste del carico di lavoro indicano che è necessario un aumento.

Controllare il registro eventi **di Microsoft-SMA** per un evento corrispondente con descrizione `Win32 Process Exited with code [4294967295]`. La causa di questo errore è che non è stata configurata l'autenticazione nei runbook o specificato le credenziali RunAs per il gruppo Hybrid Runbook Worker. Esaminare le autorizzazioni per i runbook in [Esecuzione dei runbook in un](../automation-hrw-run-runbooks.md) ruolo di lavoro ibrido per runbook per verificare che l'autenticazione per i runbook sia stata configurata correttamente.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: evento 15011 in Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Il ruolo di lavoro ibrido per runbook riceve l'evento 15011, che indica che il risultato di una query non è valido. Il seguente errore viene visualizzato quando il worker tenta di aprire una connessione con il [server SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per runbook non è stato configurato correttamente per la soluzione di distribuzione automatica. Questa soluzione contiene una parte che connette la macchina virtuale all'area di lavoro di Log Analytics.This solution contains a part that connects the VM to the Log Analytics workspace. Lo script di PowerShell cerca l'area di lavoro nella sottoscrizione con il nome fornito. In questo caso, l'area di lavoro di Log Analytics si trova in una sottoscrizione diversa. Lo script non riesce a trovare l'area di lavoro e tenta di crearne una, ma il nome è già stato eseguito. Pertanto la distribuzione non riesce.

#### <a name="resolution"></a>Risoluzione

Sono disponibili due opzioni per risolvere questo problema:

* Modificare lo script di PowerShell per cercare l'area di lavoro di Log Analytics in un'altra sottoscrizione. Questa è una buona soluzione se si prevede di distribuire molte macchine ibride per runbook worker in futuro.

* Configurare manualmente la macchina di lavoro per l'esecuzione in una sandbox di Orchestrator. Eseguire quindi un runbook creato nell'account di Automazione di Azure nel worker per testare la funzionalità.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: macchine virtuali di Windows Azure eliminate automaticamente dal gruppo di lavoro ibridoScenario: Windows Azure VMs automatically dropped from hybrid worker group

#### <a name="issue"></a>Problema

Non è possibile visualizzare il lavoratore ibrido Runbook o le macchine virtuali quando la macchina di lavoro è stata spenta per molto tempo.

#### <a name="cause"></a>Causa

Il computer Hybrid Runbook Worker non esegue il ping di Automazione di Azure per più di 30 giorni. Di conseguenza, l'automazione ha eliminato il gruppo Hybrid Runbook Worker o il gruppo System Worker. 

#### <a name="resolution"></a>Risoluzione

Avviare il computer di lavoro e quindi registrarlo nuovamente con Automazione di Azure.Start the worker machine and then reregister it with Azure Automation. Vedere le istruzioni per l'installazione dell'ambiente runbook e la connessione ad Automazione di Azure in Distribuire un ruolo di lavoro ibrido di [Windows.](../automation-windows-hrw-install.md)

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: nessun certificato trovato nell'archivio certificati in Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Un runbook in esecuzione su un ruolo di lavoro ibrido Runbook ha esito negativo con il seguente messaggio di errore.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Causa

Questo errore si verifica quando si tenta di utilizzare un [account RunAs](../manage-runas-account.md) in un runbook che viene eseguito in un lavoratore ibrido Runbook in cui il certificato dell'account RunAs non è presente. I ruoli di lavoro ibridi per runbook non dispongono dell'asset del certificato in locale per impostazione predefinita. L'account RunAs richiede che questa risorsa funzioni correttamente.

#### <a name="resolution"></a>Risoluzione

Se il ruolo di lavoro ibrido per runbook è una macchina virtuale di Azure, è possibile usare [le identità gestite per le risorse](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) di Azure.If your Hybrid Runbook Worker is an Azure VM, you can use Managed identities for Azure resources instead. Questo scenario semplifica l'autenticazione consentendo di eseguire l'autenticazione alle risorse di Azure usando l'identità gestita della macchina virtuale di Azure anziché l'account RunAs. Quando il ruolo di lavoro ibrido per runbook è un computer locale, è necessario installare il certificato dell'account RunAs nel computer. Per informazioni su come installare il certificato, vedere la procedura per eseguire il runbook di PowerShell **Export-RunAsCertificateToHybridWorker** in [Running runbooks on a Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: Errore 403 durante la registrazione del ruolo di lavoro ibrido per runbook

#### <a name="issue"></a>Problema

La fase di registrazione iniziale del lavoratore non riesce e viene visualizzato il seguente errore (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Causa

Le possibili cause sono le seguenti:

* Nelle impostazioni dell'agente è presente un ID area di lavoro o una chiave dell'area di lavoro digitata in modo errato. 

* Il ruolo di lavoro ibrido Runbook non è in grado di scaricare la configurazione, causando un errore di collegamento dell'account. Quando Azure abilita le soluzioni, supporta solo determinate aree per il collegamento di un'area di lavoro di Log Analytics e di un account di automazione. È anche possibile che nel computer siano impostate una data e/o un'ora errate. Se l'ora è di 15 minuti dall'ora corrente, l'onboarding non riesce.

#### <a name="resolution"></a>Risoluzione

##### <a name="mistyped-workspace-idkey"></a>ID/chiave dell'area di lavoro digitata in modo errato
Per verificare se l'ID dell'area di lavoro dell'agente o la chiave dell'area di lavoro è stata digitata in modo errato, vedere Aggiunta o rimozione di [un'area](../../azure-monitor/platform/agent-manage.md#windows-agent) di lavoro, vedere Aggiunta o rimozione di un agente Windows per l'agente [Windows.](../../azure-monitor/platform/agent-manage.md#linux-agent)  Assicurarsi di selezionare la stringa completa dal portale di Azure e copiarla e incollarla con attenzione.

##### <a name="configuration-not-downloaded"></a>Configurazione non scaricata

L'area di lavoro di Log Analytics e l'account di automazione devono trovarsi in un'area collegata. Per un elenco delle aree supportate, vedere [Mapping dell'area](../how-to/region-mappings.md)di lavoro di Automazione di Azure e Log Analytics .

Potrebbe anche essere necessario aggiornare la data e/o il fuso orario del computer. Se si seleziona un intervallo di tempo personalizzato, assicurarsi che l'intervallo sia in formato UTC, che può differire dal fuso orario locale.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker dipende [dall'agente di Log Analytics per Linux per](../../azure-monitor/platform/log-analytics-agent.md) comunicare con l'account di automazione per registrare il lavoratore, ricevere processi runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: L'agente di Log Analytics per Linux non è in esecuzione

#### <a name="issue"></a>Problema

L'agente di Log Analytics per Linux non è in esecuzione

#### <a name="cause"></a>Causa

Se l'agente non è in esecuzione, impedisce al ruolo di lavoro ibrido Linux di comunicare con L'automazione di Azure.If the agent isn't running, it prevents the Linux Hybrid Runbook Worker from communicating with Azure Automation. L'agente potrebbe non essere in esecuzione per vari motivi.

#### <a name="resolution"></a>Risoluzione

 Verificare che l'agente `ps -ef | grep python`sia in esecuzione immettendo il comando . Si dovrebbe vedere un output simile al seguente, i processi Python con account utente **nxautomation.** Se la soluzione Update Management o Azure Automation non è abilitata, nessuno dei processi seguenti è in esecuzione.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

L'elenco seguente mostra i processi avviati per un ruolo di lavoro ibrido per runbook di Linux. Tutti si trovano nella directory **/var/opt/microsoft/omsagent/state/automationworker/.**

* **oms.conf** - Il processo di gestione dei lavoratori. Viene avviato direttamente da DSC.

* **worker.conf** - Il processo di lavoro ibrido autoregistrato. Viene avviato dal gestore del ruolo di lavoro. Questo processo viene usato da Gestione aggiornamenti ed è trasparente all'utente. Questo processo non è presente se la soluzione Gestione aggiornamenti non è abilitata nel computer.

* **diy/worker.conf** - Il processo di lavoro ibrido fai da te. Il processo del ruolo di lavoro ibrido registrato manualmente viene usato per eseguire i runbook dell'utente nel ruolo di lavoro ibrido per runbook. Si differenzia solo dal processo di lavoro ibrido autoregistrato nel dettaglio chiave che utilizza una configurazione diversa. Questo processo non è presente se la soluzione di automazione di Azure è disabilitata e il ruolo di computer ibrido di Business E-Business Web Windows Ibrido di Windows Windows Non è stato registrato.

Se l'agente non è in esecuzione, eseguire `sudo /opt/microsoft/omsagent/bin/service_control restart`il comando seguente per avviare il servizio: .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: la classe specificata non esiste

Se viene visualizzato `The specified class does not exist..` l'errore nel file **/var/opt/microsoft/omsconfig/omsconfig.log**, l'agente di Log Analytics per Linux deve essere aggiornato. Eseguire il comando seguente per reinstallare l'agente:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>WINDOWS

Il ruolo di lavoro per runbook ibrido di Windows dipende [dall'agente di Log Analytics per Windows per](../../azure-monitor/platform/log-analytics-agent.md) comunicare con l'account di automazione per registrare il worker, ricevere processi di runbook e segnalare lo stato. Se la registrazione del lavoratore non riesce, questa sezione include alcuni possibili motivi.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenario: L'agente di Log Analytics per Windows non è in esecuzione

#### <a name="issue"></a>Problema

Il `healthservice` non è in esecuzione sul computer ibrido Runbook Worker.

#### <a name="cause"></a>Causa

Se il servizio Log Analytics per Windows non è in esecuzione, il ruolo di lavoro ibrido per runbook non è in grado di comunicare con Automazione di Azure.If the Log Analytics for Windows service isn't running, the Hybrid Runbook Worker can't communicate with Azure Automation.

#### <a name="resolution"></a>Risoluzione

Verificare che l'agente sia in esecuzione `Get-Service healthservice`immettendo il comando seguente in PowerShell: . Se il servizio viene arrestato, immettere il comando seguente in PowerShell per avviare il servizio: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Scenario: evento 4502 nel registro di Operations Manager

#### <a name="issue"></a>Problema

Nel registro eventi **Registri applicazioni e servizi di Operations Manager** vengono visualizzati gli eventi 4502 e EventMessage con `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` la seguente descrizione:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Causa

Questo problema può essere causato dal firewall proxy o di rete che blocca la comunicazione con Microsoft Azure. Verificare che il computer disponga dell'accesso in uscita a **.azure-automation.net** sulla porta 443. 

#### <a name="resolution"></a>Risoluzione

I registri vengono archiviati in locale in ogni worker ibrido in **C:** È possibile verificare se sono presenti eventi di avviso o di errore nei registri dell'applicazione e dei **servizi, ovvero Microsoft-SMA, Operations** e **registri applicazioni e servizi, Operations Manager.** Questi log indicano una connettività o un altro tipo di problema che influisce sull'onboarding del ruolo in Automazione di Azure o un problema rilevato durante le normali operazioni. Per ulteriori informazioni sulla risoluzione dei problemi relativi all'agente di Log Analytics, vedere [Risolvere i problemi relativi all'agente Windows di Log Analytics.](../../azure-monitor/platform/agent-windows-troubleshoot.md)

I lavoratori ibridi inviano [l'output e](../automation-runbook-output-and-messages.md) i messaggi di Runbook ad Automazione di Azure nello stesso modo in cui i processi di runbook in esecuzione nel cloud inviano messaggi e output. È possibile abilitare i flussi Dettagliato e Stato come per i runbook.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Scenario: Orchestrator.Sandbox.exe Impossibile connettersi a Office 365 tramite proxy

#### <a name="issue"></a>Problema

Uno script in esecuzione in un windows Hybrid Runbook Worker non può connettersi come previsto a Office 365 in una sandbox di Orchestrator. Lo script utilizza [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) per la connessione. 

Se si modifica **Orchestrator.Sandbox.exe.config** per impostare il proxy e l'elenco di esclusione, la sandbox non si connette ancora correttamente. Un file **Powershell_ise.exe.config** con lo stesso proxy e le stesse impostazioni dell'elenco di esclusione sembra funzionare come previsto. I log di Service Management Automation (SMA) e i log di PowerShell non forniscono informazioni sul proxy.

#### <a name="cause"></a>Causa

La connessione ad Active Directory Federation Services (ADFS) sul server non può ignorare il proxy. Tenere presente che una sandbox di PowerShell viene eseguita come utente connesso. Tuttavia, una sandbox di Orchestrator è fortemente personalizzata e potrebbe ignorare le impostazioni del file **Orchestrator.Sandbox.exe.config.** Dispone di codice speciale per la gestione delle impostazioni del proxy MMA o della macchina, ma non per la gestione di altre impostazioni proxy personalizzate. 

#### <a name="resolution"></a>Risoluzione

È possibile risolvere il problema per la sandbox di Orchestrator eseguendo la migrazione dello script per l'utilizzo dei moduli di Azure AD anziché del modulo MSOnline per i cmdlet di PowerShell. Vedere [Migrazione da Orchestrator ad Automazione di Azure (Beta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Se si desidera continuare a utilizzare i cmdlet del modulo MSOnline, modificare lo script in modo da utilizzare [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Specificare i `ComputerName` `Credential` valori per i parametri e . 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Questa modifica del codice avvia una sessione di PowerShell completamente nuova nel contesto delle credenziali specificate. Deve consentire al traffico di passare attraverso un server proxy che autentica l'utente attivo.

>[!NOTE]
>Questa soluzione rende superfluo manipolare il file di configurazione sandbox. Anche se si riesce a far funzionare il file di configurazione con lo script, il file viene cancellato ogni volta che viene aggiornato l'agente di lavoro ibrido per runbook.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: lavoratore runbook ibrido non segnalatoScenario: Hybrid Runbook Worker not reporting

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

#### <a name="resolution"></a>Risoluzione

Per risolvere questo problema, accedere al ruolo di lavoro ibrido per runbook ed eseguire lo script seguente. Questo script arresta l'agente di Log Analytics per Windows, ne rimuove la cache e riavvia il servizio. Questa azione costringe il ruolo di lavoro ibrido per runbook a scaricare nuovamente la configurazione da Automazione di Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: You can't add a Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Quando si prova ad aggiungere un ruolo di lavoro ibrido per runbook usando il cmdlet `Add-HybridRunbookWorker` viene visualizzato il messaggio seguente.

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Questo problema può essere causato se il computer è già registrato con un account di automazione diverso o se si tenta di aggiungere nuovamente il ruolo di lavoro ibrido Runbook dopo averlo rimosso da un computer.

#### <a name="resolution"></a>Risoluzione

Per risolvere questo problema, rimuovere la `HealthService`seguente chiave `Add-HybridRunbookWorker` del Registro di sistema, riavviare il e riprovare a utilizzare il cmdlet.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Passaggi successivi

Se non vedi il problema sopra o non riesci a risolvere il problema, prova uno dei seguenti canali per ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.