---
title: 'Risoluzione di problemi: ruoli di lavoro ibridi per runbook di Automazione di Azure'
description: Questo articolo contiene informazioni sulla risoluzione di problemi relativi ai ruoli di lavoro ibridi per runbook di Automazione di Azure
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 12/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0ec099e0f210fc267a0a34f76136a517e0ae6ccc
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744518"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Risolvere i problemi di ruoli di lavoro ibridi per runbook

Questo articolo contiene informazioni sulla risoluzione di problemi relativi ai ruoli di lavoro ibridi per runbook.

## <a name="general"></a>Generale

Il ruolo di lavoro ibrido per runbook dipende da un agente per comunicare con l'account di Automazione di Azure per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Per Windows, questo agente è Microsoft Monitoring Agent. Per Linux, è l'agente di OMS per Linux.

### <a name="runbook-execution-fails"></a>Scenario: si verifica un errore di esecuzione del runbook

#### <a name="issue"></a>Problema

L'esecuzione del runbook ha esito negativo e viene visualizzato l'errore seguente:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Il runbook viene sospeso dopo il terzo tentativo di esecuzione. Alcune condizioni possono interrompere il runbook prima del completamento. In questo caso, il messaggio di errore correlato potrebbe non includere informazioni aggiuntive che ne indicano il motivo.

#### <a name="cause"></a>Causa

Le cause possibili sono:

* I runbook non possono autenticarsi con le risorse locali

* Il ruolo di lavoro ibrido è protetto da proxy o firewall

* I runbook non possono autenticarsi con le risorse locali

* Il computer configurato per eseguire il ruolo di lavoro ibrido per runbook non soddisfa i requisiti hardware minimi.

#### <a name="resolution"></a>Risoluzione

Verificare che il computer abbia accesso in uscita a *.azure-automation.net sulla porta 443.

I computer che eseguono il ruolo di lavoro ibrido per runbook devono soddisfare i requisiti hardware minimi per poter ospitare questa funzionalità. I runbook e i processi in background in uso potrebbero causare un sovraccarico al sistema e causare ritardi o timeout nei processi di runbook.

Verificare che il computer designato per svolgere il ruolo di lavoro ibrido per runbook soddisfi i requisiti hardware minimi. In caso affermativo, monitorare l'utilizzo di CPU e memoria per determinare eventuali correlazioni tra le prestazioni dei processi del ruolo di lavoro ibrido per runbook e Windows. Un utilizzo elevato di CPU o memoria può indicare la necessità di eseguire un aggiornamento delle risorse. In alternativa, selezionare una risorsa di calcolo diversa in grado di supportare i requisiti minimi e di ridimensionare quando le esigenze del carico di lavoro indicano la necessità di un aumento.

Controllare il registro eventi **Microsoft-SMA** per un evento corrispondente con la descrizione *Processo Win32 terminato con codice [4294967295]*. La causa dell'errore è che l'autenticazione non è stata configurata nei runbook oppure le credenziali Esegui come non sono state specificate per il gruppo del ruolo di lavoro ibrido. Per verificare di aver configurato correttamente l'autenticazione per i runbook, vedere [Autorizzazioni per i runbook](../automation-hrw-run-runbooks.md#runbook-permissions).

### <a name="no-cert-found"></a>Scenario: Non è stato trovato alcun certificato nell'archivio certificati del ruolo di lavoro ibrido per runbook

#### <a name="issue"></a>Problema

Un runbook in esecuzione nel ruolo di lavoro ibrido per runbook ha esito negativo con il messaggio di errore seguente:

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando si prova a usare un [Account RunAs](../manage-runas-account.md) in un runbook che viene eseguito nel ruolo di lavoro ibrido per runbook in cui non è presente il certificato dell'account RunAs. Il ruolo di lavoro ibrido per runbook non dispone dell'asset del certificato in locale per impostazione predefinita, necessario all'account RunAs per funzionare correttamente.

#### <a name="resolution"></a>Risoluzione

Se il ruolo di lavoro ibrido per runbook è una macchina virtuale di Azure, è possibile usare in alternativa le [Identità gestite per le risorse di Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources). Questo scenario consente di eseguire l'autenticazione alle risorse di Azure usando l'identità gestita della macchina virtuale di Azure anziché l'account RunAs, semplificando l'autenticazione. Quando il ruolo di lavoro ibrido per runbook è un computer locale, è necessario installare sul computer il certificato dell'account RunAs. Per informazioni su come installare il certificato, vedere la procedura per eseguire il runbook [Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script).

## <a name="linux"></a>Linux

Il ruolo di lavoro ibrido per runbook di Linux dipende dall'agente OMS per Linux per comunicare con l'account di automazione per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore:

### <a name="oms-agent-not-running"></a>Scenario: L'agente di Operations Management Suite per Linux non è in esecuzione

#### <a name="issue"></a>Problema

L'agente OMS per Linux non è in esecuzione

#### <a name="cause"></a>Causa

Se l'agente di OMS per Linux non è in esecuzione, il ruolo di lavoro ibrido per runbook di Linux non è in grado di comunicare con Automazione di Azure. L'agente potrebbe non essere in esecuzione per diversi motivi.

