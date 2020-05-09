---
title: Risoluzione dei problemi dell'agente di Windows Update in automazione di Azure Gestione aggiornamenti
description: Informazioni su come risolvere i problemi relativi all'agente di Windows Update tramite la soluzione Gestione aggiornamenti.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: e9af9c6472f49ebccd36e8d73688636c98918ff1
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996448"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Risolvere i problemi dell'agente di Windows Update

Ci possono essere diversi motivi per cui il computer non viene visualizzato come pronto (integro) in Gestione aggiornamenti. È possibile verificare l'integrità di un agente di lavoro ibrido per Runbook Windows per determinare il problema sottostante. Di seguito sono riportati i tre stati di conformità per un computer:

* Pronto: il ruolo di lavoro ibrido per Runbook è stato distribuito e l'ultimo è stato rilevato meno di un'ora fa.
* Disconnesso: il ruolo di lavoro ibrido per Runbook è stato distribuito e l'ultimo è stato rilevato più di un'ora fa.
* Non configurato: il ruolo di lavoro ibrido per Runbook non è stato trovato o non è stato completato il caricamento.

> [!NOTE]
> È possibile che si verifichi un lieve ritardo tra il portale di Azure visualizzato e lo stato corrente di un computer.

Questo articolo illustra come eseguire lo strumento di risoluzione dei problemi per i computer Azure dalla portale di Azure e da computer non Azure nello [scenario offline](#troubleshoot-offline). 

> [!NOTE]
> Lo script di risoluzione dei problemi include ora i controlli per Windows Server Update Services (WSUS) e per le chiavi di download e di installazione. 

## <a name="start-the-troubleshooter"></a>Avviare la risoluzione dei problemi

Per le macchine virtuali di Azure, è possibile avviare la pagina risoluzione dei problemi dell'agente di aggiornamento selezionando il collegamento **risoluzione dei problemi** nella colonna **conformità agente di aggiornamento** nel portale. Per i computer non Azure, il collegamento riporta a questo articolo. Vedere le [istruzioni offline](#troubleshoot-offline) per risolvere i problemi relativi a un computer non Azure.

![Screenshot dell'elenco di Gestione aggiornamenti di macchine virtuali](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Per verificare l'integrità del ruolo di lavoro ibrido per Runbook, la macchina virtuale deve essere in esecuzione. Se la macchina virtuale non è in esecuzione, viene visualizzato il pulsante **Avvia macchina virtuale**.

Nella pagina Risoluzione dei problemi dell'agente di aggiornamento fare clic su **Esegui controlli** per avviare la risoluzione dei problemi. Lo strumento di risoluzione dei problemi usa il [comando Run](../../virtual-machines/windows/run-command.md) per eseguire uno script nel computer, per verificare le dipendenze. Una volta completata la risoluzione dei problemi, restituisce il risultato dei controlli.

![Screenshot della pagina risoluzione dei problemi dell'agente di aggiornamento](../media/update-agent-issues/troubleshoot-page.png)

I risultati vengono visualizzati nella pagina quando sono pronti. Le sezioni dei controlli mostrano il contenuto di ogni controllo.

![Screenshot della risoluzione dei problemi dei controlli degli agenti di aggiornamento](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controlli dei prerequisiti

### <a name="operating-system"></a>Sistema operativo

Il controllo del sistema operativo verifica se il ruolo di lavoro ibrido per Runbook esegue uno dei sistemi operativi indicati nella tabella seguente.

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2012 e versioni successive |È necessario .NET Framework 4,6 o versione successiva. ([Scaricare il .NET Framework](/dotnet/framework/install/guide-for-developers)).<br/> Windows PowerShell 5,1 è obbligatorio.  ([Scaricare Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616)).        |

### <a name="net-462"></a>.NET 4.6.2

Il controllo .NET Framework verifica che nel sistema sia installato [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) o versione successiva.

### <a name="wmf-51"></a>WMF 5.1

Il controllo WMF verifica che il sistema disponga della versione necessaria di Windows Management Framework (WMF), ovvero [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Questo controllo determina se si usa TLS 1.2 per crittografare le comunicazioni. TLS 1.0 non è più supportato dalla piattaforma. Usare TLS 1,2 per comunicare con Gestione aggiornamenti.

## <a name="connectivity-checks"></a>Controlli della connettività

### <a name="registration-endpoint"></a>Endpoint di registrazione

Questo controllo determina se l'agente può comunicare correttamente con il servizio agente.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con l'endpoint di registrazione. Per un elenco di indirizzi e porte da aprire, vedere [pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Endpoint delle operazioni

Questo controllo determina se l'agente può comunicare correttamente con il servizio dati del processo di runtime.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con il servizio dati del processo di runtime. Per un elenco di indirizzi e porte da aprire, vedere [pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Criteri di integrità del servizio della macchina virtuale

### <a name="monitoring-agent-service-status"></a>Monitoraggio dello stato del servizio agente

Questo controllo determina se l'agente di Log Analytics per Windows`healthservice`() è in esecuzione nel computer. Per ulteriori informazioni sulla risoluzione dei problemi relativi al servizio, vedere [l'articolo relativo all'log Analytics agente per Windows non è in esecuzione](hybrid-runbook-worker.md#mma-not-running).

Per reinstallare l'agente di Log Analytics per Windows, vedere [Install the Agent for Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitoraggio degli eventi del servizio agente

Questo controllo determina se gli eventi 4502 vengono visualizzati nel log Operations Manager di Azure nel computer nelle ultime 24 ore.

Per ulteriori informazioni su questo evento, vedere l' [evento 4502 nel registro Operations Manager](hybrid-runbook-worker.md#event-4502) per questo evento.

## <a name="access-permissions-checks"></a>Controlli sulle autorizzazioni di accesso

> [!NOTE]
> Lo strumento di risoluzione dei problemi non instrada attualmente il traffico attraverso un server proxy, se ne è stato configurato uno.

### <a name="crypto-folder-access"></a>Accesso alla cartella Crypto

Il controllo dell'accesso alla cartella Crypto determina se l'account di sistema locale può accedere a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Risolvere i problemi offline

È possibile usare lo strumento di risoluzione dei problemi in un ruolo di lavoro ibrido per Runbook offline, eseguendo lo script localmente. Ottenere lo script seguente dalla PowerShell Gallery: [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration). Per eseguire lo script, è necessario che sia installato WMF 4,0 o versione successiva. Per scaricare la versione più recente di PowerShell, vedere [installazione di diverse versioni di PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

L'output di questo script è simile al seguente:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Passaggi successivi

[Risolvere i problemi di ruoli di lavoro ibridi per runbook](hybrid-runbook-worker.md)
