---
title: Comprendere i risultati del controllo dell'agente Linux in Gestione aggiornamenti di Azure
description: Informazioni su come risolvere i problemi con l'agente Gestione aggiornamenti.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 075cf254dbf7c5d03f1afac64315f6c6f773159c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60597813"
---
# <a name="understand-the-linux-agent-check-results-in-update-management"></a>Comprendere i risultati del controllo dell'agente Linux in Gestione aggiornamenti

Sono molti i motivi possibili per cui un computer non viene visualizzato come **Pronto** in Gestione aggiornamenti. In Gestione aggiornamenti è possibile controllare l'integrità di un agente del ruolo di lavoro ibrido per determinare il problema sottostante. Questo articolo illustra come eseguire la risoluzione dei problemi per i computer Azure dal portale di Azure e per i computer non Azure nello [scenario offline](#troubleshoot-offline).

L'elenco seguente indica i tre possibili stati di conformità di un computer:

* **Pronto**: l'agente di aggiornamento è implementato e l'ultima visualizzazione risale a meno di 1 ora fa.
* **Disconnesso**: l'agente di aggiornamento è implementato e l'ultima visualizzazione risale a più di 1 ora fa.
* **Non configurato**: l'agente di aggiornamento non viene trovato o non è stato completato l'onboarding.

> [!NOTE]
> Potrebbe esserci un leggero ritardo tra i quali viene illustrato il portale di Azure e lo stato corrente della macchina.

## <a name="start-the-troubleshooter"></a>Avviare la risoluzione dei problemi

Nel caso di computer Azure, facendo clic sul collegamento **Risoluzione dei problemi** nella colonna **Update Agent Readiness** (Idoneità agente di aggiornamento) nel portale, si apre la pagina **Troubleshoot Update Agent** (Risoluzione dei problemi dell'agente di aggiornamento). Nel caso di computer non Azure, facendo clic sul collegamento, si accede a questo articolo. Vedere le istruzioni relative allo scenario offline, per risolvere i problemi di un computer non Azure.

![Pagina con l'elenco delle macchine virtuali](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> I controlli richiedono che la macchina virtuale sia in esecuzione. Se la macchina virtuale non è in esecuzione viene visualizzato il pulsante **Start the VM** (Avvia la macchina virtuale).

Nella pagina **Troubleshoot Update Agent** (Risolvere i problemi dell'agente di aggiornamento) fare clic su **Run Checks** (Esegui controlli) per avviare la risoluzione dei problemi. La risoluzione dei problemi usa [Run command](../../virtual-machines/linux/run-command.md) (Esegui comando) per eseguire uno script nel computer al fine di verificare le dipendenze dell'agente. Una volta completata la risoluzione dei problemi, restituisce il risultato dei controlli.

![Pagina della risoluzione dei problemi](../media/update-agent-issues-linux/troubleshoot-page.png)

Al termine i risultati sono mostrati nella finestra. Le sezioni di controllo forniscono informazioni su ciò che ogni controllo cerca.

![Pagina dei controlli dell'agente di aggiornamento](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controlli dei prerequisiti

### <a name="operating-system"></a>Sistema operativo

Il controllo del sistema operativo verifica se il ruolo di lavoro ibrido per runbook sta eseguendo uno dei sistemi operativi seguenti:

|Sistema operativo  |Note  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti. L'applicazione di patch basata sulla classificazione richiede "yum" per restituire i dati sulla sicurezza che non sono predefiniti in CentOS.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.         |

## <a name="monitoring-agent-service-health-checks"></a>Controlli di integrità dei servizi dell'agente di monitoraggio

### <a name="oms-agent"></a>Agente OMS

Questo controllo verifica che l'agente OMS per Linux sia installato. Per istruzioni su come installarlo, vedere [Installare l'agente per Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="oms-agent-status"></a>Stato dell'agente OMS

Questo controllo verifica che l'agente OMS per Linux sia in esecuzione. Se l'agente non è in esecuzione, è possibile eseguire il comando seguente per tentare di riavviarlo. Per altre informazioni sulla risoluzione dei problemi dell'agente, vedere [Risolvere i problemi di ruoli di lavoro ibridi per runbook (Linux)](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Questo controllo determina se l'agente invia report a più aree di lavoro. Il multihoming non è supportato da Gestione aggiornamenti.

### <a name="hybrid-runbook-worker"></a>ruolo di lavoro ibrido per runbook

Questo controllo verifica che l'agente OMS per Linux abbia il pacchetto del ruolo di lavoro ibrido per runbook. Questo pacchetto è necessario per il funzionamento di Gestione aggiornamenti.

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

Questo controllo determina se l'agente può comunicare correttamente con il servizio agente.

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

## <a name="troubleshoot-offline"></a>Risolvere i problemi offline

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

Per informazioni su come risolvere altri problemi dei ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi di ruoli di lavoro ibridi per runbook](hybrid-runbook-worker.md)

