---
title: Risoluzione dei problemi relativi al ruolo di lavoro ibrido per runbook di Automazione di Azure
description: Questo articolo spiega come risolvere i problemi che si verificano con i ruoli di lavoro ibrido per runbook di Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2149fd68cdf5f2991d6035f245f70515e920045c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187201"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Risolvere i problemi di un ruolo di lavoro ibrido per runbook

Questo articolo contiene informazioni sulla risoluzione dei problemi relativi ai ruoli di lavoro ibridi per runbook di Automazione di Azure. Per informazioni generali, vedere [Panoramica dei ruoli di lavoro ibrido per runbook](../automation-hybrid-runbook-worker.md).

## <a name="general"></a>Generale

Il ruolo di lavoro ibrido per runbook si affida a un agente per comunicare con l'account di Automazione di Azure per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. In Windows, si tratta dell'agente di Log Analytics per Windows, mentre in Linux è l'agente di Log Analytics per Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: si verifica un errore di esecuzione del runbook

#### <a name="issue"></a>Problema

L'esecuzione del runbook ha esito negativo e viene visualizzato il messaggio di errore seguente:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Il runbook viene sospeso dopo il terzo tentativo di esecuzione. Alcune condizioni possono interrompere il runbook prima del completamento. Il relativo messaggio di errore potrebbe essere privo di informazioni aggiuntive.

#### <a name="cause"></a>Causa

Le possibili cause sono le seguenti:

* I runbook non possono autenticarsi con le risorse locali.
* Il ruolo di lavoro ibrido è protetto da proxy o firewall.
* Il computer designato per eseguire il ruolo di lavoro ibrido per runbook non soddisfa i requisiti hardware minimi.

#### <a name="resolution"></a>Risoluzione

Verificare che il computer abbia accesso in uscita a * **.azure-automation.net** sulla porta 443.

I computer che eseguono il ruolo di lavoro ibrido per runbook devono soddisfare i requisiti hardware minimi per consentire al ruolo di lavoro di ospitare questa funzionalità. I runbook e il processo in background in uso potrebbero causare un sovraccarico al sistema e provocare ritardi o timeout nei processi di runbook.

Verificare che il computer designato per svolgere il ruolo di lavoro ibrido per runbook soddisfi i requisiti hardware minimi. In caso affermativo, monitorare l'utilizzo di CPU e memoria per determinare eventuali correlazioni tra le prestazioni dei processi del ruolo di lavoro ibrido per runbook e Windows. Un uso elevato di CPU o memoria può indicare la necessità di aggiornamento delle risorse. In alternativa, selezionare una risorsa di calcolo diversa in grado di supportare i requisiti minimi e il ridimensionamento quando le esigenze del carico di lavoro indicano la necessità di un aumento.

Verificare se nel registro eventi **Microsoft-SMA** è presente un evento corrispondente alla descrizione `Win32 Process Exited with code [4294967295]`. L'errore è causato dalla mancata configurazione dell'autenticazione nei runbook oppure le credenziali Esegui come non sono state specificate per il gruppo del ruolo di lavoro ibrido. Controllare le autorizzazioni del runbook in [Esecuzione di runbook in un ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md) per verificare di avere configurato correttamente l'autenticazione per i runbook.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: Evento 15011 nel ruolo di lavoro ibrido per runbook

#### <a name="issue"></a>Problema

