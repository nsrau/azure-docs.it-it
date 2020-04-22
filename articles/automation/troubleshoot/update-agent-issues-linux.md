---
title: Risoluzione dei problemi relativi all'agente di aggiornamento Linux in Gestione aggiornamenti di Automazione di AzureTroubleshooting Linux update agent issues in Azure Automation Update Management
description: Informazioni su come risolvere i problemi relativi all'agente di aggiornamento di Windows Linux tramite la soluzione di gestione degli aggiornamenti.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: bba1c7e89a9c3bb1c9aa1567e36dd71a40f14636
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679066"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Risolvere i problemi relativi all'agente di aggiornamento LinuxTroubleshoot Linux update agent issues

Ci possono essere molte ragioni per cui il computer non viene visualizzato come pronto (integro) in Gestione aggiornamenti. In Gestione aggiornamenti è possibile controllare l'integrità di un agente di lavoro ibrido per runbook per determinare il problema sottostante. Questo articolo illustra come eseguire lo strumento di risoluzione dei problemi per le macchine di Azure dal portale di Azure e dalle macchine non azure nello [scenario offline.](#troubleshoot-offline) 

L'elenco seguente indica i tre possibili stati di conformità di un computer:

* Pronto: il ruolo di lavoro ibrido per runbook è stato distribuito ed è stato visualizzato l'ultima volta meno di 1 ora fa.
* Disconnesso: il ruolo di lavoro ibrido per runbook è stato distribuito ed è stato visualizzato l'ultima volta 1 ora fa.
* Non configurato: il ruolo di lavoro ibrido per runbook non viene trovato o non ha completato l'onboarding.

> [!NOTE]
> Può esserci un leggero ritardo tra ciò che viene visualizzato dal portale di Azure e lo stato corrente di una macchina.

## <a name="start-the-troubleshooter"></a>Avviare la risoluzione dei problemi

Nel caso di computer Azure, facendo clic sul collegamento **Risoluzione dei problemi** nella colonna **Update Agent Readiness** (Idoneità agente di aggiornamento) nel portale, si apre la pagina Troubleshoot Update Agent (Risoluzione dei problemi dell'agente di aggiornamento). Per le macchine non Azure, il collegamento porta a questo articolo. Vedere le istruzioni offline per risolvere i problemi di un computer non Azure.See the offline instructions to troubleshoot a non-Azure machine.

![Pagina con l'elenco delle macchine virtuali](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> I controlli richiedono che la macchina virtuale sia in esecuzione. Se la macchina virtuale non è in esecuzione, viene visualizzato un pulsante **Avvia la macchina virtuale.**

Nella pagina Troubleshoot Update Agent (Risolvere i problemi dell'agente di aggiornamento) fare clic su **Run Checks** (Esegui controlli) per avviare la risoluzione dei problemi. Lo strumento di risoluzione dei problemi utilizza il [comando Run](../../virtual-machines/linux/run-command.md) per eseguire uno script nel computer per verificare le dipendenze. Una volta completata la risoluzione dei problemi, restituisce il risultato dei controlli.

![Pagina della risoluzione dei problemi](../media/update-agent-issues-linux/troubleshoot-page.png)

Al termine i risultati sono mostrati nella finestra. Le sezioni di controllo forniscono informazioni su ciò che ogni controllo cerca.

![Pagina dei controlli dell'agente di aggiornamento](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controlli dei prerequisiti

### <a name="operating-system"></a>Sistema operativo

Il controllo del sistema operativo verifica se il ruolo di lavoro ibrido per runbook esegue uno dei sistemi operativi seguenti:

|Sistema operativo  |Note  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti. L'applicazione di patch basata sulla classificazione richiede "yum" per restituire i dati sulla sicurezza che non sono predefiniti in CentOS.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.         |

## <a name="monitoring-agent-service-health-checks"></a>Controlli di integrità dei servizi dell'agente di monitoraggio

### <a name="log-analytics-agent"></a>Agente di Log Analytics

Questo controllo garantisce che l'agente Log Analytics per Linux sia installato. Per istruzioni su come installarlo, vedere [Installare l'agente per Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Stato dell'agente di Log Analytics

Questo controllo garantisce che l'agente Log Analytics per Linux sia in esecuzione. Se l'agente non è in esecuzione, è possibile eseguire il comando seguente per tentare di riavviarlo. Per altre informazioni sulla risoluzione dei problemi dell'agente, vedere [Risolvere i problemi di ruoli di lavoro ibridi per runbook (Linux)](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Questo controllo determina se l'agente invia report a più aree di lavoro. Il multihoming non è supportato da Gestione aggiornamenti.

### <a name="hybrid-runbook-worker"></a>ruolo di lavoro ibrido per runbook

Questo controllo verifica se l'agente di Log Analytics per Linux dispone del pacchetto Hybrid Runbook Worker. Questo pacchetto è necessario per il funzionamento di Gestione aggiornamenti.

### <a name="hybrid-runbook-worker-status"></a>Stato del ruolo di lavoro ibrido per runbook

Questo controllo garantisce che il ruolo di lavoro ibrido per runbook sia in esecuzione nel computer. I processi seguenti devono essere presenti se il ruolo di lavoro ibrido per runbook è correttamente in esecuzione. Per altre informazioni, vedere la [risoluzione dei problemi dell'Agente di Log Analytics per Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Controlli della connettività

### <a name="general-internet-connectivity"></a>Connettività Internet generale

Questo controllo verifica che il computer abbia accesso a Internet.

### <a name="registration-endpoint"></a>Endpoint di registrazione

Questo controllo determina se il ruolo di lavoro ibrido per runbook può comunicare correttamente con l'area di lavoro di Automazione di Azure.This check determines if the Hybrid Runbook Worker can properly communicate with Azure Automation the Log Analytics workspace.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con l'endpoint di registrazione. Per un elenco di indirizzi e porte da aprire, vedere l'articolo sulla [pianificazione di rete per i ruoli di lavoro ibridi](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Endpoint delle operazioni

Questo controllo determina se l'agente può comunicare correttamente con il servizio dati del processo di runtime.

Le configurazioni di proxy e firewall devono consentire all'agente del ruolo di lavoro ibrido per runbook di comunicare con il servizio dati del processo di runtime. Per un elenco di indirizzi e porte da aprire, vedere l'articolo sulla [pianificazione di rete per i ruoli di lavoro ibridi](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Endpoint di Log Analytics 1

Questo controllo verifica che il computer abbia accesso agli endpoint necessari per l'agente di Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Endpoint di Log Analytics 2

Questo controllo verifica che il computer abbia accesso agli endpoint necessari per l'agente di Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Endpoint di Log Analytics 3

Questo controllo verifica che il computer abbia accesso agli endpoint necessari per l'agente di Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Risolvere i problemi offline

È possibile usare la risoluzione dei problemi offline per i ruoli di lavoro ibridi per runbook eseguendo lo script in locale. Lo script python [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) è disponibile in Script Center. Di seguito è riportato un esempio dell'output di questo script:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Passaggi successivi

Per risolvere altri problemi relativi ai ruoli di lavoro per i runbook ibridi, vedere [Risoluzione dei problemi - Ruoli di lavoro per runbook ibridi](hybrid-runbook-worker.md).
