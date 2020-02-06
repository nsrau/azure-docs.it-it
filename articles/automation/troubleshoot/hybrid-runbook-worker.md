---
title: 'Risoluzione di problemi: ruoli di lavoro ibridi per runbook di Automazione di Azure'
description: Questo articolo contiene informazioni sulla risoluzione di problemi relativi ai ruoli di lavoro ibridi per runbook di Automazione di Azure
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4d804499116631be6f922f67f8b8f6c7063a6d5c
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030728"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Risolvere i problemi di ruoli di lavoro ibridi per runbook

Questo articolo contiene informazioni sulla risoluzione di problemi relativi ai ruoli di lavoro ibridi per runbook.

## <a name="general"></a>Generale

Il ruolo di lavoro ibrido per runbook dipende da un agente per comunicare con l'account di Automazione di Azure per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Per Windows, questo agente è l'agente Log Analytics per Windows, noto anche come Microsoft Monitoring Agent (MMA). Per Linux, si tratta dell'agente Log Analytics per Linux.

### <a name="runbook-execution-fails"></a>Scenario: l'esecuzione del runbook ha esito negativo

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

* Il computer configurato per eseguire la funzionalità di ruolo di lavoro ibrido per Runbook non soddisfa i requisiti hardware minimi.

#### <a name="resolution"></a>Risoluzione

Verificare che il computer abbia accesso in uscita a *. azure-automation.net sulla porta 443.

I computer che eseguono il ruolo di lavoro ibrido per Runbook devono soddisfare i requisiti hardware minimi prima che il thread di lavoro sia configurato per ospitare questa funzionalità. Manuali operativi e il processo in background che usano potrebbero causare il sovrautilizzo del sistema e causare ritardi o timeout del processo Runbook.

Verificare che il computer in cui è in esecuzione la funzionalità Hybrid Runbook Workers soddisfi i requisiti hardware minimi. In caso affermativo, monitorare l'utilizzo di CPU e memoria per determinare eventuali correlazioni tra le prestazioni dei processi del ruolo di lavoro ibrido per runbook e Windows. Qualsiasi pressione della memoria o della CPU può indicare la necessità di aggiornare le risorse. È anche possibile selezionare una risorsa di calcolo diversa che supporti i requisiti minimi e la scalabilità quando le richieste di carico di lavoro indicano che è necessario un aumento.

Controllare il registro eventi **Microsoft-SMA** per un evento corrispondente con la descrizione *Processo Win32 terminato con codice [4294967295]* . La ragione di questo errore è che non è stata configurata l'autenticazione nella manuali operativi o sono state specificate le credenziali RunAs per il gruppo di lavoro ibrido. Verificare le [autorizzazioni di Runbook](../automation-hrw-run-runbooks.md#runbook-permissions) per verificare di avere configurato correttamente l'autenticazione per il manuali operativi.

### <a name="no-cert-found"></a>Scenario: non è stato trovato alcun certificato nell'archivio certificati del ruolo di lavoro ibrido per Runbook

#### <a name="issue"></a>Problema

Un Runbook in esecuzione in un ruolo di lavoro ibrido per Runbook non riesce con il messaggio di errore seguente.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Causa

Questo errore si verifica quando si tenta di usare un [account RunAs](../manage-runas-account.md) in un Runbook in esecuzione in un ruolo di lavoro ibrido per Runbook in cui il certificato dell'account RunAs non è presente. Per impostazione predefinita, i ruoli di lavoro ibridi per Runbook non hanno l'asset di certificato, che è necessario per il corretto funzionamento dell'account RunAs.

#### <a name="resolution"></a>Risoluzione