Il ruolo di lavoro ibrido per runbook riceve l'evento 15011 a indicare che il risultato di una query non è valido. L'errore seguente viene visualizzato quando il ruolo di lavoro tenta di aprire una connessione con il [server SignalR](/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per runbook non è stato configurato correttamente per la distribuzione automatica delle funzionalità, ad esempio per Gestione aggiornamenti. La distribuzione contiene una parte che connette la macchina virtuale all'area di lavoro Log Analytics. Lo script di PowerShell cerca l'area di lavoro nella sottoscrizione con il nome specificato. In questo caso, l'area di lavoro Log Analytics si trova in una sottoscrizione diversa. Lo script non è in grado di trovare l'area di lavoro e tenta di crearne una, ma il nome è già in uso. Di conseguenza, la distribuzione ha esito negativo.

#### <a name="resolution"></a>Risoluzione

Per risolvere il problema sono disponibili due opzioni:

* Modificare lo script di PowerShell per cercare l'area di lavoro Log Analytics in un'altra sottoscrizione. Si tratta di una soluzione efficace se si prevede di distribuire molti computer con ruolo di lavoro ibrido per runbook in futuro.

* Configurare manualmente il computer di lavoro per l'esecuzione in una sandbox di Orchestrator. Eseguire quindi un runbook creato nell'account di Automazione di Azure nel ruolo di lavoro per testare la funzionalità.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: Macchine virtuali di Windows Azure eliminate automaticamente da un gruppo del ruolo di lavoro ibrido

#### <a name="issue"></a>Problema

Non è possibile visualizzare le VM o il ruolo di lavoro ibrido per runbook quando il computer di lavoro è rimasto inattivo per molto tempo.

#### <a name="cause"></a>Causa

Il computer con ruolo di lavoro ibrido per runbook non ha eseguito il ping con Automazione di Azure per più di 30 giorni. Di conseguenza, Automazione ha rimosso il gruppo del ruolo di lavoro ibrido per runbook o del ruolo di lavoro di sistema. 

#### <a name="resolution"></a>Risoluzione

Avviare il computer con ruolo di lavoro, quindi registrarlo nuovamente con Automazione di Azure. Per istruzioni su come installare l'ambiente per runbook e connettersi ad Automazione di Azure, vedere [Distribuzione di un ruolo di lavoro ibrido per runbook di Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: Non è stato trovato alcun certificato nell'archivio certificati del ruolo di lavoro ibrido per runbook

#### <a name="issue"></a>Problema

Un runbook in esecuzione nel ruolo di lavoro ibrido per runbook ha esito negativo con il messaggio di errore seguente:

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Causa

Questo errore si verifica quando si prova a usare un [account RunAs](../manage-runas-account.md) in un runbook eseguito nel ruolo di lavoro ibrido per runbook in cui non è presente il certificato dell'account RunAs. Per impostazione predefinita, i ruoli di lavoro ibridi per runbook non hanno l'asset del certificato in locale. L'account RunAs richiede il corretto funzionamento di questo asset.

#### <a name="resolution"></a>Risoluzione

Se il ruolo di lavoro ibrido per runbook in una macchina virtuale di Azure, è possibile [usare l'autenticazione del runbook con le identità gestite](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities). Questo scenario rende più semplice l'autenticazione, consentendo all'utente di autenticarsi nelle risorse di Azure usando l'identità gestita della macchina virtuale di Azure anziché l'account RunAs. Quando il ruolo di lavoro ibrido per runbook è un computer locale, è necessario installare sul computer il certificato dell'account RunAs. Per informazioni su come installare il certificato, vedere la procedura di esecuzione del runbook di PowerShell **Export-RunAsCertificateToHybridWorker** in [Esecuzione di runbook in un ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: Errore 403 durante la registrazione di un ruolo di lavoro ibrido per runbook

#### <a name="issue"></a>Problema

La fase di registrazione iniziale del ruolo di lavoro ha esito negativo e si visualizza l'errore 403 seguente:

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Causa

Le possibili cause sono le seguenti:

* Errore nella digitazione di un ID o una chiave (primaria) dell'area di lavoro nelle impostazioni dell'agente. 
* Download della configurazione da parte del ruolo di lavoro ibrido per runbook non riuscito, provocando un errore di collegamento degli account. Quando Azure abilita funzioni sui computer, il collegamento tra un'area di lavoro Log Analytics e un account di Automazione è supportato solo in determinate aree. È anche possibile che nel computer sia impostata una data o un'ora non corretta. Se si rileva una differenza di circa 15 minuti nell'orario, la distribuzione della funzionalità ha esito negativo.

#### <a name="resolution"></a>Risoluzione

##### <a name="mistyped-workspace-id-or-key"></a>Errore di digitazione di un ID o una chiave dell'area di lavoro
Per verificare la presenza di errori di digitazione nell'ID o nella chiave dell'area di lavoro dell'agente, vedere [Aggiunta o rimozione di un'area di lavoro: Agente di Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) per l'agente Windows o [Aggiunta o rimozione di un'area di lavoro: Agente di Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) per l'agente di Linux. Assicurarsi di selezionare la stringa completa nel portale di Azure, quindi copiarla e incollarla con cautela.

##### <a name="configuration-not-downloaded"></a>Configurazione non scaricata

L'area di lavoro Log Analytics e l'account di Automazione devono trovarsi in un'area collegata. Per un elenco delle aree supportate, vedere [Mapping dell'area di lavoro Log Analytics e di Automazione di Azure](../how-to/region-mappings.md).

Potrebbe inoltre essere necessario aggiornare la data o il fuso orario del computer. Se si seleziona un intervallo di tempo personalizzato, assicurarsi di usare il fuso orario UTC, che può essere diverso da quello locale.

## <a name="linux"></a>Linux