#### <a name="resolution"></a>Risoluzione

 Verificare l'esecuzione dell'agente immettendo il comando seguente: `ps -ef | grep python`. L'output dovrebbe essere simile al seguente, i processi Python con l'account utente **nxautomation**. Se le soluzioni Gestione aggiornamenti e Automazione di Azure non sono abilitate, nessuno dei processi seguenti viene eseguito.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

L'elenco seguente mostra i processi avviati per un ruolo di lavoro ibrido per runbook di Linux. Si trovano tutti nella directory `/var/opt/microsoft/omsagent/state/automationworker/`.


* **oms.conf**: questo valore costituisce il processo di gestione del ruolo di lavoro. Viene avviato direttamente da DSC.

* **worker.conf**: è il processo del ruolo di lavoro ibrido registrato automaticamente che viene avviato dallo strumento di gestione del ruolo di lavoro. Questo processo viene usato da Gestione aggiornamenti ed è trasparente all'utente. Questo processo non è presente se la soluzione Gestione aggiornamenti non è abilitata nel computer.

* **diy/worker.conf**: è il processo del ruolo di lavoro ibrido registrato manualmente. Il processo del ruolo di lavoro ibrido registrato manualmente viene usato per eseguire i runbook dell'utente nel ruolo di lavoro ibrido per runbook. L'unica differenza con il processo del ruolo di lavoro ibrido registrato automaticamente è che usa una configurazione diversa. Questo processo non è presente se la soluzione Automazione di Azure non è abilitata e il ruolo di lavoro ibrido di Linux manuale non è registrato.

Se l'agente di Operations Management Suite per Linux non è in esecuzione, eseguire il comando seguente per avviare il servizio: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scenario: La classe specificata non esiste

Se viene visualizzato l'errore: **La classe specificata non esiste** nel log `/var/opt/microsoft/omsconfig/omsconfig.log`, è necessario aggiornare l'agente OMS per Linux. Eseguire il comando seguente per reinstallare l'agente OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a> Windows

Il ruolo di lavoro ibrido per runbook di Windows dipende da Microsoft Monitoring Agent per comunicare con l'account di Automazione di Azure per registrare il ruolo di lavoro, ricevere i processi del runbook e segnalare lo stato. Se la registrazione del ruolo di lavoro non riesce, ecco alcune possibili cause dell'errore:

### <a name="mma-not-running"></a>Scenario: Microsoft Monitoring Agent non è in esecuzione

#### <a name="issue"></a>Problema

Il servizio `healthservice` non è in esecuzione nel computer del ruolo di lavoro ibrido per runbook.

#### <a name="cause"></a>Causa

Se il servizio Microsoft Monitoring Agent di Windows non è in esecuzione, questo stato impedisce al ruolo di lavoro ibrido per runbook di comunicare con Automazione di Azure.

#### <a name="resolution"></a>Risoluzione

Verificare l'esecuzione dell'agente immettendo il comando seguente in PowerShell: `Get-Service healthservice`. Se il servizio viene arrestato, immettere il comando seguente in PowerShell per avviare il servizio: `Start-Service healthservice`.

### <a name="event-4502"></a> Evento 4502 nel registro Operations Manager

#### <a name="issue"></a>Problema

Nel log eventi **Registri applicazioni e servizi\Operations Manager** vengono visualizzati l'evento 4502 ed EventMessage contenente **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** con la descrizione seguente: *Il certificato presentato dal servizio \<wsid\>.oms.opinsights.azure.com non è stato rilasciato da un'autorità di certificazione usata per i servizi Microsoft. Contattare l'amministratore di rete per verificare che il proxy che intercetta la comunicazione TLS/SSL sia in esecuzione. L'articolo KB3126513 contiene altre informazioni sulla risoluzione dei problemi di connettività.*

#### <a name="cause"></a>Causa

Questo problema può essere causato dal firewall proxy o di rete che blocca la comunicazione con Microsoft Azure. Verificare che il computer abbia accesso in uscita a *.azure-automation.net sulle porte 443.

#### <a name="resolution"></a>Risoluzione

I log vengono archiviati localmente in ogni ruolo di lavoro ibrido in C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. È possibile verificare se sono presenti eventi di avviso o errore nei log eventi **Registri applicazioni e servizi\Microsoft-SMA\Operazioni** e **Registri applicazioni e servizi\Operations Manager** che indicano la presenza di un problema di connettività o altro problema che interessa il caricamento del ruolo in Automazione di Azure o che si verifica durante l'esecuzione di normali operazioni.

[Output di runbook e messaggi](../automation-runbook-output-and-messages.md) vengono inviati ad Automazione di Azure da ruoli di lavoro ibridi nello stesso modo in cui vengono eseguiti i processi per runbook nel cloud. È anche possibile abilitare i flussi Verbose e Progress come per qualsiasi altro runbook.

### <a name="corrupt-cache"></a> Nessuna segnalazione da parte dei ruoli di lavoro ibrido per runbook

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

Per risolvere questo problema, accedere al ruolo di lavoro ibrido per runbook ed eseguire lo script seguente. Questo script arresta Microsoft Monitoring Agent, rimuove la cache e riavvia il servizio. Questa azione costringe il ruolo di lavoro ibrido per runbook a scaricare nuovamente la configurazione da Automazione di Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.

