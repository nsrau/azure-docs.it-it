---
title: Risolvere i problemi relativi all'agente di Windows Update in Automazione di Azure
description: Questo articolo descrive come risolvere i problemi relativi all'agente di Windows Update durante l'uso di Gestione aggiornamenti.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: ff996227e23836bf85cc3885d9184ae6d7d6c61d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680836"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Risolvere i problemi relativi all'agente di aggiornamento Windows

I motivi per cui il computer non risulta pronto (integro) durante una distribuzione di Gestione aggiornamenti possono essere diversi. In Gestione aggiornamenti è possibile controllare l'integrità dell'agente di un ruolo di lavoro ibrido per runbook di Windows per determinare il problema sottostante. I seguenti sono i tre stati di idoneità per un computer:

* Preparazione - il ruolo di lavoro ibrido per runbook è stato distribuito ed è stato osservato meno di un'ora fa.
* Disconnesso: il ruolo di lavoro ibrido per runbook è stato distribuito ed è stato osservato più di un'ora fa.
* Non configurato: Il ruolo di lavoro ibrido per runbook non è stato trovato o non ha completato la distribuzione.

> [!NOTE]
> È possibile che si verifichi un lieve ritardo tra quanto visualizzato dal portale di Azure e lo stato corrente di un computer.

Questo articolo descrive come eseguire la risoluzione dei problemi per i computer Azure dal portale di Azure e per i computer non Azure nello [scenario offline](#troubleshoot-offline). 

> [!NOTE]
> Lo script di risoluzione dei problemi include ora i controlli per Windows Server Update Services (WSUS) e per le chiavi di download e di installazione. 

## <a name="start-the-troubleshooter"></a>Avviare la risoluzione dei problemi

Per i computer Azure, è possibile avviare la pagina Risoluzione dei problemi dell'agente di aggiornamento selezionando il collegamento **Risoluzione dei problemi** nella colonna **Update Agent Readiness** (Idoneità agente di aggiornamento) nel portale. Per i computer non Azure, il collegamento indirizza a questo articolo. Per risolvere i problemi in un computer non Azure, vedere [Risolvere i problemi offline](#troubleshoot-offline).

![Screenshot dell'elenco di macchine virtuali di Gestione aggiornamenti](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Per controllare l'integrità del ruolo di lavoro ibrido per runbook, la macchina virtuale deve essere in esecuzione. Se la macchina virtuale non è in esecuzione, viene visualizzato il pulsante **Avvia macchina virtuale**.

Nella pagina Risoluzione dei problemi dell'agente di aggiornamento selezionare **Esegui controlli** per avviare lo strumento di risoluzione dei problemi. Lo strumento di risoluzione dei problemi usa [Esegui comando](../../virtual-machines/windows/run-command.md) per eseguire uno script nel computer in modo da verificare le dipendenze. Una volta completata la risoluzione dei problemi, restituisce il risultato dei controlli.

![Screenshot della pagina Risoluzione dei problemi dell'agente di aggiornamento](../media/update-agent-issues/troubleshoot-page.png)

I risultati vengono visualizzati nella pagina quando sono pronti. Le sezioni dei controlli mostrano il contenuto di ogni controllo.

![Screenshot dei controlli della pagina Risoluzione dei problemi dell'agente di aggiornamento](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controlli dei prerequisiti

### <a name="operating-system"></a>Sistema operativo

Il controllo del sistema operativo verifica se il ruolo di lavoro ibrido per runbook esegue uno dei sistemi operativi indicati nella tabella seguente.

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2012 e versioni successive |È necessario .NET Framework 4.6 o versione successiva. [Scaricare .NET Framework](/dotnet/framework/install/guide-for-developers).<br/> È necessario Windows PowerShell 5.1.  [Scaricare Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).        |

### <a name="net-462"></a>.NET 4.6.2

Il controllo di .NET Framework verifica se nel sistema è installato [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) o versione successiva.

### <a name="wmf-51"></a>WMF 5.1

Il controllo di WMF verifica che nel sistema sia disponibile la versione richiesta di Windows Management Framework (WMF), ovvero [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Questo controllo determina se si usa TLS 1.2 per crittografare le comunicazioni. TLS 1.0 non è più supportato dalla piattaforma. Usare TLS 1.2 per comunicare con Gestione aggiornamenti.

## <a name="connectivity-checks"></a>Controlli della connettività

### <a name="registration-endpoint"></a>Endpoint di registrazione

Questo controllo determina se l'agente può comunicare correttamente con il servizio agente.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con l'endpoint di registrazione. Per un elenco degli indirizzi e delle porte da aprire, vedere [Pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Endpoint delle operazioni

Questo controllo determina se l'agente può comunicare correttamente con il servizio dati del processo di runtime.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con il servizio dati del processo di runtime. Per un elenco degli indirizzi e delle porte da aprire, vedere [Pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Criteri di integrità del servizio della macchina virtuale

### <a name="monitoring-agent-service-status"></a>Monitoraggio dello stato del servizio agente

Questo controllo determina se nel sistema è in esecuzione l'agente di Log Analytics per Windows (`healthservice`). Per altre informazioni sulla risoluzione dei problemi del servizio, vedere [Mancata esecuzione dell'agente di Log Analytics per Windows](hybrid-runbook-worker.md#mma-not-running).

Per reinstallare l'agente di Log Analytics per Windows, vedere [Installare l'agente per Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitoraggio degli eventi del servizio agente

Questo controllo determina se nel log di Azure Operations Manager nel computer sono indicati eventi 4502 relativi alle ultime 24 ore.

Per altre informazioni su questo evento, vedere [Evento 4502 nel log di Operations Manager](hybrid-runbook-worker.md#event-4502).

## <a name="access-permissions-checks"></a>Controlli sulle autorizzazioni di accesso

> [!NOTE]
> Lo strumento di risoluzione dei problemi attualmente non instrada il traffico tramite un server proxy se ne è configurato uno.

### <a name="crypto-folder-access"></a>Accesso alla cartella Crypto

Il controllo dell'accesso alla cartella Crypto determina se l'account di sistema locale ha accesso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Risolvere i problemi offline

È possibile usare la risoluzione dei problemi per un ruolo di lavoro ibrido per runbook offline eseguendo lo script in locale. Ottenere lo script seguente da PowerShell Gallery: [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration). Per eseguire lo script, deve essere installato WMF 4.0 o versione successiva. Per scaricare la versione più recente di PowerShell vedere [Installazione delle diverse versioni di PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

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

[Risolvere i problemi relativi al ruolo di lavoro ibrido per runbook](hybrid-runbook-worker.md).