Il ruolo di lavoro ibrido per runbook di Linux si affida all'[agente di Log Analytics per Linux](../../azure-monitor/platform/log-analytics-agent.md) per comunicare con l'account di Automazione e registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Scenario: Il ruolo di lavoro ibrido per runbook di Linux riceve la richiesta di una password durante la firma di un runbook

#### <a name="issue"></a>Problema

L'esecuzione del comando `sudo` per un ruolo di lavoro ibrido per runbook di Linux recupera una richiesta di password imprevista.

#### <a name="cause"></a>Causa

L'account **nxautomationuser** per l'agente di Log Analytics per Linux non è configurato correttamente nel file **sudoers**. Il ruolo di lavoro ibrido per runbook richiede una configurazione adeguata delle autorizzazioni dell'account e di altri dati, in modo che possa firmare i runbook nel ruolo di lavoro per runbook di Linux.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi che il ruolo di lavoro ibrido per runbook includa il file eseguibile GnuPG (GPG) nel computer.

* Verificare la configurazione dell'account **nxautomationuser** nel file **sudoers**. Vedere [Esecuzione di runbook in un ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: L'agente di Log Analytics per Linux non è in esecuzione

#### <a name="issue"></a>Problema

L'agente di Log Analytics per Linux non è in esecuzione.

#### <a name="cause"></a>Causa

Se l'agente non è in esecuzione, il ruolo di lavoro ibrido per runbook di Linux non è in grado di comunicare con Automazione di Azure. L'agente potrebbe non essere in esecuzione per diversi motivi.

#### <a name="resolution"></a>Risoluzione

 Verificare che l'agente sia in esecuzione immettendo il comando `ps -ef | grep python`. L'output dovrebbe essere simile al seguente. Sono inclusi i processi Python e l'account utente **nxautomation**. Se la funzionalità di Automazione di Azure non è abilitata, non viene eseguito nessuno dei processi seguenti.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

L'elenco seguente mostra i processi avviati per un ruolo di lavoro ibrido per runbook di Linux. Si trovano tutti nella directory /var/opt/microsoft/omsagent/state/automationworker/.

* **oms.conf**: processo di gestione del ruolo di lavoro. Viene avviato direttamente da DSC.
* **worker.conf**: processo del ruolo di lavoro ibrido registrato automaticamente. Viene avviato dal gestore del ruolo di lavoro. Questo processo viene usato da Gestione aggiornamenti ed è trasparente all'utente. Non è presente se la soluzione Gestione aggiornamenti non è abilitata nel computer.
* **diy/worker.conf**: processo di lavoro ibrido registrato manualmente. Il processo del ruolo di lavoro ibrido registrato manualmente viene usato per eseguire i runbook dell'utente nel ruolo di lavoro ibrido per runbook. L'unica differenza con il processo del ruolo di lavoro ibrido registrato automaticamente consiste nell'uso di una configurazione diversa. Questo processo non è presente se Automazione di Azure non è abilitata e il ruolo di lavoro ibrido manuale di Linux non è registrato.

Se l'agente non è in esecuzione, eseguire il comando seguente per avviare il servizio: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: La classe specificata non esiste

Se si visualizza il messaggio di errore `The specified class does not exist..` in **/var/opt/microsoft/omsconfig/omsconfig.log**, è necessario aggiornare l'agente di Log Analytics per Linux. Eseguire il comando seguente per reinstallare l'agente.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Il ruolo di lavoro ibrido per runbook di Windows si affida all'[agente di Log Analytics per Windows](../../azure-monitor/platform/log-analytics-agent.md) per comunicare con l'account di Automazione e registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro ha esito negativo, questa sezione include alcune possibili cause.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenario: L'agente di Log Analytics per Windows non è in esecuzione

#### <a name="issue"></a>Problema

Il servizio `healthservice` non è in esecuzione nel computer del ruolo di lavoro ibrido per runbook.

#### <a name="cause"></a>Causa

Se il servizio di Log Analytics per Windows non è in esecuzione, il ruolo di lavoro ibrido per runbook di Linux non è in grado di comunicare con Automazione di Azure.

#### <a name="resolution"></a>Risoluzione

Verificare l'effettiva esecuzione dell'agente immettendo il comando seguente in PowerShell: `Get-Service healthservice`. Se il servizio viene arrestato, immettere il comando seguente in PowerShell per avviare il servizio: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Scenario: Evento 4502 nel registro Operations Manager

#### <a name="issue"></a>Problema

Nel registro eventi **Application and Services Logs\Operations Manager** viene visualizzato l'evento 4502 e un messaggio di evento contenente `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` con la descrizione seguente:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Causa

Questo problema può essere causato dal firewall proxy o di rete che blocca la comunicazione con Microsoft Azure. Verificare che il computer abbia accesso in uscita a * **.azure-automation.net** sulla porta 443.

