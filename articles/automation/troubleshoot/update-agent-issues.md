---
title: Comprendere i risultati del controllo dell'agente Windows in Gestione aggiornamenti di Azure
description: Informazioni su come risolvere i problemi con l'agente Gestione aggiornamenti.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/14/2018
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 5637d409233abc68983bd7cf1e55f406f15e3b59
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432989"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>Comprendere i risultati del controllo dell'agente Windows in Gestione aggiornamenti

Sono molti i motivi possibili per cui un computer non viene visualizzato come **Pronto** in Gestione aggiornamenti. In Gestione aggiornamenti è possibile controllare l'integrità di un agente del ruolo di lavoro ibrido per determinare il problema sottostante. Questo articolo illustra come eseguire la risoluzione dei problemi per i computer Azure dal portale di Azure e per i computer non Azure nello [scenario offline](#troubleshoot-offline).

L'elenco seguente indica i tre possibili stati di conformità di un computer:

* **Pronto**: l'agente di aggiornamento è implementato e l'ultima visualizzazione risale a meno di 1 ora fa.
* **Disconnesso**: l'agente di aggiornamento è implementato e l'ultima visualizzazione risale a più di 1 ora fa.
* **Non configurato**: l'agente di aggiornamento non viene trovato o non è stato completato l'onboarding.

## <a name="start-the-troubleshooter"></a>Avviare la risoluzione dei problemi

Nel caso di computer Azure, facendo clic sul collegamento **Risoluzione dei problemi** nella colonna **Update Agent Readiness** (Idoneità agente di aggiornamento) nel portale, si apre la pagina **Troubleshoot Update Agent** (Risoluzione dei problemi dell'agente di aggiornamento). Nel caso di computer non Azure, facendo clic sul collegamento, si accede a questo articolo. Per risolvere i problemi di un computer non Azure, vedere le [istruzioni relative allo scenario offline](#troubleshoot-offline).

![Aggiornare l'elenco di gestione delle macchine virtuali](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Per controllare l'integrità di un agente, la macchina virtuale deve essere in esecuzione. Se la macchina virtuale non è in esecuzione, viene visualizzato il pulsante **Avvia macchina virtuale**.

Nella pagina **Risoluzione dei problemi dell'agente di aggiornamento** fare clic su **Esegui controlli** per avviare la risoluzione dei problemi. La risoluzione dei problemi usa [Esegui comando](../../virtual-machines/windows/run-command.md) per eseguire uno script nel computer al fine di verificare le dipendenze dell'agente. Una volta completata la risoluzione dei problemi, restituisce il risultato dei controlli.

![Pagina Risoluzione dei problemi dell'agente di aggiornamento](../media/update-agent-issues/troubleshoot-page.png)

I risultati vengono visualizzati nella pagina quando sono pronti. Le [sezioni dei controlli](#prerequisiste-checks) mostrano il contenuto di ogni controllo.

![Controlli di risoluzione dei problemi dell'agente di aggiornamento](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controlli dei prerequisiti

### <a name="operating-system"></a>Sistema operativo

Il controllo del sistema operativo verifica se il ruolo di lavoro ibrido per runbook esegue uno di questi sistemi operativi:

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Supporta solo le valutazioni degli aggiornamenti.         |
|Windows Server 2008 R2 SP1 e versioni successive |È necessario .NET Framework 4.5.1 o versione successiva. ([Scaricare .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> È necessario Windows PowerShell 4.0 o versioni successive. ([Scaricare Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 è consigliato per la sua maggiore affidabilità.  ([Scaricare Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-451"></a>.NET 4.5.1

Il controllo di .NET Framework verifica se nel sistema è installato almeno [.NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653).

### <a name="wmf-51"></a>WMF 5.1

Il controllo WMF verifica se nel sistema è disponibile la versione richiesta di Windows Management Framework (WMF). [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) è la versione minima supportata. Si consiglia di installare [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) per una maggiore affidabilità del ruolo di lavoro ibrido per runbook.

### <a name="tls-12"></a>TLS 1.2

Questo controllo determina se si usa TLS 1.2 per crittografare le comunicazioni. TLS 1.0 non è più supportato dalla piattaforma. È consigliabile che i client usino TLS 1.2 per comunicare con Gestione aggiornamenti.

## <a name="connectivity-checks"></a>Controlli della connettività

### <a name="registration-endpoint"></a>Endpoint di registrazione

Questo controllo determina se l'agente può comunicare correttamente con il servizio agente.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con l'endpoint di registrazione. Per un elenco di indirizzi e porte da aprire, vedere le informazioni sulla [pianificazione di rete per i ruoli di lavoro ibridi](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Endpoint delle operazioni

Questo controllo determina se l'agente può comunicare correttamente con il servizio dati del processo di runtime.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con il servizio dati del processo di runtime. Per un elenco di indirizzi e porte da aprire, vedere le informazioni sulla [pianificazione di rete per i ruoli di lavoro ibridi](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Criteri di integrità del servizio della macchina virtuale

### <a name="monitoring-agent-service-status"></a>Monitoraggio dello stato del servizio agente

Questo controllo determina se `HealthService`, ovvero Microsoft Monitoring Agent, è in esecuzione nel computer.

Per altre informazioni sulla risoluzione dei problemi del servizio, vedere [Microsoft Monitoring Agent non è in esecuzione](hybrid-runbook-worker.md#mma-not-running).

Per reinstallare Microsoft Monitoring Agent, vedere [Installare l'agente per Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitoraggio degli eventi del servizio agente

Questo controllo determina se nel log di Azure Operations Manager nel computer sono comparsi eventi `4502` nelle ultime 24 ore.

Per altre informazioni su questo evento, vedere la [guida alla risoluzione dei problemi](hybrid-runbook-worker.md#event-4502) per questo evento.

## <a name="access-permissions-checks"></a>Controlli sulle autorizzazioni di accesso

### <a name="machinekeys-folder-access"></a>Accesso alla cartella MachineKeys

Il controllo dell'accesso alla cartella Crypto determina se l'account sistema locale ha accesso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Risolvere i problemi offline

È possibile usare la risoluzione dei problemi per un ruolo di lavoro ibrido per runbook offline eseguendo lo script in locale. Lo script [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) è disponibile in PowerShell Gallery. L'output di questo script è simile al seguente:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .Net Framework 4.5+
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
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

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

Per informazioni su come risolvere altri problemi dei ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi di ruoli di lavoro ibridi per runbook](hybrid-runbook-worker.md).