Se il ruolo di lavoro ibrido per Runbook è una macchina virtuale di Azure, è invece possibile usare [identità gestite per le risorse di Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Questo scenario semplifica l'autenticazione consentendo di eseguire l'autenticazione alle risorse di Azure usando l'identità gestita della macchina virtuale di Azure anziché l'account RunAs. Quando il ruolo di lavoro ibrido per Runbook è un computer locale, è necessario installare il certificato dell'account RunAs nel computer. Per informazioni su come installare il certificato, vedere la procedura per eseguire il Runbook Export-RunAsCertificateToHybridWorker di PowerShell in esecuzione di manuali operativi in un ruolo di [lavoro ibrido per Runbook](../automation-hrw-run-runbooks.md).

### <a name="error-403-on-registration"></a>Scenario: errore 403 durante la registrazione del ruolo di lavoro ibrido per Runbook

#### <a name="issue"></a>Problema

La fase di registrazione iniziale del processo di lavoro ha esito negativo e viene visualizzato l'errore seguente (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Causa

Le possibili cause sono le seguenti:
* È presente un ID dell'area di lavoro o una chiave dell'area di lavoro (primaria) non tipizzata nelle impostazioni dell'agente. 
* Il ruolo di lavoro ibrido per Runbook non può scaricare la configurazione, causando un errore di collegamento dell'account. Quando Azure Abilita soluzioni, supporta solo determinate aree per collegare un'area di lavoro Log Analytics e un account di automazione. È anche possibile che nel computer sia impostata una data e/o un'ora errate. Se l'ora è +/-15 minuti dall'ora corrente, il caricamento ha esito negativo.

#### <a name="resolution"></a>Risoluzione

##### <a name="mistyped-workspace-idkey"></a>ID/chiave dell'area di lavoro con digitazione
Per verificare se l'ID dell'area di lavoro dell'agente o la chiave dell'area di lavoro è stata digitata in modo non riuscito, vedere [aggiunta o rimozione di un'area di lavoro-agente Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) per l'agente Windows o [aggiunta o rimozione di un'area di lavoro-agente Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) per l'agente Linux  Assicurarsi di selezionare la stringa completa dall'portale di Azure e copiarla e incollarla con cautela.

##### <a name="configuration-not-downloaded"></a>Configurazione non scaricata

L'area di lavoro Log Analytics e l'account di automazione devono trovarsi in un'area collegata. Per un elenco delle aree supportate, vedere [automazione di Azure e mapping dell'area di lavoro log Analytics](../how-to/region-mappings.md).

Potrebbe inoltre essere necessario aggiornare la data e il fuso orario del computer. Se si seleziona un intervallo di tempo personalizzato, assicurarsi che l'intervallo sia in formato UTC, che può essere diverso dal fuso orario locale.

## <a name="linux"></a>Linux

Il ruolo di lavoro ibrido per Runbook di Linux dipende dall' [agente log Analytics per Linux](../../azure-monitor/platform/log-analytics-agent.md) per comunicare con l'account di automazione per registrare il ruolo di lavoro, ricevere i processi Runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore:

### <a name="oms-agent-not-running"></a>Scenario: l'agente Log Analytics per Linux non è in esecuzione

#### <a name="issue"></a>Problema

L'agente di Log Analytics per Linux non è in esecuzione

#### <a name="cause"></a>Causa

Se l'agente non è in esecuzione, impedisce al ruolo di lavoro ibrido per Runbook di Linux di comunicare con automazione di Azure. Per diversi motivi è possibile che l'agente non sia in esecuzione.

#### <a name="resolution"></a>Risoluzione

 Verificare l'esecuzione dell'agente immettendo il comando seguente: `ps -ef | grep python`. L'output dovrebbe essere simile al seguente, i processi Python con l'account utente **nxautomation**. Se le soluzioni Gestione aggiornamenti e Automazione di Azure non sono abilitate, nessuno dei processi seguenti viene eseguito.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

L'elenco seguente mostra i processi avviati per un ruolo di lavoro ibrido per runbook di Linux. Si trovano tutti nella directory `/var/opt/microsoft/omsagent/state/automationworker/`.

* **OMS. conf** : processo di gestione del ruolo di lavoro. Viene avviato direttamente da DSC.

* **Worker. conf** : processo di lavoro ibrido registrato automaticamente. Viene avviato dal gestore del ruolo di lavoro. Questo processo viene usato da Gestione aggiornamenti ed è trasparente all'utente. Questo processo non è presente se la soluzione Gestione aggiornamenti non è abilitata nel computer.

* **DIY/Worker. conf** : il processo di lavoro ibrido DIY. Il processo del ruolo di lavoro ibrido registrato manualmente viene usato per eseguire i runbook dell'utente nel ruolo di lavoro ibrido per runbook. Si differenzia solo dal processo di lavoro ibrido registrato automaticamente nei dettagli chiave che usa una configurazione diversa. Questo processo non è presente se la soluzione automazione di Azure è disabilitata e il ruolo di lavoro ibrido per Linux DIY non è registrato.

Se l'agente non è in esecuzione, eseguire il comando seguente per avviare il servizio: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scenario: la classe specificata non esiste

Se viene visualizzato l'errore **, la classe specificata non esiste.** nel `/var/opt/microsoft/omsconfig/omsconfig.log`è necessario aggiornare l'agente Log Analytics per Linux. Eseguire il comando seguente per reinstallare l'agente:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>WINDOWS

Il ruolo di lavoro ibrido per Runbook di Windows dipende dall' [agente di log Analytics per Windows](../../azure-monitor/platform/log-analytics-agent.md) per comunicare con l'account di automazione per registrare il ruolo di lavoro, ricevere i processi Runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore:

### <a name="mma-not-running"></a>Scenario: l'Microsoft Monitoring Agent non è in esecuzione

#### <a name="issue"></a>Problema

Il servizio `healthservice` non è in esecuzione nel computer del ruolo di lavoro ibrido per runbook.

#### <a name="cause"></a>Causa

Se il Microsoft Monitoring Agent servizio Microsoft non è in esecuzione, questo stato impedisce al ruolo di lavoro ibrido per Runbook di comunicare con automazione di Azure.

#### <a name="resolution"></a>Risoluzione

Verificare l'esecuzione dell'agente immettendo il comando seguente in PowerShell: `Get-Service healthservice`. Se il servizio viene arrestato, immettere il comando seguente in PowerShell per avviare il servizio: `Start-Service healthservice`.

### <a name="event-4502"></a>Scenario: evento 4502 nel log Operations Manager

#### <a name="issue"></a>Problema

Nel registro eventi di **Servizi\operations Manager dell'applicazione e dei servizi** vengono visualizzati gli eventi 4502 e eventMessage che contengono **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** con la seguente descrizione: *il certificato presentato dal servizio \<WSID\>. OMS.opinsights.Azure.com non è stato emesso da un'autorità di certificazione usata per i servizi Microsoft. Contattare l'amministratore di rete per verificare se è in esecuzione un proxy che intercetta la comunicazione TLS/SSL.*

#### <a name="cause"></a>Causa

Questo problema può essere causato dal firewall proxy o di rete che blocca la comunicazione con Microsoft Azure. Verificare che il computer abbia accesso in uscita a *.azure-automation.net sulle porte 443. 

#### <a name="resolution"></a>Risoluzione

I log vengono archiviati localmente in ogni ruolo di lavoro ibrido in C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. È possibile verificare se sono presenti eventi di avviso o di errore nel registro eventi di **Logs\Microsoft-SMA\Operations** e **Application and Services servizi\operations Manager** , che indicano una connettività o un altro problema che influiscono sull'onboarding del ruolo in automazione di Azure o sul problema durante le normali operazioni. Per ulteriori informazioni sulla risoluzione dei problemi relativi all'agente di Log Analytics, vedere [risolvere i problemi relativi all'agente di log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

[Output di runbook e messaggi](../automation-runbook-output-and-messages.md) vengono inviati ad Automazione di Azure da ruoli di lavoro ibridi nello stesso modo in cui vengono eseguiti i processi per runbook nel cloud. È anche possibile abilitare i flussi Verbose e Progress come per qualsiasi altro runbook.

### <a name="corrupt-cache"></a>Scenario: il ruolo di lavoro ibrido per Runbook non segnala

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

Per risolvere questo problema, accedere al ruolo di lavoro ibrido per runbook ed eseguire lo script seguente. Questo script arresta Microsoft Monitoring Agent, rimuove la cache e riavvia il servizio. Questa azione impone al ruolo di lavoro ibrido per Runbook di riscaricare la configurazione da automazione di Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Scenario: non è possibile aggiungere un ruolo di lavoro ibrido per Runbook

#### <a name="issue"></a>Problema

Quando si prova ad aggiungere un ruolo di lavoro ibrido per runbook usando il cmdlet `Add-HybridRunbookWorker` viene visualizzato il messaggio seguente.

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Questo problema può essere causato dal fatto che il computer è già registrato con un account di automazione diverso o se si prova a leggere il ruolo di lavoro ibrido per runbook dopo averlo rimosso da un computer.

#### <a name="resolution"></a>Risoluzione

Per risolvere questo problema, rimuovere la chiave del Registro di sistema seguente, riavviare `HealthService` e provare di nuovo a eseguire il cmdlet `Add-HybridRunbookWorker`:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.