#### <a name="resolution"></a>Risoluzione

I log vengono archiviati localmente in ogni ruolo di lavoro ibrido in C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. È possibile verificare la presenza di eventuali avvisi o eventi di errore nei registri eventi **Application and Services Logs\Microsoft-SMA\Operations** e **Application and Services Logs\Operations Manager**. Questi registri indicano problemi di connettività o di altro tipo che influiscono sull'abilitazione del ruolo in Automazione di Azure o problemi riscontrati durante normali operazioni. Per altre informazioni sulla risoluzione dei problemi relativi all'agente di Log Analytics, vedere [Risoluzione dei problemi relativi all'agente dell'analisi dei log di Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

I ruoli di lavoro ibridi inviano [output e messaggi del runbook](../automation-runbook-output-and-messages.md) ad Automazione di Azure nello stesso modo in cui li inviano i processi del runbook in esecuzione nel cloud. È possibile abilitare i flussi dettagliati e di stato esattamente come per i runbook.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Scenario: Connessione di Orchestrator.Sandbox.exe a Office 365 tramite proxy non riuscita

#### <a name="issue"></a>Problema

Uno script in esecuzione in un ruolo di lavoro ibrido per runbook di Windows non si connette come previsto a Office 365 su una sandbox di Orchestrator. Lo script usa [Connect-MsolService](/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) per la connessione. 

Se si modifica **Orchestrator.Sandbox.exe.config** per configurare il proxy e l'elenco di esclusione, la sandbox continua a non connettersi correttamente. Un file **Powershell_ise.exe.config** con le stesse impostazioni del proxy e dell'elenco di esclusione. I registri di Service Management Automation (SMA) e PowerShell non forniscono alcuna informazione relativa al proxy.

#### <a name="cause"></a>Causa

La connessione ad Active Directory Federation Services (AD FS) nel server non può escludere il proxy. Tenere presente che una sandbox di PowerShell viene eseguita come utente registrato. Tuttavia, una sandbox di Orchestrator è altamente personalizzata e potrebbe ignorare le impostazioni del file **Orchestrator.Sandbox.exe.config**. La sandbox dispone di codice speciale per gestire le impostazioni del computer o del proxy agente di Log Analytics, ma non per la gestione di altre impostazioni del proxy personalizzate. 

#### <a name="resolution"></a>Risoluzione

È possibile risolvere il problema della sandbox di Orchestrator eseguendo la migrazione dello script per usare i moduli di Azure Active Directory invece del modulo MSOnline per i cmdlet di PowerShell. Per altre informazioni, vedere [Migrazione da Orchestrator ad Automazione di Azure (Beta)](../automation-orchestrator-migration.md).

Se si vuole continuare a usare i cmdlet del modulo MSOnline, modificare lo script in modo da usare [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Specificare i valori per i parametri `ComputerName` e `Credential`. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Questa modifica del codice avvia una sessione di PowerShell completamente nuova nel contesto delle credenziali specificate. Questo dovrebbe abilitare il flusso del traffico tramite un server proxy che esegue l'autenticazione dell'utente attivo.

>[!NOTE]
>Questa risoluzione rende non necessaria la modifica del file di configurazione sandbox. Anche se funziona correttamente con lo script, il file di configurazione viene cancellato ogni volta che si aggiorna l'agente del ruolo di lavoro ibrido per runbook.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: Nessuna segnalazione da parte dei ruoli di lavoro ibrido per runbook

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

Per risolvere questo problema, accedere al ruolo di lavoro ibrido per runbook ed eseguire lo script seguente. Questo script l'agente di Log Analytics per Windows, rimuove la cache e riavvia il servizio. Questa azione costringe il ruolo di lavoro ibrido per runbook a scaricare nuovamente la configurazione da Automazione di Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: Impossibile installare un ruolo di lavoro ibrido per runbook

#### <a name="issue"></a>Problema

Quando si prova ad aggiungere un ruolo di lavoro ibrido per runbook usando il cmdlet `Add-HybridRunbookWorker` viene visualizzato il messaggio seguente:

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Questo problema può verificarsi se il computer è già registrato con un account di Automazione diverso o se si prova ad aggiungere nuovamente il ruolo di lavoro ibrido per runbook dopo averlo rimosso da un computer.

#### <a name="resolution"></a>Risoluzione

Per risolvere questo problema, rimuovere la chiave del Registro di sistema seguente, riavviare `HealthService` e provare di nuovo a eseguire il cmdlet `Add-HybridRunbookWorker`.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottenere risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto di Azure mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.
